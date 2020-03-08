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
ms.openlocfilehash: 54efe7b5c392ad2b4cc3a0de414e04951b268508
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674234"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Quickstart: Implemente o seu primeiro módulo IoT Edge para um dispositivo Virtual Windows

Experimente o Azure IoT Edge implantando código contentorizado para um dispositivo virtual IoT Edge. O IoT Edge permite-lhe gerir remotamente o código nos seus dispositivos para que possa enviar mais cargas de trabalho para a borda. Para este arranque rápido, recomendamos a utilização de uma máquina virtual Azure para o seu dispositivo IoT Edge. A utilização de uma máquina virtual permite-lhe criar rapidamente uma máquina de teste, instalar os pré-requisitos e, em seguida, eliminá-la quando terminar.

Neste início rápido, vai aprender a:

1. Criar um centro de ioT.
2. Registar um dispositivo IoT Edge no seu hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no seu dispositivo virtual.
4. Implementar remotamente um módulo num dispositivo IoT Edge e enviar telemetria para o Hub IoT.

![Diagrama - arquitetura de início rápido para o dispositivo e na cloud](./media/quickstart/install-edge-full.png)

Este quickstart leva-o através da criação de uma máquina virtual do Windows e configurando-a para ser um dispositivo IoT Edge. Em seguida, pode implementar um módulo a partir do portal do Azure no seu dispositivo. O módulo que vai implementar neste início rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge tiram partido do seu trabalho aqui realizado, ao implementar módulos que analisam os dados simulados de informações empresariais.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa o Azure CLI para completar muitos dos passos neste arranque rápido. O Azure IoT tem uma extensão para permitir uma funcionalidade adicional.

Adicione a extensão do IoT do Azure à instância da shell da cloud.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recursos da cloud:

* Um grupo de recursos para gerir todos os recursos que utilizar neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge

* Uma máquina virtual do Windows para funcionar como o seu dispositivo IoT Edge. Pode criar esta máquina virtual utilizando o seguinte comando, substituindo `{password}` por uma senha segura:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Pode levar alguns minutos para criar e iniciar a nova máquina virtual. Em seguida, pode descarregar um ficheiro RDP para utilização quando estiver ligado à sua máquina virtual:

  1. Navegue para a sua nova máquina virtual Windows no portal Azure.
  1. Selecione **Ligar**.
  1. No **separador RDP,** selecione **Download RDP File**.

  Abra este ficheiro com a Ligação remota de ambiente de trabalho para ligar à sua máquina virtual Windows utilizando o nome do administrador e a palavra-passe que especificou com o comando `az vm create`.

