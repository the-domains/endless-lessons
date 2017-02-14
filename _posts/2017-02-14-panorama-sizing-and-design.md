---
inFeed: true
description: |-
  The Panorama solution is comprised of two overall functions:
  Device Management and Log Collection/Reporting. A brief overview of these two
  main functions follow:
dateModified: '2017-02-14T02:51:21.607Z'
datePublished: '2017-02-14T02:51:23.909Z'
title: 'Panorama Sizing and Design '
author: []
publisher: {}
via: {}
hasPage: true
starred: false
datePublishedOriginal: '2017-02-14T02:51:23.909Z'
sourcePath: _posts/2017-02-14-panorama-sizing-and-design.md
url: panorama-sizing-and-design/index.html
_type: Article

---
# Panorama Sizing and Design 

## **Panorama Management and Logging Overview**

The Panorama solution is comprised of two overall functions:
Device Management and Log Collection/Reporting. A brief overview of these two
main functions follow:

**Device Management:** This
includes activities such as configuration management and deployment, deployment
of PAN-OS and content updates.

**Log Collection: **This
includes collecting logs from one or multiple firewalls, either to a single
Panorama or to a distributed log collection infrastructure. In addition to
collecting logs from deployed firewalls, reports can be generated based on that
log data whether it resides locally to the Panorama (e.g single M-series or VM
appliance) for on a distributed logging infrastructure.

The Panorama solution allows for flexibility in design by
assigning these functions to different physical pieces of the management
infrastructure. For example: Device management may be performed from a VM
Panorama, while the firewalls forward their logs to colocated dedicated log
collectors:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/73212b0b-c2b3-4713-8318-5c64208eed20.png)

In the example above, device management function and reporting
are performed on a VM Panorama appliance. There are three log collector groups.
Group A, contains two log collectors and receives logs from three standalone
firewalls. Group B, consists of a single collector and receives logs from a
pair of firewalls in an Active/Passive high availability (HA) configuration.
Group C contains two log collectors as well, and receives logs from two HA
pairs of firewalls. The number of log collectors in any given location is
dependent on a number of factors. The design considerations are covered below.
Note: any platform can be a dedicated manager, but only M-Series can be a
dedicated log collector.

## Log Collection

### Managed Devices

While all current Panorama platforms have an upper limit of 1000
devices for management purposes, it is important for Panorama sizing to
understand what the incoming log rate will be from all managed devices. To
start with, take an inventory of the total firewall appliances that will be
managed by Panorama.

Use the following spreadsheet to take an inventory of your devices that need to store logs:

