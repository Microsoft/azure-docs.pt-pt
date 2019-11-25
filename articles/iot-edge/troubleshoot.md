---
title: Troubleshoot - Azure IoT Edge | Microsoft Docs
description: Use this article to learn standard diagnostic skills for Azure IoT Edge, like retrieving component status and logs, and resolve common issues
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc05d820a409500e66e2525654954e4dddf48e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457256"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções do Azure IoT Edge

Se ocorrerem problemas com o Azure IoT Edge no seu ambiente, utilize este artigo como guia para a resolução de problemas.

## <a name="run-the-iotedge-check-command"></a>Run the iotedge 'check' command

Your first step when troubleshooting IoT Edge should be to use the `check` command, which performs a collection of configuration and connectivity tests for common issues. The `check` command is available in [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) and later.

You can run the `check` command as follows, or include the `--help` flag to see a complete list of options:

* On Linux:

  ```bash
  sudo iotedge check
  ```

* No Windows:

  ```powershell
  iotedge check
  ```

The types of checks run by the tool can be classified as:

* Configuration checks: Examines details that could prevent Edge devices from connecting to the cloud, including issues with *config.yaml* and the container engine.
* Connection checks: Verifies the IoT Edge runtime can access ports on the host device and all the IoT Edge components can connect to the IoT Hub.
* Production readiness checks: Looks for recommended production best practices, such as the state of device certificate authority (CA) certificates and module log file configuration.

