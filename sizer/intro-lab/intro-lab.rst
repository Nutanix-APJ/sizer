.. _sizer-intro-lab:

---------------------------
实验 - Sizer介绍/RAW 输入
---------------------------

简介
--------

本实验将采用来自:ref:`rvtools` 和 :ref:`rvtools_answers` 的数据创建Sizer场景并向你介绍众多的Sizer关键特性。

接入 Sizer
---------------

Nutanix Sizer 工具在Nutanix员工和合作伙伴链接 http://sizer.nutanix.com

**Nutanix Employees** - 选择 **Salesforce Login**

.. note::虽然Nutanix员工可以通过他们的My Nutanix凭证访问Sizer，但通过Salesforce访问Sizer可以简化上传BOM和创建报价的工作流程。
**Partners** - Choose **My Nutanix Login**

.. figure:: images/1.png

创建一个场景
-------------------

登录之后, 点击 **+ Create New Scenario**.

提供一个 **Scenario Name**.

对于员工，提供一个 **Customer Account**.

.. note::

客户帐户和Opportunity名称将在您开始输入时自动填充。 创建scenario不需要创建Opportunity名称，但稍后需要将BOM推送到Salesforce。

在**使用供应商模型**下，选择所需的硬件平台（例如** Nutanix型号**）。

提供 **Executive Summary** 和 **Requirements**. Executive Summary应总结项目以及我们正在寻求解决的任何问题。 Requirements应总结用于计算工作负载大小的关键发现数据。

在 **Use Vendor Models**下面, 选择所需的硬件平台(e.g. **Nutanix Models**).

.. note::

  创建scenario目前不需要Executive Summary and Requirements ，但在导出方案的BOM之前需要这些信息。 所有其他字段都是可选字段，但填写它们会整合调整方案的源信息，在共享或引用旧方案时很有用。


.. figure:: images/2.png

点击 **Create**.

增添工作负载
----------------

*潜在客户表示他们希望在当前环境中整合在* **Cluster1** *and* **Mgmt** *上运行的混合工作负载。*

点击 **+ Add Workload**.

特定 **Cluster1** 作为 **Workload Name**.

.. note:: It is helpful to provide meaningful names to each workload in a scenario. Using the customer's cluster or project naming makes it easier for a customer to follow the logic behind your Nutanix sizing scenario.

选择 **RAW Input** 作为 **Workload Type**.

.. note::

  You could also select **Server Virtualization** as a Workload Type and specify the average VM specifications, rather than the totals for RAW Input.

.. figure:: images/3.png

Click **Next**.

填充如下字段:

- **vCPUs** - 257
- **vCPU:pCore ratio** - 4
- **RAM** - 1195 GiB
- **HDD Storage** - 0
- **SSD Storage** - 18.7 TiB

.. note::


Under **Profile Info**, click **Customize**.

Specify **32** GiB as the **RAM Overhead per CVM**. Click **Save**.

CPU要求也可以用GHz定义。 当您使用Live Optics或vRealize Operations等工具获得历史CPU利用率时，使用此输入非常有用。

在**个人资料信息**下，点击**自定义**。

指定** 32 ** GiB作为每个CVM的** RAM开销**。 点击**保存**。

.. figure:: images/4.png

.. note::

  Refer to :ref:`ntnx` for additional information on CVM Overheads. Even if a customer is not immediately planning on using features that would require additional CVM memory (e.g. deduplication), it is generally a good idea to account for 32 GiB per CVM. A notable exception is in ROBO deployments where memory is often at a significant premium compared to storage capacity requirements.

Select your desired **Container Replication Factor**.

Select **No** for **Disable Compression for pre-compressed data**, unless your discovery of the existing environment indicates there are workloads storing pre-compressed data (images, audio, video, compressed archive files).

Leave **Container Compression**, **Deduplication**, and **Erasure Coding** at their defaults for the workload.

