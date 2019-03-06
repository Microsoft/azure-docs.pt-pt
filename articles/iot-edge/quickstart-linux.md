---
title: Início rápido criar um dispositivo Azure IoT Edge no Linux | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar um dispositivo IoT Edge e, em seguida, implementar código pré-criados remotamente a partir do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/28/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: cb8d686b32aae295e47b127f0312ba6eba9f77e3
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408552"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-device"></a>Início rápido: Implementar o seu primeiro módulo do IoT Edge num dispositivo de Linux

O Azure IoT Edge move o poder da cloud para os seus dispositivos de Internet das Coisas. Neste guia de início rápido, irá aprender a utilizar a interface da cloud para implementar código pré-construído remotamente num dispositivo IoT Edge.

Neste início rápido, vai aprender a:

1. Criar um Hub IoT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Instalar e iniciar o runtime do IoT Edge no seu dispositivo.
4. Implementar remotamente um módulo num dispositivo IoT Edge.

![Diagrama - arquitetura de início rápido para o dispositivo e na cloud](./media/quickstart-linux/install-edge-full.png)

Este início rápido explica como criar uma máquina virtual do Azure que está configurada para ser dispositivo IoT Edge. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

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

* Um dispositivo ou máquina virtual do Linux que funcione como o seu dispositivo IoT Edge. Deve usar o disponibilizadas pela Microsoft [do Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) máquina virtual, que preinstalls tudo o que precisa para executar o IoT Edge num dispositivo. Crie esta máquina virtual com o seguinte comando:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Pode demorar alguns minutos para criar e iniciar a nova máquina virtual.

   Poderá receber um `MarketplacePurchaseEligibilityFailed` erro neste momento. Se isto acontecer, tem de aceitar os termos, acedendo a [do Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) e clicando em `Get It Now`. Inicie sessão e aceitar os termos antes de tentar novamente o comando.

   Quando cria uma nova máquina virtual, anote o **publicIpAddress**, que é fornecido como parte da saída do comando de criar. Utilizará este endereço IP público para ligar à máquina virtual mais tarde no início rápido.