For a complete list of diagnostic checks, see [Built-in troubleshooting functionality](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Passos de diagnóstico padrão

If you encounter an issue, you can learn more about the state of your IoT Edge device by reviewing the container logs and the messages that pass to and from the device. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Check the status of the IoT Edge Security Manager and its logs

On Linux:
- To view the status of the IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

- To view the logs of the IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- To view more detailed logs of the IoT Edge Security Manager:

   - Edit the iotedge daemon settings:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Update the following lines:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Restart the IoT Edge Security Daemon:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:
- To view the status of the IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

- To view the logs of the IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>If the IoT Edge Security Manager is not running, verify your yaml configuration file

> [!WARNING]
> YAML files cannot contain tabs as indentation. Use 2 spaces instead.

On Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Check container logs for issues

Once the IoT Edge Security Daemon is running, look at the logs of the containers to detect issues. Start with your deployed containers, then look at the containers that make up the IoT Edge runtime: edgeAgent and edgeHub. The IoT Edge agent logs typically provide info on the lifecycle of each container. The IoT Edge hub logs provide info on messaging and routing. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>View the messages going through the IoT Edge hub

You can view the messages going through the IoT Edge hub, and gather insights from verbose logs from the runtime containers. To turn on verbose logs on these containers, set `RuntimeLogLevel` in your yaml configuration file. To open the file:

On Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

By default, the `agent` element will look like the following example:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Replace `env: {}` with:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML files cannot contain tabs as identation. Use 2 spaces instead.

Save the file and restart the IoT Edge security manager.

Também pode verificar as mensagens que são enviadas entre o Hub IoT e os dispositivos do IoT Edge. View these messages by using the [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) extension (formerly Azure IoT Toolkit extension) for Visual Studio Code. For more information, see [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Restart containers
After investigating the logs and messages for information, you can try restarting containers:

```
iotedge restart <container name>
```

Restart the IoT Edge runtime containers:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Restart the IoT Edge security manager

If issue is still persisting, you can try restarting the IoT Edge security manager.

On Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent stops after about a minute

The edgeAgent module starts and runs successfully for about a minute, then stops. The logs indicate that the IoT Edge agent attempts to connect to IoT Hub over AMQP, and then attempts to connect using AMQP over WebSocket. When that fails, the IoT Edge agent exits. 

Example edgeAgent logs:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

**Root cause**

A networking configuration on the host network is preventing the IoT Edge agent from reaching the network. O agente primeiro tenta ligar através de AMQP (porta 5671). If the connection fails, it tries WebSockets (port 443).

O runtime do IoT Edge configura uma rede para comunicar em cada um dos módulos. No Linux, esta rede é uma rede de ponte. No Windows, utiliza NAT. Este problema é mais comum nos dispositivos Windows com contentores do Windows que utilizam a rede NAT. 

**Resolution**

Certifique-se de que existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Por vezes, uma configuração de VPN no anfitrião sobrepõe-se à rede do IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub fails to start

The edgeHub module fails to start, and prints the following message to the logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

**Root cause**

Outro processo no computador anfitrião vinculou a porta 443. The IoT Edge hub maps ports 5671 and 443 for use in gateway scenarios. Este mapeamento de portas falha se outro processo já tiver vinculado esta porta. 

**Resolution**

Encontre e pare o processo que está a utilizar a porta 443. Este processo é normalmente um servidor Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent can't access a module's image (403)
A container fails to run, and the edgeAgent logs show a 403 error. 

**Root cause**

The Iot Edge agent doesn't have permissions to access a module's image. 

**Resolution**

Make sure that your registry credentials are correctly specified in your deployment manifest

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge security daemon fails with an invalid hostname

The command `sudo journalctl -u iotedge` fails and prints the following message: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Root cause**

The IoT Edge runtime can only support hostnames that are shorter than 64 characters. Physical machines usually don't have long hostnames, but the issue is more common on a virtual machine. The automatically generated hostnames for Windows virtual machines hosted in Azure, in particular, tend to be long. 

**Resolution**

When you see this error, you can resolve it by configuring the DNS name of your virtual machine, and then setting the DNS name as the hostname in the setup command.

1. In the Azure portal, navigate to the overview page of your virtual machine. 
2. Select **configure** under DNS name. If your virtual machine already has a DNS name configured, you don't need to configure a new one. 

   ![Configure DNS name of virtual machine](./media/troubleshoot/configure-dns.png)

3. Provide a value for **DNS name label** and select **Save**.
4. Copy the new DNS name, which should be in the format **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. Inside the virtual machine, use the following command to set up the IoT Edge runtime with your DNS name:

   - On Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stability issues on resource constrained devices 
You may encounter stability problems on constrained devices like the Raspberry Pi, especially when used as a gateway. Symptoms include out of memory exceptions in the edge hub module, downstream devices cannot connect or the device stops sending telemetry messages after a few hours.

**Root cause**

The IoT Edge hub, which is part of the IoT Edge runtime, is optimized for performance by default and attempts to allocate large chunks of memory. This optimization is not ideal for constrained edge devices and can cause stability problems.

**Resolution**

For the IoT Edge hub, set an environment variable **OptimizeForPerformance** to **false**. There are two ways to do this:

In the UI: 

In the portal, navigate to **Device Details** > **Set Modules** > **Configure advanced Edge Runtime settings**. Create an environment variable for the Edge Hub module called *OptimizeForPerformance* that is set to *false*.

![OptimizeForPerformance set to false](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

In the deployment manifest:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Can't get the IoT Edge daemon logs on Windows
If you get an EventLogException when using `Get-WinEvent` on Windows, check your registry entries.

**Root cause**

The `Get-WinEvent` PowerShell command relies on a registry entry to be present to find logs by a specific `ProviderName`.

**Resolution**

Set a registry entry for the IoT Edge daemon. Create a **iotedge.reg** file with the following content, and import in to the Windows Registry by double-clicking it or using the `reg import iotedge.reg` command:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge module fails to send a message to the edgeHub with 404 error

A custom IoT Edge module fails to send a message to the edgeHub with a 404 `Module not found` error. The IoT Edge daemon prints the following message to the logs: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Root cause**

The IoT Edge daemon enforces process identification for all modules connecting to the edgeHub for security reasons. It verifies that all messages being sent by a module come from the main process ID of the module. If a message is being sent by a module from a different process ID than initially established, it will reject the message with a 404 error message.

**Resolution**

As of version 1.0.7, all module processes are authorized to connect. If upgrading to 1.0.7 isn't possible, complete the following steps. For more information, see the [1.0.7 release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Make sure that the same process ID is always used by the custom IoT Edge module to send messages to the edgeHub. For instance, make sure to `ENTRYPOINT` instead of `CMD` command in your Docker file, since `CMD` will lead to one process ID for the module and another process ID for the bash command running the main program whereas `ENTRYPOINT` will lead to a single process ID.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Firewall and Port configuration rules for IoT Edge deployment
Azure IoT Edge allows communication from an on-premises server to Azure cloud using supported IoT Hub protocols, see [choosing a communication protocol](../iot-hub/iot-hub-devguide-protocols.md). For enhanced security, communication channels between Azure IoT Edge and Azure IoT Hub are always configured to be Outbound. This configuration is based on the [Services Assisted Communication pattern](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), which minimizes the attack surface for a malicious entity to explore. Inbound communication is only required for specific scenarios where Azure IoT Hub needs to push messages to the Azure IoT Edge device. Cloud-to-device messages are protected using secure TLS channels and can be further secured using X.509 certificates and TPM device modules. The Azure IoT Edge Security Manager governs how this communication can be established, see [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

While IoT Edge provides enhanced configuration for securing Azure IoT Edge runtime and deployed modules, it is still dependent on the underlying machine and network configuration. Hence, it is imperative to ensure proper network and firewall rules are set up for secure edge to cloud communication. The following table can be used as a guideline when configuration firewall rules for the underlying servers where Azure IoT Edge runtime is hosted:

|Protocolo|Porta|Incoming|Outgoing|Orientações|
|--|--|--|--|--|
|MQTT|8883|BLOCKED (Default)|BLOCKED (Default)|<ul> <li>Configure Outgoing (Outbound) to be Open when using MQTT as the communication protocol.<li>1883 for MQTT is not supported by IoT Edge. <li>Incoming (Inbound) connections should be blocked.</ul>|
|AMQP|5671|BLOCKED (Default)|OPEN (Default)|<ul> <li>Default communication protocol for IoT Edge. <li> Must be configured to be Open if Azure IoT Edge is not configured for other supported protocols or AMQP is the desired communication protocol.<li>5672 for AMQP is not supported by IoT Edge.<li>Block this port when Azure IoT Edge uses a different IoT Hub supported protocol.<li>Incoming (Inbound) connections should be blocked.</ul></ul>|
|HTTPS|443|BLOCKED (Default)|OPEN (Default)|<ul> <li>Configure Outgoing (Outbound) to be Open on 443 for IoT Edge provisioning. This configuration is required when using manual scripts or Azure IoT Device Provisioning Service (DPS). <li>Incoming (Inbound) connection should be Open only for specific scenarios: <ul> <li>  If you have a transparent gateway with leaf devices that may send method requests. In this case, Port 443 does not need to be open to external networks to connect to IoTHub or provide IoTHub services through Azure IoT Edge. Thus the incoming rule could be restricted to only open Incoming (Inbound) from the internal network. <li> For Client to Device (C2D) scenarios.</ul><li>80 for HTTP is not supported by IoT Edge.<li>If non-HTTP protocols (for example, AMQP or MQTT) cannot be configured in the enterprise; the messages can be sent over WebSockets. Port 443 will be used for WebSocket communication in that case.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent module continually reports 'empty config file' and no modules start on the device

The device has trouble starting modules defined in the deployment. Only the edgeAgent is running but continually reporting 'empty config file...'.

**Root cause**

By default, IoT Edge starts modules in their own isolated container network. The device may be having trouble with DNS name resolution within this private network.

**Resolution**


**Option 1: Set DNS server in container engine settings**

Specify the DNS server for your environment in the container engine settings which will apply to all container modules started by the engine. Create a file named `daemon.json` specifying the DNS server to use. Por exemplo:

```
{
    "dns": ["1.1.1.1"]
}
```

The above example sets the DNS server to a publicly accessible DNS service. If the edge device cannot access this IP from its environment, replace it with DNS server address that is accessible.

Place `daemon.json` in the right location for your platform: 

| Plataforma | Localização |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows host with Windows containers | `C:\ProgramData\iotedge-moby\config` |

If the location already contains `daemon.json` file, add the **dns** key to it and save the file.

*Restart the container engine for the updates to take effect*

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Option 2: Set DNS server in IoT Edge deployment per module**

You can set DNS server for each module's *createOptions* in the IoT Edge deployment. Por exemplo:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Be sure to set this for the *edgeAgent* and *edgeHub* modules as well. 

## <a name="next-steps"></a>Passos seguintes
Encontrou um erro na plataforma do IoT Edge? [Submit an issue](https://github.com/Azure/iotedge/issues) so that we can continue to improve. 

If you have more questions, create a [Support request](https://portal.azure.com/#create/Microsoft.Support) for help. 