If required, select **Block Awareness** for the Automatic Sizer result to elect for a homogenous node configuration capable of supporting Block Awareness.

.. note::

  This does **NOT** account for additional compute and storage capacity to run all workloads in the event of an entire block (to to N + 4), it simply accounts for data availability in the event of a full block failure.

  If targeting 1N1U or 1N2U platforms, enabling Block Awareness has no impact on sizing configuration.

Under **Advanced Options**, note the ability to consolidate workloads within a single Nutanix cluster, or provide multiple clusters within a single Sizer scenario. Workloads that require dedicated clusters can also be created in separate Sizer scenarios, this is a matter of personal preference and scope of the scenario.

.. note::

  By default, the initial cluster will be named **Cluster-1**. Selecting **Create New Cluster** from the dropdown menu will allow you to create a custom name for the cluster.

If desired, select **Encrypted Storage for VM** for Sizer to select Self-Encrypting Drives (SEDs) for applicable SSDs and HDDs. Hardware-based encryption should typically only be used in high performance environments where the additional, small amount of CPU overhead from software-based encryption cannot be tolerated.

.. figure:: images/5.png

*For* **Cluster1**\ *, the prospect would like to retain the last 24 hourly snapshots and 10 daily snapshots locally. They will also require an identical remote site for DR. Based on daily incremental backup reports, the prospect estimates their daily change rate for these workloads at 2%.*

Under **Data Protection**, select **Yes**.

Select **Async** for snapshots with a maximum RPO of 60 minutes.

.. note::

  **NearSync** supports a maximum RPO of 1 minute, but currently Sizer only allows for a maximum retention of the last 15 1-minute snapshots.

Specify **2%** as the **Daily Change Rate %**.

Specify **24 Hourly Snapshots Retained** and **10 Daily Snapshots Retained**

Select **Remote Snapshot** and specify a **Cluster Name** for the remote cluster.

.. note::

  This will create a second cluster sized with minimal compute and adequate storage capacity based on the workload capacity, change rate, and retention policy. The retention policy for the remote site can be adjusted independent of the local snapshot policy.

Select **Disaster Recovery**.

.. note::

  This selection impacts the compute sizing of the remote cluster. It now needs to have enough storage capacity for the remote replication of workloads, and also enough CPU and RAM to run the workloads in the event of a cluster failure in the primary site. If not all workloads need to be restored during a site failure, this should be identified when analyzing discovery data to break up groups into those requiring remote replication and DR capabilities.

.. figure:: images/6.png

Click **Save**.

Repeat this process using the details for **Mgmt**. Under **Advanced Options**, select **Cluster-1** to indicate the workloads should be consolidated on the same Nutanix cluster as the previous workload.

*For* **Mgmt**\ *, the prospect would like to retain the last 12 hourly snapshots and 5 daily snapshots locally, but do not require remote replication or DR. Based on daily incremental backup reports, the prospect estimates their daily change rate for these workloads at 1%.*

Once completed, you will be presented with the Automatic Sizer results for **Cluster-1** and **SiteB-Cluster-1**.

.. figure:: images/7.png

.. note::

  Due to changing model/part availability, your Automatic result may not reflect the result pictured.

Customizing Automatic Sizing
----------------------------

Select **Cluster-1** from the **Summary for** drop down menu. This will update the **Sizing Summary** and **Sizing Details** for that particular cluster. Notice that the CPU utilization is slightly higher than **SiteB-Cluster-1** as it will not be a DR target for **Mgmt** cluster workloads.

.. figure:: images/8.png

Select **Modify** from the **Sizing Options** menu.

.. figure:: images/9.png

**Sizing Options** provides customizable considerations for Sizer to deliver an optimized Automatic result:

- **Cluster Type**

  - Mixed clusters will allow multiple node types within the same cluster (e.g. in a scenario with large storage capacity requirements, a mix of 3060 and 6035C nodes may be suggested).
  - Homogeneous forces all nodes within the cluster to have the same configuration.
