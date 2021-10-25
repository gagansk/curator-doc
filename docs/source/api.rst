API
===


**API Deployment** 

Deploy the API to Openshift


   1.  Install CRD

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

   2.  Deploy the HTTP API

      .. code:: shell

          kustomize build apis | oc apply -f-

   3.1  Download Raw Koku-metric report for given time frame



      .. code:: shell

        oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
        curl -XGET "http://localhost:5000/download?start=2021-08-22%2003:00:00.000000&end=2021-08-22%2004:00:00.000000"

   3.2  Create a sample ``Report`` by defining the parameters


      -  reportingEnd: Required, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime. Create reports for the past N days until reportingEnd
         (includes reportingEnd).
      -  reportingStart: Optional, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime.
      -  reportPeriod: Optional, String, one of Day, Week, Month. Report period N =
         1, 7, 30 days.
      -  namespace: Optional, String. Show report for namespace only. If omitted, show report for all namespace.

      Method 1: time frame report
      Provide parameter for both ``reportingStart`` and ``reportingEnd``. (``reportPeriod`` will be ignored if provided)
      Result report contains all raw CPU and memory metrics for time frame [``reportingStart``, ``reportingEnd``).

      .. code:: yaml

        # apis/report/config/samples/batch_v1_report.yaml
        apiVersion: batch.curator.openshift.io/v1
        kind: Report
        namespace: report-system
        metadata:
          name: timeframe-report-sample
        spec:
          reportingStart: "2021-10-25T00:00:00Z"
          reportingEnd: "2021-10-26T00:00:00Z"  # prevents Reports targeting future time
          namespace: koku-metrics-operator


      Method 2: stand daily, weekly, monthly report
      Provide parameter for both ``reportPeriod`` and ``reportingEnd``.
      Result report contains all raw CPU and memory metrics for the past N days until reportingEnd (includes reportingEnd).

      .. code:: yaml

        # apis/report/config/samples/batch_v1_report.yaml
        apiVersion: batch.curator.openshift.io/v1
        kind: Report
        namespace: report-system
        metadata:
          name: daily-report-sample
        spec:
          reportingEnd: "2021-08-26T00:00:00Z"  # prevents Reports targeting future time
          reportPeriod: Day
          namespace: koku-metrics-operator

      Create one of two Report above you just defined

      .. code:: shell

          oc project report-system
          # Using project "report-system" on server ...
          oc apply -f apis/report/config/samples/batch_v1_report.yaml


      Access the ``Report`` database on namespace and name of ``Report`` you just created. For example:

      .. code:: shell

        oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
        curl -XGET "http://localhost:5000/report?reportName=daily-report-sample&reportNamespace=report-system"



.. autosummary::
   :toctree: generated

   curator
