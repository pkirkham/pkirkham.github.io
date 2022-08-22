---
layout: article
title: Importing from SketchUp into Blender
modified:
categories: blog
excerpt: Importing from Sketchup 2022 into Blender 3.2 whilst preserving outline view structure.
tags: [blender, sketchup, 3d_modelling, architecture, python]
image:
  feature: feature-sketchup-to-blender-1024x256.png
  teaser: teaser-sketchup-to-blender-400x250.jpg
  thumb:
comments: false
---

SketchUp is an intuitive yet powerful 3D modelling program for creating models, yet it does not excel at all tasks. For instance, Blender is more powerful when it comes to rendering and animation. One workflow of interest involves building the 3D model initially in SketchUp and then exporting to Blender where the model can be rendered and animated. The problem is that there is no direct export to Blender from Sketchup, and export to an intermediate file format is more often than not problematic. Similarly there is no built-in importer in Blender that understands the SketchUp native file format.

SketchUp has a [public API that can be used to manipulate the content of the files it creates](https://extensions.sketchup.com/sketchup-sdk). This API can be accessed via the python language to translate the file contents into a format that is understood by Blender. The downside is that as SketchUp and Blender are continually being updated this presents something of a moving target. It requires constant minor updates to ensure that any add-ons for importing between the two programs are kept up to date and functional.

Fortunately, as noted by the [Blender 3D Architect site](https://www.blender3darchitect.com/python-scripts-for-archviz/how-to-import-sketchup-2022-skp-files-to-blender-3-0/) a [Blender add-on importer for SketchUp files originally written by martijnberger](https://github.com/martijnberger/pyslapi) has proven to be useful enough that it has been [forked and kept updated by RedHaloStudio](https://github.com/RedHaloStudio/Sketchup_Importer/releases). This add-on implements a SketchUp importer within Blender that uses the official SketchUp API to read from the native SketchUp file, and the release version has been kept updated as new versions of both programs are released.

## Performance Comparison for Existing Import-Export Methods

A problem with using the add-on importer for Blender is that it does not preserve the outliner structure. To illustrate the issue a simple test SketchUp file has been created. This file contains various different SketchUp objects that are manipulated in different ways and therefore present different challenges for migrating the model between SketchUp and Blender whilst preserving as much information as possible.

Ideally the following data would be imported from SketchUp into Blender to ease animation and rendering tasks:

 - **Visible and hidden meshes.** All model data is imported, and the visibility of objects as set in SketchUp is honoured.
 - **Materials.** Most materials will likely be re-created in Blender using PBR materials. However, each material should be identifiable as unique so that they can be systematically replaced.
 - **Scenes.** Scenes set up what objects should be visible and a specific camera angle. All scenes should be imported into Blender as cameras.
 - **Repeated elements.** SketchUp allows for repeated objects either as groups or components with the same name to be used. All these repeated objects should be imported into Blender and assigned unique names.
 - **Components.** Components are copies of a single object that can be scaled and rotated. Blender should ensure that all these copied components are included in the model with the correct scaling and rotations applied.

A test model was setup in SketchUp to investigate how these elements are handled by export using an intermediate file format and by the Blender add-on for SketchUp native file import.

The configuration is shown in the top-down view of Figure 1 below. The model consists of a very basic house structure with a floor slab and four walls (one of which is cutaway to help show the contents of the house). The house contains a multitude of furniture items which are built from two components. Four <Leg> components arranged together with a flat top to form a <Chair> component. These <Chair> components are then duplicated with the follows:

 - different rotation
 - different scaling 
 - multiple rotated components included as part of a group
 - multiple rotated components included as part of a group that is also rotated itself
 - component given a unique name 'Table' which is also assigned the tag 'Excluded'

In addition primitive geometry for two cylindrical discs and a basic cube object are included. The house has different materials applied to the inside of the walls, outside of the walls and the floor. Selected in this image is the "Table <Chair>" component that is tagged 'Excluded' and should also be exported.

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-05 SketchUp import test setup.jpg" data-lightbox="image-1" data-title="SketchUp model test setup for export into Blender using variety of groups and components.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-05 SketchUp import test setup.jpg" alt=""/>
	</a>
	<figcaption><strong>SketchUp model test setup for export into Blender using variety of groups and components.</strong></figcaption>
</figure>

For export a scene is set up using a perspective view of all objects with the exception of the 'Table' named component for which visibility has been turned off. Therefore the imported model should contain two cameras: the top-down view and the perspective view.

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-05 SketchUp scene setup.jpg" data-lightbox="image-2" data-title="Scene setup to test active camera import into Blender.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-05 SketchUp scene setup.jpg" alt=""/>
	</a>
	<figcaption><strong>Scene setup to test active camera import into Blender.</strong></figcaption>
</figure>

The model is exported as a [Collada digital asset exchange .dae file](https://www.khronos.org/collada/) and as a [Wavefront object .obj file](https://www.loc.gov/preservation/digital/formats/fdd/fdd000507.shtml). These files are then imported into Blender using the built-in importers.

### Collada Digital Asset Exchange Import

Our first import using the Collada digital asset exchange format is shown below. Most of grouped geometry appears to have been imported but components (particularly chair legs) are just not shown or are located incorrectly.

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using Collada.jpg" data-lightbox="image-3" data-title="Import into Blender using Collada .dae as intermediate file.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using Collada.jpg" alt=""/>
	</a>
	<figcaption><strong>Import into Blender using Collada .dae as intermediate file.</strong></figcaption>
</figure>

A positive is that the outliner structure appears to have been preserved. The loose entities and the cube are outside the house group which contains the rest of the model. The import of components within the hierarchy appears to have labelled separate instances, but not all legs are present and none are visible. Finally, the scenes have been imported as cameras, including the last SketchUp view when the model was saved.

### Wavefront Object Import

The next attempted import is via the Wavefront object format. Examining the exported file shows that everything appears to have been exported. There are two files created: a material library (.mtl) and an ASCII text file that describes the geometry (.obj). There are two Wavefront object import methods within Blender. These are a legacy importer and an experimental one. Neither appear to import any data.

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using Wavefront.jpg" data-lightbox="image-4" data-title="Import into Blender using Wavefront .obj as intermediate file.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using Wavefront.jpg" alt=""/>
	</a>
	<figcaption><strong>Import into Blender using Wavefront .obj as intermediate file.</strong></figcaption>
</figure>

The material library appears to have been imported, but there is no geometry information. This is surprising because the geometry is fully described in the .obj file -- an advantage of the Wavefront object file format is that it is encoded in ASCII meaning it can easily be deciphered and read using any text editor.

### SketchUp Importer Add-on for Blender Import

Given the problems associated with using an intermediate file format, it is perhaps not surprising that a direct importer for SketchUp files was developed. This uses Python bindings to the SketchUp API. Blender has a Python interpreter built-in, so an add-on script written in Python can directly access a SketchUp file using Python commands that invoke the appropriate methods in the API. Because the official API is being used, it is very likely that updates to the SketchUp file format can be handled by updating the Python bindings to that API.

The downside is that this is a fairly low-level import method that reads vertex, face, and material data directly. This leaves it up to the writer of the Python import script how to interpret the data that is accessed from the SketchUp file, and to store it within Blender's own internal model structure.

An import using the latest add-on is shown below:

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using add-on importer.jpg" data-lightbox="image-5" data-title="Import into Blender using SketchUp importer add-on.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using add-on importer.jpg" alt=""/>
	</a>
	<figcaption><strong>Import into Blender using SketchUp importer add-on.</strong></figcaption>
</figure>

The model appears to have been imported but there are several issues:

1. Geometry that has been hidden using the 'Excluded' tag was not imported. In itself this is not a show-stopper as the workaround would simply be to ensure that all geometry to be rendered in Blender is visible when saving the SketchUp file.
2. The outliner structure is completely missing. The script iterates over all the geometry in the SketchUp file, and simply imports it into new meshes. This is a show-stopper if the intent is to use grouped objects for animation.

## Improving the Python Script

In Windows the Python script can be found in the "%APPDATA%\Blender Foundation\Blender\3.2\scripts\addons\sketchup_importer" folder and is called `__init__.py` -- note double underscore characters either side of the 'init'.

Given that Python is not a language I use often, there was a [learning curve to go up](https://wiki.python.org/moin/MovingToPythonFromOtherLanguages) in understanding how this worked. Fortunately, with both the [SketchUp API SDK documentation](https://extensions.sketchup.com/developers/sketchup_c_api/sketchup/index.html) and [Blender API reference](https://docs.blender.org/api/current/index.html) documents, it is possible to figure out what is going on. With some modifications to the script it was possible to preserve the hierarchy, and fix importing of the non-visible tagged objects and scenes.

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using modified add-on importer.jpg" data-lightbox="image-6" data-title="Import into Blender using SketchUp modified importer add-on.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-06 Blender import using modified add-on importer.jpg" alt=""/>
	</a>
	<figcaption><strong>Import into Blender using SketchUp modified importer add-on.</strong></figcaption>
</figure>

Overall this is looking like a good improvement although some testing is still needed. A colleague of mine has let me know that some components in a different model are not located correctly. This can happen with deeply nested components and groups. Nonetheless, this updated script is a good start.

<div class="notice-info"><strong>UPDATE 22-Aug-2022:</strong> I've continued to work on the import script and have solved the issues with nesting of groups and components. The issue turned out to be related to a mix of objects with the same name not being recognised as separate entities, and with transformations not being correctly applied to groups containing both nested loose mesh data and groups. These problems have been fixed and the importer is now a lot more robust. I've noticed that there are still some occasional import issues. This occurs where groups have had a few transformations applied. The solution is to explode and re-group the geometry in SketchUp which seems to reset the transformations and fixes the import problems.</div>

Once the model has been imported into Blender, it is now possible to use Cycles for rendering. An example using the camera set to Scene 1 and with the 'Excluded' table set not to show in the render is below. There are no lights in the model so the render is a little on the dark side (environmental lighting only).

<figure>
	<a href="{{ site.url }}/images/Screenshot 2022-07-06 Blender modified import render.jpg" data-lightbox="image-7" data-title="Test render using Scene 1 imported camera and hiding 'Excluded' geometry.">
		<img src="{{ site.url }}/images/Screenshot 2022-07-06 Blender modified import render.jpg" alt=""/>
	</a>
	<figcaption><strong>Test render using Scene 1 imported camera and hiding 'Excluded' geometry.</strong></figcaption>
</figure>

### Download the Modified Script

If you'd like to try preserving the hierarchy of your SketchUp model when importing from SketchUp into Blender, the `__init__.py` file can be downloaded. Simply replace the file found in the Blender add-on folder "%APPDATA%\Blender Foundation\Blender\3.2\scripts\addons\sketchup_importer" and restart Blender.

<a href="{{ site.url }}/downloads/__init__.py" class="btn-inverse">Download `__init__.py` (22-Aug-2022 version)</a>

It doesn't look as if the original script by martijnberger has been updated on GitHub for a while. If I ever get around to testing this more thoroughly I'll look at creating a pull request to update the original script, or create my own fork.