API
===


**API Deployment** 

Deploy the API to Openshift

   1.  Copy configuration file:

      .. code:: shell

          mkdir -p apis/config; cp Documentation/config/config.env apis/config/config.env

   2.  Install CRD

      This part will be generated using
      `kubebuilder <https://github.com/kubernetes-sigs/kubebuilder>`_,
      which requires `Go <https://golang.org/doc/go1.16>`_ v1.16+.


      To use a prebuilt image, follow the below steps:

      .. code:: shell

          cd apis/report
          make install
          make deploy IMG=quay.io/operate-first/curator-crd
          cd ../..

      If you would like to build CRD from scratch or you made changes to the apis/report source code, 
      then follow the below steps:

      .. code:: shell

          cd apis/report
          make install
          make docker-build docker-push IMG=<some-registry>/<project-name>:tag
          make deploy IMG=<some-registry>/<project-name>:tag
          cd ../..

   3.  Create a sample ``Report`` by defining the paramaters


      -  reportingEnd: `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime. Create reports for the past N days until reportingEnd
         (includes reportingEnd).
      -  reportPeriod: String, one of Day, Week, Month. Report period N =
         1, 7, 30 days.
      -  namespace: String. Show report for namespace only. (Report
         metrics are grouped by namespace and accumulated by taking sum
         over the N days reportPeriod.)

      .. code:: yaml

        # apis/report/config/samples/batch_v1_report.yaml
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

   4.  Deploy the HTTP API

      .. code:: shell

          kustomize build apis | oc apply -f-

**Access API**

Access the ``Report`` database on namespace and name of ``Report`` you just created. For example:

  .. code:: shell

      oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
      curl -XGET "http://localhost:5000/report?reportName=report-sample&reportNamespace=report-system"



.. autosummary::
   :toctree: generated

   curator
