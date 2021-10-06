About
=====

Project Curator
---------------
Project Curator is an infrastructure consumption analysis project for the OpenShift Platform. Project Curator retrieves infrastructure utilization for the OpenShift Platform using `koku-metrics-operator <https://github.com/project-koku/koku-metrics-operator>`_ and provides users the ability to query the infrastructure utilization based on time period and namespaces. Users can generate periodic and custom reports of the infrastructure utilization and get them delivered through emails.

Project Curator also provides APIs to query the information utilization data and can be used to feed data to any infrastructure billing system or business intelligence system.

You need to have administrator access to an OpenShift v.4.5+ cluster to deploy Project Curator. Once deployed, all the authorized users and systems will be able to track the infrastructure utilization of OpenShift.

To submit an issue or a feature request, please raise an issue `here <https://github.com/operate-first/curator/issues>`_.


Persona
-------
With Project Curator,

* Administrators can

 * know the entire OpenShift cluster utilization.
 * know the utilization grouped by projects.
 * know the utilization trend over time.

* End Users can

 * know the usage for my projects in OpenShift.
 * get weekly, daily and monthly reports.
 * get alerts for usage limits.

* Analysts can

 * pass the usage to an internal billing system.
 * feed the usage data to BI systems.
 * export the usage data to my own tools.


