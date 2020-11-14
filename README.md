# Learn by Doing: Service Template Conditionals

NSO has a ton of features. This repository is the first of a series which will show a simple use case, along with a feature. The purpose is dual:

1. See NSO applied to a variety of configuration situations and see how versatile it is..
1. Learn something new and have an example to follow

This example is a service package deploying:

**NTP Configuration**

and the feature I am showcasing is:

**Service Template XML Conditionals**

This example assumes a working knowledge of services and NSO. 

## Installation

[Reserve the NSO Reservable Sandbox](https://devnetsandbox.cisco.com/RM/Diagram/Index/43964e62-a13c-4929-bde7-a2f68ad6b27c?diagramType=Topology)

If you need to revisit some of the NSO Basics, you can [start here](https://developer.cisco.com/learning/lab/learn-nso-the-easy-way/step/1). 

Use some type of file transfer program or [VS Code has remote SSH](https://code.visualstudio.com/docs/remote/ssh) (drag and drop the package into the packages directory)

This repo should go here:

`/var/opt/ncs/packages/`

If you are reproducing this elsewhere, here are the version details:
```
using NSO 5.3.0.1
cisco-ios-cli-6.44
on
CSR1000V 16.11.01b
```

After the package is on the configured NSO system install server (10.10.20.49) at the packages folder, make sure the yang is compiled reload packages and configure some sample service instances:

```
** transfer package over **
cd /var/opt/ncs/packages/ntp_server_example/src
make
ncs_cli
packages reload
conf
ntp_server_example first-instance endpoint 1st-dev ntp-device dist-rtr01
commit dry-run outformat native
region AMER
commit dry-run outformat native
exit
endpoint 2nd-dev ntp-device dist-rtr02 region APJC
exit
endpoint 3rd-dev ntp-device core-rtr01 region AMER
exit
endpoint 4th-dev ntp-device core-rtr02 region EMEA
exit
endpoint 5th-dev ntp-device dist-sw01 region AMER
exit
endpoint 6th-dev ntp-device dist-sw02 region APJC
exit
endpoint 7th-dev ntp-device edge-sw01 region EMEA
exit
endpoint 8th-dev ntp-device internet-rtr01 region AMER
commit dry-run outformat native
commit
```

here is some sample output:

```
[developer@nso src]$ ncs_cli -C
developer@ncs# packages reload
reload-result {
    package cisco-asa-cli-6.8
    result true
}
reload-result {
    package cisco-ios-cli-6.44
    result true
}
reload-result {
    package cisco-iosxr-cli-7.20
    result true
}
reload-result {
    package cisco-nx-cli-5.15
    result true
}
reload-result {
    package ntp_server_example
    result true
}
reload-result {
    package resource-manager
    result true
}
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem stopped: ncs-dp-134-cisco-ios-cli-6.44:IOSDp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem stopped: ncs-dp-135-cisco-nx-cli-5.15:NexusDp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem stopped: ncs-dp-136-resource-manager:AddressallocationIPvalidation
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem stopped: ncs-dp-133-cisco-asa-cli-6.8:ASADp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem started: ncs-dp-137-cisco-asa-cli-6.8:ASADp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem started: ncs-dp-138-cisco-ios-cli-6.44:IOSDp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem started: ncs-dp-139-cisco-nx-cli-5.15:NexusDp
developer@ncs#
System message at 2020-11-13 15:50:55...
    Subsystem started: ncs-dp-140-resource-manager:AddressallocationIPvalidation
developer@ncs#
developer@ncs# conf
Entering configuration mode terminal
developer@ncs(config)# ntp_server_example first-instance endpoint 1st-dev ntp-device dist-rtr01
developer@ncs(config-endpoint-1st-dev)# commit dry-run outformat native
native {
}
developer@ncs(config-endpoint-1st-dev)# region AMER
developer@ncs(config-endpoint-1st-dev)# commit dry-run outformat native
native {
    device {
        name dist-rtr01
        data ntp server 10.20.30.40
    }
}
developer@ncs(config-endpoint-1st-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 2nd-dev ntp-device dist-rtr02 region APJC
developer@ncs(config-endpoint-2nd-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 3rd-dev ntp-device core-rtr01 region AMER
developer@ncs(config-endpoint-3rd-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 4th-dev ntp-device core-rtr02 region EMEA
developer@ncs(config-endpoint-4th-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 5th-dev ntp-device dist-sw01 region AMER
developer@ncs(config-endpoint-5th-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 6th-dev ntp-device dist-sw02 region APJC
developer@ncs(config-endpoint-6th-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 7th-dev ntp-device edge-sw01 region EMEA
developer@ncs(config-endpoint-7th-dev)# exit
developer@ncs(config-ntp_server_example-first-instance)# endpoint 8th-dev ntp-device internet-rtr01 region AMER
developer@ncs(config-endpoint-8th-dev)# commit dry-run outformat native
native {
    device {
        name core-rtr01
        data ntp server 10.20.30.40
             exit
    }
    device {
        name core-rtr02
        data ntp
              server 192.20.30.60
             exit
    }
    device {
        name dist-rtr01
        data ntp server 10.20.30.40
    }
    device {
        name dist-rtr02
        data ntp server 172.20.30.50
    }
    device {
        name dist-sw01
        data ntp server 10.20.30.40
    }
    device {
        name dist-sw02
        data ntp server 172.20.30.50
    }
    device {
        name edge-sw01
        data ntp server 192.20.30.60
    }
    device {
        name internet-rtr01
        data ntp server 10.20.30.40
    }
}
developer@ncs(config-endpoint-8th-dev)#
developer@ncs(config-endpoint-8th-dev)#
developer@ncs(config-endpoint-8th-dev)#
developer@ncs(config-endpoint-8th-dev)# commit
Commit complete.
developer@ncs(config-endpoint-8th-dev)# end
developer@ncs#
```



## Service Template Conditional Statements

The conditional statement example can be found in the NSO Development guide PDF with the installation, in the  chapter 11 section called `Conditional Statements`. 

Basically conditional statements allow us to control which pieces of the configuration we want to exist or not, without using any Python/Java code. You will need to make some type of statement that evaluates to true or false. In our example we are checking to see which region the device is set to, and giving it that NTP server. 

The configuration we want to filter looks like this:
```
ntp server 10.20.30.40
```
Depending on which region our device sits, we want a different IP address for the server. 

In this service package we have the following data model:
```javascript
module ntp_server_example {
  namespace "http://com/example/ntp_server_example";
  prefix ntp_server_example;

  import ietf-inet-types {
    prefix inet;
  }
  import tailf-ncs {
    prefix ncs;
  }
  import tailf-common {
    prefix tailf;
  }

  list ntp_server_example {
    key name;

    uses ncs:service-data;
    ncs:servicepoint "ntp_server_example";

    leaf name {
      type string;
    }

   list endpoint {
        key "id";
        leaf id{
          tailf:info "Endpoint identifier";
          type string;
        }
        leaf ntp-device {
          mandatory true;
          type leafref {
            path "/ncs:devices/ncs:device/ncs:name";
          }
        }
        leaf region {
          type enumeration {
            enum "AMER";
            enum "APJC";
            enum "EMEA";
          }
        }


      }


    // // may replace this with other ways of refering to the devices.
    // leaf-list device {
    //   type leafref {
    //     path "/ncs:devices/ncs:device/ncs:name";
    //   }
    // }

    // // replace with your own stuff here
    // leaf dummy {
    //   type empty;
    // }
  }
}

```

I replaced the single device leafref with a list of `endpoints` which leafref back to the device list, but also give us an additional leaf of `region` to tag each one with a region for the conditional to check against. 

Since NTP servers are on pretty much every device, and don't change often, they are usually lumped together with other static config. For the sake of simplicity and instruction of concepts, they are the only configuration this service is using. 

### Foreach Device Looping

Even though this repo is focusing on conditionals, I took the opportunity to give an example of looping over a list of devices (`endpoint` list) in the template, so we could create one service instance that iterates over the `endpoint` list. 

```xml
<config-template xmlns="http://tail-f.com/ns/config/1.0"
                 servicepoint="ntp_server_example">
  <devices xmlns="http://tail-f.com/ns/ncs">
      <?foreach {/endpoint}?>
      <?set NTP_DEVICE={ntp-device}?>
      <?set NTP_REGION={region}?>
    <device>
      <name>{$NTP_DEVICE}</name>
      <config>
```

The `foreach` is referring to the `endpoint` list in the yang, and I also used the ability to set local variable names, just to illustrate the functionality `<?set NTP_DEVICE={ntp-device}?>`. So when the template runs, it will loop over each device and take the device name and set it to a new variable and set the associated region to a variable. 

### Conditionals

The template looks like as follows:

```xml
<config-template xmlns="http://tail-f.com/ns/config/1.0"
                 servicepoint="ntp_server_example">
  <devices xmlns="http://tail-f.com/ns/ncs">
      <?foreach {/endpoint}?>
      <?set NTP_DEVICE={ntp-device}?>
      <?set NTP_REGION={region}?>
    <device>
      <name>{$NTP_DEVICE}</name>
      <config>
        <!--
        AMER NTP Servers
        -->
        <?if {contains($NTP_REGION, 'AMER')}?>
          <ntp xmlns="urn:ios">
            <server>
              <peer-list>
                <name>10.20.30.40</name>
              </peer-list>
            </server>
          </ntp>

          <ntp xmlns="http://tail-f.com/ned/cisco-ios-xr">
            <server>
              <server-list>
                <name>10.20.30.40</name>
              </server-list>
            </server>
          </ntp>
          
          <ntp xmlns="http://tail-f.com/ned/cisco-nx">
            <server>
              <id>10.20.30.40</id>
            </server>
          </ntp>
         <?end?> <!-- end if -->
        <!--
        APJC NTP Servers
        -->
        <?if {contains($NTP_REGION, 'APJC')}?>
          <ntp xmlns="urn:ios">
            <server>
              <peer-list>
                <name>172.20.30.50</name>
              </peer-list>
            </server>
          </ntp>

          <ntp xmlns="http://tail-f.com/ned/cisco-ios-xr">
            <server>
              <server-list>
                <name>172.20.30.50</name>
              </server-list>
            </server>
          </ntp>
          
          <ntp xmlns="http://tail-f.com/ned/cisco-nx">
            <server>
              <id>172.20.30.50</id>
            </server>
          </ntp>
         <?end?> <!-- end if -->
        <!--
        EMEA NTP Servers
        -->
        <?if {contains($NTP_REGION, 'EMEA')}?>
          <ntp xmlns="urn:ios">
            <server>
              <peer-list>
                <name>192.20.30.60</name>
              </peer-list>
            </server>
          </ntp>
          
          <ntp xmlns="http://tail-f.com/ned/cisco-ios-xr">
            <server>
              <server-list>
                <name>192.20.30.60</name>
              </server-list>
            </server>
          </ntp>

          <ntp xmlns="http://tail-f.com/ned/cisco-nx">
            <server>
              <id>192.20.30.60</id>
            </server>
          </ntp>
         <?end?> <!-- end if -->
      </config>
    </device>
    <?end?> <!-- end foreach {/endpoint} -->
  </devices>
</config-template>
```

I used the XPATH function `contains()` to check to see if the region was one of our three options. You can see common XPATH functions [here](https://developer.mozilla.org/en-US/docs/Web/XPath/Functions). If you look at the sample output in this README above, you will notice that the first commit dry run does not include a region on purpose, to show you that the check is working and no config is created. 

Related to this discussion be careful of using a list key as a conditional check, [see this discussion] (https://community.cisco.com/t5/nso-developer-hub-discussions/conditional-quot-if-quot-statement-is-not-working-as-expected/td-p/4019274)