**MODEL - PAN-OS (Major Branch \#) - Location - Measured Average Log Rate  **

Ex: 5060    Ex: 6.1.0 Ex: Main Data Center  Ex. 2500 logs/s

**Logging Requirements**

This section will cover the information needed to properly size
and deploy Panorama logging infrastructure to support customer requirements.
There are three main factors when determining the amount of total storage
required and how to allocate that storage via Distributed Log Collectors. These
factors are:

\*      
Log Ingestion Requirements: This is the total number of logs
that will be sent per second to the Panorama infrastructure.

\*      
Log Storage Requirements: This is the timeframe for which the
customer needs to retain logs on the management platform. There are different
driving factors for this including both policy based and regulatory compliance
motivators.

\*      
Device Location: The physical location of the firewalls can
drive the decision to place DLC appliances at remote locations based on WAN
bandwidth etc.

Each of these factors are discussed in the sections below:

**Log Ingestion Requirements**

The aggregate log forwarding rate for managed devices needs to
be understood in order to avoid a design where more logs are regularly
being sent to Panorama than it can receive, process, and write to disk. The
table below outlines the maximum number of logs per second that each hardware
platform can forward to Panorama and can be used when designing a solution
to calculate the maximum number of logs that can be forwarded to Panorama in
the customer environment.
![](https://imgflo.herokuapp.com/graph/2b2431f8e7ba7b0/bf90863928b8f2411df10f16909e6d30/croprotate.png?cropheight=916&cropwidth=588&degrees=0&input=https%3A%2F%2Fthe-grid-user-content.s3-us-west-2.amazonaws.com%2Fa4061bfb-631e-46af-b5cf-f6e057f0c102.png&x=38&y=0)

The log ingestion rate on Panorama is influenced by the platform
and mode in use (mixed mode verses logger mode). The table below shows the
ingestion rates for Panorama on the different available platforms and modes of
operation. ![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/4f725ed9-4ba6-479c-aae7-af1b5ce98c7e.png)

The above numbers are all maximum values. In live deployments,
the actual log rate is generally some fraction of the supported maximum.
Determining actual log rate is heavily dependent on the customer's traffic mix
and isn't necessarily tied to throughput. For example, a single offloaded SMB
session will show high throughput but only generate one traffic log.
Conversely, you can have a smaller throughput comprised of thousands of UDP DNS
queries that each generate a separate traffic log. For sizing, a rough
correlation can be drawn between connections per second and logs per second.

Methods
for Determining Log Rate

New Customer:

\*      
Leverage information from existing customer sources. Many
customers have a third party logging solution in place such as Splunk,
ArcSight, Qradar, etc. The number of logs sent from their existing firewall
solution can pulled from those systems. When using this method, get a log count
from the third party solution for a full day and divide by 86,400 (number of
seconds in a day). Do this for several days to get an average. Be sure to
include both business and non-business days as there is usually a large
variance in log rate between the two.

\*      
Use data from evaluation device. This information can provide a
very useful starting point for sizing purposes and, with input from the
customer, data can be extrapolated for other sites in the same design. 
This method has the advantage of yielding an average over several days. A
script (with instructions) to assist with calculating this information can be
found is attached to this document. To use, download the file named "**ts\_lps.zip**".
Unpack the zip file and reference the README.txt for instructions.

\*      
If no information is available, use the Device Log Forwarding
table above as reference point. This will be the least accurate method for any
particular customer.

Existing Customer:

For existing customers, we can leverage data
gathered from their existing firewalls and log collectors:

\*      
To check the log rate of a single firewall, download the
attached file named "D**evice.zip**", unpack the zip file and
reference the README.txt file for instructions. This package will query a
single firewall over a specified period of time (you can choose how many
samples) and give an average number of logs per second for that period. If the
customer does not have a log collector, this process will need to be run
against each firewall in the environment.

\*      
If the customer has a log collector (or log collectors),
download the attached file named "**lc\_lps.zip**", unpack the zip
file and reference the README.txt file for instructions This package will
query the log collector MIB to take a sample of the incoming log rate over a
specified period.

**Log Storage Requirements**

Factors
Affecting Log Storage Requirements

There are several factors that drive log storage requirements.
Most of these requirements are regulatory in nature. Customers may need to meet
compliance requirements for HIPAA, PCI, or Sarbanes-Oxely.

\*      
[PCI DSS requirement 10.7][0]

\*      
[Sarbanes-Oxely Act, Section 802][1]

\*      
[HIPAA - § 164.316(b)(2)(i)][2]

There are other governmental and industry standards that may
need to be considered. Additionally, some companies have internal requirements.
For example: that a certain number of days worth of logs be maintained on the
original management platform. Ensure that all of these requirements are
addressed with the customer when designing a log storage solution.

**Note that some companies have maximum
retention policies as well. **

**Calculating Required Storage**

Calculating required storage space based on a given customer's
requirements is fairly straight forward process. The following chart shows the
size of each log type on disk. This number accounts for both the logs
themselves as well as the associated indices. The Threat database is the data
source for Threat logs as well as URL, Wildfire Submissions, and Data Filtering
logs. The number below reflects an aggregate average.

**Note that we may not be the logging solution
for long term archival.  In these cases suggest Syslog forwarding for
archival purposes. **
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/29bc5634-e8ce-4bc2-bc5a-0b0b4dc81b56.png)

Total log storage is the sum of the results of the equation
above for each log type. Traffic and Threat logs will generally have a much
higher log rate than either Config or System logs and will consequently account
for a majority of the storage requirements in any design.

There is a quota system on Panorama that must also be accounted
for when sizing the log collection solution.  In the above example, we
only calculated the size required for traffic logs for 30 days.  The
default quota size for a log collector for traffic logs is 25%. 
Therefore, we would need 1.1TB of log space for just traffic logs and by
default that is 25% of our total storage size.  Customer can adjust quota
based on desired retention periods.  

The other factor to consider is the ratio of traffic to threat
logs.  We have measured the average ratio to be approximately 70% traffic
and 30% threat on our testbed when using URL filtering.  If you don't know
the ratio, or want to be on the safe side, use 400 bytes in the above
calculations to account for the ratio and that would account for both threat
and traffic logs.

**LogDB Storage Quotas**

After determining the total storage required for the solution,
consider how that storage is allocated.In order to meet the customer
requirement, the design may call for an adjustment of the quota settings. The
default storage quotas for the M-series log collectors are:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/5944b17e-325c-42fe-a67f-f829e1518ed5.png)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/3cd3b3b4-7e88-48e7-8858-a5692d382325.png)

