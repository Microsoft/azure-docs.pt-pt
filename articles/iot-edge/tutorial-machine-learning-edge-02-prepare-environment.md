---
title: 'Tutorial: Configurar ambiente - Machine Learning on Azure IoT Edge'
description: 'Tutorial: Prepare o seu ambiente para o desenvolvimento e implantação de módulos para aprendizagem automática no limite.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 22628af5b3e62edfc96699ad051b6a00cf9c1429
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078945"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Criar um ambiente para aprendizagem automática em IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, nós o encorajamos a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para os melhores resultados.

Este artigo ajuda-o a preparar o seu ambiente para o desenvolvimento e implantação. Primeiro, crie uma máquina de desenvolvimento com todas as ferramentas necessárias. Em seguida, crie os recursos de nuvem necessários em Azure.

## <a name="set-up-the-development-vm"></a>Configurar o VM de desenvolvimento

Este passo é normalmente realizado por um desenvolvedor de nuvens. Alguns dos softwares também podem ser úteis para um cientista de dados.

Criámos um script PowerShell que cria uma máquina virtual Azure com muitos dos pré-requisitos já configurados. O VM que criamos precisa de ser capaz de lidar com a [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), razão pela qual escolhemos um [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) tamanho da máquina.

O desenvolvimento VM será criado com:

