---
title: Quickstart cria um dispositivo Azure IoT Edge no Windows Microsoft Docs
description: Neste arranque rápido, aprenda a criar um dispositivo IoT Edge e, em seguida, implemente código pré-construído remotamente a partir do portal Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 791358fe6a9524e3275ed75ce34bdc86123f0ee0
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572359"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Quickstart: Implemente o seu primeiro módulo IoT Edge num dispositivo Virtual Windows

Experimente o Azure IoT Edge neste arranque rápido, implantando código contentorizado num dispositivo virtual do Windows IoT Edge. O IoT Edge permite-lhe gerir remotamente código nos seus dispositivos para que possa enviar mais cargas de trabalho para a borda. Para este arranque rápido, recomendamos a utilização de uma máquina virtual Azure para o seu dispositivo IoT Edge. A utilização de uma máquina virtual permite-lhe criar rapidamente uma máquina de teste, instalar os pré-requisitos e, em seguida, eliminá-la quando terminar.

Neste início rápido, vai aprender a:

* Criar um hub IoT.
* Registar um dispositivo IoT Edge no seu hub IoT.
* Instale e inicie o tempo de funcionaamento do IoT Edge no seu dispositivo virtual.
* Implementar remotamente um módulo num dispositivo IoT Edge e enviar telemetria para o Hub IoT.

![Diagrama - Arquitetura quickstart para dispositivo e nuvem](./media/quickstart/install-edge-full.png)

Este arranque rápido acompanha-o através da criação de uma máquina virtual Windows e configura-a para ser um dispositivo IoT Edge. Em seguida, coloque um módulo do portal Azure para o seu dispositivo. O módulo utilizado neste arranque rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge baseiam-se no trabalho que faz aqui, implantando módulos adicionais que analisam os dados simulados para insights de negócios.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Prepare o seu ambiente para o Azure CLI.