In the table above, it can be seen that even when the total
storage requirement is met, it may be necessary to adjust the quota
configuration to meet the specification of the solution. When designing log
collector groups, available storage for any given log type is the aggregate of
the storage quotas allocated on each member of the group.

Device Location

The last design consideration for logging infrastructure is
location of the firewalls relative to the Panorama platform they are logging
to. If the device is separated from Panorama by a low speed network segment
(e.g. T1/E1), it is recommended to place a Dedicated Log Collector (DLC) on
site with the firewall. This allows log forwarding to be confined to the higher
speed LAN segment while allowing Panorama to query the log collector when
needed. For reference, the following table shows bandwidth usage for log
forwarding at different log rates. This includes both logs sent to Panorama and
the acknowledgement from Panorama to the firewall.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/5a9abaa3-af5b-4264-9a91-ed0dac2710da.png)

## Device Management

There are several factors to consider when choosing a platform
for a Panorama deployment. Initial factors include:

\*      
Number of concurrent administrators need to be supported?

\*      
Does the Customer have VMWare virtualization infrastructure that
the security team has access to?

\*      
Does the customer require dual power supplies?

\*      
What is the estimated configuration size?

\*      
Will the device handle log collection as well?

### Panorama Virtual Appliance

This platform has the lowest log ingestion rate, but management
capabilities will scale with additional resources. Adding additional vCPU and
vRAM will not increase the log ingestion rate, but will allow the appliance to
handle management tasks more quickly (e.g commits, running reports). See table
below:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/e0133f5a-e035-4b17-9eb9-3b09361816d3.png)

When to choose Virtual Appliance?

\*      
The customer has large VMWare Infrastructure that the security
has access to

\*      
Customer is using dedicated log collectors and are not in mixed
mode

When not to choose Virtual Appliance?

\*      
Server team and Security team are separate and do not want to share

\*      
Mixed mode with more than 10k log/s or more than 8TB required
for log retention

M-100
Hardware Platform

This platform has dedicated hardware and can handle up to
concurrent 15 administrators. When in mixed mode, is capable of ingesting
10,000 - 15,000 logs per second.

When to choose M-100?   
  

\*      
The customer needs a dedicated platform, but is very price
sensitive

\*      
Customer is using dedicated log collectors and are not in mixed
mode but do not have VM infrastructure

When not to choose M-100?

\*      
If dual power supplies are required

\*      
Mixed mode with more than 10k log/s or more than 8TB required
for log retention

\*      
Has more than 15 concurrent admins

M-500
Hardware Platform

This platform has the highest log ingestion rate, even when in
mixed mode. The higher resource availability will handle larger configurations
and more concurrent administrators (15-30). Offers dual power supplies, and has
a strong growth roadmap.

When to choose M-500?   
  

\*      
The customer needs a dedicated platform, and has a large or
growing deployment

\*      
Customer is using dual mode with more than 10k log/s and need
less than 16TB for log retention

\*      
Customer want to future proof their investments

\*      
Customer need a dedicated appliance but has more than 15
concurrent admins

\*      
Requires dual power supplies

When not to choose M-500?

\*      
If the customer has VM Infrastructure that they want to take
advantage of for management

\*      
The customer is very price sensitive

High Availability

This section will address design considerations when planning
for a high availability deployment. Panorama high availability is
Active/Passive only and both appliances need to be fully licensed. There are
two aspects to high availability when deploying the Panorama solution. These
aspects are Device Management and Logging. The two aspects are closely related,
but each has specific design and configuration requirements.

**Device Management HA:** The
ability to retain device management capabilities upon the loss of a Panorama
device (either an M-series or virtual appliance).

**Logging HA or Log Redundancy:** The ability to retain firewall logs upon the loss of a
Panorama device (M-series only).

**Device Management HA**

When deploying the Panorama solution in a high availability
design, many customers choose to place HA peers in separate physical locations.
From a design perspective, there are two factors to consider when deploying a
pair of Panorama appliances in a High Availability configuration. These
concerns are network latency and throughput.

**Network Latency**

The latency of intervening network segments affects the control
traffic between the HA members. HA related timers can be adjusted to the need
of the customer deployment. The maximum recommended value is 1000 ms.

\*      
**Preemption Hold Time:** If
the Preemptive option is enabled, the Preemption Hold Time is the amount of
time the passive device will wait before taking the active role. In this case,
both devices are up, and the timer applies to the device with the
"Primary" priority.

\*      
**Promotion Hold Time:** The
promotion hold timer specifies the interval that the Secondary device will wait
before assuming the active rote. In this case, there has been a failure of the
primary device and this timer applies to the Secondary device.

\*      
**Hello Interval:** This
timer defines the number of milliseconds between Hello packets to the peer
device. Hello packets are used to verify that the peer device is operational.

