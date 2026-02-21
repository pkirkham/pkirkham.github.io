---
layout: article
title: Handling Units with Generics in Java
modified:
categories: pyrus
excerpt: How generics are used to enforce unit consistency with Java in Pyrus.
tags: [java, generics, programming, strong_typing, units]
image:
  feature: feature-strong-typing-1024x256.jpg
  teaser: teaser-strong-typing-400x250.jpg
  linkedin: teaser-strong-typing-400x250.jpg
  thumb:
comments: false
mathjax: true
---

A recent article posted to LinkedIn discussed the [history of the Ada programming language](https://www.linkedin.com/pulse/today-history-1995-ada-95-gets-real-carl-watts-3c3ze/). This caught my attention for the wonderful opening line:

> When "strongly typed" meant "we prefer not to crash fighter jets."

Just brilliant.

A brief explanation for those that aren't vaguely familiar with differences in programming languages. Typing is the use of a specific data type that is used to represent a variable. At the simplest level are primitives: the most basic form of data type include integers, floating point numbers and strings. At a more complex level are objects, or classes. These can comprise a collection of different primitives.

If you are in any way logical and sensible, you might expect that trying to multiply a string ("two") and an integer (2) cannot be done, and therefore this should lead to an error. And it does. After all a string is a sequence of characters (representing letters, numbers and punctuation), and an integer is an actual number. How do I multiply a series of characters by a number. It isn't mathematics!

Yet the devil is in the detail, and for different programming languages [how they handle types leads](https://en.wikipedia.org/wiki/Type_safety) to two fundamentally different philosophies:

 - **Strongly typed:** the type associated with a variable is fixed. If `var_a` and `var_b` are two different types, say `var_a` is a string = "2" and `var_b` is an integer = 2, then these are considered incompatible. It is not possible to add them together using `var_c = var_a + var_b`. Because the compiler knows the types before the program is even run, the program will not compile and will flag an error.
 - **Weakly typed:** the type associated with a variable is flexible and the environment in which the program is being run will attempt to infer the type. If `var_a` and `var_b` are two different types, say `var_a` is a string = "2" and `var_b` is an integer = 2, then the program will run. Depending on the language, `var_c = var_a + var_b` might lead to a var_c as a string "22", an integer 4 or throw a runtime exception.

There is also dynamic typing and static typing. With dynamic typing the type of a value assigned to a variable is not fixed and can be changed. The type is inferred on first use. With static typing the programmer must declare the type of the variable in the program and it cannot be changed.

Naturally some languages take the approach that static and strongly typed variables are like putting on a strait jacket. It requires more effort to write the program and isn't as flexible. That's all well and good up until your program is causing runtime exceptions due a bug related to types that is hard to find.

I'd rather not have to deal with avoidable problems, and it is one of the reasons I was attracted to the Java language when it came out. These days Java is often criticised for its verboseness. Because of the static strong typing, more characters are required to express a particular concept in a program. Whilst that might be seen as inefficient compared to more concise languages like Python (a dynamic strongly typed language), having the compiler catch errors before the program even runs is a godsend. I also prefer not to crash fighter jets!

## Brief Explanation of Generics in Java

Java doubles down on the static strongly typed philosophy with the use of generics. With Objects we can define an associated type. For example, consider a list. The list to contains a collection of Objects, which is useful, but ultimately suffers from the same problems as weak typing. We simply don't know what type of object we are going to get when accessing objects in the list, and thus it is not possible to enforce compile time type safety.

For example consider a very basic list containing two integers which we would like to add. You can't get more simple than:

$$2+2=4$$

A naive implementation of this in Java might look like:

```java
ArrayList al = new ArrayList();
al.add("2"); // adding number 2 as a String
al.add(2); // adding number 2 as an integer primitive
int var_a = (int) al.get(0);
int var_b = (int) al.get(1);
int var_c = var_a + var_b;
System.out.println(String.format("%d", var_c));
```

The code will compile, but attempting to run this code will lead to `java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer`. Let's just agree that runtime exceptions are bad and move on...

With generics we can instead specify that our list should only contain Integer objects. Our revised code now looks like:

```java
ArrayList<Integer> al = new ArrayList<>();
al.add("2");
al.add(2);
int var_a = al.get(0);
int var_b = al.get(1);
int var_c = var_a + var_b;
System.out.println(String.format("%d", var_c));
```

This time our program will not even compile. Attempts to compile this throw an error: `error: incompatible types: String cannot be converted to Integer`. This checking at compile time is incredibly useful as it helps to prevent code with hard-to-find bugs ever being produced. In addition, most integrated development environments will do similar error checking **as you type** meaning that the errors are probably found and fixed before even attempting to compile.

### Handling Units with Generics in Java

Generics are a powerful addition to the Java language, and allow us to specify units associated with values. Thus a length isn't just '2'. It is 2 feet or 2 metres etc. The history of introducing units into Java has a fraught history. The original proposed version was based on the [JScience](https://jscience.org/) library which I started using early on when writing Pyrus. Over the years various attempts were made to have this incorporated as part of the base Java language, each time leading to a requirement to refactor my codebase.

Eventually [JSR-385 was accepted into the Java language](https://jcp.org/en/jsr/detail?id=385) and defines the specification of quantities (mass, length, time etc.) and units (gram, metre, second etc.). These units of measurement are part of the standard java language. For reasons I cannot begin to fathom, the use of these units is far from logical, and depends on various interfaces. For better or worse, in Pyrus I ended up sticking with a version of the JScience version 4.3.1 class `Amount` which has served me well over many years and allows reasonably straightforward creation of objects that conform to a particular `Quantity` type and can be expressed in the form of a double precision value and an associated `Unit`.

As an example of how to use an `Amount`, consider the following example class which wraps a length value. The main point of this class is to illustrate the imports that are required and the use of the `Amount.valueOf` static method that allows creation of an `Amount` based on a value and a `Unit` (in this case a METRE which is part of the defined units in `javax.measure.unit.SI.*`).

```java
import javax.measure.quantity.Length;
import org.jscience.physics.amount.Amount;
import static javax.measure.unit.SI.*;

public class LengthObject {
	public Amount<Length> length;

    /**
     * Sets the length of this object.
     *
     * @param len the length in metres
     */
    public LengthObject(double len) {
        this.length = Amount.valueOf(len, METRE);
    }
}
```

Once we have our unit, we can get its value in any other unit as follows:

```java
Amount<Length> length = Amount.valueOf(1.0, METRE);
double length_metre = length.doubleValue(METRE);
double length_foot = length.doubleValue(FOOT);
System.out.println(String.format("Length = %.3f m or %.3f ft", length_metre, length_foot));
```

Which produces the output `Length = 1.000 m or 3.281 ft`.

Use of the `Amount` class allows us to create methods that are defined in terms of their expected quantities, and allows us to handle units without ambiguity within the method logic. For example, to calculate the area of a rectangle we could use a method as follows:

```java
/**
 * Calculate the area of a rectangle.
 * 
 * @param width width of the rectangle
 * @param height height of the rectangle
 * @return area of the rectangle
 */
private Amount<Area> rectangleArea(Amount<Length> width, Amount<Length> height) {
    double area_sqm = width.doubleValue(METRE) * height.doubleValue(METRE);
    return Amount.valueOf(area_sqm, SQUARE_METRE);
}
```

The benefit is that we don't need to specify the units for the width and height. They could be set using any compatible unit such as a metre, foot, centimetre, mile etc. It doeesn't matter. The correct unit conversions are handled within the method, and an area is returned. The returned `Amount<Area>` can in turn be converted to any other unit desired such as a square foot.

By leveraging JSR-385 Units of Measurement and generics in Java, we can add unit safety to our compile time checks and improve the robustness of our code.

## Oilfield Units in Pyrus

The benefits of using clearly defined units should be evident to even the most skeptical engineer. Whilst JSR-385 defines a range of common S.I. and non-S.I. units, it does not cover the range of oilfield units that are typically used by the oil and gas industry. For example, whilst megaPascal (MPa) is commonly used for pressure, the oilfield likes to define this using pounds per square inch, both relative to absolute conditions (psia) or gauge (psig). Furthermore, there are a variety of quantities that are encountered such as permeability which are not defined.

To address this, the quantities and associated units are extended using the `Oilfield` class which allows us to convert units like:

```java
Amount<Pressure> bhp = Amount.valueOf(5000.0, POUND_PER_SQUARE_INCH);
double p_psig = bhp.doubleValue(POUND_PER_SQUARE_INCH_GAUGE);
double p_mpa = bhp.doubleValue(MEGA(PASCAL));
System.out.println(String.format("Pressure = %.3f psig or %.3f MPa", p_psig, p_mpa));
```

This gives the pressure in converted units as `Pressure = 4985.304 psig or 34.474 MPa`.

A variety of addtional quantities have been added in Pyrus.

| Quantity | Description | Default Unit |
| --- | --- | --- |
| Diffusivity | Represents diffusivity being a rate of diffusion, a measure of the rate at which particles or heat or fluids can spread. | cm<sup>2</sup> / s |
| EnergyDensity | Represents energy density being the calorific heating value of a substance for a given volume. | Btu / ft<sup>3</sup> |
| Enthalpy | Represents the sum of a thermodynamic system's internal energy and the product of its pressure and volume. | J / mol |
| JouleThomsonEffect | Represents the amount of cooling or heating experienced by a gas when it undergoes a change in pressure (expansion or compression). | K / bar |
| MassRate | In physics and engineering, mass flow rate is the mass of a substance which passes per unit of time. Its unit is kilogram per second in SI units. | kg / s |
| Permeability | Represents permeability being the resistance to flow of a fluid through a porous matrix. | Darcy |
| SpecificEnergy | Represents potential combustible energy contained within of a substance for a given mass. This is also known as the specific energy which is energy per unit mass. | Btu / lbm |
| SpecificHeatCapacity | The specific heat capacity (symbol c) of a substance is the amount of heat that must be added to one unit of mass of the substance in order to cause an increase of one unit in temperature. | J / kg &middot; K |
| SurfaceTension | Surface tension is the tendency of liquid surfaces to shrink into the minimum surface area possible and has the dimension of force per unit length, or of energy per unit area. | Dyne / cm |
| ThermalRate | The change in temperature per unit length. Typically used to describe a geothermal temperature gradient. | &deg;R / ft |
| VolumetricRate | Represents volumetric production rate per day. | m<sup>3</sup> / day |

### Java Code for Oilfield Units

The `Oilfield` class is constantly being expanded as I encounter new quantities and units that need to be handled in the various equations that I write. Generally I plan on allowing this class to grow organically rather than attempting to invest large amounts of time trying to create a comprehensive solution at the outset.

```java
/*
 * This software is the property of New Wave Geophysical
 * Created on 9 December 2005
 * Last modified on 20 February 2025
 */
package au.com.newwavegeo.peteng;

import java.util.Collections;
import java.util.Set;
import javax.measure.converter.MultiplyConverter;
import javax.measure.quantity.*;
import javax.measure.unit.ProductUnit;
import javax.measure.unit.SystemOfUnits;
import javax.measure.unit.Unit;
import javax.measure.unit.UnitFormat;
import javolution.util.FastSet;
import org.jscience.physics.amount.Amount;

import static javax.measure.unit.NonSI.*;
import static javax.measure.unit.SI.*;
import static javax.measure.unit.SI.MetricPrefix.*;

/**
 * Similar to the NonSI class in JScience library, this class defines additional units that are commonly in use by the
 * oil industry but are not included in the NonSI or SI defined units. As of JScience 5.0 there is now included in the
 * underlying JSR-275 spec a UCUM definition of standard units. This has been used here where it makes sense to do so.
 * However to maintain compatibility with existing Pyrus source, and also so as to implement our custom quantity types,
 * we retain this class and its defined units.
 *
 * @author Peter Kirkham
 */
@SuppressWarnings("rawtypes")
public final class Oilfield extends SystemOfUnits {

    /* == DEFINE CONSTANTS == */
    static final MultiplyConverter E12 = new MultiplyConverter(1e12);
    static final MultiplyConverter E9 = new MultiplyConverter(1e09);
    static final MultiplyConverter E6 = new MultiplyConverter(1e06);
    static final MultiplyConverter E3 = new MultiplyConverter(1e03);

    /* == DECLARE GLOBAL VARIABLES == */

    /* == ENABLE LOGGING == */
    transient private static final java.util.logging.Logger LOG
            = java.util.logging.Logger.getLogger(Oilfield.class.getName());

    /**
     * Default constructor (prevents this class from being instantiated).
     */
    private Oilfield() {
    }

    /**
     * Holds collection of Oilfield units. For some reason this needs to appear right at the top of the class.
     */
    private static final FastSet<Unit<?>> UNITS = new FastSet<>();

    /* == DEFINE OILFIELD UNITS FROM SI AND NON SI UNITS == */
    public static final Unit<Area> SQUARE_INCH
            = oilfield(new ProductUnit<>(INCH.pow(2)));

    public static final Unit<Area> SQUARE_FOOT
            = oilfield(new ProductUnit<>(FOOT.pow(2)));

    public static final Unit<Area> SQUARE_KILOMETRE
            = oilfield(new ProductUnit<>(KILO(METRE).pow(2)));

    public static final Unit<Area> ACRE
            = oilfield(javax.measure.unit.UCUM.ACRE_US_SURVEY);

    public static final Unit<Volume> CUBIC_FOOT
            = oilfield(new ProductUnit<>(FOOT.pow(3)));

    public static final Unit<Volume> CUBIC_CENTIMETRE
            = oilfield(new ProductUnit<>(CENTIMETRE.pow(3)));

    public static final Unit<Volume> ACRE_FOOT
            = oilfield(new ProductUnit<>(ACRE.times(FOOT)));

    // Barrel is exactly 42 US liquid gallons, 1 US liquid gallon is exactly 231 cubic inches
    public static final Unit<Volume> BARREL
            = oilfield(CUBIC_INCH.times(231L).times(42L).asType(Volume.class));

    public static final Unit<Velocity> FEET_PER_MINUTE
            = oilfield(FEET_PER_SECOND.divide(60L).asType(Velocity.class));

    public static final Unit<Pressure> POUND_PER_SQUARE_INCH
            = oilfield(KILO(PASCAL).times(6.8947572931783).asType(Pressure.class));

    public static final Unit<Pressure> POUND_PER_SQUARE_INCH_GAUGE
            = oilfield(POUND_PER_SQUARE_INCH.plus(Amount.valueOf(1.0, ATMOSPHERE).doubleValue(POUND_PER_SQUARE_INCH)));

    public static final Unit<Pressure> BAR_GAUGE
            = oilfield(BAR.plus(Amount.valueOf(1.0, ATMOSPHERE).doubleValue(BAR)));

    public static final Unit<Pressure> POUND_PER_SQUARE_FOOT
            = oilfield(POUND_PER_SQUARE_INCH.times(0.0069444).asType(Pressure.class));

    public static final Unit<DynamicViscosity> CENTIPOISE
            = oilfield(CENTI(POISE).asType(DynamicViscosity.class));

    public static final Unit<DynamicViscosity> PASCAL_SECOND
            = oilfield(CENTIPOISE.times(1000.0));

    public static final Unit<DynamicViscosity> POUND_PER_FOOT_SECOND
            = oilfield(new ProductUnit<>(POUND.divide(FOOT).divide(SECOND)));

    public static final Unit<VolumetricDensity> GRAM_PER_CUBIC_CENTIMETRE
            = oilfield(new ProductUnit<>(GRAM.divide(CENTIMETRE.pow(3))));

    public static final Unit<VolumetricDensity> KILOGRAM_PER_LITRE
            = oilfield(new ProductUnit<>(KILOGRAM.divide(LITRE)));

    public static final Unit<VolumetricDensity> KILOGRAM_PER_CUBIC_METRE
            = oilfield(new ProductUnit<>(KILOGRAM.divide(METRE.pow(3))));

    public static final Unit<VolumetricDensity> POUND_PER_CUBIC_FOOT
            = oilfield(new ProductUnit<>(POUND.divide(CUBIC_FOOT)));

    public static final Unit<VolumetricDensity> POUND_PER_GALLON
            = oilfield(new ProductUnit<>(POUND.divide(GALLON_UK)));

    public static final Unit<VolumetricDensity> PSI_PER_FOOT
            = oilfield(new ProductUnit<>(POUND.divide(SQUARE_INCH).divide(FOOT)));

    public static final Unit<VolumetricDensity> PASCAL_PER_METRE
            = oilfield(new ProductUnit<>(POUND.divide(SQUARE_INCH).divide(6894.7572931783).divide(METRE)));

    public static final Unit<Dimensionless> FRACTION
            = oilfield(Unit.ONE);

    // Conversion to SI units uses factor that is reciprocal of 1.013250 (conversion factor from atm to bar)
    public static final Unit<AmountOfSubstance> LBMMOL
            = oilfield(MOLE.divide(453.59237).asType(AmountOfSubstance.class));

    @SuppressWarnings("unchecked")
    public static final Unit<Permeability> DARCY = 
            (Unit<Permeability>) oilfield((METRE.times(100)).pow(2).divide(1.01325e08)); // must use cast syntax here

    public static final Unit<Permeability> MILLIDARCY
            = oilfield(MILLI(DARCY).asType(Permeability.class));

    public static final Unit<Energy> BTU
            = oilfield(javax.measure.unit.UCUM.BTU_INTERNATIONAL_TABLE);

    public static final Unit<EnergyDensity> BTU_PER_CUBIC_FOOT
            = oilfield(new ProductUnit<>(BTU.divide(CUBIC_FOOT)));

    public static final Unit<EnergyDensity> KILOJOULE_PER_CUBIC_METRE
            = oilfield(new ProductUnit<>(KILO(JOULE).divide(CUBIC_METRE)));

    public static final Unit<SpecificEnergy> BTU_PER_POUND
            = oilfield(new ProductUnit<>(BTU.divide(POUND)));

    public static final Unit<SpecificEnergy> JOULE_PER_GRAM
            = oilfield(new ProductUnit<>(JOULE.divide(GRAM)));

    public static final Unit<SpecificEnergy> KILOJOULE_PER_KILOGRAM
            = oilfield(new ProductUnit<>(KILO(JOULE).divide(KILOGRAM)));

    public static final Unit<SpecificEnergy> MEGAJOULE_PER_KILOGRAM
            = oilfield(new ProductUnit<>(MEGA(JOULE).divide(KILOGRAM)));

    public static final Unit<Enthalpy> JOULE_PER_MOLE
            = oilfield(new ProductUnit<>(JOULE.divide(MOLE)));

    public static final Unit<Enthalpy> BTU_PER_LBMMOL
            = oilfield(JOULE_PER_MOLE.divide(0.42992261392949266));

    public static final Unit<SpecificHeatCapacity> JOULE_PER_KILOGRAM_KELVIN
            = oilfield(new ProductUnit<>(JOULE.divide(KILOGRAM.times(KELVIN))));

    public static final Unit<SpecificHeatCapacity> BTU_PER_POUND_RANKINE
            = oilfield(new ProductUnit<>(BTU.divide(POUND.times(RANKINE))));

    public static final Unit<JouleThomsonEffect> KELVIN_PER_BAR
            = oilfield(new ProductUnit<>(KELVIN.divide(BAR)));

    public static final Unit<JouleThomsonEffect> KELVIN_PER_KPA
            = oilfield(new ProductUnit<>(KELVIN.divide(KILO(PASCAL))));

    public static final Unit<JouleThomsonEffect> RANKINE_PER_PSI
            = oilfield(new ProductUnit<>(RANKINE.divide(POUND_PER_SQUARE_INCH)));

    public static final Unit<Compressibility> PER_PASCAL
            = oilfield(new ProductUnit<>(Unit.ONE.divide(PASCAL)));

    public static final Unit<Compressibility> PER_KPA
            = oilfield(new ProductUnit<>(Unit.ONE.divide(KILO(PASCAL))));

    public static final Unit<Compressibility> PER_PSI
            = oilfield(new ProductUnit<>(Unit.ONE.divide(POUND_PER_SQUARE_INCH)));

    public static final Unit<Compressibility> PER_BAR
            = oilfield(new ProductUnit<>(Unit.ONE.divide(BAR)));

    public static final Unit<Compressibility> PER_ATM
            = oilfield(new ProductUnit<>(Unit.ONE.divide(ATMOSPHERE)));

    public static final Unit<VolumetricRate> CUBIC_METRE_PER_DAY
            = oilfield(new ProductUnit<>(CUBIC_METRE.divide(DAY)));

    public static final Unit<Diffusivity> SQUARE_CENTIMETRE_PER_SECOND
            = oilfield(new ProductUnit<>(CENTIMETRE.pow(2).divide(SECOND)));

    public static final Unit<Diffusivity> SQUARE_METRE_PER_SECOND
            = oilfield(new ProductUnit<>(SQUARE_METRE.divide(SECOND)));

    public static final Unit<Diffusivity> SQUARE_FOOT_PER_DAY
            = oilfield(new ProductUnit<>(SQUARE_FOOT.divide(DAY)));

    public static final Unit<Diffusivity> SQUARE_METRE_PER_DAY
            = oilfield(new ProductUnit<>(SQUARE_METRE.divide(DAY)));

    public static final Unit<Diffusivity> SQUARE_CENTIMETRE_PER_HOUR
            = oilfield(new ProductUnit<>(CENTIMETRE.pow(2).divide(HOUR)));

    public static final Unit<VolumetricRate> BARREL_PER_DAY
            = oilfield(new ProductUnit<>(BARREL.divide(DAY)));

    public static final Unit<VolumetricRate> CUBIC_FOOT_PER_DAY
            = oilfield(new ProductUnit<>(CUBIC_FOOT.divide(DAY)));

    public static final Unit<VolumetricRate> CUBIC_FOOT_PER_HOUR
            = oilfield(new ProductUnit<>(CUBIC_FOOT.divide(HOUR)));

    public static final Unit<VolumetricRate> CUBIC_FOOT_PER_MINUTE
            = oilfield(new ProductUnit<>(CUBIC_FOOT.divide(MINUTE)));

    public static final Unit<VolumetricRate> CUBIC_FOOT_PER_SECOND
            = oilfield(new ProductUnit<>(CUBIC_FOOT.divide(SECOND)));

    public static final Unit<SurfaceTension> DYNE_PER_CENTIMETRE
            = oilfield(new ProductUnit<>(DYNE.divide(CENTIMETRE)));   

    public static final Unit<SurfaceTension> PASCAL_METRE
            = oilfield(new ProductUnit<>(PASCAL.times(METRE)));   

    public static final Unit<MassRate> KILOGRAM_PER_SECOND
            = oilfield(new ProductUnit<>(KILOGRAM.divide(SECOND)));   

    public static final Unit<MassRate> POUND_PER_DAY
            = oilfield(new ProductUnit<>(POUND.divide(DAY)));

    public static final Unit<MassRate> POUND_PER_HOUR
            = oilfield(new ProductUnit<>(POUND.divide(HOUR)));

    public static final Unit<MassRate> POUND_PER_SECOND
            = oilfield(new ProductUnit<>(POUND.divide(SECOND)));

    public static final Unit<ThermalRate> RANKINE_PER_FOOT
            = oilfield(new ProductUnit<>(RANKINE.divide(FOOT)));

    public static final Unit<ThermalRate> KELVIN_PER_METRE
            = oilfield(new ProductUnit<>(KELVIN.divide(METRE)));

    // Set the formats for all the new unit types where appropriate.
    static {

        // for some reason JSR-275 0.9.4 is changing this to 'ng'
        UnitFormat.getStandard().getSymbolMap().label(FOOT, "ft");
        UnitFormat.getStandard().getSymbolMap().label(ACRE, "ac");
        UnitFormat.getStandard().getSymbolMap().label(ACRE_FOOT, "ac-ft");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_FOOT, "cf");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_CENTIMETRE, "cc");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT.times(1e3), "mcf");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT.times(1e6), "mmcf");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_METRE.times(1e6), "mmcm");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT.times(1e9), "bcf");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT.times(1e12), "tcf");
        UnitFormat.getStandard().getSymbolMap().label(BARREL, "bbl");
        UnitFormat.getStandard().getSymbolMap().alias(BARREL.times(1e03), "mbbl");
        UnitFormat.getStandard().getSymbolMap().alias(BARREL.times(1e06), "mmbbl");
        UnitFormat.getStandard().getSymbolMap().label(FEET_PER_MINUTE, "fpm");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_GALLON, "ppg");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_HOUR, "lbm/hr");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_SECOND, "lbm/sec");
        UnitFormat.getStandard().getSymbolMap().label(PSI_PER_FOOT, "psi/ft");
        UnitFormat.getStandard().getSymbolMap().label(PASCAL_PER_METRE, "Pa/m");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_SQUARE_INCH, "psia");
        UnitFormat.getStandard().getSymbolMap().alias(POUND_PER_SQUARE_INCH, "psi");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_SQUARE_INCH_GAUGE, "psig");
        UnitFormat.getStandard().getSymbolMap().label(POUND_PER_SQUARE_FOOT, "psf");
        UnitFormat.getStandard().getSymbolMap().label(LBMMOL, "lbm-mol");
        UnitFormat.getStandard().getSymbolMap().label(CENTIPOISE, "cP");
        UnitFormat.getStandard().getSymbolMap().label(PASCAL_SECOND, "Pa-s");
        UnitFormat.getStandard().getSymbolMap().label(DARCY, "D");
        UnitFormat.getStandard().getSymbolMap().label(MILLIDARCY, "mD");
        UnitFormat.getStandard().getSymbolMap().label(BTU, "Btu");
        UnitFormat.getStandard().getSymbolMap().alias(BTU.times(1e6), "mmBtu");
        UnitFormat.getStandard().getSymbolMap().label(PER_BAR, "1/bar");
        UnitFormat.getStandard().getSymbolMap().label(PER_PASCAL, "1/Pa");
        UnitFormat.getStandard().getSymbolMap().label(PER_KPA, "1/kPa");
        UnitFormat.getStandard().getSymbolMap().label(PER_PSI, "1/psi");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_METRE_PER_DAY.times(1e06), "mmcmd");
        UnitFormat.getStandard().getSymbolMap().label(BARREL_PER_DAY, "bpd");
        UnitFormat.getStandard().getSymbolMap().alias(BARREL_PER_DAY.times(1e03), "kbpd");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_FOOT_PER_DAY, "cfd");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_FOOT_PER_HOUR, "cfph");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_FOOT_PER_MINUTE, "cfpm");
        UnitFormat.getStandard().getSymbolMap().label(CUBIC_FOOT_PER_SECOND, "cfps");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT_PER_DAY.times(1e03), "mcfd");
        UnitFormat.getStandard().getSymbolMap().alias(CUBIC_FOOT_PER_DAY.times(1e06), "mmcfd");
        UnitFormat.getStandard().getSymbolMap().alias(DYNE_PER_CENTIMETRE, "dyn/cm");
        UnitFormat.getStandard().getSymbolMap().alias(PASCAL_METRE, "Pa-m");
        UnitFormat.getStandard().getSymbolMap().alias(GRAM_PER_CUBIC_CENTIMETRE, "g/cc");
        UnitFormat.getStandard().getSymbolMap().alias(BTU_PER_POUND, "Btu/lbm");
        UnitFormat.getStandard().getSymbolMap().alias(JOULE_PER_GRAM, "J/g");
        UnitFormat.getStandard().getSymbolMap().alias(KILOJOULE_PER_KILOGRAM, "kJ/kg");
        UnitFormat.getStandard().getSymbolMap().alias(MEGAJOULE_PER_KILOGRAM, "MJ/kg");
        UnitFormat.getStandard().getSymbolMap().alias(BTU_PER_POUND_RANKINE, "Btu/lbm-°R");
        UnitFormat.getStandard().getSymbolMap().alias(JOULE_PER_KILOGRAM_KELVIN, "J/kg-K");
        UnitFormat.getStandard().getSymbolMap().alias(RANKINE_PER_PSI, "°R/psi");
        UnitFormat.getStandard().getSymbolMap().alias(KELVIN_PER_BAR, "K/bar");
        UnitFormat.getStandard().getSymbolMap().alias(KELVIN_PER_KPA, "K/kPa");
        UnitFormat.getStandard().getSymbolMap().alias(RANKINE_PER_FOOT, "°R/ft");
        UnitFormat.getStandard().getSymbolMap().alias(KELVIN_PER_METRE, "K/m");
    }

    /**
     * Returns the specified unit multiplied by the factor 10<sup>12</sup>
     *
     * @param <Q>
     * @param unit any unit.
     * @return unit.multiply(1e12).
     */
    public static <Q extends Quantity> Unit<Q> TRILLION(Unit<Q> unit) {
        return unit.transform(E12);
    }

    /**
     * Returns the specified unit multiplied by the factor 10<sup>9</sup>
     *
     * @param <Q>
     * @param unit any unit.
     * @return unit.multiply(1e9).
     */
    public static <Q extends Quantity> Unit<Q> BILLION(Unit<Q> unit) {
        return unit.transform(E9);
    }

    /**
     * Returns the specified unit multiplied by the factor 10<sup>6</sup>
     *
     * @param <Q>
     * @param unit any unit.
     * @return unit.multiply(1e6).
     */
    public static <Q extends Quantity> Unit<Q> MILLION(Unit<Q> unit) {
        return unit.transform(E6);
    }

    /**
     * Returns the specified unit multiplied by the factor 10<sup>3</sup>
     *
     * @param <Q>
     * @param unit any unit.
     * @return unit.multiply(1e3).
     */
    public static <Q extends Quantity> Unit<Q> THOUSAND(Unit<Q> unit) {
        return unit.transform(E3);
    }

    /**
     * Static method to force class initialization.
     */
    static void initializeClass() {
    }

    /**
     * Adds a new unit to the collection.
     *
     * @param unit the unit being added.
     * @return unit.
     */
    private static <U extends Unit<?>> U oilfield(U unit) {
        UNITS.add(unit);
        return unit;
    }

    /**
     * Returns the unique instance of this class.
     *
     * @return the Oilfield instance.
     */
    public static Oilfield getInstance() {
        return INSTANCE;
    }

    private static final Oilfield INSTANCE = new Oilfield();

    /**
     * Returns a read only view over the units defined in this class.
     *
     * @return the collection of Oilfield units.
     */
    @Override
    public Set<Unit<?>> getUnits() {
        return Collections.unmodifiableSet(UNITS);
    }
}
```

Where we need to define some additional quantities as follows:

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents diffusivity being a rate of diffusion, a measure of the rate at which particles or heat or fluids can 
 * spread.
 */
public interface Diffusivity extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<Diffusivity> UNIT = Oilfield.SQUARE_CENTIMETRE_PER_SECOND;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents energy density being the calorific heating value of a substance for a given volume.
 */
public interface EnergyDensity extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<EnergyDensity> UNIT = Oilfield.BTU_PER_CUBIC_FOOT;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents the sum of a thermodynamic system's internal energy and the product of its pressure and volume.
 */
public interface Enthalpy extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<Enthalpy> UNIT = Oilfield.JOULE_PER_MOLE;  
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents the amount of cooling or heating experienced by a gas when it undergoes a change in pressure (expansion or
 * compression).
 */
public interface JouleThomsonEffect extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<JouleThomsonEffect> UNIT = Oilfield.KELVIN_PER_BAR;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * In physics and engineering, mass flow rate is the mass of a substance which passes per unit of time. Its unit is 
 * kilogram per second in SI units.
 */
public interface MassRate extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<MassRate> UNIT = Oilfield.KILOGRAM_PER_SECOND;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents permeability being the resistance to flow of a fluid through a porous matrix.
 */
public interface Permeability extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<Permeability> UNIT = Oilfield.DARCY;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents potential combustible energy contained within of a substance for a given mass. This is also known as the
 * specific energy which is energy per unit mass.
 */
public interface SpecificEnergy extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<SpecificEnergy> UNIT = Oilfield.BTU_PER_POUND;  
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * The specific heat capacity (symbol c) of a substance is the amount of heat that must be added to one unit of mass of
 * the substance in order to cause an increase of one unit in temperature. It is also referred to as massic heat
 * capacity or as the specific heat. More formally it is the heat capacity of a sample of the substance divided by the
 * mass of the sample. Specific heat capacity often varies with temperature, and is different for each state of matter.
 * The specific heat capacity of a substance, especially a gas, may be significantly higher when it is allowed to expand
 * as it is heated (specific heat capacity at constant pressure) than when it is heated in a closed vessel that prevents
 * expansion (specific heat capacity at constant volume). These two values are usually denoted by c<sub>p</sub> and
 * c<sub>V</sub>, respectively; their quotient &gamma; = c<sub>p</sub> / c<sub>V</sub> is the heat capacity ratio.
 */
public interface SpecificHeatCapacity extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<SpecificHeatCapacity> UNIT = Oilfield.JOULE_PER_KILOGRAM_KELVIN;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Surface tension is the tendency of liquid surfaces to shrink into the minimum surface area possible. Surface tension
 * allows insects (e.g. water striders), usually denser than water, to float and slide on a water surface. At liquid–air
 * interfaces, surface tension results from the greater attraction of liquid molecules to each other (due to cohesion)
 * than to the molecules in the air (due to adhesion). There are two primary mechanisms in play. One is an inward force
 * on the surface molecules causing the liquid to contract. Second is a tangential force parallel to the surface of the
 * liquid. The net effect is the liquid behaves as if its surface were covered with a stretched elastic membrane.
 * Because of the relatively high attraction of water molecules to each other through a web of hydrogen bonds, water has
 * a higher surface tension (72.8 millinewtons (mN) per meter at 20 °C) than most other liquids. Surface tension is an
 * important factor in the phenomenon of capillarity. Surface tension has the dimension of force per unit length, or of
 * energy per unit area. The two are equivalent, but when referring to energy per unit of area, it is common to use the
 * term surface energy, which is a more general term in the sense that it applies also to solids. In materials science,
 * surface tension is used for either surface stress or surface energy.
 */
public interface SurfaceTension extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<SurfaceTension> UNIT = Oilfield.DYNE_PER_CENTIMETRE;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * The change in temperature per unit length. Typically used to describe a geothermal temperature gradient.
 */
public interface ThermalRate extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<ThermalRate> UNIT = Oilfield.RANKINE_PER_FOOT;
}
```

```java
import javax.measure.quantity.*;
import javax.measure.unit.*;

/**
 * Represents volumetric production rate per day.
 */
public interface VolumetricRate extends Quantity {

    /**
     * Holds the oilfield unit for this quantity.
     */
    public static final Unit<VolumetricRate> UNIT = Oilfield.CUBIC_METRE_PER_DAY;  
}
```