* Se preferir executar o tempo de execução do Azure IoT Edge no seu próprio dispositivo, siga as instruções em [instalar o runtime do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) ou [runtime de instalar o Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o guia de introdução ao criar um hub IoT com a CLI do Azure.

![Diagrama - criar um hub IoT na cloud](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O código seguinte cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua *{hub_name}* por um nome exclusivo para o hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se obtiver um erro que o nome do IoT Hub não está disponível, significa que outra pessoa já tenha um concentrador com esse nome. Experimente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagrama - Registre-se um dispositivo com uma identidade do IoT Hub](./media/quickstart-linux/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo IoT Edge, de modo a que este possa comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos do IoT Edge se comportar e podem ser geridos de forma diferente do que os dispositivos de IoT típicos, declarar esta identidade para um dispositivo IoT Edge com o `--edge-enabled` sinalizador.

1. No Azure Cloud Shell, introduza o comando seguinte para criar um dispositivo com o nome **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Se obtiver um erro sobre iothubowner chaves da política, certifique-se de que o cloud shell está a executar a versão mais recente da extensão do azure-cli-iot-ext.

2. Obtenha a cadeia de ligação para o seu dispositivo, que liga o dispositivo físico à respetiva identidade do Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor do `cs` da chave a partir da saída do JSON e guarde-o. Este valor é a cadeia de ligação do dispositivo. Usará esta cadeia de ligação para configurar o runtime do IoT Edge na próxima seção.

   ![Obter cadeia de ligação da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurar o seu dispositivo IoT Edge

Inicie o tempo de execução do Azure IoT Edge no seu dispositivo IoT Edge.

![Diagrama - início o tempo de execução no dispositivo](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge arranca e arranca o dispositivo ao iniciar o agente do IoT Edge. O **agente do IoT Edge** facilita a implementação e a monitorização de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub do IoT Edge** gere as comunicações entre os módulos no dispositivo do IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do runtime, apresenta a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Definir a cadeia de ligação no dispositivo IoT Edge

Se estiver a utilizar o Azure IoT Edge na máquina virtual do Ubuntu, tal como descrito nos pré-requisitos, em seguida, o dispositivo já tem o runtime do IoT Edge instalado. Terá de configurar o seu dispositivo com a cadeia de ligação do dispositivo que obteve na secção anterior. Pode fazer isso remotamente sem ter de se ligar à máquina virtual. Execute o seguinte comando, substituindo **{device_connection_string}** com sua própria cadeia de caracteres.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

Se estiver a executar o IoT Edge no seu computador local ou um dispositivo de ARM32, terá de instalar o runtime do IoT Edge e seus pré-requisitos no seu dispositivo. Siga as instruções em [instalar o runtime do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) ou [runtime de instalar o Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md), em seguida, volte neste início rápido.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

O restante dos comandos neste guia de introdução ocorrem no seu dispositivo IoT Edge em si, para que pode ver o que está acontecendo no dispositivo. Se estiver a utilizar uma máquina virtual, ligue-se para essa máquina agora usando o endereço IP público que estava a saída do comando de criação. Também pode encontrar o endereço IP público na página de descrição geral da sua máquina virtual no portal do Azure. Utilize o seguinte comando para ligar à máquina virtual. Substitua **{azureuser}** se utilizou um nome de utilizador diferente daquela sugerido nos pré-requisitos. Substitua **{publicIpAddress}** com o endereço da sua máquina.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Certifique-se de que o tempo de execução foi corretamente instalado e configurado no seu dispositivo IoT Edge.

>[!TIP]
>Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, utilize o **sudo** à frente dos comandos.

1. Verifique se o Daemon de Segurança do Edge está em execução como serviço de sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Ver o Daemon do Edge em execução como serviço de sistema](./media/quickstart-linux/iotedged-running.png)

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
![Diagrama - implementar módulo a partir da cloud para dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, utilizou o portal do Azure para implementar um módulo do IoT Edge para executar no dispositivo, sem ter de efetuar alterações ao dispositivo.

Neste caso, o módulo que enviou cria dados de exemplo que pode utilizar para fins de teste. O módulo do sensor de temperatura simulada gera dados de ambiente que pode utilizar para fins de teste mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente em torno da máquina. Por exemplo, este sensor pode ser numa sala de servidor, num chão de fábrica ou numa turbina eólica. A mensagem inclui temperatura ambiente e umidade, temperatura de máquina e pressão e um carimbo. Os tutoriais do IoT Edge utilizam os dados criados por este módulo como dados para análise de teste.

Abra a linha de comandos no seu dispositivo IoT Edge novamente ou utilize a ligação de SSH do CLI do Azure. Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo do IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Ver três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Ver as mensagens sendo enviadas pelo módulo do sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Comandos do IoT Edge diferenciam maiúsculas de minúsculas quando nos Referimos a nomes de módulos.

   ![Ver os dados a partir do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Também pode ver as mensagens de chegar ao seu hub IoT utilizando o [extensão de Kit de ferramentas do Azure IoT Hub para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Caso contrário, pode eliminar os recursos do Azure que criou e remover o runtime do IoT Edge do seu dispositivo.

### <a name="delete-azure-resources"></a>Eliminar recursos do Azure

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que existem 's nada que quer manter. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais em vez disso.

Remova o grupo de **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources
   ```

### <a name="remove-the-iot-edge-runtime"></a>Remover o runtime do IoT Edge

Se quiser remover as instalações do seu dispositivo, utilize os comandos seguintes.  

Remova o runtime do IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Remova o runtime do contentor.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Passos Seguintes

Este início rápido é o pré-requisito para todos os tutoriais do IoT Edge. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre como o Azure IoT Edge o pode ajudar a transformar estes dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Filtrar os dados de sensores com uma Função do Azure](tutorial-deploy-function.md)
