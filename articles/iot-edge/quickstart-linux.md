---
title: Quickstart create an Azure IoT Edge device on Linux | Microsoft Docs
description: In this quickstart, learn how to create an IoT Edge device and then deploy prebuilt code remotely from the Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/09/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6163a7f9b7c3aa1b37b263433c4dea7f0c3bcf5e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457674"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quickstart: Deploy your first IoT Edge module to a virtual Linux device

Test out Azure IoT Edge in this quickstart by deploying containerized code to a virtual IoT Edge device. IoT Edge allows you to remotely manage code on your devices so that you can send more of your workloads to the edge. For this quickstart, we recommend using an Azure virtual machine for your IoT Edge device, which allows you to quickly create a test machine with all prerequisites installed and then delete it when you're finished. 

Neste início rápido, vai aprender a:

1. Criar um Hub IoT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Install and start the IoT Edge runtime on your virtual device.
4. Implementar remotamente um módulo num dispositivo IoT Edge.

![Diagram - Quickstart architecture for device and cloud](./media/quickstart-linux/install-edge-full.png)

This quickstart walks you through creating a Linux virtual machine that's configured to be IoT Edge device. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vai utilizar a CLI do Azure para concluir muitos dos passos neste início rápido e o IoT do Azure tem uma extensão para ativar funcionalidades adicionais.

Adicione a extensão do IoT do Azure à instância da shell da cloud.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Pré-requisitos

Recursos da cloud:

* Um grupo de recursos para gerir todos os recursos que utilizar neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge

* Um dispositivo ou máquina virtual do Linux que funcione como o seu dispositivo IoT Edge. You should use the Microsoft-provided [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) virtual machine, which preinstalls everything you need to run IoT Edge on a device. Accept the terms of use and create this virtual machine using the following commands:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   It may take a few minutes to create and start the new virtual machine.

   When you create a new virtual machine, make a note of the **publicIpAddress**, which is provided as part of the create command output. You will use this public IP address to connect to the virtual machine later in the quickstart.

* If you prefer to run the Azure IoT Edge runtime on your own device, follow instructions at [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Start the quickstart by creating an IoT hub with Azure CLI.

![Diagram - Create an IoT hub in the cloud](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O código seguinte cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua *{hub_name}* por um nome exclusivo para o hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. If you get an error that the IoT Hub name isn't available, it means that someone else already has a hub with that name. Try a new name.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagram - Register a device with an IoT Hub identity](./media/quickstart-linux/register-device.png)

Create a device identity for your IoT Edge device so that it can communicate with your IoT hub. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Since IoT Edge devices behave and can be managed differently than typical IoT devices, declare this identity to be for an IoT Edge device with the `--edge-enabled` flag.

1. No Azure Cloud Shell, introduza o comando seguinte para criar um dispositivo com o nome **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   If you get an error about iothubowner policy keys, make sure that your cloud shell is running the latest version of the azure-cli-iot-ext extension.

2. Obtenha a cadeia de ligação para o seu dispositivo, que liga o dispositivo físico à respetiva identidade do Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copy the value of the `connectionString` key from the JSON output and save it. This value is the device connection string. You'll use this connection string to configure the IoT Edge runtime in the next section.

   ![Retrieve connection string from CLI output](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configure your IoT Edge device

Start the Azure IoT Edge runtime on your IoT Edge device.

![Diagram - Start the runtime on device](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. The **IoT Edge security daemon** starts each time an IoT Edge device boots and bootstraps the device by starting the IoT Edge agent. O **agente do IoT Edge** facilita a implementação e a monitorização de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub do IoT Edge** gere as comunicações entre os módulos no dispositivo do IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do runtime, apresenta a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Set the connection string on the IoT Edge device

If you're using the Azure IoT Edge on Ubuntu virtual machine as described in the prerequisites, then your device already has the IoT Edge runtime installed. You just need to configure your device with the device connection string that you retrieved in the previous section. You can do this remotely without having to connect to the virtual machine. Run the following command, replacing **{device_connection_string}** with your own string.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

If you're running IoT Edge on your local machine or an ARM32 or ARM64 device, you need to install the IoT Edge runtime and its prerequisites on your device. Follow the instructions in [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md), then return to this quickstart.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

The rest of the commands in this quickstart take place on your IoT Edge device itself, so that you can see what's happening on the device. If you're using a virtual machine, connect to that machine now using the public IP address that was output by the creation command. You can also find the public IP address on your virtual machine's overview page in the Azure portal. Use the following command to connect to your virtual machine. Replace **{azureuser}** if you used a different username than the one suggested in the prerequisites. Replace **{publicIpAddress}** with your machine's address.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Verify that the runtime was successfully installed and configured on your IoT Edge device.

>[!TIP]
>Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, utilize o **sudo** à frente dos comandos.

1. Check to see that the IoT Edge security daemon is running as a system service.

   ```bash
   sudo systemctl status iotedge
   ```

   ![See the IoT Edge daemon running as a system service](./media/quickstart-linux/iotedged-running.png)

2. Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

   ```bash
   journalctl -u iotedge
   ```

3. Veja os módulos em execução no seu dispositivo.

   ```bash
   sudo iotedge list
   ```

   ![Ver um módulo no seu dispositivo](./media/quickstart-linux/iotedge-list-1.png)

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implantados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gira o seu dispositivo Azure IoT Edge a partir da cloud para implementar um módulo que irá enviar dados telemétricos para o Hub IoT.
![Diagram - deploy module from cloud to device](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Then, you used the Azure portal to deploy an IoT Edge module to run on the device without having to make changes to the device itself.

In this case, the module that you pushed creates sample data that you can use for testing. The simulated temperature sensor module generates environment data that you can use for testing later. The simulated sensor is monitoring both a machine and the environment around the machine. For example, this sensor might be in a server room, on a factory floor, or on a wind turbine. The message includes ambient temperature and humidity, machine temperature and pressure, and a timestamp. The IoT Edge tutorials use the data created by this module as test data for analytics.

Open the command prompt on your IoT Edge device again, or use the SSH connection from Azure CLI. Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo do IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Ver três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

View the messages being sent from the temperature sensor module:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge commands are case-sensitive when referring to module names.

   ![Ver os dados a partir do seu módulo](./media/quickstart-linux/iotedge-logs.png)

You can also watch the messages arrive at your IoT hub by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Otherwise, you can delete the Azure resources that you created to avoid charges.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Double check the contents of the resource group to make sure that there's nothing you want to keep. If you don't want to delete the whole group, you can delete individual resources instead.

Remova o grupo de **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Passos seguintes


In this quickstart, you created an IoT Edge device and used the Azure IoT Edge cloud interface to deploy code onto the device. Agora tem um dispositivo de teste a gerar dados não processados sobre o seu ambiente.

The next step is to set up your local development environment so that you can start creating IoT Edge modules that run your business logic. 

> [!div class="nextstepaction"]
> [Start developing IoT Edge modules for Linux devices](tutorial-develop-for-linux.md)
