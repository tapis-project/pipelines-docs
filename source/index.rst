===============
Tapis Pipelines
===============

Welcome to the Tapis Pipelines documentation.

What are Tapis Pipelines?
_________________________
Tapis Pipelines automate data analysis workflows on TACC Cloud and HPC resources in a secure, robust, and
fault-tolerant way. The Tapis Pipelines software integrates directly with the `Tapis v3 API Framework <https://tapis.readthedocs.io>`_
to allow users to define workflows that incorporate Tapis services such as the Tapis Metadata,
Actors (functions-as-a-service), Systems, Apps, Jobs and other services.

Tapis Pipelines have been architected to fit within broader data analysis processing workflows where only a part of the
computation actually runs at TACC. This work grew out of collaborations with NASA JPL and partners to support
the processing of data (exposures) captured by the NEID telescope instrument at Kitt Peak National Observatory. Partner
institutions include Caltech, IPAC (Infrared Processing & Analysis Center), JPL, Kitt Peak, Penn State, and the
University of Arizona.

There are two ways to build Tapis pipelines. First, the Tapis Pipelines software is available as a Python package that
can be installed and configured directly on a Linux-compliant machine. This method is available today and provides the
ability to customize the Pipeline software itself, offering the most flexibility. The second method, currently in development,
is to utilize the Tapis Pipelines Service, a hosted version of the software developed and maintained by TACC staff.

.. figure:: images/overview.png
    :width: 1000px
    :align: center


10,000 Foot View
----------------
At a high level, each Tapis Pipeline consists of the following steps:

  0. Files to be processed are copied to the pipeline's configured *Remote Outbox*. (This step is not performed by the
     Tapis Pipelines software).
  1. As new files arrive in the Remote Outbox, they are staged to the pipeline's configured *Local Inbox* at TACC where
     basic validation and pre-processing can occur.
  2. The pipeline's configured *entrypoint* is launched to "process" files in the Local Inbox. Each execution of the entrypoint
     is referred to as a *pipeline job*.
  3. As the pipeline job runs, output files are written to the pipeline's *Local Outbox* at TACC.
  4. On successful completion of a pipeline job, files in the Local Outbox are transferred to the pipeline's configured
     *Remote Inbox*. From there, additional workflow steps can occur outside of TACC.


For each step above, there are several configurable aspects, and the software includes mechanisms for ensuring robustness
and integrity of the pipeline processing. Some of the most salient aspects include:

  1. The Remote Inbox can be configured to be a Globus endpoint or a path on a Tapis system. Tapis supports defining systems
     via a number of protocols and transfer methods, including SSH (e.g., an SFTP server) as well cloud object store
     (e.g., an AWS S3 bucket) among others.
  2. Files staged to the Remote Inbox include *manifest files* which describe a set of files that should be processed
     together as a single job. Manifest files must conform to a schema specific to Tapis Pipelines software.
  3. The Local Inbox should be chosen in conjunction with the execution system(s) where pipeline jobs will run. For
     example, the Local Inbox could be a directory on TACC's global file system, Stockyard, a directory on a scratch file
     system on one of the HPC systems, or a directory on TACC's Corral storage resource, mounted on a number of cloud
     and high-throughput execution systems.
  4. Pipeline jobs are configured with one or more execution targets, a *primary* and optionally, one or more *secondary*
     systems at TACC where pipeline jobs will actually run. These can be traditional HPC supercomputers or cloud/high-throughput
     systems. When a primary system is offline -- for example, due to a planned maintenance -- jobs will be automatically routed
     to a secondary system. (Note: the Local Inbox must be available on each execution target).
  5. Each pipeline configuration includes a *run schedule* which dictates how new pipeline jobs are scheduled. The options
     include a fixed schedule, similar to cron (e.g., run every day at 2 AM), or *streaming*, which runs jobs as soon as
     new manifest files arrive in the Remote Outbox.


These topics are covered in more detail in the :doc:`user/index`. Additionally, The :doc:`developer/index`
includes design and implementation details about the Tapis Pipelines software itself, useful for customizing and
extending the software with new features.
