TADalyzer
====

TADalyzer is a tool that automatically discovers TAM/TAD for real systems using instrumentation and tracing techniques. 
For detailed discussion on TAM/TAD and how to use TAM/TAD to understand system scheduling,  please refer to our OSDI'18 paper: [Principled Schedulability Analysis for Distributed Storage Systems using Thread Architecture Models](https://www.usenix.org/conference/osdi18/presentation/yang).

##Workflow

The workflow to generate TAM of a given system with TADalyzer consists of four steps: *Stage Naming*, *Stage Annotation*, *Monitoring* and *Generating*.

####1. Stage Naming

The user lists (and names) important stages in the system. 

This step is performed manually, and requires knowledge about the system.

####2. Stage Annotation

The user identifies thread creation code in the code base and annotates if the new thread belongs to one of the stages previously named. Threads not explicitly annotated default to a special NULL stage. 

This step is also performed manually: for each new thread,  the user needs to call SetStage(stage_id) immediately after the thread starts. The SetStage() function simply get the (OS-level) thread id of the thread, and logs that this thread belongs to a specific stage. In principle the setStage() function can be automatically generated from the stage_list (produced in the *stage naming* step); however for now we require the user to provide the setStage() function. 

In the `/annotation` subdirectory we included the annotation patch for HDFS, HBase,  MongoDB and Riak. We encourage you to check out these patches even if you would like to annotate other systems, as the basic idea is similar. 

####3. Monitoring

Once a system is annotated, the user can deploys the system and feeds various workloads to it; TADalyzer will automatically collect necessary information based on annotation for later TAM generation. The scripts for monitoring and collecting information reside in the `/monitoring` subdirectory. Check out `/monitoring/README.md` for details on how to launch the monitoring and how different types of information are collected. 

If the user missed some important stages in the *stage naming* and *stage annotation* steps, TADalyzer would notice that some threads in the NULL stage are overly active (consuming too much of the resources) and alert the user with the thread ids of the overly active threads. Based on the thread id, the user can find the stack trace of these threads (as stack traces of all treads are also collected periodically), thus identify the missing stages and repeat step 1-3. 

####4. Generating

After enough information is generated, the user can generate the TAM. The scripts for generating TAM/TAD reside in the `/generating` subdirectory; check the `README.md` in that directory on how to use the scripts. Because currently TADalyzer cannot automatically derive if a stage provides a pluggable scheduling point or has an ordering constraint, so tthe generated TAM/TAD will miss information. 

#### Workflow summary

The whole workflow requires the user to know the important (but not all) stages in the system. From our experience, someone unfamilar with the code base of a system usually misses naming some stages initially. However, the stacktrace of active threads provides enough information to point the user to the code of the missing stages to aid further annotation, and one can typically get a satisfactory TAM within a few iterations of the workflow. 

## Improving TADalyzer (or Build You Own)

TADalyzer is currently in a very primitive state; it has various limitations:

- The TAMs generated may be incomplete (depending on the workloads it monitors).
- Resource consumption monitoring only works on systems where an application thread directly corresponds a kernel thread.
- Ordering constraints and pluggable scheduling points cannot be identified. 
- And others.

Mostly TADalyzer is built to demostrate that TAM obtainment and schedulability analysis *can* be performed automatically, and has not evloved to a mature tool. We strongly encourage you to improve TADalyzer, or use other techniques to collect the TAM information and build your own TAM generation tool. 

If you have any questions, comments, or suggestions, please contact suli@cs.wisc.edu.







