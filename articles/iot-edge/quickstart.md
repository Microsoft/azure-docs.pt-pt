---
title: Quickstart para criar um dispositivo Azure IoT Edge no Windows | Microsoft Docs
description: Neste arranque rápido, aprenda a criar um dispositivo IoT Edge e, em seguida, implemente código pré-construído remotamente a partir do portal Azure.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5444f6adb9d441cb6253c180cf2d079c1c36316c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562686"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Quickstart: Implemente o seu primeiro módulo IoT Edge num dispositivo Windows (pré-visualização)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Experimente o Azure IoT Edge neste arranque rápido, implantando código contentorizado num dispositivo Linux no Windows IoT Edge. O IoT Edge permite-lhe gerir remotamente código nos seus dispositivos para que possa enviar mais cargas de trabalho para a borda. Para este arranque rápido, recomendamos usar o seu próprio dispositivo para ver como é fácil utilizar o Azure IoT Edge para o Linux no Windows.

Neste início rápido, vai aprender a:

* Criar um hub IoT.
* Registar um dispositivo IoT Edge no seu hub IoT.
* Instale e inicie o IoT Edge para Linux no tempo de funcionaamento do Windows no seu dispositivo.
* Desloque remotamente um módulo para um dispositivo IoT Edge e envie telemetria.

![Diagrama que mostra a arquitetura deste arranque rápido para o seu dispositivo e nuvem.](./media/quickstart/install-edge-full.png)

Este quickstart acompanha-o a configurar o seu Azure IoT Edge para Linux no dispositivo Windows. Em seguida, irá colocar um módulo do portal Azure para o seu dispositivo. O módulo que vai usar é um sensor simulado que gera dados de temperatura, humidade e pressão. Outros tutoriais do Azure IoT Edge baseiam-se no trabalho que faz aqui, implantando módulos que analisam os dados simulados para insights de negócio.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

>[!NOTE]
>IoT Edge for Linux on Windows está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Prepare o seu ambiente para o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Crie um grupo de recursos em nuvem para gerir todos os recursos que utilizar neste arranque rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Certifique-se de que o seu dispositivo IoT Edge cumpre os seguintes requisitos:

* Edições
  * Versão 1809 ou posterior do Windows 10; construir 17763 ou mais tarde
    * Profissional, Enterprise, IoT Enterprise
  * Windows Server 2019 construir 17763 ou mais tarde

  
* Requisitos de hardware
  * Memória mínima gratuita: 2 GB
  * Espaço mínimo de disco gratuito: 10 GB


