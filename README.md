# Pyramis library

This repository provides Pyramis, a Java library for quantitative modeling and analysis of **non-Markovian systems** specified as an **extension of UML statecharts**. Specifically: 
- Models can be defined through the formalism of **Hierarchical Semi-Markov Processes with parallel regions (HSMPs)**, a class of statecharts capturing concurrency, hierarchy, stochastic timing, and probabilistic choices. In particular, in HSMPs, steps model activities, possibly characterized by a non-Markovian duration, and they can be either **simple steps**, representing atomic activities, or **composite steps**, obtained by composition of concurrent **regions**. In turn, regions consist of steps, in a recursive composition yielding a **hierarchy** of HSMPs. 
- Models can be evaluated by a compositional technique that performs separate analysis of the Semi-Markov Process (SMP) underlying each region, supporting both evaluation of steady-state behaviour of stationary models and evaluation of transient behaviour until absorption of non-stationary models. 

The approach is presented in the paper titled "Compositional Analysis of Hierarchical UML Statecharts" authored by Laura Carnevali, Reinhard German, Francesco Santoni, and Enrico Vicario, currently submitted to the IEEE Transactions on Software Engineering after a minor revision. 

To support reproducibility of the experimental results reported in the paper, this repository contains the code that builds and evaluates the considered models, and this document illustrates the steps needed to repeat the experiments. Given that some of them require a significant amount of time, this repository already contains the files storing all the intermediate and final results. Specifically:
- Navigate to `src/test/java/it/unifi/hierarchical/analysisScalability`and execute the `main` method of the Java classes inside the package `it.unifi.hierarchical.analysisScalability` to reproduce the experiments of the case study on transient timed failure logic analysis of component-based systems (reported in Section 4 of the paper). 
- Navigate to `src/test/java/it/unifi/hierarchical/analysis`and execute the `main` method of the Java classes inside the package `it.unifi.hierarchical.analysis` to reproduce the experiments of the case study on steady-state analysis of software rejuvenation in virtual servers (reported in Section 5 of the paper). 

To execute the main method of a Java class, open the class and click on the menu `Run > Run as > Java Application`.

## Installation

