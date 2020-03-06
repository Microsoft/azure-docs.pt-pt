---
title: Início rápido criar um dispositivo Azure IoT Edge no Linux | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar um dispositivo IoT Edge e, em seguida, implementar código pré-criados remotamente a partir do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a076f9e6ae67b9a4dc27fcd058945716dfebe75
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384372"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quickstart: Implemente o seu primeiro módulo IoT Edge para um dispositivo linux virtual

Teste o Azure IoT Edge neste arranque rápido, implantando código contentorizado para um dispositivo virtual IoT Edge. O IoT Edge permite-lhe gerir remotamente o código nos seus dispositivos para que possa enviar mais cargas de trabalho para a borda. Para este arranque rápido, recomendamos a utilização de uma máquina virtual Azure para o seu dispositivo IoT Edge, que lhe permite criar rapidamente uma máquina de teste com todos os pré-requisitos instalados e depois apagá-la quando terminar.

Neste início rápido, vai aprender a:

1. Criar um Hub IoT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no seu dispositivo virtual.
4. Implementar remotamente um módulo num dispositivo IoT Edge.

![Diagrama - arquitetura de início rápido para o dispositivo e na cloud](./media/quickstart-linux/install-edge-full.png)

Este quickstart leva-o através da criação de uma máquina virtual Linux que está configurada para ser um dispositivo IoT Edge. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

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

* Um dispositivo ou máquina virtual do Linux que funcione como o seu dispositivo IoT Edge. Deve utilizar o [Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fornecido pela Microsoft na máquina virtual Ubuntu, que pré-instala tudo o que precisa para executar o IoT Edge num dispositivo. Aceite os termos de utilização e crie esta máquina virtual utilizando os seguintes comandos:

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Pode levar alguns minutos para criar e iniciar a nova máquina virtual.

   Quando criar uma nova máquina virtual, tome nota do **publicIpAddress**, que é fornecido como parte da saída de comando de criação. Utilizará este endereço IP público para ligar à máquina virtual mais tarde no início rápido.

* Se preferir executar o tempo de execução do Azure IoT Edge no seu próprio dispositivo, siga as instruções no Tempo de [execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o quickstart criando um hub IoT com O CLI Azure.

![Diagrama - criar um hub IoT na cloud](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O código seguinte cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua `{hub_name}` por um nome único para o seu hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se obtiver um erro que o nome do IoT Hub não está disponível, significa que outra pessoa já tenha um concentrador com esse nome. Experimente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagrama - Registe um dispositivo com uma identidade IoT Hub](./media/quickstart-linux/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo IoT Edge para que possa comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos IoT Edge se comportam e podem ser geridos de forma diferente dos dispositivos IoT típicos, declaram esta identidade para um dispositivo IoT Edge com a bandeira `--edge-enabled`.

1. No Azure Cloud Shell, introduza o comando seguinte para criar um dispositivo com o nome **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Se obtiver um erro sobre iothubowner chaves da política, certifique-se de que o cloud shell está a executar a versão mais recente da extensão do azure-cli-iot-ext.

2. Obtenha a cadeia de ligação para o seu dispositivo, que liga o dispositivo físico à respetiva identidade do Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave `connectionString` da saída JSON e guarde-a. Este valor é a cadeia de ligação do dispositivo. Utilizará esta cadeia de ligação para configurar o tempo de execução do IoT Edge na secção seguinte.

   ![Recuperar a cadeia de ligação da saída CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configure o seu dispositivo IoT Edge

Inicie o tempo de execução do Azure IoT Edge no seu dispositivo IoT Edge.

![Diagrama - Inicie o tempo de execução no dispositivo](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O **daemon de segurança IoT Edge** começa cada vez que um dispositivo IoT Edge botas e botas o dispositivo inicia o agente IoT Edge. O **agente do IoT Edge** facilita a implementação e a monitorização de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub do IoT Edge** gere as comunicações entre os módulos no dispositivo do IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do runtime, apresenta a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Definir a cadeia de ligação no dispositivo IoT Edge

Se estiver a utilizar o Edge Azure IoT na máquina virtual Ubuntu, conforme descrito nos pré-requisitos, então o seu dispositivo já tem o tempo de execução do IoT Edge instalado. Basta configurar o seu dispositivo com a cadeia de ligação do dispositivo que recuperou na secção anterior. Pode fazê-lo remotamente sem ter de se ligar à máquina virtual. Executa o seguinte comando, substituindo `{device_connection_string}` pela sua própria corda.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se estiver a executar ioT Edge na sua máquina local ou num dispositivo ARM32 ou ARM64, tem de instalar o tempo de funcionamento do IoT Edge e os seus pré-requisitos no seu dispositivo. Siga as instruções em Instalar o tempo de [execução do Edge Azure IoT no Linux](how-to-install-iot-edge-linux.md)e, em seguida, volte a este arranque rápido.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

O resto dos comandos neste arranque rápido ocorrem no próprio dispositivo IoT Edge, para que possa ver o que está a acontecer no dispositivo. Se estiver a utilizar uma máquina virtual, ligue-se a essa máquina utilizando agora o endereço IP público que foi saída pelo comando de criação. Também pode encontrar o endereço IP público na página de descrição geral da sua máquina virtual no portal do Azure. Utilize o seguinte comando para ligar à máquina virtual. Substitua `{azureuser}` se tiver usado um nome de utilizador diferente do sugerido nos pré-requisitos. Substitua `{publicIpAddress}` com o endereço da máquina.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Verifique se o tempo de execução foi instalado e configurado com sucesso no seu dispositivo IoT Edge.

>[!TIP]
>Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, use `sudo` em frente aos comandos.

1. Verifique se o daemon de segurança IoT Edge está funcionando como um serviço de sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o daemon IoT Edge correndo como um serviço de sistema](./media/quickstart-linux/iotedged-running.png)

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
Diagrama ![- desloque o módulo da nuvem para o dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, utilizou o portal Azure para implementar um módulo IoT Edge para funcionar no dispositivo sem ter que fazer alterações no próprio dispositivo.

Neste caso, o módulo que empurrou cria dados de amostra que pode utilizar para testes. O módulo de sensor de temperatura simulado gera dados ambientais que pode utilizar para testar mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente em torno da máquina. Por exemplo, este sensor pode estar numa sala de servidores, no chão de uma fábrica, ou numa turbina eólica. A mensagem inclui temperatura ambiente e humidade, temperatura e pressão da máquina, e uma marca de tempo. Os tutoriais IoT Edge utilizam os dados criados por este módulo como dados de teste para análise.

Abra a linha de comandos no seu dispositivo IoT Edge novamente ou utilize a ligação de SSH do CLI do Azure. Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo do IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Ver três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Veja as mensagens enviadas do módulo sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Os comandos IoT Edge são sensíveis a casos quando se referem aos nomes dos módulos.

   ![Ver os dados a partir do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Do Hub Azure IoT para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Caso contrário, pode eliminar os recursos Azure que criou para evitar encargos.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que existem 's nada que quer manter. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais em vez disso.

Remova o grupo de **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo IoT Edge e utilizou a interface cloud Azure IoT Edge para implementar código no dispositivo. Agora tem um dispositivo de teste a gerar dados não processados sobre o seu ambiente.

O próximo passo é configurar o seu ambiente de desenvolvimento local para que possa começar a criar módulos IoT Edge que gerem a sua lógica de negócio.

> [!div class="nextstepaction"]
> [Comece a desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md)
