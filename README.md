# aws-eni-resource-agent

Resource Agent for AWS EC2 ENI for Pacemaker

This resource agent script is intended for use with Amazon EC2 instances running Linux operating systems and Pacemaker.

The scripts have been tested on the following Amazon Machine Images (AMIs) version:

* Red Hat Enterprise Linux 6.5

The scripts have been tested on the following Pacemaker version:

* Pacemaker 1.0.13-1.2

## Prerequisites

This script requires [AWS Command Line Interface](http://aws.amazon.com/cli/).

### Install AWS Command Line Interface

If pip is not installed, install pip with following commands:
```
wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```
To install awscli, use pip:

```
pip install awscli
```

## Getting Started

Download "eni" or clone repository: 

```
git clone https://github.com/moomindani/aws-eni-resource-agent.git
cd aws-eni-resource-agent
```

Install eip resource agent:

```
mv eni /usr/lib/ocf/resource.d/heartbeat/
chown root:root /usr/lib/ocf/resource.d/heartbeat/eni
chmod 0755 /usr/lib/ocf/resource.d/heartbeat/eni
```

After setting up pacemaker, configure crm setting to make eni resource agent ready. 
Here's an example setting:

```
property \
    no-quorum-policy="ignore" \
    stonith-enabled="false" \
    crmd-transition-delay="0s"
 
primitive eni ocf:heartbeat:eni \
    params \
        interface_id="eni-2a718a5d" \
        device_index="1" \
        device_name="eth1" \
        gateway="172.31.0.1" \
        wait="60" \
        table_id="100" \
        table_priority="100" \
    op start   timeout="120s" interval="0s"  on-fail="stop" \
    op monitor timeout="60s" interval="10s" on-fail="restart" \
    op stop    timeout="120s" interval="0s"  on-fail="block"```

```

## Parameter

Name                       | Description
-------------------------- | -------------------------------------------------
interface_id               | The network interface id of ENI.
device_index               | The index of the device for the network interface attachment.
device_name                | The name of the device for the network interface attachment.
gateway                    | The gateway of the device for the network interface attachment.
wait                       | The wait time after starting and stopping interface in order to handle delay.
table_id                   | The table id for multi homing.
table_priority             | The table priority for multi homing.

