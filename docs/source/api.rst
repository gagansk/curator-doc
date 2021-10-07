API
===

.. autosummary::
   :toctree: generated

   curator

#. Deploy the API to Openshift

   -  Copy configuration file:

      .. code:: shell

          mkdir -p apis/config; cp Documentation/config/config.env apis/config/config.env

   -  Install CRD

      This part was generated using
      `kubebuilder <https://github.com/kubernetes-sigs/kubebuilder>`__,
      which requires go v1.16+.


      To use prebuilt image:

      .. code:: shell

          cd apis/report
          make install
          make deploy IMG=quay.io/operate-first/curator-crd
          cd ../..


      If you would like to build CRD from scratch or you made change to the apis/report scource code:

      .. code:: shell

          cd apis/report
          make install
          make docker-build docker-push IMG=<some-registry>/<project-name>:tag
          make deploy IMG=<some-registry>/<project-name>:tag
          cd ../..

   -  Create a example ``Report`` to define specification of report


      -  reportingEnd: `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`__
         Datetime. Create reports for the past N days until reportingEnd
         (includes reportingEnd).
      -  reportPeriod: String, one of Day,Week,Month. Report period N =
         1, 7, 30 days.
      -  namespace: String. Show report for namespace only. (Report
         metrics are grouped by namespace and accumulated by taking sum
         over the N days reportPeriod.)

      .. code:: yaml

        apiVersion: batch.curator.openshift.io/v1
        kind: Report
        namespace: report-system
        metadata:
          name: report-sample
        spec:
          reportPeriod: Week
          namespace: koku-metrics-operator
          reportingEnd: "2021-08-26T00:00:00Z"  # prevents Reports targeting future time

      Create to Report you just defined

      .. code:: shell

          oc apply -f apis/report/config/samples/batch_v1_report.yaml

   -  Deploy the HTTP API

      .. code:: shell

          kustomize build apis | oc apply -f-

#. Access ``Report`` data base on namespace and name of ``Report`` you just created. For example:

  .. code:: shell

      oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
      curl -XGET "http://localhost:5000/report?reportName=report-sample&reportNamespace=report-system"
