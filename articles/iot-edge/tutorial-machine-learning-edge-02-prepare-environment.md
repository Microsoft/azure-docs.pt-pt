---
title: Configurar o ambiente-Machine Learning em Azure IoT Edge | Microsoft Docs
description: Prepare seu ambiente para desenvolvimento e implantação de módulos para aprendizado de máquina na borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1db94e683a0dfb3b60b12bc5ac205c766d405d0a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299831"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Configurar um ambiente para aprendizado de máquina no IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Este artigo do Azure Machine Learning de ponta a ponta sobre IoT Edge tutorial ajuda você a preparar seu ambiente para desenvolvimento e implantação. Primeiro, configure um computador de desenvolvimento com todas as ferramentas de que você precisa. Em seguida, crie os recursos de nuvem necessários no Azure.

## <a name="set-up-a-development-machine"></a>Configurar um computador de desenvolvimento

Essa etapa normalmente é executada por um desenvolvedor de nuvem. Alguns dos softwares também podem ser úteis para um cientista de dados.

Ao longo deste artigo, executamos várias tarefas de desenvolvedor, incluindo codificação, compilação, configuração e implantação de módulos de IoT Edge e dispositivos IoT. Para facilitar o uso, criamos um script do PowerShell que crie uma máquina virtual do Azure com muitos dos pré-requisitos já configurados. A VM que criamos precisa ser capaz de lidar com a [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), motivo pelo qual escolhemos um tamanho de máquina [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) .

A VM de desenvolvimento será configurada com:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker desktop para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Gerenciador de credenciais git para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK .Net Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Extensões de VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A VM do desenvolvedor não é estritamente necessária – todas as ferramentas de desenvolvimento podem ser executadas em um computador local. No entanto, é altamente recomendável usar a VM para garantir um campo de reexecução de nível.

Leva cerca de 30 minutos para criar e configurar a máquina virtual.

### <a name="get-the-script"></a>Obter o script

Clone ou baixe o script do PowerShell do [Machine Learning e IOT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) repositório de exemplo.

### <a name="create-an-azure-virtual-machine"></a>Criar uma máquina virtual do Azure

O diretório DevVM contém os arquivos necessários para criar uma máquina virtual do Azure apropriada para concluir este tutorial.