>[!NOTE]
>Este quickstart utiliza o Windows Admin Center para criar uma implementação de IoT Edge para Linux no Windows. Também pode utilizar o PowerShell. Se desejar utilizar o PowerShell para criar a sua implantação, siga os passos no guia de como [instalar e a provisionar o Azure IoT Edge para o Linux num dispositivo Windows](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Comece por criar um hub IoT com o Azure CLI.

![Diagrama que mostra o passo para criar um hub I o T.](./media/quickstart/create-iot-hub.png)

O nível livre do Azure IoT Hub funciona para este arranque rápido. Se já usou o IoT Hub no passado e já criou um hub, pode usar esse hub IoT.

O código a seguir cria um hub **de F1** gratuito no grupo de recursos `IoTEdgeResources` . `{hub_name}`Substitua-o por um nome único para o seu hub IoT. Pode levar alguns minutos para criar um hub IoT.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Se tiver um erro porque já tem um hub gratuito na sua subscrição, altere o SKU para `S1` . Se tiver um erro que o nome do hub IoT não está disponível, alguém já tem um hub com esse nome. Tente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagrama que mostra o passo para registar um dispositivo com uma identidade de hub IoT.](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consiga comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Os dispositivos IoT Edge comportam-se e podem ser geridos de forma diferente dos dispositivos IoT típicos. Utilize a `--edge-enabled` bandeira para declarar que esta identidade se destina a um dispositivo IoT Edge.

1. Em Azure Cloud Shell, insira o seguinte comando para criar um dispositivo chamado **MyEdgeDevice** no seu hub.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Se tiver um erro nas `iothubowner` teclas de política, certifique-se de que a Cloud Shell está a executar a versão mais recente da extensão Azure IoT.

1. Veja a cadeia de ligação do seu dispositivo, que liga o seu dispositivo físico à sua identidade no IoT Hub. Contém o nome do seu hub IoT, o nome do seu dispositivo, e uma chave partilhada que autentica as ligações entre os dois.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Copie o valor da chave `connectionString` da saída em JSON e guarde-o. Este valor é a cadeia de ligação do dispositivo. Vai usá-lo para configurar o tempo de execução do IoT Edge na secção seguinte.

     ![Screenshot que mostra a ligaçãoDesução Desacosagem em Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o runtime do IoT Edge

Instale o IoT Edge para o Linux no Windows no seu dispositivo e configuure-o com a cadeia de ligação do dispositivo.

![Diagrama que mostra o passo para iniciar o tempo de execução IoT Edge.](./media/quickstart/start-runtime.png)

1. [Baixar o Windows Admin Center](https://aka.ms/wacdownload).

1. Siga as indicações no assistente de instalação para configurar o Windows Admin Center no seu dispositivo.

1. Abrir o Centro de Administração do Windows.

1. Selecione o ícone de **engrenagem Definições** no canto superior direito e, em seguida, selecione **Extensões**.

1. No **separador Feeds,** **selecione Adicionar**.

1. `https://aka.ms/wac-insiders-feed`Introduza na caixa de texto e, em seguida, selecione **Adicionar**.

1. Depois de adicionar o feed, vá ao separador **extensões disponíveis** e aguarde que a lista de extensões atualização.

1. Da lista de **extensões disponíveis,** selecione **Azure IoT Edge**.

1. Instale a extensão.

1. Quando a extensão for instalada, selecione o **Windows Admin Center** no canto superior esquerdo para ir à página principal do painel de instrumentos.

     A ligação **local** representa o PC onde está a executar o Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Screenshot da página Inicial do Administrador do Windows.":::

1. Selecione **Adicionar**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Screenshot que mostra a seleção do botão Adicionar no Windows Admin Center.":::

1. No azulejo Azure IoT Edge, selecione **Criar novo** para iniciar o assistente de instalação.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Screenshot que mostra a criação de uma nova implementação no Azure IoT Edge até.":::

1. Continue através do assistente de instalação para aceitar os Termos de Licença de Software da Microsoft e, em seguida, selecione **Seguinte**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Screenshot que mostra a seleção Seguinte para continuar através do assistente de instalação.":::

1. Selecione **dados de diagnóstico opcionais** e, em seguida, selecione **Seguinte: Implementar**. Esta seleção fornece dados de diagnóstico alargados que ajudam a Microsoft a monitorizar e manter a qualidade do serviço.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Screenshot que mostra as opções de dados de Diagnóstico.":::

1. No ecrã do **dispositivo alvo Select,** selecione o dispositivo-alvo pretendido para validar que cumpre os requisitos mínimos. Para este arranque rápido, estamos a instalar o IoT Edge no dispositivo local, por isso escolha a ligação **local.** Se o dispositivo alvo cumprir os requisitos, selecione **Seguinte** para continuar.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Screenshot que mostra a lista de dispositivos Target.":::

1. Selecione **Seguinte** para aceitar as definições predefinidos. O ecrã de implementação mostra o processo de descarregamento do pacote, instalação do pacote, configuração do anfitrião e configuração final da máquina virtual Linux (VM). Uma implementação bem sucedida é assim:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Screenshot de uma implementação bem sucedida.":::

1. Selecione **Seguinte: Ligue-se** para continuar até ao passo final para forrar o seu dispositivo Azure IoT Edge com o seu ID dispositivo a partir da sua instância do hub IoT.

1. Cole a cadeia de ligação que copiou [anteriormente neste arranque rápido no](#register-an-iot-edge-device) campo de cordas de **ligação do dispositivo.** Em seguida, selecione **Provisioning com o método selecionado**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Screenshot que mostra a cadeia de ligação no campo de cordas de ligação do dispositivo.":::

1. Depois de o provisionamento estar completo, **selecione Finish** para completar e voltar ao ecrã de partida do Windows Admin Center. Deve ver o seu dispositivo listado como um dispositivo IoT Edge.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Screenshot que mostra todas as ligações no Windows Admin Center.":::

1. Selecione o seu dispositivo Azure IoT Edge para ver o seu painel de instrumentos. Deve ver se as cargas de trabalho do seu dispositivo twin no Azure IoT Hub foram implantadas. A **Lista de Módulos IoT Edge** deve mostrar um módulo em execução **Agent,** e o **IoT Edge Status** deve estar ativo **(em funcionamento)**.

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implementados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gerencie o seu dispositivo Azure IoT Edge a partir da nuvem para implementar um módulo que envia dados de telemetria para o IoT Hub.

![Diagrama que mostra o passo para implantar um módulo.](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, utilizou o portal Azure para implantar um módulo IoT Edge para funcionar no dispositivo sem ter de fazer alterações no próprio dispositivo.

O módulo que empurrou gera dados de ambiente de amostra que pode usar para testes mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente à volta da máquina. Por exemplo, este sensor poderá estar num espaço de servidor, numa fábrica ou numa turbina eólica. As mensagens que envia incluem temperatura e humidade ambiente, temperatura e pressão da máquina e um tempotando. Os tutoriais IoT Edge utilizam os dados criados por este módulo como dados de teste para análise.

A partir da concha de comando no Windows Admin Center, confirme que o módulo que implementou a partir da nuvem está a funcionar no seu dispositivo IoT Edge.

1. Ligue-se ao seu dispositivo IoT Edge recém-criado.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Screenshot que mostra a seleção de Connect no Windows Admin Center.":::

     Na página **'Visão Geral',** verá a Lista de **Módulos IoT Edge** e **o Estado da Borda IoT**. Pode ver os módulos que foram implantados e o estado do dispositivo.  

1. Em **Ferramentas**, selecione **Comando Shell**. O invólucro de comando é um terminal PowerShell que utiliza automaticamente a Secure Shell (SSH) para ligar ao Linux VM do seu dispositivo Azure IoT Edge no seu PC Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Screenshot que mostra a abertura da concha de comando.":::

1. Para verificar os três módulos do seu dispositivo, execute o seguinte comando Bash:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Screenshot que mostra a saída da lista de comando I o T edge.":::

1. Veja as mensagens enviadas do módulo do sensor de temperatura para a nuvem.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >Os comandos IoT Edge são sensíveis a maiíssimos quando se referem aos nomes dos módulos.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Screenshot que mostra a lista de mensagens enviadas do módulo para a nuvem.":::

Também pode utilizar a [extensão Azure IoT Hub para o Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) para ver as mensagens chegarem ao seu hub IoT.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser continuar com os tutoriais IoT Edge, ignore este passo. Pode utilizar o dispositivo que registou e instalou neste arranque rápido. Caso contrário, pode eliminar os recursos Azure que criou para evitar encargos.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais.

> [!IMPORTANT]
> Verifique o conteúdo do grupo de recursos para se certificar de que não há nada que queira guardar. A eliminação de um grupo de recursos é irreversível.

Utilize o seguinte comando para remover o grupo **IoTEdgeResources.** A eliminação pode demorar alguns minutos.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Pode confirmar que o grupo de recursos é removido utilizando este comando para visualizar a lista de grupos de recursos.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Remover Borda IoT Azure para Linux no Windows

Utilize a extensão do painel de instrumentos no Windows Admin Center para desinstalar o Azure IoT Edge para o Linux no Windows.

1. Ligue-se ao dispositivo IoT Edge no Windows Admin Center. As cargas de extensão da ferramenta do painel Azure.

1. Selecione **Desinstalar**. Depois de removido o Azure IoT Edge, o Windows Admin Center remove o acesso ao dispositivo Azure IoT Edge da página **Iniciar.**

>[!Note]
>Outra forma de remover o Azure IoT Edge do seu sistema Windows é selecionar  >  **Start Settings**  >  **Apps**  >  **Azure IoT Edge**  >  **Desinstalar** no seu dispositivo IoT Edge. Este método remove o Azure IoT Edge do seu dispositivo IoT Edge, mas deixa a ligação para trás no Windows Admin Center. Para completar a remoção, desinstale também o Centro de Administração do Windows a partir do menu **Definições.**

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo IoT Edge e utilizou a interface de nuvem Azure IoT Edge para implementar código no dispositivo. Agora tem um dispositivo de teste que gera dados brutos sobre o seu ambiente.

Em seguida, crie o seu ambiente de desenvolvimento local para que possa começar a criar módulos IoT Edge que executam a sua lógica de negócio.

> [!div class="nextstepaction"]
> [Comece a desenvolver módulos IoT Edge](tutorial-develop-for-linux.md)