- Utilize [a casca de nuvem Azure](/azure/cloud-shell/quickstart-powershell) utilizando o ambiente PowerShell.

   [![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)   
- Se preferir, [instale](/cli/azure/install-azure-cli) o CLI do Azure para executar comandos de referência de CLI.
   - Se estiver a utilizar uma instalação local, inicie sessão com o CLI do Azure ao utilizar o comando [az login](/cli/azure/reference-index#az-login).  Para concluir o processo de autenticação, siga os passos apresentados no seu terminal.  Veja [Iniciar sessão com o CLI do Azure](/cli/azure/authenticate-azure-cli) para obter opções de início de sessão adicionais.
  - Quando lhe for pedido, instale as extensões do CLI do Azure durante a primeira utilização.  Para obter mais informações sobre as extensões, veja [Utilizar extensões com o CLI do Azure](/cli/azure/azure-cli-extensions-overview).
  - Execute o comando [az version](/cli/azure/reference-index?#az_version) para localizar a versão e as bibliotecas dependentes instaladas. Para atualizar para a versão mais recente, execute o comando [az upgrade](/cli/azure/reference-index?#az_upgrade).

Recursos da cloud:

- Um grupo de recursos para gerir todos os recursos que utilizar neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge

- Uma máquina virtual Windows para funcionar como o seu dispositivo IoT Edge. Pode criar esta máquina virtual utilizando o seguinte comando, substituindo `{password}` por uma senha segura:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Criar e iniciar a nova máquina virtual poderá demorar alguns minutos.

  Assim que a sua máquina virtual começar, pode descarregar um ficheiro RDP para utilização ao ligar-se à sua máquina virtual:

  1. Navegue para a sua nova máquina virtual Windows no portal Azure.
  1. Selecione **Connect** (Ligar).
  1. No separador **RDP,** selecione **Download RDP File**.

  Abra este ficheiro com Ligação de Ambiente de Trabalho Remoto para ligar à sua máquina virtual Do Windows utilizando o nome de administrador e a palavra-passe especificada com o `az vm create` comando.

> [!NOTE]
> A sua máquina virtual Windows começa com a versão 1809 do Windows (construa 17763), que é a mais recente [construção de suporte a longo prazo](/windows/release-information/)do Windows . O Windows verifica automaticamente as atualizações a cada 22 horas por predefinição. Depois de uma verificação na sua máquina virtual, o Windows empurra uma atualização de versão incompatível com o IoT Edge para windows, o que impede a utilização posterior do IoT Edge para funcionalidades do Windows. Recomendamos limitar a utilização da sua máquina virtual dentro de 22 horas ou [interromper temporariamente as atualizações do Windows](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> Este arranque rápido utiliza uma máquina virtual de ambiente de trabalho do Windows para simplificar. Para obter informações sobre quais os sistemas operativos Windows geralmente disponíveis para cenários de produção, consulte [os sistemas suportados pelo Azure IoT Edge](support.md).
>
> Se pretender configurar o seu próprio dispositivo Windows para ioT Edge, incluindo dispositivos que executam o IoT Core, siga os passos na [Instalação do tempo de execução do Azure IoT Edge](how-to-install-iot-edge.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o quickstart criando um hub IoT com Azure CLI.

![Diagrama - Criar um hub IoT na nuvem](./media/quickstart/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se já usou o IoT Hub no passado e já criou um hub, pode usar esse hub IoT.

O código a seguir cria um hub **de F1** gratuito no grupo de recursos `IoTEdgeResources` . `{hub_name}`Substitua-o por um nome único para o seu hub IoT. Pode levar alguns minutos para criar um hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Se tiver um erro que o nome IoT Hub não está disponível, significa que outra pessoa já tem um hub com esse nome. Tente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.
![Diagrama - Registar um dispositivo com identidade IoT Hub](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consiga comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos IoT Edge se comportam e podem ser geridos de forma diferente dos dispositivos IoT típicos, declare esta identidade como sendo para um dispositivo IoT Edge com a `--edge-enabled` bandeira.

1. Na Azure Cloud Shell, insira o seguinte comando para criar um dispositivo chamado **MyEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se tiver um erro sobre as teclas de política do iothubowner, certifique-se de que a sua Cloud Shell está a executar a versão mais recente da extensão azure-iot.

2. Veja a cadeia de ligação do seu dispositivo, que liga o seu dispositivo físico à sua identidade no IoT Hub. Contém o nome do seu hub IoT, o nome do seu dispositivo e, em seguida, uma chave partilhada que autentica ligações entre os dois.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave `connectionString` da saída em JSON e guarde-o. Este valor é a cadeia de ligação do dispositivo. Irá utilizar esta cadeia de ligação para configurar o runtime do IoT Edge na secção seguinte.

   ![Recuperar a cadeia de ligação a partir da saída do CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o runtime do IoT Edge

Instale o runtime do Azure IoT Edge no dispositivo IoT Edge e configure-o com uma cadeia de ligação de dispositivo.
![Diagrama - Iniciar o tempo de funcionaamento no dispositivo](./media/quickstart/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O *daemon de segurança IoT Edge* começa cada vez que um dispositivo IoT Edge arranca e arranca o dispositivo iniciando o agente IoT Edge. O *agente IoT Edge* gere a implementação e monitorização de módulos no dispositivo IoT Edge, incluindo o hub IoT Edge. O *hub IoT Edge* trata das comunicações entre os módulos do dispositivo IoT Edge e entre o dispositivo e o IoT Hub.

O script de instalação também inclui um motor de contentor chamado Moby que gere as imagens do contentor no seu dispositivo IoT Edge.

Durante a instalação do runtime, é-lhe pedida a cadeia de ligação do dispositivo. Utilize a cadeia que obteve na CLI do Azure. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="connect-to-your-iot-edge-device"></a>Ligue-se ao seu dispositivo IoT Edge

Os passos desta secção são todos realizados no seu dispositivo IoT Edge, pelo que pretende ligar-se a essa máquina virtual agora através de um ambiente de trabalho remoto.

### <a name="install-and-configure-the-iot-edge-service"></a>Instale e configuure o serviço IoT Edge

Utilize o PowerShell para transferir e instalar o runtime IoT Edge. Utilize a cadeia de ligação do dispositivo que obteve no hub IoT para configurar o dispositivo.

1. Na máquina virtual, executar o PowerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 de PowerShell para instalar ioT Edge, não PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

2. O comando **Deploy-IoTEdge** verifica se a sua máquina Do Windows está numa versão suportada, liga a funcionalidade de contentores, descarrega o tempo de execução do Moby e, em seguida, descarrega o tempo de execução IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

3. A sua máquina pode reiniciar automaticamente. Se for solicitado pelo comando Deploy-IoTEdge para reiniciar, faça-o agora.

4. Executar o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando não tem disposição manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

6. Quando lhe for pedido **DeviceConnectionString**, indique a cadeia que copiou na secção anterior. Não inclua as aspas à volta da cadeia de ligação.

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

Verifique se o runtime foi instalado e configurado corretamente. Pode levar alguns minutos para a instalação estar concluída e o módulo de agente IoT Edge para arrancar.

1. Verifique o estado do serviço IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Veja todos os módulos em execução no seu dispositivo IoT Edge. Uma vez que o serviço foi iniciado pela primeira vez, deverá ver apenas o módulo **edgeAgent** em execução. O módulo EdgeAgent funciona por predefinição e ajuda a instalar e iniciar quaisquer módulos adicionais que implemente no seu dispositivo.

    ```powershell
    iotedge list
    ```

   ![Ver um módulo no seu dispositivo](./media/quickstart/iotedge-list-1.png)

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implementados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gerencie o seu dispositivo Azure IoT Edge a partir da nuvem para implementar um módulo que envia dados de telemetria para o IoT Hub.

![Diagrama - desloque o módulo de nuvem para dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, utilizou o portal Azure para implantar um módulo IoT Edge para funcionar no dispositivo sem ter de fazer alterações no próprio dispositivo.

Neste caso, o módulo que empurrou gera dados de ambiente de amostra que pode usar para testes mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente à volta da máquina. Por exemplo, este sensor poderá estar num espaço de servidor, numa fábrica ou numa turbina eólica. A mensagem inclui temperatura ambiente e humidade, temperatura e pressão do computador e um carimbo de data/hora. Os tutoriais IoT Edge utilizam os dados criados por este módulo como dados de teste para análise.

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

Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Azure IoT Hub para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Caso contrário, pode eliminar os recursos Azure que criou para evitar encargos.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para se certificar de que não há nada que queira guardar. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível.

Remova o grupo de **IoTEdgeResources**. Pode levar alguns minutos para apagar um grupo de recursos.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Pode confirmar que o grupo de recursos é removido visualizando a lista de grupos de recursos.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo IoT Edge e utilizou a interface de nuvem Azure IoT Edge para implementar código no dispositivo. Agora tem um dispositivo de teste a gerar dados não processados sobre o seu ambiente.

O próximo passo é configurar o seu ambiente de desenvolvimento local para que possa começar a criar módulos IoT Edge que executam a sua lógica de negócio.

> [!div class="nextstepaction"]
> [Comece a desenvolver módulos IoT Edge para dispositivos Windows](tutorial-develop-for-windows.md)
