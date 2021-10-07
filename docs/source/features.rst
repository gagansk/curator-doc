Features
========

.. _features:

Feature List:
-------------
**Feature list:**

* Easy Installation

Install the Curator project and the related dependencies by executing just one installation script.

* Automated report generation grouped by namespaces

Daily, weekly and monthly reports grouped by namespaces will be generated for the infrastructure utilization in the OpenShift Cluster.

* API Access

Users will be able to query the Curator API to get the infrastructure utilization metrics. Read the :doc:`api` section for further information. 

* Custom SQL Queries

Users can manually log in to PostgreSQL and run their own custom SQL queries for specific infrastructure utilization information.

* Test suite to check code changes

Developers who make changes to the project to meet new requirements can run the test suite that comes with the package to check the project sanity.

* S3 is now optional

You can now use Curator without any S3-compatible volume. At the time of installation, you will be asked to either opt-in or opt-out of using S3 storage.

* Zipped data storage in any S3-compatible volume

Infrastructure data in zipped CSV format can be pushed to any S3-compatible volume for archiving.

* Unzipped data storage in any S3-compatible volume

Infrastructure data in unzipped CSV format can be pushed to any S3-compatible volume. for integration with third-party data analysis tools.

* Raw OCP data download

Users can download the raw infrastructure utilization data from their OpenShift cluster to their local machine.