- **Failover**

  - Aggressive does not include any additional compute/storage capacity to account for node failure.
  - Standard includes N+1 additional compute/storage capacity to account for a single node failure. Losing a node should not cause the environment to exceed defined utilization thresholds given the workload.
  - Conservative includes N+2 additional compute/storage capacity to account for two node failures. Losing up to two nodes should not cause the environment to exceed defined utilization thresholds given the workload.
- **Storage**

  - Any will choose an Automatic node configuration for the workload that may be either Hybrid or All Flash
  - All Flash will forced the Automatic node configuration to use only SSD storage.
  - Hybrid will force the Automatic node configuration to use a mix of SSD and HDD storage.

- **Model Type** - Specific models can be filtered out of Automatic results. The most common instance of this is filtering out Single Socket models in scenarios with low CPU requirements when a Single Socket node may be inappropriate (platform limitations, accounting for future growth, etc.).

- **Limit Maximum Node Count Per Cluster** - Useful when targeting a specific number of nodes for a cluster to accomodate hypervisor or other software licensing restrictions.

- **Threshold** - Default thresholds can be modified for a more conservative sizing, but should generally not be increased beyond the default values.

.. figure:: images/10.png

For this scenario, select **All Flash** to force an all SSD solution. Additionally, lower the **SSD Threshold** to **90**.

Click **Apply**.

Note that in the example configuration, the changes to the **Sizing Options** have altered the Automatic result. Can you spot the differences?

.. figure:: images/11.png

Under **View**, select **Sizing Details**.

.. figure:: images/12.png

**Sizing Details** provides a breakdown of the given cluster configuration's raw capacity, the total workload requirements, space efficiency savings, and usage percentages.

Mousing over the :fa:`info-circle` icons provide additional context to the figure.

.. figure:: images/13.png

Under **View**, select **Sizing Charts > View All Charts**.

**Sizing Charts** provide a more detailed view of cluster utilization and are helpful for inclusion in proposal documents.

.. figure:: images/14.png

Storage Capacity Calculator
---------------------------

The Nutanix Storage Capacity Calculator is a tool within Sizer, independent of Sizer scenarios, that allows you to easily determine what the **usable** amount of storage will be for a given configuration.

In our current sizing scenario, our combined **Cluster1** and **Mgmt** workloads require 20.4 TiB of storage, *but the customer has indicated to you that they require a minimum of 40 TiB of usable storage from the cluster after 2:1 savings from compression.*

From the **Username** dropdown menu in the Sizer toolbar, select **Storage Capacity Calculator**.

.. figure:: images/15.png

In our currently example, **Cluster-1** is 6 nodes, each with 4x 1.92TB SSDs and 0x HDDs.

In the **Storage Capacity Calculator**, fill out the following fields and click **Calculate**:

- **SSD Size** - 1920 GB
- Select **SSD is downstroked**

.. note:: Downstroking reserves an additional 80 GiB per SSD to extend the life of the SSD. Current guidance is to apply downstroking at all times unless using Self-Encrypting Drives (SEDs), which are generally higher endurance drives.

- **SSD Quantity** - 4
- **HDD Quantity** - 0
- **Replication Factor** - RF2
- **Node Count** - 5

.. note:: 5 nodes is used instead of 6 to determine what the maximum usable capacity that could be fully rebuilt in the event of a node failure.

- **Storage Efficiency** - 2:1

The Effective Capacity (RF2) is 27.3 TiB.

From here, using the Calculator effectively requires some trial and error, as well as an understanding of available drive combinations on different models. Our scenario is targeting the NX-3060-G6, which supports up to 6x SSDs.

Increase the **SSD Quantity** to **6** and press **Calculate**. The Effective Capacity (RF2) is 42.3 TiB, which meets the customer requirement without significant overprovisioning.

.. figure:: images/16.png

