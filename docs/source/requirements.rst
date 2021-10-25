Requirements
============

To run Project Curator, the users are expected to meet the prerequisites listed below.

Prerequisite
-----------

**Admin access to OpenShift 4.5+**


**Install Koku Metrics Operator**

* You need to install koku-metrics-operator either via operator-hub or command line.

* We are currently using koku-metrics-operator version 0.9.8 with koku-metric config specified `here. <https://github.com/operate-first/curator/blob/main/Documentation/config/kokumetris-cfg.yaml>`_


**Install PostgreSQL Image**

* Need to have postgres database up and running in cluster to store all information.

* We are currently using postgres version 10.X and config file specified `here. <https://github.com/operate-first/curator/blob/main/Documentation/config/postgres-database-cfg.yaml>`_