\*      
**Heartbeat Interval:** This
timer defines the number of milliseconds between ICMP messages sent to the
peer. Heartbeat packets are used to verify that the peer device is reachable.

**Relation between network latency and Heartbeat interval**

Because the heartbeat is used to determine reachability of the
HA peer, the Heartbeat interval should be set higher than the latency of the
link between the HA members.

**HA Timer Presets**

While customers can set their HA timers specifically to suit
their environment, Panorama also has two sets of preconfigured timers that the
customer can use. These presets cover a majority of customer deployments

Recommended:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/0185e0c0-4447-42d8-926e-94723e26357d.png)

## Configuration Sync

### **HA Sync Process**
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/7d260bbd-4fd1-492c-b514-027959a013e1.png)

The HA sync process occurs on Panorama when a change is made to
the configuration on one of the members in the HA pair. When a change is
made and committed on the Active-Primary, it will send a send a
message to the Active-Secondary that the configuration needs to be
synchronized. The Active-Secondary will send back an acknowledgement that it is
ready. The Active-Primary will then send the configuration to the
Active-Secondary. The Active-Secondary will merge the configuration sent by the
Active-Primary and enqueue a job to commit the changes. This process must
complete within three minutes of the HA-Sync message being sent from the
Active-Primary Panorama. The main concern is size of the configuration being
sent and the effective throughput of the network segment(s) that separate the
HA members.

**Log Availability**

The other piece of the Panorama High Availability solution is
providing availability of logs in the event of a hardware failure. There are
two methods for achieving this when using a log collector infrastructure
(either dedicated or in mixed mode).

**Log Redundancy**

PAN-OS 7.0 and later include an explicit option to write each
log to 2 log collectors in the log collector group. By enabling this option, a
device sends it's log to it's primary log collector, which then replicates the
log to another collector in the same group: 

Log duplication ensures that there are two copies of any given
log in the log collector group. This is a good option for customers who need to
guarantee log availability at all times. Things to consider:

1\. The replication only takes place within a log collector group.

2\. The overall available storage space is halved (because each
log is written twice).

3\. Overall Log ingestion rate will be reduced by up to 50%.

**Log Buffering**

When firewalls require an acknowledgement from the Panorama
platform that they are forwarding logs to. This means that in the event that
the firewall's primary log collector becomes unavailable, the logs will be
buffered and sent when the collector comes back online. There are two methods
to buffer logs. The first method is to configure separate log collector groups
for each log collector:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/0574f085-fd32-4428-b1e5-b824be66ec5c.png)

In this situation, if Log Collector 1 goes down, Firewall A
& Firewall B will each store their logs on their own local log partition
until the collector is brought back up. The local log partition for the
currently sold models are:
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/12fdbb81-22b4-489c-9e7a-599eed7863a4.png)

The second method is to place multiple log collectors into a
group. In this scenario, the firewall can be configured with a priority list so
if the primary log collector goes down, the second collector on the list will
buffer the logs until the primary collector is returned to service. The primary
disadvantage of this approach is that only 10 GB of space is allocated on a log
collector to hold buffered logs from all forwarding devices.

In the architecture shown below, Firewall A & Firewall B are
configured to send their logs to Log Collector 1 primarily, with Log Collector
2 as a backup. If Log Collector 1 becomes unreachable, the devices will send
their logs to Log Collector 2 for buffering. Only the first 10GB of logs
received by Log Collector 2 will be buffered. If Firewall A uses all 10 GB,
Firewall B will not have any logs buffered.  ![](https://imgflo.herokuapp.com/graph/2b2431f8e7ba7b0/97b786a6f661cbb2f224b224c80cbbea/croprotate.png?cropheight=689&cropwidth=670&degrees=0&input=https%3A%2F%2Fthe-grid-user-content.s3-us-west-2.amazonaws.com%2F5d53ab59-5289-4603-a394-057aec25897b.png&x=23&y=0)

## Example Use Cases
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/30466856-725b-4470-8696-97f99ceb87c1.png)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/afe41a8f-fd62-42f0-8c03-26388f765c94.png)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/c3a159a4-491a-4061-8950-7c3204120337.png)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/8fb815b9-8ac1-4154-8aa9-6798da108f81.png)



[0]: https://pcinetwork.org/forum/index.php?threads/pci-dss-1-x-10-7-retain-audit-trail-history-for-at-least-one-year-with-a-minimum-of-three-months-available-onli.158/
[1]: https://www.sec.gov/rules/final/33-8180.htm
[2]: http://www.hipaasurvivalguide.com/hipaa-regulations/164-316.php