This repository provides a ready-to-use Maven project that you can easily import into an Eclipse workspace to start working with the [Pyramis library](https://github.com/oris-tool/pyramis) (the version `2.0.0-SNAPSHOT` of the [Sirio library](https://github.com/oris-tool/sirio) is included as a Maven dependency). Just follow these steps:

1. **Install Java >= 11.** For Windows, you can download a [package from Oracle](https://www.oracle.com/java/technologies/downloads/#java11); for linux, you can run `apt-get install openjdk-11-jdk`; for macOS, you can run `brew install --cask java`. 

2. **Download Eclipse.** The [Eclipse IDE for Java Developers](http://www.eclipse.org/downloads/eclipse-packages/) package is sufficient.

3. **Clone this project.** Inside Eclipse:
   - Select `File > Import > Maven > Check out Maven Projects from SCM` and click `Next`.
   - If the `SCM URL` dropbox is grayed out, click on `m2e Marketplace` and install `m2e-egit`. You will have to restart Eclipse.
   - As `SCM URL`, type: `https://github.com/oris-tool/pyramis.git` and click `Next` and then `Finish`.
   

## Case study on transient timed failure logic analysis of component-based systems

This case study addresses Timed Failure Logic Analysis (TFLA) of a benchmark of component-based systems with increasing complexity. Specifically, starting from a basic pattern, a suite of synthetic models is generated by varying the behaviour length (i.e., the number of steps in sequential behaviours), the parallelism degree (i.e., the number of regions of each composite step), the hierarchy depth (i.e., the number of nested layers), the type of composite step (i.e., either type first, if the composite step terminates as soon as any of its regions terminates, or type last, if the composite step terminates when all its regions have terminated), the presence or absence of rare events, the type of duration distributions (i.e., expolynomial distributions with either small or large support), the presence or absence of cycles.

### Derivation of ground truth (requires a time ranging from nearly 15 minutes for the base model to nearly 40 hours for the most complex model)

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyramisSamplerFromFiles.java` to derive a ground truth for each model of the suite except for the model variant with rare events. Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyrStudyResultsRareEvents.java` to derive a ground truth for the model variant with rare events. Specifically: 
- The files containing the probability of each step (before absorption) are placed in `src/main/resources/pyramisSimulation/`.
- The files containing the durations of the failure process are placed in `src/main/resources/groundTruthDistributions`. Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyrStudyDrawCDF.java` to translate each of these files into a file that represents the Cumulative Distribution Function (CDF) of the failure process (used for the plots in Fig. 7) and that is placed in `src/main/resources/groundTruthCDF`.

### Evaluation of accuracy and complexity (requires a time ranging from nearly 3.5 minutes for the base model to nearly 45 hours for the most complex model)

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/Pyramis.java` to run the analysis of each model of the suite, except for the model variants with rare events, large-support duration distributions, and cycles. For each of these models, 100 simulation runs are also performed, with each run lasting at least as long as the analysis. 

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyramisLongEvents.java` to run the analysis of the model variants with large-support duration distributions and the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyramisCyles.java` to run the analysis of the model variants with cycles. Also in this case, for each of these models, 100 simulation runs are also performed, with each run lasting at least as long as the analysis. 

The files contaning the analysis computation times (reported in Table 1 and Fig. 5), the files containing the probability of each step (before absorption) produced by analysis and simulation, the files containing the CDF of the duration of the failure process produced by the analysis (used for the plots in Fig. 7) are placed in `src/main/resources/pyramisAnalytic`.

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyrStudyResults.java` to compare the analysis results with the ground truth for each model except the variant with cycles and execute `src/test/java/it/unifi/hierarchical/analysisScalability/PyrStudyResultsVarianceCycles.java` to compare the analysis results with the ground truth for the model variant with cycles, obtaining mean value and standard deviation of relative errors of analysis over simple steps (reported in Table 2 and Fig. 6). 

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysisScalability/PyrStudyResultsVarianceOverSimulations.java` to compare the simulation results with the ground truth, obtaining average (over 100 simulation runs) mean value and standard deviation of relative errors of simulation over simple steps (reported in Table 3 and Fig. 6).


## Case study on steady-state analysis of software rejuvenation in virtual servers

This case study addresses the evaluation of a non-Markovian variant of a server virtualized system from the literature on software rejuvenation.

### Derivation of ground truth (requires nearly 36.6 hours, 55.2 hours, 73.6 hours, and 92.0 hours for a simulation time of 10, 15, 20, and 25 million hours, respectively)

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysis/PyramisSampler.java` to simulate the model for a simulation time of 10, 15, 20, and 25 million hours. The files contaning the simulation computation times (reported in Table 5) and the files containing the steady-state probability of each step (used for Table 6 and Fig. 9) are placed in `src/main/resources/pyramisCaseStudyGroundTruth/`.

### Evaluation of accuracy and complexity (requires nearly 82 hours)

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysis/Pyramis.java` to run the analysis with fixed time tick for all the steps of the model, execute the `main` method of and `src/test/java/it/unifi/hierarchical/analysis/PyramisVariableTicks.java` to run the analysis with different time tick for different steps of the model. The files contaning the analysis computation times (reported in Table 7) and the files containing the steady-state probability of each step are placed in `src/main/resources/pyramis/`.

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysis/PyramisSamplerRelative.java` to run, for each analysis with variable time tick, 100 simulation runs of the model, with each run lasting at least as long as the analysis. The files containing the steady-state probability of each step are placed in `src/main/resources/pyramis/`.

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysis/PyrStudyResults.java` to compare the analysis results with the ground truth, obtaining, for each step, the relative error achieved by the analysis with respect to the ground truth (reported in Table 8 and Fig. 10).

Execute the `main` method of `src/test/java/it/unifi/hierarchical/analysis/PyrStudyResultsVariance.java` to compare the simulation results with the ground truth, obtaining, for each step, the mean value and standard deviation (over 100 runs) of relative errors achieved by the simulation with respect to the ground truth (reported in Table 9 and Fig. 10).

## Production of plots

Plots of Figs. 5, 6, 7, 9, and 10 can be obtained by executing the python scripts in `src/main/resources/plots`, which already contain the required packages. Just run `python3` followed by the name of each python script.
