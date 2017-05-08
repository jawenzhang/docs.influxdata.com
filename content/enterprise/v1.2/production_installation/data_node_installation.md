---
title: Step 2 - Data Node Installation
menu:
  enterprise_1_2:
    weight: 20
    parent: production_installation
    identifier: data_production
---

InfluxEnterprise offers highly scalable clusters on your infrastructure
and a management UI for working with clusters.
The next steps will get you up and running with the second essential component of
your InfluxEnterprise cluster: the data nodes.

If you have not set up your meta nodes, please visit
[Meta Node Installation](/enterprise/v1.2/production_installation/meta_node_installation/).
Bad things can happen if you complete the following steps without meta nodes.

<br>
# Data Node Setup Description and Requirements

The Production Installation process sets up two [data nodes](/enterprise/v1.2/concepts/glossary#data-node)
and each data node runs on its own server.
You **must** have a minimum of two data nodes in a cluster.
InfluxEnterprise clusters require at least two data nodes for high
availability and redundancy.
Note that there is no requirement for each data node to run on its own
server.

See the
[Clustering Guide](/enterprise/v1.2/concepts/clustering.md#optimal-server-counts)
for more on cluster architecture.

### Other Requirements

#### License Key or File

InfluxEnterprise requires a license key **OR** a license file to run.
Your license key is available at [InfluxPortal](https://portal.influxdata.com/licenses).
Contact support at the email we provided at signup to receive a license file.
License files are required only if the nodes in your cluster cannot reach
`portal.influxdata.com` on port `80` or `443`.

#### Networking

Data nodes communicate over ports `8088`, `8089`, and `8091`.

For licensing purposes, data nodes must also be able to reach `portal.influxdata.com`
on port `80` or `443`.
If the data nodes cannot reach `portal.influxdata.com` on port `80` or `443`,
you'll need to set the `license-path` setting instead of the `license-key`
setting in the data node configuration file.

#### Load Balancer

InfluxEnterprise does not function as a load balancer.
You will need to configure your own load balancer to send client traffic to the
data nodes on port `8086` (the default port for the [HTTP API](/influxdb/v1.2/tools/api/)).

<br>
# Data Node Setup
## Step 1: Modify the /etc/hosts File

Add your servers' hostnames and IP addresses to **each** cluster server's `/etc/hosts`
file (the hostnames below are representative).
Note that in versions prior to v1.2.2, hostnames **must** be all lowercase.

```
<Data_1_IP> enterprise-data-01
<Data_2_IP> enterprise-data-02
```

> **Verification steps:**
>
Before proceeding with the installation, verify on each meta and data server that the other
servers are resolvable. Here is an example set of shell commands using `ping`:
>
    ping -qc 1 enterprise-meta-01
    ping -qc 1 enterprise-meta-02
    ping -qc 1 enterprise-meta-03
    ping -qc 1 enterprise-data-01
    ping -qc 1 enterprise-data-02

If there are any connectivity issues resolve them before proceeding with the
installation.
A healthy cluster requires that every meta and data node can communicate
with every other meta and data node.

## Step 2: Set up, Configure, and start the Data Services

Perform the following steps on each data server.

### I. Download and Install the Data Service

#### Ubuntu & Debian (64-bit)
```
wget https://dl.influxdata.com/enterprise/releases/influxdb-data_1.2.4-c1.2.4_amd64.deb
sudo dpkg -i influxdb-data_1.2.4-c1.2.4_amd64.deb
```

#### RedHat & CentOS (64-bit)
```
wget https://dl.influxdata.com/enterprise/releases/influxdb-data-1.2.4_c1.2.4.x86_64.rpm
sudo yum localinstall influxdb-data-1.2.4_c1.2.4.x86_64.rpm
```

### II. Edit the Configuration File

First, in `/etc/influxdb/influxdb.conf`, uncomment:

* `hostname` at the top of the file and set it to the full hostname of the data node
* `auth-enabled` in the `[http]` section and set it to `true`
* `shared-secret` in the `[http]` section and set it to a long pass phrase that will be used to sign tokens for intra-cluster communication. The Enterprise Web console requires this to be consistent across all data nodes.

Second, in `/etc/influxdb/influxdb.conf`, set:

`license-key` in the `[enterprise]` section to the license key you received on InfluxPortal **OR** `license-path` in the `[enterprise]` section to the local path to the JSON license file you received from InfluxData. The `license-key` and `license-path` settings are mutually exclusive and one must remain set to the empty string.

```
# Change this option to true to disable reporting.
# reporting-disabled = false
# bind-address = ":8088"
hostname="<enterprise-data-0x>" #✨

[enterprise]
  registration-enabled = false

  registration-server-url = ""

  # license-key and license-path are mutually exclusive, use only one and leave the other blank
  license-key = "<your_license_key>" #✨ mutually exclusive with license-path

  # The path to a valid license file.  license-key and license-path are mutually exclusive,
  # use only one and leave the other blank.
  license-path = "/path/to/readable/JSON.license.file" #✨ mutually exclusive with license-key

[meta]
  # Where the cluster metadata is stored
  dir = "/var/lib/influxdb/meta" # data nodes do require a local meta directory

[...]

[http]
  # Determines whether HTTP endpoint is enabled.
  # enabled = true

  # The bind address used by the HTTP service.
  # bind-address = ":8086"

  # Determines whether HTTP authentication is enabled.
  auth-enabled = true #✨ this is recommended but not required

[...]

  # The JWT auth shared secret to validate requests using JSON web tokens.
  shared-secret = "long pass phrase used for signing tokens" #✨
```

### 3. Start the Data Service
On sysvinit systems, enter:
```
service influxdb start
```

On systemd systems, enter:
```
sudo systemctl start influxdb
```

> **Verification steps:**
>
Check to see that the process is running by entering:
>
    ps aux | grep -v grep | grep influxdb
>
You should see output similar to:
>
    influxdb  2706  0.2  7.0 571008 35376 ?        Sl   15:37   0:16 /usr/bin/influxd -config /etc/influxdb/influxdb.conf


If you do not see the expected output, the process is either not launching or is exiting prematurely. Check the [logs](/enterprise/v1.2/administration/logs/) for error messages and verify the previous setup steps are complete.

If you see the expected output, repeat for the remaining data nodes.
Once all data nodes have been installed, configured, and launched, move on to the next section to join the data nodes to the cluster.

## Join the Data Nodes to the Cluster

On one and only one of the meta nodes that you set up in the
[previous document](/enterprise/v1.2/introduction/meta_node_installation/), run:
```
influxd-ctl add-data enterprise-data-01:8088

influxd-ctl add-data enterprise-data-02:8088
```

The expected output is:
```
Added data node y at enterprise-data-0x:8088
```

Run the `add-data` command once and only once for each data node you are joining
to the cluster.

> **Verification steps:**
>
Issue the following command on any meta node:
>
    influxd-ctl show
>
The expected output is:
>
    Data Nodes
    ==========
    ID   TCP Address               Version
    4    enterprise-data-01:8088   1.2.4-c1.2.4
    5    enterprise-data-02:8088   1.2.4-c1.2.4    
>
    Meta Nodes
    ==========
    TCP Address               Version
    enterprise-meta-01:8091   1.2.4-c1.2.4
    enterprise-meta-02:8091   1.2.4-c1.2.4
    enterprise-meta-03:8091   1.2.4-c1.2.4


The output should include every data node that was added to the cluster.
The first data node added should have `ID=N`, where `N` is equal to one plus the number of meta nodes.
In a standard three meta node cluster, the first data node should have `ID=4`
Subsequently added data nodes should have monotonically increasing IDs.
If not, there may be artifacts of a previous cluster in the metastore.

If you do not see your data nodes in the output, please retry adding them
to the cluster.

Once your data nodes are part of your cluster move on to [the final step
to set up the InfluxEnterprise web console](/enterprise/v1.2/production_installation/web_console_installation/).