1. Abra o PowerShell como administrador e navegue até o diretório em que você baixou o código. Vamos nos referir ao diretório raiz de sua origem como `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Execute o comando a seguir para permitir a execução de scripts. Escolha **Sim para todos** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Execute Create-AzureDevVM. ps1 deste diretório.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Quando solicitado, forneça as seguintes informações:
      * **ID da assinatura do Azure**: Sua ID de assinatura, que pode ser encontrada na portal do Azure
      * **Nome do grupo de recursos**: O nome de um grupo de recursos novo ou existente no Azure
      * **Local**: Escolha um local do Azure onde a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para obter mais informações, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Forneça um nome fácil de memorizar para a conta de administrador que você deseja criar e usar na máquina virtual.
      * **AdminPassword**: Defina uma senha para a conta de administrador na máquina virtual.

    * Se você não tiver o Azure PowerShell instalado, o script será instalado [Azure PowerShell módulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Ser-lhe-á pedido que inicie sessão no Azure.

    * O script confirma as informações para a criação de sua VM. Pressione `y` ou`Enter` para continuar.

O script é executado por vários minutos, uma vez que executa as seguintes etapas:

* Criar o grupo de recursos se ele não existir
* Implementar a máquina virtual
* Habilitar o Hyper-V na VM
* Instalar a necessidade de software para desenvolvimento e clonar o repositório de exemplo
* Reiniciar a VM
* Criar um arquivo RDP em sua área de trabalho para conectar-se à VM

### <a name="set-auto-shutdown-schedule"></a>Definir agendamento de desligamento automático

Para ajudá-lo a reduzir o custo, a VM foi criada com um agendamento de desligamento automático definido como 1900 PST. Talvez seja necessário atualizar esse tempo dependendo do local e da agenda. Para atualizar a agenda de desligamento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue até sua máquina virtual no grupo de recursos que você forneceu na seção anterior.

3. Selecione **desligamento automático** no navegador lateral.

4. Insira um novo horário de desligamento no **desligamento agendado** ou altere o **fuso horário** e clique em **salvar**.

### <a name="connect-and-configure-development-machine"></a>Conectar e configurar o computador de desenvolvimento

Agora que criamos uma VM, precisamos concluir a instalação do software necessário para concluir o tutorial.

#### <a name="start-a-remote-desktop-session"></a>Iniciar uma sessão de área de trabalho remota

1. O script de criação de VM criou um arquivo RDP em sua área de trabalho.

2. Clique duas vezes no arquivo com o nome  **\<VM do Azure nome\>. rdp**.

3. Será exibida uma caixa de diálogo informando que o editor da conexão remota é desconhecido. Clique na caixa de seleção **não perguntar novamente sobre conexões a este computador** e, em seguida, selecione **conectar**.

4. Quando solicitado, forneça a AdminPassword que você usou ao executar o script para configurar a VM e clique em **OK**.

5. Você será solicitado a aceitar o certificado para a VM. Selecione **não perguntar novamente sobre conexões a este computador** e escolha **Sim**.

#### <a name="install-visual-studio-code-extensions"></a>Instalar extensões de Visual Studio Code

Agora que você se conectou ao computador de desenvolvimento, adicione algumas extensões úteis para Visual Studio Code para facilitar a experiência de desenvolvimento.

1. Em uma janela do PowerShell, navegue até **C\\:\\Source\\IoTEdgeAndMlSample DevVM**.

2. Permita que os scripts sejam executados na máquina virtual digitando.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Execute o script.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. O script será executado por alguns minutos instalando extensões do VS Code:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar o Hub IoT e o armazenamento

Essas etapas normalmente são executadas por um desenvolvedor de nuvem.

O Hub IoT do Azure é o coração de qualquer aplicativo IoT. Ele lida com a comunicação segura entre os dispositivos IoT e a nuvem. É o principal ponto de coordenação para a operação da solução IoT Edge Machine Learning.

* O Hub IoT usa rotas para direcionar dados de entrada de dispositivos IoT para outros serviços downstream. Aproveitaremos as rotas do Hub IoT para enviar dados do dispositivo para o armazenamento do Azure, onde ele pode ser consumido por Azure Machine Learning para treinar nosso classificador restante de RUL (vida útil).

* Posteriormente no tutorial, usaremos o Hub IoT para configurar e gerenciar nosso dispositivo de Azure IoT Edge.

Nesta seção, você usará um script para criar um hub IoT do Azure e uma conta de armazenamento do Azure. Em seguida, você configura uma rota que encaminha os dados recebidos pelo hub para um contêiner Azure Storage Blob usando o portal do Azure. Essas etapas levam cerca de 10 minutos para serem concluídas.

### <a name="create-cloud-resources"></a>Criar recursos de nuvem

1. No computador de desenvolvimento, abra uma janela do PowerShell.

1. Altere para o diretório IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Execute o script de criação. Use os mesmos valores para ID de assinatura, local e grupo de recursos que você fez ao criar a VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Ser-lhe-á pedido que inicie sessão no Azure.
    * O script confirma as informações para a criação do Hub e a conta de armazenamento. Pressione `y` ou`Enter` para continuar.

O script leva cerca de dois minutos para ser executado. Depois de concluído, o script gera o nome do Hub e a conta de armazenamento.

### <a name="review-route-to-storage-in-iot-hub"></a>Examinar a rota para o armazenamento no Hub IoT

Como parte da criação do Hub IoT, o script que executamos na seção anterior também criava um ponto de extremidade personalizado e uma rota. As rotas do Hub IoT consistem em uma expressão de consulta e um ponto de extremidade. Se uma mensagem corresponder à expressão, os dados serão enviados ao longo da rota para o ponto de extremidade associado. Os pontos de extremidade podem ser hubs de eventos, filas do barramento de serviço e tópicos. Nesse caso, o ponto de extremidade é um contêiner de BLOB em uma conta de armazenamento. Vamos usar o portal do Azure para examinar a rota criada pelo nosso script.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Escolha todos os serviços no navegador esquerdo, digite IoT na caixa de pesquisa e selecione **Hub IOT**.

1. Escolha o Hub IoT criado na etapa anterior.

1. No navegador do lado do Hub IoT, escolha **Roteamento de mensagens**.

1. A página roteamento de mensagens tem duas guias, **rotas** e **pontos de extremidade personalizados**. Selecione a guia **pontos de extremidade personalizados** .

1. Em **armazenamento de BLOBs**, selecione **turbofanDeviceStorage**.

1. Observe que esse ponto de extremidade aponta para um contêiner de blob chamado **devicedata** na conta de armazenamento criada na última etapa, que é nomeada **\<iotedgeandml\>sufixo exclusivo**.

1. Observe também que o **formato de nome de arquivo de blob** foi alterado do formato padrão para posicionar a partição como o último elemento no nome. Descobrimos que esse formato é mais conveniente para as operações de arquivo que faremos com Azure Notebooks mais adiante no tutorial.

1. Feche a folha detalhes do ponto de extremidade para retornar à página **Roteamento de mensagens** .

1. Selecione a guia **rotas** .

1. Selecione a rota chamada **turbofanDeviceDataToStorage**.

1. Observe que o ponto de extremidade da rota é o ponto de extremidade personalizado **turbofanDeviceStorage** .

1. Examine a consulta de roteamento, que é definida como **true**. Isso significa que todas as mensagens de telemetria do dispositivo corresponderão a essa rota e, portanto, todas as mensagens serão enviadas para o ponto de extremidade **turbofanDeviceStorage** .

1. Feche os detalhes da rota.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criamos um hub IoT e configuramos uma rota para uma conta de armazenamento do Azure. No próximo artigo, enviaremos dados de um conjunto de dispositivos simulados por meio do Hub IoT para a conta de armazenamento. Posteriormente no tutorial, depois de configurar o dispositivo e os módulos IoT Edge, revisitaremos as rotas e examinaremos um pouco mais na consulta de roteamento.

Para obter mais informações sobre as etapas abordadas nesta parte do Machine Learning no tutorial IoT Edge, consulte:

* [Noções básicas do IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurar o roteamento de mensagens com o Hub IoT](../iot-hub/tutorial-routing.md)
* [Criar um hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md)

Continue no próximo artigo para criar um dispositivo simulado a ser monitorado.

> [!div class="nextstepaction"]
> [Gerar dados do dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
