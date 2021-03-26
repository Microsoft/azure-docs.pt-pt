---
title: Implementar para uma borda IoT para Linux no Windows - Azure
description: Este artigo fornece orientações sobre como implementar para um IoT Edge para Linux no dispositivo Windows.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d5c3d89ae7447b062714ad90be117a6426a39581
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561088"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Implementar para um IoT Edge para Linux no dispositivo Windows (EFLOW)

Neste artigo, você vai aprender a implementar Live Video Analytics em um dispositivo de borda que tem [IoT Edge para Linux no Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md). Uma vez terminado seguindo os passos deste documento, poderá executar um [gráfico mediático](media-graph-concept.md) que detete movimento num vídeo e emita tais eventos para o hub IoT na nuvem. Em seguida, pode alternar o gráfico de mídia para cenários avançados e levar a energia do Live Video Analytics para o seu dispositivo IoT Edge baseado no Windows.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma conta Azure que tem uma subscrição ativa. [Crie uma conta gratuita se](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ainda não tiver uma.

    > [!NOTE]
    > Você precisará de uma subscrição Azure com permissões para criar principais serviços **(a função do proprietário** fornece isso). Se não tiver as permissões certas, contacte o administrador da sua conta para lhe conceder as permissões certas.
* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento. Certifique-se de que tem a [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Leia [o que é EFLOW](../../iot-edge/iot-edge-for-linux-on-windows.md).

## <a name="deployment-steps"></a>Passos da implementação

O seguinte retrata o fluxo global do documento e em 5 passos simples deve estar configurado para executar Live Video Analytics num dispositivo Windows que tenha EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT Edge para Linux no diagrama do Windows (EFLOW)":::

1. [Instale o EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md) no seu dispositivo Windows. 

    1. Se estiver a utilizar o seu PC do Windows, então na página inicial do [Windows Admin Center,](/windows-server/manage/windows-admin-center/overview) na lista de ligações, verá uma ligação de anfitrião local representando o PC onde executa o Windows Admin Center. 
    1. Quaisquer servidores adicionais, computadores ou clusters que gere também aparecerão aqui.
    1. Pode utilizar o Windows Admin Center para instalar e gerir o Azure EFLOW quer no seu dispositivo local, quer em dispositivos geridos à distância. Neste guia, a ligação de anfitrião local serviu como o dispositivo alvo para a implementação do Azure IoT Edge para o Linux no Windows. Por isso, vê-se o local também listado como um dispositivo IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Etapas de implementação - centro de administração de janelas":::
1. Clique no dispositivo IoT Edge para ligar a ele e deverá ver um separador Overview e Command Shell. O separador de identificação de comando é onde pode emitir comandos para o seu dispositivo de borda.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Etapas de implementação - Azure IoT Edge Manager":::
1. Vá para a concha de comando e escreva no seguinte comando:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    O módulo Live Video Analytics funciona no dispositivo edge com [contas de utilizadores locais](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)não privilegiadas . Além disso, [precisa de certas pastas locais](deploy-iot-edge-device.md#granting-permissions-to-device-storage) para armazenar dados de configuração de aplicações. Finalmente, para este guia estamos aproveitando um [simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) que transmite um feed de vídeo em tempo real para o módulo LVA para análise. Este simulador toma como ficheiros de vídeo pré-gravados de um diretório de entrada. 
    
    O script do dispositivo de preparação utilizado acima automatiza estas tarefas, para que possa executar um comando e ter todas as pastas de entrada e configuração relevantes, ficheiros de entrada de vídeo e contas de utilizador com privilégios criados sem problemas. Uma vez que o comando termine com sucesso, deverá ver as seguintes pastas criadas no seu dispositivo de borda. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Note os ficheiros de vídeo (*.mkv) na pasta /home/lvaedgeuser/samples/input, que servem como ficheiros de entrada a serem analisados. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Análise":::
1. Agora que tem o dispositivo de borda configurado, registado no hub e funcionando com sucesso com as estruturas de pastas corretas criadas, o próximo passo é configurar os seguintes recursos Azure adicionais e implementar o módulo LVA. 

    * Conta de armazenamento
    * Conta Azure Media Services

    Para isso, recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos necessários na sua subscrição Azure. Para tal, siga estes passos:

    1. Abra o [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Casca de nuvem":::
    1. Se estiver a utilizar a Cloud Shell pela primeira vez, será solicitado que selecione uma subscrição para criar uma conta de armazenamento e uma partilha de Ficheiros Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da conta que o script criará para utilizar com a sua conta Azure Media Services.
    1. No menu suspenso no lado esquerdo da janela Cloud Shell, selecione Bash como seu ambiente.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Ambiente de bash":::
    1. Execute o seguinte comando.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Certifique-se de escolher **Y** quando solicitado para escolher o seu próprio dispositivo de borda como um dispositivo IoT Edge, uma vez que criou o dispositivo e o IoT Hub mais cedo. Também será solicitado para o seu nome IoT Hub e id do dispositivo IoT Edge. Pode obter ambos iniciando sessão no portal Azure e clicando no seu IoT Hub e, em seguida, indo para a opção IoT Edge na sua lâmina do portal IoT Hub.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Ver dispositivos IoT Edge":::

    Uma vez terminado, pode voltar a entrar na concha do comando do dispositivo IoT Edge e executar o seguinte comando.
    
    `sudo iotedge list`
    
    Deve ver os seguintes quatro módulos implantados e a funcionar no seu dispositivo de borda. Por favor, note que o script de criação de recursos implementa o módulo LVA juntamente com módulos IoT Edge (edgeAgent e edgeHub) e um módulo simulador RTSP para fornecer o feed de vídeo RTSP simulado.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Ver estado":::
1. Com os módulos implantados e configurados, está pronto para executar o seu primeiro gráfico de mídia LVA no EFLOW. Pode executar um gráfico simples de deteção de movimentos como abaixo e visualizar os resultados executando os seguintes passos:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Gráfico de deteção de movimentos":::

    1. [Configure](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) a extensão Azure IoT Tools.
    
        > [!Note]
        > Utilize o seguinte caminho: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Desative a topologia, instantie um gráfico e ative-o através destas [chamadas de métodos diretos](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Observe os resultados](get-started-detect-motion-emit-events-quickstart.md#observe-results) no Hub.
    1. Invocar [métodos de limpeza.](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)
    1. Elimine os seus recursos se não for necessário mais.

        > [!IMPORTANT]
        > Os recursos não descaídos podem ainda ser ativos e incorrer em custos Azure.
    
## <a name="next-steps"></a>Passos seguintes

* Tente a deteção de movimentos juntamente com a gravação de vídeos relevantes na nuvem. Siga os passos do movimento de [deteção, grave os videoclips para o](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) Quickstart dos Media Services.
* Executar [IA em Live Video](use-your-model-quickstart.md#overview) (pode saltar a configuração pré-requisito como já foi feito acima)
* Utilize a nossa [extensão vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) para visualizar gráficos de mídia adicionais.
* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera)  que suporte o RTSP em vez de utilizar o simulador RTSP. Pode encontrar câmaras IP que suportam RTSP na página de [produtos conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.