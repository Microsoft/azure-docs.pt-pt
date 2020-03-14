---
title: 'Tutorial: Configurar o ambiente - Machine Learning on Azure IoT Edge'
description: 'Tutorial: Prepare o seu ambiente para o desenvolvimento e implementação de módulos para aprendizagem automática no limite.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296812"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Criar um ambiente para aprendizagem automática em IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Este artigo ajuda-o a preparar o seu ambiente para desenvolvimento e implantação. Primeiro, instale uma máquina de desenvolvimento com todas as ferramentas necessárias. Em seguida, crie os recursos em nuvem necessários em Azure.

## <a name="set-up-the-development-vm"></a>Configurar o VM de desenvolvimento

Este passo é normalmente realizado por um desenvolvedor de nuvem. Parte do software também pode ser útil para um cientista de dados.

Criámos um script PowerShell que cria uma máquina virtual Azure com muitos dos pré-requisitos já configurados. O VM que criamos precisa de ser capaz de lidar com a [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), razão pela qual escolhemos uma [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) tamanho da máquina.

O VM de desenvolvimento será criado com:

* Windows 10
* [Chocolate](https://chocolatey.org/)
* [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Git Credential Manager para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK .NET Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Extensões de código VS](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

O VM do desenvolvedor não é estritamente necessário – todas as ferramentas de desenvolvimento podem ser executadas numa máquina local. No entanto, recomendamos vivamente a utilização do VM para garantir um nível de igualdade de condições.

Leva cerca de 30 minutos para criar e configurar a máquina virtual.

1. Clone ou descarregue o repositório de amostras [machine learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) para o seu computador local.

1. Abra a PowerShell como administrador e navegue para o diretório **\IoTEdgeAndMlSample\DevVM** localizado sob o diretório raiz onde descarregou o código. Referimo-nos ao diretório de raiz da sua fonte como `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   O diretório DevVM contém os ficheiros necessários para criar uma máquina virtual Azure adequada para completar este tutorial.

1. Executar o seguinte comando para permitir a execução de scripts. Escolha **Sim a Todos** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Executar Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Quando solicitado, forneça as seguintes informações:

    * **ID de subscrição Azure**: O seu ID de subscrição, que pode ser encontrado em [Assinaturas Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal.
    * **Nome do Grupo de Recursos**: O nome de um novo ou existente grupo de recursos em Azure.
    * **Localização**: Escolha um local Azure onde a máquina virtual será criada. Por exemplo, "West US 2" ou "North Europe". Para mais informações, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **Nome de utilizador**: Forneça um nome memorável para a conta de administrador para o VM.
    * **Palavra-passe**: Definir uma palavra-passe para a conta de administrador para o VM.

   O script funciona durante vários minutos enquanto executa os seguintes passos:

    1. Instala o [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Pede-lhe que assine o Azure.
    1. Confirma a informação para a criação do seu VM. Pressione **y** ou **Enter** para continuar.
    1. Cria o grupo de recursos se não existir.
    1. Implanta a máquina virtual.
    1. Ativa o Hyper-V no VM.
    1. Instala a necessidade de desenvolvimento do software e clona o repositório da amostra.
    1. Reinicia o VM.
    1. Cria um ficheiro RDP no seu ambiente de trabalho para ligar ao VM.

   Se for solicitado que o nome do VM o reinicie, pode copiar o seu nome a partir da saída do script. A saída também mostra o caminho para o ficheiro RDP para a ligação ao VM.

### <a name="set-auto-shutdown-schedule"></a>Definir horário de paragem automática

Para ajudá-lo a reduzir os custos, o VM de desenvolvimento foi criado com um calendário de paragem automática que está definido para 1900 PST. Poderá ter de atualizar esta definição dependendo da sua localização e horário. Para atualizar o calendário de encerramento:

1. No portal Azure, navegue para o VM que o guião criou.

1. A partir do menu do painel esquerdo, em **Operações,** selecione **a paragem automática**.

1. Ajuste a **paragem programada** e o **fuso horário** conforme desejado e selecione **Guardar**.

## <a name="connect-to-the-development-vm"></a>Ligue-se ao vm de desenvolvimento

Agora que criámos um VM, precisamos de terminar a instalação do software necessário para completar o tutorial.

1. Clique duas vezes no ficheiro RDP que o script criou no seu ambiente de trabalho.

1. Será-lhe apresentado um diálogo dizendo que o editor da ligação remota é desconhecido. Isto é aceitável, por isso selecione **Connect**.

1. Forneça a palavra-passe do administrador que forneceu para criar o VM e clique EM **OK**.

1. Será solicitado a aceitar o certificado para o VM. Selecione **Sim**.

## <a name="install-visual-studio-code-extensions"></a>Instalar extensões de Código de Estúdio Visual

Agora que ligou à máquina de desenvolvimento, adicione algumas extensões úteis ao Visual Studio Code para facilitar a experiência de desenvolvimento.

1. Ligue-se ao vm de desenvolvimento, abra uma janela PowerShell e navegue para o diretório **C:\source\IoTEdgeAndMlSample\DevVM.** Este diretório foi criado pelo guião que criou o VM.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Executar o seguinte comando para permitir a execução de scripts. Escolha **Sim a Todos** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Executar o script de extensões de extensões do Código do Estúdio Visual.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. O script será executado durante alguns minutos instalando extensões de código VS:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar hub e armazenamento ioT

Estes passos são normalmente realizados por um desenvolvedor de nuvens.

O Azure IoT Hub é o coração de qualquer aplicação IoT, uma vez que lida com uma comunicação segura entre dispositivos IoT e a nuvem. É o principal ponto de coordenação para o funcionamento da solução de machine learning IoT Edge.

* O IoT Hub utiliza rotas para direcionar os dados de entrada de dispositivos IoT para outros serviços a jusante. Vamos aproveitar as rotas do IoT Hub para enviar dados do dispositivo para o Armazenamento Azure. No Armazenamento Azure, os dados do dispositivo são consumidos pela Azure Machine Learning para treinar o nosso restante classificador de vida útil (RUL).

* Mais tarde no tutorial, usaremos o IoT Hub para configurar e gerir o nosso dispositivo Azure IoT Edge.

Nesta secção, você usa um script para criar um hub Azure IoT e uma conta de Armazenamento Azure. Em seguida, no portal Azure, configura uma rota que reencaminha os dados recebidos pelo hub para um recipiente de armazenamento Azure. Estes passos demoram cerca de 10 minutos a ser concluídos.

1. Ligue-se ao vm de desenvolvimento, abra uma janela PowerShell e navegue para o diretório **IoTHub.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Executa o guião da criação. Use os mesmos valores para id de subscrição, localização e grupo de recursos como você fez ao criar o VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Ser-lhe-á pedido que inicie sessão no Azure.
    * O script confirma a informação para a criação da sua conta Hub e Armazenamento. Pressione **y** ou **Enter** para continuar.

O guião leva cerca de dois minutos para correr. Uma vez concluído, o script produz o nome do hub IoT e a conta de armazenamento.

## <a name="review-route-to-storage-in-iot-hub"></a>Rever rota para armazenamento em IoT Hub

Como parte da criação do hub IoT, o guião que executamos na secção anterior também criou um ponto final personalizado e uma rota. As rotas do IoT Hub consistem numa expressão de consulta e num ponto final. Se uma mensagem corresponder à expressão, os dados são enviados ao longo do percurso até ao ponto final associado. Os pontos finais podem ser Hubs de Eventos, Filas de Ônibus de Serviço e Tópicos. Neste caso, o ponto final é um recipiente de bolha numa conta de armazenamento. Vamos usar o portal Azure para rever a rota criada pelo nosso script.

1. Abra o [portal Azure](https://portal.azure.com) e vá ao grupo de recursos que está a usar para este tutorial.

1. Na lista de recursos, selecione o IoT Hub que o script criou. Terá um nome terminando com personagens aleatórios como `IotEdgeAndMlHub-jrujej6de6i7w`.

1. A partir do menu do painel esquerdo, em **Mensagens,** selecione **o encaminhamento de mensagens**.

1. Na página de **encaminhamento de Mensagens,** selecione o separador **de pontos finais Personalizado.**

1. Expandir a secção **de Armazenamento:**

   ![Verifique que o turbofanDeviceStorage está na lista de pontos finais personalizados](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Vemos **que o turbofanDeviceStorage** está na lista de pontos finais personalizados. Note as seguintes características sobre este ponto final:

   * Aponta para o recipiente de armazenamento blob que criou com o nome `devicedata` indicado pelo **nome contentor**.
   * O seu **formato Filename** tem a partição como último elemento no nome. Achamos que este formato é mais conveniente para as operações de arquivo que faremos com os Cadernos Azure mais tarde no tutorial.
   * O seu **estado** deve ser saudável.

1. Selecione o separador **Rotas.**

1. Selecione a rota chamada **turbofanDeviceDataToStorage**.

1. Na página de detalhes das **Rotas,** note que o ponto final da rota é o ponto final do **turbofanDeviceStorage.**

   ![Rever detalhes sobre a rota turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Olhe para a **consulta de Routing,** que está definida para **ser verdade.** Esta definição significa que todas as mensagens de telemetria do dispositivo corresponderão a esta rota; e, portanto, todas as mensagens serão enviadas para o ponto final do **turbofanDeviceStorage.**

1. Como não foram feitas edificações, basta fechar esta página.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criámos um Hub IoT e configuramos uma rota para uma conta de Armazenamento Azure. Em seguida, enviaremos dados de um conjunto de dispositivos simulados através do IoT Hub para a conta de armazenamento. Mais tarde no tutorial, depois de configurarmos o nosso dispositivo e módulos IoT Edge, vamos revisitar rotas e olhar um pouco mais para a consulta de encaminhamento.

Para obter mais informações sobre os passos abordados nesta parte do tutorial de Machine Learning no IoT Edge, consulte:

* [Noções básicas do IoT do Azure](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configure o encaminhamento de mensagens com o Hub IoT](../iot-hub/tutorial-routing.md)
* [Crie um hub IoT usando o portal Azure](../iot-hub/iot-hub-create-through-portal.md)

Continue para o próximo artigo para criar um dispositivo simulado para monitorizar.

> [!div class="nextstepaction"]
> [Gerar dados do dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
