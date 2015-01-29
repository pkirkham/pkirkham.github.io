---
layout: article
title: Parallel Algorithm In NetBeans
modified:
categories: pyrus
excerpt: How to integrate a parallel algorithm and progress measurements into a NetBeans platform application.
tags: [java, netbeans, programming, snippet, parallel_algorithm, parallel_processing, progress_handler, thread_handling]
image:
  feature: 
  teaser: teaser-parallel-algorithm-400x250.jpg
  thumb:
comments: true
---

Whilst writing the Pyrus Suite I have had a need to use parallel algorithms on many occasions. Parallel algorithms are ideally suited to modern computers which feature multiple cores, and not taking advantage of this means that a program is missing a trick for optimisation. One of the barriers to implementation of parallel algorithms is simply that the code can be more awkward to write than a linear program.

## Creating a Parallel Algorithm

Routines that are ideally suited to parallel algorithms include those which have computationally intensive loops. In the case of Pyrus there is often a need to process large images, where that image can be broken up into smaller tiles and the tiles can be processed in parallel. Notably this also saves on memory, as well as increasing execution speed. Another useful task for for a parallel algorithm is a Monte Carlo simulation, where multiple independent runs of an analysis are run, and the statistic of the outcomes are then analysed.

Rather than jump straight into the parallel algorithm, a reliable approach is to first create a working algorithm using linear programming techniques. This allows the algorithm to be fine tuned and debugged, before converting it to utilise a parallel approach. Bear in mind that a parallel approach is not required for the algorithm to work, but it a very effective method to increase the execution speed.

Once the main algorithm is working, it should then be a trivial matter to break it up into chunks and run those chunks in parallel.

## Parallel Algorithm Recipe

The code below contains boilerplate for a class that implements a parallel algorithm. The algorithm will automatically use as many cores or processors as are available to it. This class is also written to take advantage of the NetBeans platform progress bar, and will update this depending on the amount of progress achieved.

To implement the algorithm it is necessary to write an implementation for the <code>calculateIteration</code> method.

{% highlight java %}
package parallel.example;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicInteger;
import org.netbeans.api.progress.aggregate.AggregateProgressFactory;
import org.netbeans.api.progress.aggregate.ProgressContributor;

/**
 * Example boilerplate code that can be used as a starting point for a class that implements a parallel algorithm in
 * NetBeans. The algorithm can be broken down into one or more stages. A progress meter is updated with progress in the
 * status bar.
 *
 * @author Peter Kirkham
 */
public class ParallelAlgorithm {

    /**
     * Instantiate the class and determine how many processors are available.
     */
    public ParallelAlgorithm() {
        progress_contribs = new ProgressContributor[PROGRESS_PHASES];
        for (int i = 0; i < progress_contribs.length; i++) {
            progress_contribs[i] = AggregateProgressFactory.createProgressContributor("ParallelAlgorithm");
        }

        // Custom instantiation code follows...
       
    }

    /**
     * Implement the framework to manage the running of the parallel algorithm.
     */
    private void parallelMethod() {
	
        // Calculate the total number of workunits that will be processed by this algorithm
        final int[] workunits = new int[PROGRESS_PHASES];
        for (int phase = 0; phase < PROGRESS_PHASES; phase++) {
            workunits[phase] = iterations;
        }
        startProgress(workunits);

        // Set up parallelisation to speed up calculation
        int num_threads = Runtime.getRuntime().availableProcessors();
        ExecutorService exec_pool = Executors.newFixedThreadPool(num_threads);
		
        // The parallel parts of the algorithm are run in stages or phases
        final AtomicInteger[] cumulative_progress = new AtomicInteger[PROGRESS_PHASES];
        for (int phase = 0; phase < PROGRESS_PHASES; phase++) {
			final int ph = phase;
			final CountDownLatch flag = new CountDownLatch(iterations);
			cumulative_progress[phase] = new AtomicInteger(); // progress counter
			final AtomicInteger aj = new AtomicInteger(); // loop from initial aj
			final int nj = iterations; // loop up to nj - 1
			Thread[] worker = new Thread[num_threads];
			for (int thread = 0; thread < num_threads; ++thread) {
				worker[thread] = new Thread(new Runnable() {

					@Override
					public void run() {
						for (int j = aj.getAndIncrement(); j < nj;
								j = aj.getAndIncrement()) {
							double[] result = calculateIteration(ph);
							for (int r = 0; r < results; r++) {
								output[r][j] = result[r];
							}
							flag.countDown();
							synchronized(ParallelAlgorithm.this) {
								progress_contribs[ph].progress(
										MathLib.min(cumulative_progress[ph].incrementAndGet(),
												workunits[ph] - 1));
							}
						}
					}
				});
			exec_pool.execute(worker[thread]);
			}

            // Block until all interpolation calculations have completed
            try {
                flag.await();
            } catch (InterruptedException e1) {
                LOG.severe(e1.getMessage());
            }
            finishProgress(phase);
        }
        exec_pool.shutdown();
    }

    /**
     * Override this method to do the actual calculation work for the current iteration.
     *
	 * @param phase the phase (or stage) at which the algorithm has reached
     * @return array of results for this iteration. Order depends on algorithm.
     */
    protected abstract double[] calculateIteration(int phase);
    
    /**
     * We are likely to want to know how far along this process has got since it will be quite long running. This will
     * return a NetBeans platform object that can help monitor the progress.
     *
     * @return an array of progress contributors
     */
    public ProgressContributor[] getProgressContributors() {
        return progress_contribs;
    }

    /**
     * To set up our progress contributors we need to know how many units are going to be processed. Once this is set
     * we can start all of the progress threads.
     *
     * @param units number of work units to track progress for each phase
     */
    public void startProgress(int[] units) {
        for (int phase = 0; phase < PROGRESS_PHASES; phase++) {
            progress_contribs[phase].start(units[phase]);
        }
    }

    /**
     * When the parallel algorithm has completed we should notify the handlers that this has happened by finishing
     * the progress contributors.
     * 
     * @param phase the phase (or stage) of the algorithm that has been completed
     */
    public void finishProgress(int phase) {
        progress_contribs[phase].finish();
    }

    /* == DEFINE CONSTANTS == */
    private static final int PROGRESS_PHASES = 1; // Number of algorithm phases (or stages)
    private static final int DEFAULT_NUM_ITERATIONS = 10000;
    private static final int DEFAULT_NUM_RESULTS = 1;

    /* == DECLARE GLOBAL VARIABLES == */
    private ProgressContributor[] progress_contribs;
    private int iterations = DEFAULT_NUM_ITERATIONS;
    private int results = DEFAULT_NUM_RESULTS;
    private double[][] output; // An array holding all the output results

    /* == ENABLE LOGGING == */
    transient private static final java.util.logging.Logger LOG
            = java.util.logging.Logger.getLogger(ParallelAlgorithm.class.getName());
}
{% endhighlight %}