* Windows 10
* [Chocolate](https://chocolatey.org/)
* [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Git Credential Manager para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK .NET Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-1.1.0)
* [Extensões de código VS](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

O VM do desenvolvedor não é estritamente necessário – todas as ferramentas de desenvolvimento podem ser executadas numa máquina local. No entanto, recomendamos vivamente a utilização do VM para garantir condições de igualdade.

Leva cerca de 30 minutos para criar e configurar a máquina virtual.

1. Clone ou descarregue o repositório de amostras [Machine Learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) para o seu computador local.

1. Open PowerShell como administrador e navegue para o **diretório \IoTEdgeAndMlSample\DevVM** localizado sob o diretório de raiz onde descarregou o código. Vamos referir-nos ao diretório de raiz para a sua fonte como `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   O diretório DevVM contém os ficheiros necessários para criar uma máquina virtual Azure adequada para completar este tutorial.

1. Executar o seguinte comando para permitir a execução de scripts. Escolha **Sim a Todos** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Corre Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Quando solicitado, forneça as seguintes informações:

    * **ID de subscrição Azure:** O seu ID de subscrição, que pode ser encontrado em [Subscrições Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal.
    * **Nome do Grupo de Recursos**: O nome de um grupo de recursos novo ou existente em Azure.
    * **Localização**: Escolha um local azul onde a máquina virtual será criada. Por exemplo, "West US 2" ou "North Europe". Para mais informações, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **Nome de utilizador**: Forneça um nome memorável para a conta do administrador para o VM.
    * **Senha**: Desajuste de uma palavra-passe para a conta do administrador para o VM.

   O script é executado por vários minutos enquanto executa os seguintes passos:

    1. Instala o [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Pede-lhe que assine com Azure.
    1. Confirma a informação para a criação do seu VM. Pressione **y** ou **Enter** para continuar.
    1. Cria o grupo de recursos se não existir.
    1. Implanta a máquina virtual.
    1. Ativa o Hiper-V no VM.
    1. Instala a necessidade de desenvolvimento do software e clona o repositório de amostras.
    1. Reinicia o VM.
    1. Cria um ficheiro RDP no seu ambiente de trabalho para ligar ao VM.

   Se for solicitado que o nome do VM o reinicie, pode copiar o seu nome a partir da saída do script. A saída também mostra o caminho para o ficheiro RDP para a ligação ao VM.

### <a name="set-auto-shutdown-schedule"></a>Definir horário de paragem automática

Para ajudá-lo a reduzir custos, o VM de desenvolvimento foi criado com um programa de paragem automática que está definido para 1900 PST. Poderá necessitar de atualizar esta definição dependendo da sua localização e horário. Para atualizar o horário de paragem:

1. No portal Azure, navegue para o VM que o script criou.

1. A partir do menu do painel esquerdo, em **Operações,** **selecione Auto-shutdown**.

1. Ajuste o **paragem programado** e **o fuso horário** conforme desejado e selecione **Save**.

## <a name="connect-to-the-development-vm"></a>Ligar-se ao VM de desenvolvimento

Agora que criámos um VM precisamos de terminar a instalação do software necessário para completar o tutorial.

1. Clique duas vezes no ficheiro RDP que o script criado no seu ambiente de trabalho.

1. Será apresentado um diálogo dizendo que o editor da ligação remota é desconhecido. Isto é aceitável, por isso selecione **Connect**.

1. Forneça a senha do administrador que forneceu para criar o VM e clique **em OK**.

1. Será solicitado que aceite o certificado para o VM. Selecione **Sim**.

## <a name="install-visual-studio-code-extensions"></a>Instalar extensões do Visual Studio Code

Agora que ligou à máquina de desenvolvimento, adicione algumas extensões úteis ao Código do Estúdio Visual para facilitar a experiência de desenvolvimento.

1. Ligue-se ao desenvolvimento VM, abra uma janela PowerShell e navegue para o diretório **C:\source\IoTEdgeAndMlSample\DevVM.** Este diretório foi criado pelo script que criou o VM.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Executar o seguinte comando para permitir a execução de scripts. Escolha **Sim a Todos** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Executar o script de extensões do Código do Estúdio Visual.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. O script será executado por alguns minutos instalando extensões de código VS:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar o Hub ioT e armazenamento

Estes passos são normalmente realizados por um desenvolvedor de nuvens.

O Azure IoT Hub é o coração de qualquer aplicação IoT, uma vez que lida com uma comunicação segura entre dispositivos IoT e a nuvem. É o principal ponto de coordenação para o funcionamento da solução de aprendizagem automática IoT Edge.

* O IoT Hub utiliza rotas para direcionar os dados de entrada de dispositivos IoT para outros serviços a jusante. Vamos aproveitar as rotas do IoT Hub para enviar dados do dispositivo para o Azure Storage. No Azure Storage, os dados do dispositivo são consumidos pela Azure Machine Learning para treinar o nosso restante classificador de vida útil (RUL).

* Mais tarde no tutorial, usaremos o IoT Hub para configurar e gerir o nosso dispositivo Azure IoT Edge.

Nesta secção, você usa um script para criar um hub Azure IoT e uma conta de Armazenamento Azure. Em seguida, no portal Azure, configura uma rota que encaminha os dados recebidos pelo hub para um recipiente de Armazenamento Azure. Estes passos demoram cerca de 10 minutos a ser concluídos.

1. Ligue-se ao VM de desenvolvimento, abra uma janela PowerShell e navegue até ao diretório **IoTHub.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Executar o roteiro da criação. Use os mesmos valores para iD de subscrição, localização e grupo de recursos como fez ao criar o VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Ser-lhe-á pedido que inicie sessão no Azure.
    * O script confirma a informação para a criação da sua conta Hub e Armazenamento. Pressione **y** ou **Enter** para continuar.

O script demorará cerca de dois minutos a ser executado. Uma vez concluído, o script produz o nome do hub IoT e da conta de armazenamento.

## <a name="review-route-to-storage-in-iot-hub"></a>Rota de revisão para armazenamento no IoT Hub

Como parte da criação do hub IoT, o script que fizemos na secção anterior também criou um ponto final personalizado e uma rota. As rotas do IoT Hub consistem numa expressão de consulta e num ponto final. Se uma mensagem corresponder à expressão, os dados são enviados ao longo do percurso até ao ponto final associado. Os pontos finais podem ser Centros de Eventos, Filas de Autocarros de Serviço e Tópicos. Neste caso, o ponto final é um recipiente de bolhas numa conta de armazenamento. Vamos usar o portal Azure para rever a rota criada pelo nosso script.

1. Abra o [portal Azure](https://portal.azure.com) e vá ao grupo de recursos que está a usar para este tutorial.

1. Na lista de recursos, selecione o Hub IoT que o script criou. Terá um nome terminando com caracteres aleatórios como `IotEdgeAndMlHub-jrujej6de6i7w` .

1. A partir do menu do painel esquerdo, em **Mensagens,** selecione **Message roting**.

1. Na página **de encaminhamento de mensagens,** selecione o **separador pontos finais personalizados.**

1. Expandir a secção **de Armazenamento:**

   ![Verifique que turbofanDeviceStorage está na lista de pontos finais personalizados](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Vemos **que turbofanDeviceStorage** está na lista de pontos finais personalizados. Note as seguintes características sobre este ponto final:

   * Aponta para o recipiente de armazenamento de bolhas que criou, indicado `devicedata` pelo **nome do contentor.**
   * O seu **formato Filename** tem a partição como o último elemento do nome. Achamos que este formato é mais conveniente para as operações de ficheiros que faremos com os Cadernos Azure mais tarde no tutorial.
   * O seu **estatuto** deve ser saudável.

1. Selecione o separador **Rotas**.

1. Selecione a rota denominada **turbofanDeviceDataToStorage**.

1. Na página de detalhes das **Rotas,** note que o ponto final da rota é o ponto final **turbofanDeviceStorage.**

   ![Rever detalhes sobre a rota turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Veja a **consulta de encaminhamento,** que está definida como **verdadeira.** Esta definição significa que todas as mensagens de telemetria do dispositivo corresponderão a esta rota; e, portanto, todas as mensagens serão enviadas para o ponto final **turbofanDeviceStorage.**

1. Como não foram feitas edições, fecha esta página.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criámos um Hub IoT e configuramos uma rota para uma conta de Armazenamento Azure. Em seguida, enviaremos dados de um conjunto de dispositivos simulados através do IoT Hub para a conta de armazenamento. Mais tarde no tutorial, depois de configurarmos o nosso dispositivo e módulos IoT Edge, vamos revisitar rotas e olhar um pouco mais para a consulta de encaminhamento.

Para obter mais informações sobre os passos abordados nesta parte do tutorial de Machine Learning on IoT Edge, consulte:

* [Noções básicas do IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurar o encaminhamento de mensagens com ioT Hub](../iot-hub/tutorial-routing.md)
* [Criar um hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md)

Continue até ao próximo artigo para criar um dispositivo simulado para monitorizar.

> [!div class="nextstepaction"]
> [Gerar dados de dispositivos](tutorial-machine-learning-edge-03-generate-data.md)
