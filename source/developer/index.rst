===============
Developer Guide
===============

The Developer Guide provides details about the architecture and design of the Tapis Pipelines software. This information
is intended for developers wanting to customizing and/or extend the software with new features, or who just want to
understand how Tapis Pipelines work at a deeper level.


Steps in Pipeline
=================

These steps are designed to be somewhat idempotent, so that each step may be run multiple times and it will not harm
existing or previously finished jobs.

This helps to facilitate the automation of the jobs.

Each job is associated with an input data set and each is tracked separately using Tapis Metadata service.

1. Get Remote
-------------

This downloads input files from the remote source. It scans the remote source (Remote Outbox) for files which have not already been downloaded and initiates transfer.

Once transferred, it sets the metadata to transfer_to_local_done.

If required by the app, it also unpacks the data (e.g. if it is inside a .tar file.) It then sets the metadata to unpack_on_local_done.

The implementation of this step is captured in the 'get-remote-via-globus-personal.py' script.

2. Compute Job
--------------

This submits the job to the computing resource, determines that the compute job has finished, and optionally runs post-compute checks. These checks can determine if the job finished correctly or should be resubmitted.

In the case of submitting a Slurm job to a queue, it creates the sbatch file from a template. Since this template is application specific, it is provided by the project. A simple example of such a template is provided in the repository (sbatch_template.j2).

This step can optionally have several components or sub-steps, as required by the project. Complex decision trees are possible within this step.

Once processing is finished, metadata is set to processing_data_done.

The implementation of this step is captured in the 'compute-local-jobs-auto.py' script.


3. Send Results
---------------

This packs up the job output and sends it back to the Remote Inbox. It optionally packs the data into an archive file (e.g. tar).

Once transfer is finished, metadata is set to transfer_to_remote_done.

The implementation of this step is captured in the 'send-results-via-globus-personal.py' script.


4. Finished Pipeline
--------------------

Once the output is successfully transferred to Remote storage, the pipeline for this job is considered complete and metadata is set to pipeline_done.





Automating the Steps
====================

In a future release, these components may be launched via more advanced Tapis API processes (e.g. Actors, Jobs, etc.)

We include examples of scripts to run the various steps periodically from cron. Each runs separately so that they do not need to wait for each other to finish. They record the output of the scripts in dated files.


.. code-block:: bash

  $ crontab -l
  SHELL=/bin/bash
  1 * * * * . /etc/profile; $HOME/pipeline/01runget
  2 * * * * . /etc/profile; $HOME/pipeline/02runcompute
  3 * * * * . /etc/profile; $HOME/pipeline/03runtarup
  4 * * * * . /etc/profile; $HOME/pipeline/04runsend