手工 Sizing
-------------

在许多情况下，您可能需要手动配置节点配置。 示例可能包括希望匹配现有群集的配置，或者在本练习的情况下，选择超出工作负载的直接要求的存储量。

** Sizer **以返回到方案列表并打开测试实验室方案。

单击顶部工具栏中的 **Sizer** 以返回到方案列表并打开测试实验室方案。

从 **Sizing Options** 目录.选择 **Modify** 

切换 **Automatic** 到 **Manual**.

.. figure:: images/17.png

You now have complete control over node count and configuration. Mouse over **Cluster-1** and click **Edit**.

.. figure:: images/18.png

From the **Edit Model** screen, make the necessary changes to the configuration to meet the storage capacity requirement. Click **Save > Apply**.

.. figure:: images/19.png

Select **Cluster-1** from the **Summary for** drop down menu and note the drop in **SSD Usage** in the **Sizing Summary**.

.. figure:: images/20.png

.. note::

  When performing a Manual sizing it is **critical** to account for compute and storage availability in the event of a node failure.

Modify the cluster to reduce the **Node Quantity** by 1 (optionally by 2 if planning for N+2 availability for an RF3 cluster). Check that all utilization values are below acceptable thresholds and then re-add the node(s) to the configuration.

.. figure:: images/21.png

Cloning
-------

Throughout the course of a deal you may work on several different iterations of your proposed configuration, including evaluating different hardware vendors.

To clone a scenario, select the :fa:`ellipsis-v` icon located to the right of the scenario name, and select **Clone Scenario**.

.. figure:: images/22.png

Change the **Scenario Name**, note any changes considered in this iteration in the **Scenario Objectives** fields, and if necessary, select an alternate hardware vendor.

.. note::

  Changing hardware vendors will restore **Sizing Options** to Automatic defaults, as component options may vary from vendor to vendor.

Click **Clone**.

.. note::

  After cloning your scenario you will be returned **to your original scenario**.

Sharing
-------

Sizer supports the ability to share **read-only** copies of a Sizer scenario with other Nutanix employees and partners.

To share a scenario, select the menu icon located to the right of the scenario name, and select **Share Scenario**.

.. figure:: images/23.png

Provide the e-mail address of the user and click **Share > Done**.

.. note:: The user must already be registered in Sizer.

.. figure:: images/24.png

To view scenarios that have been shared with you, return to the Sizer homepage and select **Shared Scenarios**.

.. figure:: images/25.png

.. note::

  You will not be able to directly edit scenarios that have been shared with you, you must first clone the scenario.

BOMs
----

A Sizer BOM is an exported PDF that can contain the following details of a Sizer scenario:

- **Scenario Objectives**
- **Financial Analysis** - Based on a current IDC TCO Study comparing Nutanix NX to legacy infrastructure. *Available to Nutanix employees only*.
- **Rack View** - Nutanix NX scenarios will also include power, cooling, and weight for the proposed solution.
- **Sizing Details**
- **Recommended SKUs** - Used for building quotes. For Software Only platforms, refer to the HCL for complete configuration SKUs.

To export a BOM, select the :fa:`ellipsis-v` icon located to the right of the scenario name, and select **Download BOM**.

.. figure:: images/26.png

Select the desired components and click **Download**.

.. figure:: images/27.png

Open the PDF and familiarize yourself with the layout of the output.

For Nutanix employees, attaching the BOM to the appropriate Salesforce opportunity is a critical step in the sales cycle. To upload the BOM to Salesforce, select the :fa:`ellipsis-v` icon located to the right of the scenario name, and select **Push BOM to Salesforce**.

详述 **Opportunity Name** 点击 **Push BOM**.

.. figure:: images/28.png

BOMs can be accessed in the **Sizer BOMs** section of each Salesforce Opportunity. Multiple BOMs can be uploaded to a single opportunity.

.. figure:: images/29.png
