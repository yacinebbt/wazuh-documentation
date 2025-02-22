.. Copyright (C) 2015, Wazuh, Inc.

.. meta::
   :description: Learn how to configure the Amazon Inspector Classic service to integrate with Wazuh.

Amazon Inspector Classic
========================

`Amazon Inspector Classic <https://docs.aws.amazon.com/inspector/v1/userguide/inspector_introduction.html>`__ is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS. Amazon Inspector Classic automatically assesses applications for exposure, vulnerabilities, and deviations from best practices. After performing an assessment, Amazon Inspector Classic produces a detailed list of security findings prioritized by level of severity. These findings can be reviewed directly or as part of detailed assessment reports which are available via the Amazon Inspector console or API.

.. note::

   Wazuh does not yet support the new `Amazon Inspector <https://aws.amazon.com/inspector/>`__ service, but it has support for the `Amazon Inspector Classic <https://docs.aws.amazon.com/inspector/v1/userguide/inspector_introduction.html>`__ service.

AWS configuration
-----------------

Learn how to configure the Amazon Inspector Classic service to integrate with Wazuh.

Amazon Inspector Classic configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. To start using Amazon Inspector Classic, go to the AWS management console, search for "*Amazon Inspector*" and click it from the results. Once there, click on the left-side menu.

   .. thumbnail:: /images/cloud-security/aws/inspector/01-left-side-menu.png
      :align: center
      :width: 80%

#. Click on **Switch to Inspector Classic**.

   .. thumbnail:: /images/cloud-security/aws/inspector/02-switch-to-inspector-classic.png
      :align: center
      :width: 80%

#. On the Amazon Inspector Classic page, click **Get started**.

   .. thumbnail:: /images/cloud-security/aws/inspector/03-click-get-started.png
      :align: center
      :width: 80%

#. Click on **Advanced setup**.

   .. thumbnail:: /images/cloud-security/aws/inspector/04-advanced-setup.png
      :align: center
      :width: 80%

#. Configure the assessment target:

   #. Choose a name for the assessment target.
   #. Select if you are going to include all EC2 instances in the scans or if you prefer to filter them based on a tag by enabling or disabling the **Include all EC2 instances in this AWS account and region** checkbox.
   #. If you prefer to install the agents manually, disable the **Install the Amazon Inspector Agent on all EC2 instances in this assessment target** checkbox.
   #. Click on **Next**.

   .. thumbnail:: /images/cloud-security/aws/inspector/05-configure-the-assessment-target.png
      :align: center
      :width: 80%

   .. note::

      If you prefer to install the Inspector Classic agents manually, follow the instructions on the `installing Amazon Inspector Classic agents <https://docs.aws.amazon.com/inspector/v1/userguide/inspector_installing-uninstalling-agents.html>`__ guide.

#. Configure the assessment template. Choose the name, duration, and frequency of the analysis and click on **Next**.

   .. thumbnail:: /images/cloud-security/aws/inspector/06-configure-the-assessment-template.png
      :align: center
      :width: 80%

#. Finally, review the details of the configured target and template and click on **Next**.

   .. thumbnail:: /images/cloud-security/aws/inspector/07-review-and-click-next.png
      :align: center
      :width: 80%

.. _inspector_policy_configuration:

Policy configuration
^^^^^^^^^^^^^^^^^^^^

.. include:: /_templates/cloud/amazon/create_policy.rst

.. include:: /_templates/cloud/amazon/read_only_policy_description.rst

.. code-block:: json

   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "VisualEditor0",
               "Effect": "Allow",
               "Action": [
                   "inspector:ListFindings",
                   "inspector:DescribeFindings"
               ],
               "Resource": "*"
           }
       ]
   }

.. include:: /_templates/cloud/amazon/attach_policy.rst

Configure Wazuh to process Amazon Inspector Classic logs
--------------------------------------------------------

#. Access the Wazuh configuration in **Server management** > **Settings** using the Wazuh dashboard or by manually editing the ``/var/ossec/etc/ossec.conf`` file in the Wazuh server or agent.

   .. thumbnail:: /images/cloud-security/aws/inspector/01-wazuh-configuration.png
      :align: center
      :width: 80%

   .. thumbnail:: /images/cloud-security/aws/inspector/02-wazuh-configuration.png
      :align: center
      :width: 80%

#. Add the following :doc:`Wazuh module for AWS </user-manual/reference/ossec-conf/wodle-s3>` configuration block to enable the integration with Inspector Classic:

   .. code-block:: xml

      <wodle name="aws-s3">
        <disabled>no</disabled>
        <interval>10m</interval>
        <run_on_start>no</run_on_start>
        <skip_on_error>no</skip_on_error>
        <service type="inspector">
          <aws_profile>default</aws_profile>
          <regions>us-east-1,us-east-2</regions>
        </service>
      </wodle>

   You must specify at least a region. You can add multiple comma-separated regions.

#. Save the changes and restart Wazuh to apply the changes. The service can be manually restarted using the following command outside the Wazuh dashboard:

   -  Wazuh manager:

      .. code-block:: console

         # systemctl restart wazuh-manager

   -  Wazuh agent:

      .. code-block:: console

         # systemctl restart wazuh-agent
