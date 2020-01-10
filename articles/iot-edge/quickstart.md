---
title: Início rápido criar um dispositivo Azure IoT Edge no Windows | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar um dispositivo IoT Edge e, em seguida, implementar código pré-criados remotamente a partir do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ab3805e39112d4d37635571d8aa43030a1896951
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552445"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Início rápido: implantar seu primeiro módulo de IoT Edge em um dispositivo virtual do Windows

Teste Azure IoT Edge neste guia de início rápido implantando código em contêineres em um dispositivo de IoT Edge virtual. IoT Edge permite que você gerencie remotamente o código em seus dispositivos para que você possa enviar mais de suas cargas de trabalho para a borda. Para este guia de início rápido, é recomendável usar uma máquina virtual do Azure para seu dispositivo IoT Edge, que permite criar rapidamente um computador de teste, instalar os pré-requisitos e, em seguida, excluí-lo quando terminar.

Neste início rápido, vai aprender a:

1. Crie um hub IoT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Instale e inicie o tempo de execução de IoT Edge em seu dispositivo virtual.
4. Implementar remotamente um módulo num dispositivo IoT Edge e enviar telemetria para o Hub IoT.

![Diagrama - arquitetura de início rápido para o dispositivo e na cloud](./media/quickstart/install-edge-full.png)

Este guia de início rápido explica como criar uma máquina virtual do Windows e configurá-la para ser um dispositivo IoT Edge. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa o CLI do Azure para concluir muitas das etapas neste guia de início rápido. O Azure IoT tem uma extensão para habilitar a funcionalidade adicional.

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

* Uma máquina virtual do Windows para atuar como seu IoT Edge dispositivo. Você pode criar essa máquina virtual usando o comando a seguir, substituindo `{password}` por uma senha segura:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Criar e iniciar a nova máquina virtual poderá demorar alguns minutos. Em seguida, você pode baixar um arquivo RDP para usar ao conectar-se à sua máquina virtual:

  1. Navegue até sua nova máquina virtual do Windows no portal do Azure.
  1. Selecione **Ligar**.
  1. Na guia **RDP** , selecione **baixar arquivo RDP**.

  Abra esse arquivo com Conexão de Área de Trabalho Remota para se conectar à sua máquina virtual do Windows usando o nome do administrador e a senha que você especificou com o comando `az vm create`.