> [!NOTE]
> Este quickstart utiliza uma máquina virtual de ambiente de trabalho windows para a simplicidade. Para obter informações sobre quais os sistemas operativos Windows que estão geralmente disponíveis para cenários de produção, consulte [os sistemas suportados pelo Azure IoT Edge](support.md).
>
> Se estiver pronto para configurar o seu próprio dispositivo Windows para IoT Edge, incluindo dispositivos em execução do IoT Core, siga os passos em Instalação do tempo de [funcionamento do Edge Azure IoT no Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o quickstart criando um hub IoT com O CLI Azure.

![Diagrama - criar um hub IoT na cloud](./media/quickstart/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se tiver utilizado o Hub IoT anteriormente e já tiver um hub gratuito criado, pode utilizar esse hub IoT. Cada subscrição só pode ter um Hub IoT gratuito.

O seguinte código cria um hub **de F1** gratuito no grupo de recursos `IoTEdgeResources`. Substitua `{hub_name}` por um nome único para o seu hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se obtiver um erro que o nome do IoT Hub não está disponível, significa que outra pessoa já tenha um concentrador com esse nome. Experimente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.
Diagrama ![- Registe um dispositivo com uma identidade IoT Hub](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consiga comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos IoT Edge se comportam e podem ser geridos de forma diferente dos dispositivos IoT típicos, declaram esta identidade para um dispositivo IoT Edge com a bandeira `--edge-enabled`.

1. No Azure Cloud Shell, introduza o comando seguinte para criar um dispositivo com o nome **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Se tiver um erro sobre as chaves da política do iothubowner, certifique-se de que a sua casca de nuvem está a executar a versão mais recente da extensão azure-iot.

2. Obtenha a cadeia de ligação para o seu dispositivo, que liga o dispositivo físico à respetiva identidade do Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave `connectionString` da saída JSON e guarde-a. Este valor é a cadeia de ligação do dispositivo. Utilizará esta cadeia de ligação para configurar o tempo de execução do IoT Edge na secção seguinte.

   ![Recuperar a cadeia de ligação da saída CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o runtime do IoT Edge

Instale o runtime do Azure IoT Edge no dispositivo IoT Edge e configure-o com uma cadeia de ligação de dispositivo.
Diagrama ![- Inicie o tempo de execução no dispositivo](./media/quickstart/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O **daemon de segurança IoT Edge** começa cada vez que um dispositivo IoT Edge botas e botas o dispositivo inicia o agente IoT Edge. O **agente IoT Edge** gere a implementação e monitorização de módulos no dispositivo IoT Edge, incluindo o hub IoT Edge. O **hub IoT Edge** lida com comunicações entre módulos no dispositivo IoT Edge e entre o dispositivo e o IoT Hub.

O script de instalação também inclui um motor de contentor chamado Moby que gere as imagens do recipiente no seu dispositivo IoT Edge.

Durante a instalação do runtime, é-lhe pedida a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="connect-to-your-iot-edge-device"></a>Ligar ao seu dispositivo IoT Edge

Os passos nesta secção são todos realizados no seu dispositivo IoT Edge, pelo que pretende ligar-se a essa máquina virtual agora através de um ambiente de trabalho remoto.

### <a name="install-and-configure-the-iot-edge-service"></a>Instale e configure o serviço IoT Edge

Utilize o PowerShell para transferir e instalar o runtime IoT Edge. Utilize a cadeia de ligação do dispositivo que obteve no hub IoT para configurar o dispositivo.

1. Se ainda não o fez, siga os passos no [Registo de um novo dispositivo Azure IoT Edge](how-to-register-device.md) para registar o seu dispositivo e recuperar a cadeia de ligação do dispositivo.

2. Na máquina virtual, execute a PowerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 da PowerShell para instalar o IoT Edge, não o PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a usar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores, descarrega o tempo de execução do Moby e, em seguida, descarrega o tempo de execução do IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. A sua máquina pode reiniciar automaticamente. Se for solicitado pelo comando Deploy-IoTEdge para reiniciar, faça-o agora.

5. Executar powerShell novamente como administrador.

6. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao fornecimento manual com recipientes Windows.

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

3. Veja todos os módulos em execução no seu dispositivo IoT Edge. Uma vez que o serviço foi iniciado pela primeira vez, deverá ver apenas o módulo **edgeAgent** em execução. O módulo EdgeAgent funciona por defeito e ajuda a instalar e iniciar quaisquer módulos adicionais que implemente para o seu dispositivo.

    ```powershell
    iotedge list
    ```

   ![Ver um módulo no seu dispositivo](./media/quickstart/iotedge-list-1.png)

Pode levar alguns minutos para a instalação estar concluída e o módulo do agente IoT Edge a arrancar.

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implantados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gerencie o seu dispositivo Azure IoT Edge a partir da nuvem para implementar um módulo que envia dados de telemetria para o IoT Hub.
Diagrama ![- desloque o módulo da nuvem para o dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste arranque rápido, registou um dispositivo IoT Edge e instalou o tempo de funcionação do IoT Edge nele. Em seguida, utilizou o portal Azure para implementar um módulo IoT Edge para funcionar no dispositivo sem ter que fazer alterações no próprio dispositivo.

Neste caso, o módulo que empurrou cria dados de amostra que pode utilizar para testes. O módulo de sensor de temperatura simulado gera dados ambientais que pode utilizar para testar mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente em torno da máquina. Por exemplo, este sensor pode estar numa sala de servidores, no chão de uma fábrica, ou numa turbina eólica. A mensagem inclui temperatura ambiente e humidade, temperatura e pressão da máquina, e uma marca de tempo. Os tutoriais IoT Edge utilizam os dados criados por este módulo como dados de teste para análise.

Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo IoT Edge.

```powershell
iotedge list
```

   ![Ver três módulos no seu dispositivo](./media/quickstart/iotedge-list-2.png)

Veja as mensagens enviadas do módulo do sensor de temperatura para a nuvem.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Os comandos IoT Edge são sensíveis a casos quando se referem aos nomes dos módulos.

   ![Ver os dados a partir do seu módulo](./media/quickstart/iotedge-logs.png)

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
> [Comece a desenvolver módulos IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md)
