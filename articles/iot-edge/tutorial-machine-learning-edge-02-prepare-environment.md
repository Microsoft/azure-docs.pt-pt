---
title: Configurar o ambiente - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Prepare o ambiente para desenvolvimento e implementação de módulos de aprendizagem automática à periferia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f8652dab5db8bbd59982cb1dda727698868df23c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057744"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Configurar um ambiente para aprendizagem do IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Este artigo de ponta a ponta do Azure Machine Learning no tutorial do IoT Edge ajuda-o a preparar o ambiente para desenvolvimento e implementação. Primeiramente, configure uma máquina de desenvolvimento com todas as ferramentas que necessárias. Em seguida, crie os recursos da cloud necessárias no Azure.

## <a name="set-up-a-development-machine"></a>Configurar uma máquina de desenvolvimento

Este passo é, normalmente realizado por um desenvolvedor de cloud. Alguns dos softwares também podem ser úteis para um cientista de dados.

Ao longo deste artigo, iremos efetuar várias tarefas de desenvolvedor incluindo codificação, compilação, a configuração e a implementação de módulos do IoT Edge e dispositivos IoT. Facilidade de utilização, criamos um script do PowerShell que cria uma máquina virtual do Azure com muitos dos pré-requisitos já configurados. Tem de ser capaz de lidar com a VM que criamos [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), que é o motivo pelo qual escolhemos um tamanho de máquina DS8V3.