> [!NOTE]
> Este guia de início rápido usa uma máquina virtual da área de trabalho do Windows para simplificar. Para obter informações sobre quais sistemas operacionais Windows estão geralmente disponíveis para cenários de produção, consulte [Azure IOT Edge sistemas com suporte](support.md).
>
> Se você estiver pronto para configurar seu próprio dispositivo Windows para IoT Edge, incluindo dispositivos que executam o IoT Core, siga as etapas em [instalar o Azure IOT Edge Runtime no Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o início rápido criando um hub IoT com CLI do Azure.

![Diagrama - criar um hub IoT na cloud](./media/quickstart/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O código a seguir cria um hub **F1** gratuito no grupo de recursos `IoTEdgeResources`. Substitua `{hub_name}` por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se obtiver um erro que o nome do IoT Hub não está disponível, significa que outra pessoa já tenha um concentrador com esse nome. Experimente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.
![Diagrama - Registre-se um dispositivo com uma identidade do IoT Hub](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consiga comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos do IoT Edge se comportar e podem ser geridos de forma diferente do que os dispositivos de IoT típicos, declarar esta identidade para um dispositivo IoT Edge com o `--edge-enabled` sinalizador.

1. No Azure Cloud Shell, introduza o comando seguinte para criar um dispositivo com o nome **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Se obtiver um erro sobre iothubowner chaves da política, certifique-se de que o cloud shell está a executar a versão mais recente da extensão do azure-cli-iot-ext.

2. Obtenha a cadeia de ligação para o seu dispositivo, que liga o dispositivo físico à respetiva identidade do Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave de `connectionString` da saída JSON e salve-o. Este valor é a cadeia de ligação do dispositivo. Irá utilizar esta cadeia de ligação para configurar o runtime do IoT Edge na secção seguinte.

   ![Recuperar cadeia de conexão da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o runtime do IoT Edge

Instale o runtime do Azure IoT Edge no dispositivo IoT Edge e configure-o com uma cadeia de ligação de dispositivo.
![Diagrama - início o tempo de execução no dispositivo](./media/quickstart/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O **daemon de segurança IOT Edge** é iniciado sempre que um dispositivo IOT Edge é inicializado e inicializar o dispositivo iniciando o agente de IOT Edge. O **agente de IOT Edge** gerencia a implantação e o monitoramento de módulos no dispositivo IOT Edge, incluindo o hub de IOT Edge. O **Hub de IOT Edge** lida com as comunicações entre os módulos no dispositivo IOT Edge e entre o dispositivo e o Hub IOT.

O script de instalação também inclui um mecanismo de contêiner chamado Moby que gerencia as imagens de contêiner em seu dispositivo IoT Edge.

Durante a instalação do runtime, é-lhe pedida a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="connect-to-your-iot-edge-device"></a>Ligar ao seu dispositivo IoT Edge

Todas as etapas nesta seção ocorrem em seu IoT Edge dispositivo, portanto, você deseja se conectar a essa máquina virtual agora por meio da área de trabalho remota.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalar e configurar o serviço de IoT Edge

Utilize o PowerShell para transferir e instalar o runtime IoT Edge. Utilize a cadeia de ligação do dispositivo que obteve no hub IoT para configurar o dispositivo.

1. Se você ainda não fez isso, siga as etapas em [registrar um novo Azure IOT Edge dispositivo](how-to-register-device.md) para registrar seu dispositivo e recuperar a cadeia de conexão do dispositivo.

2. Na máquina virtual, execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão AMD64 do PowerShell para instalar IoT Edge, não o PowerShell (x86). Se você não tiver certeza de qual tipo de sessão está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres, baixa o tempo de execução do Moby e, em seguida, baixa o tempo de execução de IOT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Seu computador pode ser reiniciado automaticamente. Se você for solicitado pelo comando Deploy-IoTEdge para reinicializar, faça isso agora.

5. Execute o PowerShell como administrador novamente.

6. O comando **Initialize-IoTEdge** configura o tempo de execução de IOT Edge em seu computador. O comando usa como padrão o provisionamento manual com contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Quando lhe for pedido **DeviceConnectionString**, indique a cadeia que copiou na secção anterior. Não inclua as aspas à volta da cadeia de ligação.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

Verifique se o runtime foi instalado e configurado corretamente.

1. Verifique o estado do serviço IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Veja todos os módulos em execução no seu dispositivo IoT Edge. Uma vez que o serviço foi iniciado pela primeira vez, deverá ver apenas o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e iniciar quaisquer módulos adicionais que você implantar em seu dispositivo.

    ```powershell
    iotedge list
    ```

   ![Ver um módulo no seu dispositivo](./media/quickstart/iotedge-list-1.png)

Pode levar alguns minutos para que a instalação seja concluída e o módulo IoT Edge Agent seja iniciado.

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implantados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gerencie seu dispositivo de Azure IoT Edge da nuvem para implantar um módulo que envia dados de telemetria para o Hub IoT.
![Diagrama - implementar módulo a partir da cloud para dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste guia de início rápido, você registrou um dispositivo IoT Edge e instalou o IoT Edge Runtime nele. Em seguida, você usou o portal do Azure para implantar um módulo IoT Edge a ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo.

Nesse caso, o módulo enviado por push cria dados de exemplo que você pode usar para teste. O módulo sensor de temperatura simulado gera dados de ambiente que você pode usar para testar posteriormente. O sensor simulado está monitorando um computador e o ambiente em todo o computador. Por exemplo, este sensor poderá estar num espaço de servidor, numa fábrica ou numa turbina eólica. A mensagem inclui temperatura ambiente e humidade, temperatura e pressão do computador e um carimbo de data/hora. Os tutoriais de IoT Edge usam os dados criados por este módulo como dados de teste para análise.

Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo IoT Edge.

```powershell
iotedge list
```

   ![Ver três módulos no seu dispositivo](./media/quickstart/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo sensor de temperatura para a nuvem.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge comandos diferenciam maiúsculas de minúsculas ao se referir a nomes de módulo.

   ![Ver os dados a partir do seu módulo](./media/quickstart/iotedge-logs.png)

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
> [Iniciar o desenvolvimento de módulos de IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md)
