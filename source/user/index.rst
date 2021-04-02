===============
User Guide
===============

This User guide provides a complete reference to using the Tapis Pipeline software.

Overview and Prerequisites
==========================

The Tapis Pipeline software is designed to assist you in running recurring computational jobs against multiple data sets. The design is based on the idea of ETL <https://en.wikipedia.org/wiki/Extract,_transform,_load>. It has been designed with TACC resources in mind, but should be broadly applicable to others' as well. The general idea is:

  1. Download input data from remote source
  2. Compute data using whatever software you like
  3. Send resulting output back to a remote storage site

Before you begin building your first Tapis Pipeline, there are a few decisions to make and some prerequisites your
project should meet. We collect these here. Think of it as checklist.

TACC Storage and Computing Allocation
-------------------------------------
Your project will need a storage allocation on some TACC storage resource (for example, Corral, Stockyard, or one of our
Cloud-based storage resources) to serve as the project's Local Inbox and Outbox. The size of the required allocation
greatly depends on the size of the files that will be processed in the pipeline.

Your project will also need one or more allocations on a computing system at TACC, such as Frontera, Stampede2, Lonestar5,
or one of cloud computing systems. The allocation will be used to run pipeline jobs.

Packaging and Installation of Analysis (Pipeline Job) Software
---------------------------------------------------------------
The ultimate goal of a pipeline is to process data via one or more programs, defined by the project. This software,
referred to as the *pipeline job* software, must be packaged and accessible to the Tapis Pipelines software.

There are a few packaging options available:

  1. Create a container image with the job software. We recommend using Singularity containers for running jobs on HPC
     systems and Docker containers for running jobs on cloud resources, such as the Kubernetes Freetail system.
  2. Package the software using conventional methods, such as RPMs or Python packages.

These lead to the following installation options:

  1. If the job software is packaged as a container as in option 1, the software can be registered as a Tapis App
     (Singularity or Docker) or Tapis Actor/function (Docker). This is the preferred approach, as it does not require
     maintaining a separate installation of the job software on each execution system to be used. It also simplifies
     permissions on the underlying files that must be maintained so that the Tapis Pipelines software can execute it.
  2. Install the job software on the head node (login node) of a TACC execution system. Choose the system matching the
     allocation for the project. If there are multiple systems, the software must be installed on each one. This method
     is **not recommended**.

Remote Outbox and Inbox
-----------------------
Each pipeline must configure a Remote Outbox and a Remote Inbox where files requiring processing (respectively, output
files resulting from processing) will be stored. Conceptually, the Remote Outbox and Inbox are storage resources
independent of TACC, but they must provide programmatic access. Options for the Remote Outbox and Inbox include:

  1. A path on a Tapis System, including POSIX (SSH/SFTP) and Object storage (S3-compatible).
  2. A Globus endpoint.

With Option 1, the Tapis Pipelines software will be able to utilize Tapis transfers to move data to/from the Remote
Outbox and Inbox to any TACC resource. This is the recommended option.

With Option 2, the Tapis Pipelines software utilizes
`Globus Personal Connect <https://www.globus.org/globus-connect-personal>`_ to move data to/from the Remote
Outbox and Inbox to the Local Outbox and Inbox. From there, Tapis transfers will be utilized, as needed.

Manifest Files
--------------
A key to the Tapis Pipeline architecture is the *manifest file*.
Pipelines jobs process files that get transferred to the Remote Outbox. Each job will process any number of files, and
the number of files processed by a single job is determined by the manifest file. The manifest file is a simple
JSON file that describes one or more files to be processed by a job. It can include basic validation information
(such as an MD5 checksum of the file), project generated identifies for the job, and some limited support for
overriding the default job runner behavior (for example, to specify the job can run at a lower priority than other
jobs).

Critically, **the presence of a manifest file in the Remote Outbox instructs the Tapis Pipelines software that the
files referenced within it are ready to be processed as a job**. In particular, all data transfer to the Remote Outbox
has completed. No files in the Remote Outbox will be processed until they are included in some manifest file.

The manifest file must adhere to a required format described by a JSON Schema. Invalid manifest files are never
processed.


Installing Tapis Pipelines Software
===================================
The Tapis Pipelines software is available as a Python package. To install it, simply type:

.. code-block:: bash

  $ pip install tapis-pipelines

Installing Tapis Pipelines in a virtualenv is recommended.

Alternatively, you can install Tapis Pipelines from source by checking out the
`repository <https://github.com/tapis-project/pipelines>`_ from GitHub.


Configuration of Tapis Pipelines
================================
An instance of the Tapis Pipelines software must be configured for a specific pipeline. In this section we cover
all the basic configurations available.

*To do, coming soon...*


Testing A Pipeline
==================
A number of measures can be take to validate that a pipeline will run correctly before

Validate Configuration
----------------------
The Tapis Pipelines software includes a config validator that can be run to ensure that all required configurations
are present and valid. The validator does basic type checking of all fields. Run the config validator first before
moving on to subsequent validation.

Package tests
-------------
The Tapis Pipelines software includes a package of tests that can be run once the software is configured. These tests
exercise some of the primary functions of the software, such as interacting with the Tapis APIs using the configured
authentication. If any of these functions fails, some installation or configuration step is likely missing or incorrect
and the pipeline jobs are unlikely to run correctly.

Test Pipeline Runs
------------------
In some cases, it can be possible to issue end-to-end test runs of a pipeline using sample data.
*To do, more on this coming soon...*


Production Pipelines and Dashboard
==================================



Troubleshooting and FAQ
=======================

*Coming soon...*

