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
ms.openlocfilehash: 2a5a5bc91e4d83975d05d63dbab4b621734a0ac5
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75494720"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Início rápido: implantar seu primeiro módulo de IoT Edge em um dispositivo Linux Virtual

Teste Azure IoT Edge neste guia de início rápido implantando código em contêineres em um dispositivo de IoT Edge virtual. IoT Edge permite que você gerencie remotamente o código em seus dispositivos para que você possa enviar mais de suas cargas de trabalho para a borda. Para este guia de início rápido, é recomendável usar uma máquina virtual do Azure para seu dispositivo IoT Edge, que permite criar rapidamente um computador de teste com todos os pré-requisitos instalados e, em seguida, excluí-lo quando você terminar.

Neste início rápido, vai aprender a:

1. Criar um Hub IoT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Instale e inicie o tempo de execução de IoT Edge em seu dispositivo virtual.
4. Implementar remotamente um módulo num dispositivo IoT Edge.

![Diagrama - arquitetura de início rápido para o dispositivo e na cloud](./media/quickstart-linux/install-edge-full.png)

Este guia de início rápido orienta você na criação de uma máquina virtual Linux configurada para ser IoT Edge dispositivo. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

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

* Um dispositivo ou máquina virtual do Linux que funcione como o seu dispositivo IoT Edge. Você deve usar o Azure IoT Edge fornecido pela Microsoft na máquina virtual [Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) , que desinstala tudo o que você precisa para executar IOT Edge em um dispositivo. Aceite os termos de uso e crie essa máquina virtual usando os seguintes comandos:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Pode levar alguns minutos para criar e iniciar a nova máquina virtual.

   Quando cria uma nova máquina virtual, anote o **publicIpAddress**, que é fornecido como parte da saída do comando de criar. Utilizará este endereço IP público para ligar à máquina virtual mais tarde no início rápido.

* Se você preferir executar o Azure IoT Edge Runtime em seu próprio dispositivo, siga as instruções em [instalar o Azure IOT Edge Runtime no Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o início rápido criando um hub IoT com CLI do Azure.

![Diagrama - criar um hub IoT na cloud](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O código seguinte cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua `{hub_name}` por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se obtiver um erro que o nome do IoT Hub não está disponível, significa que outra pessoa já tenha um concentrador com esse nome. Experimente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagrama-registrar um dispositivo com uma identidade do Hub IoT](./media/quickstart-linux/register-device.png)

Crie uma identidade de dispositivo para seu dispositivo de IoT Edge para que ele possa se comunicar com o Hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

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

3. Copie o valor da chave de `connectionString` da saída JSON e salve-o. Esse valor é a cadeia de conexão do dispositivo. Você usará essa cadeia de conexão para configurar o tempo de execução de IoT Edge na próxima seção.

   ![Recuperar cadeia de conexão da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurar seu dispositivo de IoT Edge

Inicie o tempo de execução de Azure IoT Edge em seu dispositivo IoT Edge.

![Diagrama-iniciar o tempo de execução no dispositivo](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O **daemon de segurança IOT Edge** é iniciado sempre que um dispositivo IOT Edge é inicializado e inicializar o dispositivo iniciando o agente de IOT Edge. O **agente do IoT Edge** facilita a implementação e a monitorização de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub do IoT Edge** gere as comunicações entre os módulos no dispositivo do IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do runtime, apresenta a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Definir a cadeia de ligação no dispositivo IoT Edge

Se você estiver usando o Azure IoT Edge na máquina virtual do Ubuntu, conforme descrito em pré-requisitos, o dispositivo já terá o tempo de execução do IoT Edge instalado. Você só precisa configurar seu dispositivo com a cadeia de conexão do dispositivo que você recuperou na seção anterior. Você pode fazer isso remotamente sem precisar se conectar à máquina virtual. Execute o comando a seguir, substituindo `{device_connection_string}` pela sua própria cadeia de caracteres.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se estiver executando IoT Edge em seu computador local ou em um dispositivo ARM32 ou ARM64, você precisará instalar o tempo de execução do IoT Edge e seus pré-requisitos em seu dispositivo. Siga as instruções em [instalar o Azure IOT Edge Runtime no Linux](how-to-install-iot-edge-linux.md)e, em seguida, retorne a este guia de início rápido.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

O restante dos comandos neste guia de início rápido ocorre no seu próprio dispositivo IoT Edge, para que você possa ver o que está acontecendo no dispositivo. Se você estiver usando uma máquina virtual, conecte-se a esse computador agora usando o endereço IP público que foi apresentado pelo comando de criação. Também pode encontrar o endereço IP público na página de descrição geral da sua máquina virtual no portal do Azure. Utilize o seguinte comando para ligar à máquina virtual. Substitua `{azureuser}` se você usou um nome de usuário diferente daquele sugerido nos pré-requisitos. Substitua `{publicIpAddress}` pelo endereço do computador.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Verifique se o tempo de execução foi instalado e configurado com êxito no dispositivo IoT Edge.

>[!TIP]
>Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

1. Verifique se o daemon de segurança do IoT Edge está sendo executado como um serviço do sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Consulte o daemon IoT Edge em execução como um serviço do sistema](./media/quickstart-linux/iotedged-running.png)

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

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, você usou o portal do Azure para implantar um módulo IoT Edge a ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo.

Nesse caso, o módulo enviado por push cria dados de exemplo que você pode usar para teste. O módulo sensor de temperatura simulado gera dados de ambiente que você pode usar para testar posteriormente. O sensor simulado está monitorando um computador e o ambiente em todo o computador. Por exemplo, esse sensor pode estar em uma sala de servidores, em um piso de fábrica ou em uma turbina de vento. A mensagem inclui temperatura ambiente e umidade, temperatura e pressão do computador e um carimbo de data/hora. Os tutoriais de IoT Edge usam os dados criados por este módulo como dados de teste para análise.

Abra a linha de comandos no seu dispositivo IoT Edge novamente ou utilize a ligação de SSH do CLI do Azure. Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo do IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Ver três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge comandos diferenciam maiúsculas de minúsculas ao se referir a nomes de módulo.

   ![Ver os dados a partir do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Você também pode observar que as mensagens chegam ao seu hub IoT usando a [extensão do kit de ferramentas do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, a extensão do kit de ferramentas do Azure IOT).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Caso contrário, você pode excluir os recursos do Azure que você criou para evitar cobranças.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que existem 's nada que quer manter. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais em vez disso.

Remova o grupo de **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo IoT Edge e usou a interface Azure IoT Edge Cloud para implantar o código no dispositivo. Agora tem um dispositivo de teste a gerar dados não processados sobre o seu ambiente.

A próxima etapa é configurar seu ambiente de desenvolvimento local para que você possa começar a criar IoT Edge módulos que executam sua lógica de negócios.

> [!div class="nextstepaction"]
> [Iniciar o desenvolvimento de módulos de IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md)
