---
id: version-1.1.X-magma_faq
title: Frequently Asked Questions
hide_title: true
original_id: magma_faq
---
# Frequently Asked Questions
This section lists some of the commonly asked questions related to Magma operation.

## Access Gateway
### How can I check the status of eNodeB(s) connected on a particular gateway?
  - Run `sudo enodebd_cli.py get_all_status` to check eNodeB(s) status.
  - **NOTE:** Output of this command will only show eNodeB(s) configured with TR069 protocol.

### How can I see different interfaces of gateway?
  - Run `sudo ip addr` to see all configured interfaces.
  - Commonly, interface `eth0` is used for internet connectivity and `eth1` is used for RAN connectivity.
  - You may run `ping google.com -I eth0` to check AGW connectivity with internet.
  - Also, you may run `ping <eNodeB IP> -I eth1` to check AGW connectivity with eNodeB. You can get eNodeB IP from `enodeb_cli` as mentioned in above question.

### How can I see attached subscribers list on a particular gateway and their browsing status?
  - Run `sudo mobility_cli.py get_subscriber_table`, to see all attached subscribers.
  - Note down the IP obtained by the user from above command, then run `sudo pipelined_cli.py debug display_flows | grep <USER IP>` for packet flow info of that user.

### How can I check services running on a gateway and their status?
  - **Check all services:** `sudo service magma@* status`.
  - **Check specific service:** `sudo service magma@<SERVICE_NAME> status`. e.g. `sudo service magma@enodebd status`.

### How can I collect logs for a particular service?
  - Run `sudo journalctl -fu magma@<SERVICE NAME>`, e.g. for enodeb service, run `sudo journalctl -fu magma@enodebd`.

### How can I check the status of the OVS module on a gateway?
  - Run `sudo ovs-vsctl show` to check.

### How can I capture packets for troubleshooting?
  - You can use general purpose tcpdump command to capture traffic at any of your interfaces. e.g. run `sudo tcpdump -i eth1 -w /tmp/capture.pcap` to capture packet at eth1 interface.

### What are the common troubleshooting logs in gateway?
  - **syslog:** `/var/log/syslogs`.
  - **MME logs:** `/var/log/mme.log`.
  - **eNodeB logs:** `/var/log/enodebd.log`.

### Where can I find the configuration of a gateway?
  - File `/var/opt/magma/configs/gateway.mconfig` is the configuration streamed down from Orchestrator.
  - Any custom static configs will also be in directory `/var/opt/magma/configs` with a `.yml` extension.
  - Default static configs are located at `/etc/magma/configs`.
  - systemd unit files are located at `/etc/system/systemd`.

### How can I restart gateway services?
  - There are 2 options to restart gateway services. First from CLI and other from NMS UI.
  - To restart services from CLI, login to AGW node, then perform below steps:
    - Run `sudo service magma@* stop` to stop services.
    - Then, run `sudo service magma@magmad restart` to restart services.
  - To restart services from NMS UI, perform below steps:
    - Login to NMS UI and go to **Gateways** option from left hand pane menu.
    - Then, click on **edit** option against the gateway for which you wish to reboot services.
    - From the popup window, select tab **COMMANDS**. Then, check section **Reboot**.
    - Click on **Restart Services** to restart all AGW services.

### How can I reboot the gateway?
  - There are 2 options to reboot gateway. First from CLI and other from NMS UI.
  - To reboot AGW from CLI, login to AGW node, then run `sudo init 6` to reboot whole AGW node.
  - To reboot AGW from NMS UI, perform below steps:
    - Login to NMS UI and go to **Gateways** option from left hand pane menu.
    - Then, click on **edit** option against the gateway which you wish to reboot.
    - From the popup window, select tab **COMMANDS**. Then, check section **Reboot**.
    - Click on **Reboot** to reboot the AGW node.

## Orchestrator
### How can I check production pods in Orchestrator running on Kubernetes?
  - **For all pods:** `kubectl -n magma get pods`.
  - **Logs of a particular pod:** `kubectl -n magma logs -f <CONTROLLER PODNAME>`.
  - **NOTE:** In above commands, please use the actual namespace after `-n` you have chosen to install orc8r in.

### How can I test Orchestrator connection with Gateway?
  - Login to Gateway, then run `sudo checkin_cli.py`.

### Where can I find API endpoint?
  - If you have followed the [install guide](../orc8r/deploy_install.md), it'll be at `https://api.youdomain.com/apidocs/v1/` (the trailing slash is important).
  - You will need to load the `admin_operator.pfx` certificate into your keychain/browser, otherwise, API access will be blocked.

### How can I use Swagger UI to trigger API request?
  - Open Swagger UI, then got to interested section, e.g. **EnodeBs**.
  - Then click on API trigger action button e.g. **GET**, **PUT**, **DELETE** etc.
  - Click on **Try it out** button on right hand side.
  - Put in the required inputs and click **Execute**.

## NMS
### What is an NMS (Network Management System)?
  - NMS is a simple UI based solution provided to manage, configuring, and monitor the network effectively. You can monitor various network metrics,  configure and monitor alerts, subscribers, gateways, eNodeBs etc.

### How can I check various network metrics over NMS?
  - Login to NMS UI, then click **Metrics** on the left hand side menu to check various network metrics available.

### How can I configure an alert on NMS?
  - Login to NMS UI, then click on **Alert** on left hand side menu. Go to tab **Alert Rules** to configure rule for alert.

### How can I make sure Gateway is properly connected with NMS?
  - Login to NMS and select **Gateway** from left hand side menu bar.
  - You can see all the Gateways configured in NMS.
  - Check for the Green light besides any particular Gateway, if it is green that means it is properly connected.

### How can I restart eNodeB from NMS?
  - Login to NMS UI and go to **Gateways** option from left hand pane menu.
  - Then, click on **edit** option against the gateway with which eNodeB is connected.
  - From the popup window, select tab **COMMANDS**. Then, check section **Reboot eNodeB**.
  - Enter eNodeB serial ID of eNodeB and click on **Reboot** to reboot that particular eNodeB.
  - **Note:**Only those eNodeBs can be rebooted in this manner which are configured with TR069 protocol.
