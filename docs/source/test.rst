Testing
=======

Before deploying the application, you can run ``verify_connection.sh`` to test your S3 bucket and PostgreSQL database connectivity.

To run database/S3 connectivity check: (OC cluster access required)

.. code-block:: console

   sh verify_connection.sh

To run test case with provided example test data: (Testing at local python environments. Some package required)

.. code-block:: console

   cd testing; python -m unittest curator-test.py

.. autosummary::
   :toctree: generated

   curator