O desenvolvimento VM irá ser configurada com:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Ambiente de trabalho do docker para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Gestor de credenciais do Git para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK .Net Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Extensões de código do Visual Studio](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

O desenvolvedor VM não é estritamente necessário – todas as ferramentas de desenvolvimento podem ser executadas numa máquina local. No entanto, recomendamos vivamente utilizar a VM para garantir uma nível de atuação.

Demora cerca de 30 minutos para criar e configurar a máquina virtual.

### <a name="get-the-script"></a>Obter o script

Clonar ou transferir o script do PowerShell a partir da [Machine Learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) repositório de exemplo.

### <a name="create-an-azure-virtual-machine"></a>Criar uma máquina virtual do Azure

O diretório de DevVM contém os arquivos necessários para criar uma máquina virtual do Azure adequada para a conclusão deste tutorial.

1. Abra o Powershell como administrador e navegue para o diretório onde transferiu o código. Nós nos referiremos para o diretório de raiz para a sua origem como `<srcdir>`.

    ```powershell
    cd \<srcdir\>\IoTEdgeAndMlSample\DevVM
    ```

2. Execute o seguinte comando para permitir a execução de scripts. Escolher **Sim para todos** quando lhe for pedido.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Execute AzureDevVM.ps1 de criar a partir deste diretório.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Quando lhe for pedido, forneça as seguintes informações:
      * **ID de subscrição do Azure**: ID da subscrição, o que pode ser encontrado no portal do Azure
      * **Nome do grupo de recursos**: O nome de um grupo de recursos novo ou existente no Azure
      * **Localização**: Escolha uma localização do Azure onde será criada a máquina virtual. Por exemplo, westus2 ou northeurope. Para obter mais informações, consulte [localizações do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Forneça um nome fácil de memorizar para a conta de administrador que pretende criar e utilizar na máquina virtual.
      * **AdminPassword**: Defina uma palavra-passe para a conta de administrador na máquina virtual.

    * Se não tiver o Azure PowerShell instalada, o script instalará [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Ser-lhe-á pedido que inicie sessão no Azure.

    * O script confirma as informações para a criação da sua VM. Prima `y` ou `Enter` para continuar.

O script é executado durante vários minutos, pois ele executa os seguintes passos:

* Se não existir, crie o grupo de recursos
* Implementar a máquina virtual
* Ativar Hyper-V na VM
* Instalar a necessidade de software para desenvolvimento e clonar o repositório de exemplo
* Reinicie a VM
* Criar um ficheiro RDP no ambiente de trabalho para ligar à VM

### <a name="set-auto-shutdown-schedule"></a>Agendamento de encerramento automático de conjunto

Para ajudar a reduzir os custos, a VM tiver sido criada com uma agenda de encerramento automático estiver definida como PST de 1900. Terá de atualizar este tempo consoante a localização e o agendamento. Para atualizar o agendamento de encerramento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue para a máquina virtual no grupo de recursos que são fornecidos na secção anterior.

3. Selecione **encerramento automático** sobre o navegador de lado.

4. Introduza uma nova hora de encerramento na **agendada encerramento** ou altere a **fuso horário** , em seguida, clique em **guardar**.

### <a name="connect-and-configure-development-machine"></a>Ligar e configurar o computador de desenvolvimento

Agora que já criou uma VM, é necessário concluir a instalação do software necessário para concluir o tutorial.

#### <a name="start-a-remote-desktop-session"></a>Iniciar uma sessão de ambiente de trabalho remota

1. O script de criação de VM criado um ficheiro RDP no ambiente de trabalho.

2. Faça duplo clique no ficheiro com o nome  **\<nome da VM do Azure\>. rdp**.

3. Será apresentada uma caixa de diálogo dizendo que o publicador da ligação remota é desconhecido. Clique nas **não perguntar novamente para ligações a este computador** caixa de verificação, em seguida, selecione **Connect**.

4. Quando solicitado, forneça o AdminPassword que utilizou ao executar o script para configurar a VM e clique em **OK**.

5. Será solicitado a aceitar o certificado para a VM. Selecione **não perguntar novamente para ligações a este computador** e escolha **Sim**.

#### <a name="install-visual-studio-code-extensions"></a>Instalar as extensões do Visual Studio Code

Agora que tiver ligado à máquina de desenvolvimento, adicione algumas extensões úteis para Visual Studio Code, para tornar o desenvolvimento mais fácil a experiência.

1. Numa janela do PowerShell, navegue até **c:\\origem\\IoTEdgeAndMlSample\\DevVM**.

2. Permitir que scripts sejam executados na máquina virtual ao escrever.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Execute o script.

    ```powershell
    .\\Enable-CodeExtensions.ps1
    ```

4. O script será executado durante alguns minutos, a instalação de extensões de código do Visual Studio:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar o IoT Hub e de armazenamento

Estes passos são, normalmente realizados por um desenvolvedor de cloud.

O IoT Hub do Azure é o coração de qualquer aplicativo de IoT. Ele manipula a comunicação segura entre dispositivos IoT e na cloud. Ele é o ponto de coordenação de principal para a operação de solução de aprendizagem automática do IoT Edge.

* IoT Hub utiliza rotas para direcionar os dados de entrada dos dispositivos IoT para outros serviços downstream. Será aproveitamos do IoT Hub rotas para enviar dados de dispositivo para o armazenamento do Azure, onde podem ser consumido do Azure Machine Learning para treinar o classificador de vida útil (RUL) restantes.

* Mais tarde no tutorial, utilizamos o IoT Hub para configurar e gerir o nosso dispositivo Azure IoT Edge.

Nesta secção, vai utilizar um script para criar um hub IoT do Azure e uma conta de armazenamento do Azure. Em seguida, configurar uma rota que encaminha dados recebidos pelo hub para um contentor de BLOBs de armazenamento do Azure no portal do Azure. Estes passos demorar cerca de 10 minutos a concluir.

### <a name="create-cloud-resources"></a>Criar recursos na cloud

1. No computador de desenvolvimento, abra uma janela do PowerShell.

1. Mude para o diretório de IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Execute o script de criação. Utilize os mesmos valores para a subscrição ID, localização e grupo de recursos, como fez quando criar a VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Ser-lhe-á pedido que inicie sessão no Azure.
    * O script confirma as informações para a criação da sua conta de armazenamento e do Hub. Prima `y` ou `Enter` para continuar.

O script demora cerca de dois minutos para ser executado. Depois de concluído, o script devolve o nome do hub e a conta de armazenamento.

### <a name="review-route-to-storage-in-iot-hub"></a>Reveja a rota para o armazenamento no IoT Hub

Como parte da criação do hub IoT, o script que fizemos na secção anterior também criado um ponto final personalizado e uma rota. As rotas do IoT Hub é composto por uma expressão de consulta e um ponto de extremidade. Se uma mensagem corresponde à expressão, os dados são enviados ao longo da rota para o ponto final associado. Pontos de extremidade podem ser dos Hubs de eventos, filas do Service Bus e tópicos. Neste caso, o ponto final é um contentor de BLOBs numa conta de armazenamento. Vamos utilizar o portal do Azure para rever a rota criada por nosso script.

1. Abra o [Portal do Azure](http://portal.azure.com).

1. Selecione todos os serviços do navegador à esquerda, escreva IoT na caixa de pesquisa e selecione **IoT Hub**.

1. Escolha que o IoT Hub criado no passo anterior.

1. No navegador de lado o IoT Hub, escolha **roteamento de mensagens**.

1. A mensagem de encaminhamento de página tem dois separadores, **rotas** e **pontos finais personalizados**. Selecione o **pontos finais personalizados** separador.

1. Sob **armazenamento de BLOBs**, selecione **turbofanDeviceStorage**.

1. Tenha em atenção que este ponto final aponta para um contentor de BLOBs denominado **devicedata** a conta de armazenamento que criou no último passo, que tem o nome **iotedgeandml\<sufixo exclusivo\>** .

1. Tenha também em atenção a **formato de nome de ficheiro Blob** foi alterada do formato predefinido para colocar em vez disso, a partição como o último elemento no nome. Encontrámos que este formato é mais conveniente para as operações de arquivo que vamos fazer com blocos de notas do Azure mais tarde no tutorial.

1. Feche o painel de detalhes do ponto final para retornar para o **roteamento de mensagens** página.

1. Selecione o **rotas** separador.

1. Selecione a rota com o nome **turbofanDeviceDataToStorage**.

1. Tenha em atenção que o ponto final da rota é o **turbofanDeviceStorage** ponto final personalizado.

1. Analise a consulta de encaminhamento, o que está definida como **true**. Isso significa que todas as mensagens de telemetria do dispositivo serão correspondente esta rota e, portanto, todas as mensagens serão enviadas para o **turbofanDeviceStorage** ponto final.

1. Feche os detalhes de rota.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, cria um IoT Hub e configurada uma rota para uma conta de armazenamento do Azure. No próximo artigo, iremos enviar dados de um conjunto de dispositivos simulados através do IoT Hub para a conta de armazenamento. Mais tarde no tutorial, depois, configuramos nosso dispositivo IoT Edge e os módulos, iremos rever as rotas e uma aparência um pouco mais a consulta de encaminhamento.

Para obter mais informações sobre as etapas abordadas nessa parte do Machine Learning no tutorial do IoT Edge, veja:

* [Noções básicas do IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurar o encaminhamento de mensagens com o IoT Hub](../iot-hub/tutorial-routing.md)
* [Criar um hub IoT com o portal do Azure](../iot-hub/iot-hub-create-through-portal.md)

Avance para o artigo seguinte para criar um dispositivo simulado para monitorizar.

> [!div class="nextstepaction"]
> [Gerar dados de dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
