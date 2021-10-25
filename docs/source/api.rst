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
      then follow the steps:

      .. code:: shell

          cd apis/report
          make install
          make docker-build docker-push IMG=<some-registry>/<project-name>:tag
          make deploy IMG=<some-registry>/<project-name>:tag
          cd ../..

   2.  Deploy the HTTP API

      .. code:: shell

          kustomize build apis | oc apply -f-

   3.1  Download raw Koku-Metric-Operator reports for given time frame

      .. code:: shell

        oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
        curl -XGET "http://localhost:5000/download?start=2021-08-22%2003:00:00.000000&end=2021-08-22%2004:00:00.000000"

      ``start`` and ``end`` parameters will be cast to PostgreSQL timestamp. Therefore multiple formats are supported.

      Downloaded report follows similar structure of a Koku-Metrics-Operator uploaded report.
      ::

          <start>-<end>-koku-metrics.tar.gz
          ├── <start>-<end>-koku-metrics.0.csv
          ├── <start>-<end>-koku-metrics.1.csv
          ├── <start>-<end>-koku-metrics.2.csv
          └── <start>-<end>-koku-metrics.3.csv

   3.2  Create a sample ``Report`` by defining the parameters

      -  reportingEnd: Required, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime.
      -  reportingStart: Optional, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime.
      -  reportPeriod: Optional, String. One of Day, Week, Month. Report period N =
         1, 7, 30 days.
      -  namespace: Optional, String. Show report for namespace only. If omitted, show report for all namespace.

      **Method 1**: Time frame report

      Provide parameter for both ``reportingStart`` and ``reportingEnd``. (``reportPeriod`` will be ignored if provided)

      Result report contains raw CPU and memory metrics for time frame [``reportingStart``, ``reportingEnd``) in project ``namespace`` (if provided).

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


      **Method 2**: Standard daily, weekly, monthly report

      Provide parameter for both ``reportPeriod`` and ``reportingEnd``.

      Result report contains raw CPU and memory metrics for the past N days until reportingEnd (including reportingEnd) in project ``namespace`` (if provided).

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

      Create one of the two Reports above you just defined:

      .. code:: shell

          oc project report-system
          # Using project "report-system" on server ...
          oc apply -f apis/report/config/samples/batch_v1_report.yaml


      Access the Report by identifying Report by name and namespace it was created.
      For example, to access ``daily-report-sample`` on namespace ``report-system``:

      .. code:: shell

        oc port-forward $(oc get pods -l=app=curator-api -o name) 5000:5000
        curl -XGET "http://localhost:5000/report?reportName=daily-report-sample&reportNamespace=report-system"



.. autosummary::
   :toctree: generated

   curator
