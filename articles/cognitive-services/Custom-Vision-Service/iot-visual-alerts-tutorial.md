---
title: 'Tutorial: exemplo de alertas visuais de IoT'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usa Visão Personalizada com um dispositivo IoT para reconhecer e relatar estados visuais do feed de vídeo de uma câmera.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: pafarley
ms.openlocfilehash: b19f5a4f4f61285bc7b1a30073ea7d33d95420e0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "73519401"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Tutorial: usar Visão Personalizada com um dispositivo IoT para relatar estados visuais

Este aplicativo de exemplo ilustra como usar Visão Personalizada para treinar um dispositivo com uma câmera para detectar os Estados visuais. Você pode executar esse cenário de detecção em um dispositivo IoT usando um modelo ONNX exportado do serviço Visão Personalizada.

Um estado visual descreve o conteúdo de uma imagem: uma sala vazia ou uma sala com pessoas, um automóvel vazio ou um automóvel com um caminhão e assim por diante. Na imagem abaixo, você pode ver que o aplicativo detecta quando um banana ou uma Apple é colocado na frente da câmera.

![Animação de uma interface do usuário rotulando as frutas na frente da câmera](./media/iot-visual-alerts-tutorial/scoring.gif)

Este tutorial irá mostrar-lhe como:
> [!div class="checklist"]
> * Configure o aplicativo de exemplo para usar seus próprios recursos de Visão Personalizada e Hub IoT.
> * Use o aplicativo para treinar seu Visão Personalizada projeto.
> * Use o aplicativo para pontuar novas imagens em tempo real e enviar os resultados para o Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Este projeto precisa ser um projeto de classificação de imagem **compacta** , pois iremos exportar o modelo para ONNX mais tarde.
* Você também precisará [criar um recurso do Hub IOT](https://ms.portal.azure.com/#create/Microsoft.IotHub) no Azure.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com/downloads/)
* Opcionalmente, um dispositivo IoT que executa o Windows 10 IoT core versão 17763 ou superior. Você também pode executar o aplicativo diretamente do seu PC.
   * Para o Raspberry Pi 2 e 3, você pode configurar o Windows 10 diretamente do aplicativo de painel de IoT. Para outros dispositivos, como DrangonBoard, você precisará fazer o flash usando o [método eMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Se precisar de ajuda para configurar um novo dispositivo, consulte [configurando seu dispositivo](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) na documentação do Windows IOT.

## <a name="about-the-visual-alerts-app"></a>Sobre o aplicativo de alertas visuais

O aplicativo de alertas visuais do IoT é executado em um loop contínuo, alternando entre quatro Estados diferentes, conforme apropriado:

* **Sem modelo**: um estado de não op. O aplicativo será suspenso continuamente por um segundo e verificará a câmera.
* **Capturando imagens de treinamento**: nesse estado, o aplicativo captura uma imagem e a carrega como uma imagem de treinamento para o projeto de visão personalizada de destino. Em seguida, o aplicativo é suspenso por 500 MS e repete a operação até que o número alvo do conjunto de imagens seja capturado. Em seguida, ele dispara o treinamento do modelo de Visão Personalizada.
* **Aguardando modelo treinado**: nesse estado, o aplicativo chama a API de visão personalizada a cada segundo para verificar se o projeto de destino contém uma iteração treinada. Quando ele encontra um, ele baixa o modelo de ONNX correspondente em um arquivo local e alterna para o estado de **Pontuação** .
* **Pontuação**: nesse estado, o aplicativo usa o Windows ml para avaliar um único quadro da câmera em relação ao modelo ONNX local. A classificação de imagem resultante é exibida na tela e enviada como uma mensagem para o Hub IoT. O aplicativo então é suspenso por um segundo antes de Pontuação de uma nova imagem.

## <a name="understand-the-code-structure"></a>Entender a estrutura de código

Os arquivos a seguir tratam da funcionalidade principal do aplicativo.

| Ficheiro | Descrição |
|-------------|-------------|
| [MainPage. XAML](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Esse arquivo define a interface do usuário XAML. Ele hospeda o controle de câmera da Web e contém os rótulos usados para atualizações de status.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Esse código controla o comportamento da interface do usuário XAML. Ele contém o código de processamento da máquina de estado.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Essa classe é um wrapper que manipula a integração com o Serviço de Visão Personalizada.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Essa classe é um wrapper que manipula a integração com o Windows ML para carregar o modelo ONNX e as imagens de pontuação em relação a ele.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Essa classe é um wrapper que lida com a integração com o Hub IoT para carregar os resultados de pontuação no Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Configurar o aplicativo de alertas visuais

Siga estas etapas para obter o aplicativo de alertas visuais de IoT em execução em seu PC ou dispositivo IoT.

1. Clone ou baixe o [exemplo de IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) no github.
1. Abra a solução _IoTVisualAlerts. sln_ no Visual Studio
1. Integre seu projeto de Visão Personalizada:
    1. No script _CustomVision\CustomVisionServiceWrapper.cs_ , atualize a variável `ApiKey` com sua chave de treinamento.
    1. Em seguida, atualize a variável `Endpoint` com a URL do ponto de extremidade associada à sua chave.
    1. Atualize a variável `targetCVSProjectGuid` com a ID correspondente do projeto Visão Personalizada que você deseja usar. 
1. Configurar o recurso do Hub IoT:
    1. No script _IoTHub\IotHubWrapper.cs_ , atualize a variável `s_connectionString` com a cadeia de conexão apropriada para seu dispositivo. 
    1. Na portal do Azure, carregue a instância do Hub IoT, clique em **dispositivos IOT** em **gerenciadores**, selecione no dispositivo de destino (ou crie um, se necessário) e localize a cadeia de conexão na **cadeia de conexão primária**. A cadeia de caracteres conterá o nome do Hub IoT, a ID do dispositivo e a chave de acesso compartilhado; Ele tem o seguinte formato: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Executar a aplicação

Se você estiver executando o aplicativo em seu computador, selecione **computador local** para o dispositivo de destino no Visual Studio e selecione **x64** ou **x86** para a plataforma de destino. Em seguida, pressione F5 para executar o programa. O aplicativo deve iniciar e exibir o feed ao vivo da câmera e uma mensagem de status.

Se estiver implantando em um dispositivo IoT com um processador ARM, você precisará selecionar **ARM** como a plataforma de destino e a **máquina remota** como o dispositivo de destino. Forneça o endereço IP do seu dispositivo quando solicitado (ele deve estar na mesma rede que seu PC). Você pode obter o endereço IP do aplicativo padrão IoT do Windows depois de inicializar o dispositivo e conectá-lo à rede. Pressione F5 para executar o programa.

Quando você executa o aplicativo pela primeira vez, ele não terá nenhum conhecimento dos Estados visuais. Ele exibirá uma mensagem de status informando que nenhum modelo está disponível. 

## <a name="capture-training-images"></a>Capturar imagens de treinamento

Para configurar um modelo, você precisa colocar o aplicativo no estado **capturando imagens de treinamento** . Execute uma das seguintes etapas:
* Se você estiver executando o aplicativo no PC, use o botão no canto superior direito da interface do usuário.
* Se você estiver executando o aplicativo em um dispositivo IoT, chame o método `EnterLearningMode` no dispositivo por meio do Hub IoT. Você pode chamá-lo por meio da entrada de dispositivo no menu do Hub IoT na portal do Azure ou com uma ferramenta como o [Hub iot Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
Quando o aplicativo entra no estado **capturando imagens de treinamento** , ele capturará cerca de duas imagens a cada segundo até atingir o número de imagens de destino. Por padrão, essas são 30 imagens, mas você pode definir esse parâmetro passando o número desejado como um argumento para o método de Hub IoT `EnterLearningMode`. 

Enquanto o aplicativo estiver capturando imagens, você deve expor a câmera aos tipos de Estados visuais que deseja detectar (por exemplo, uma sala vazia, uma sala com pessoas, uma mesa vazia, uma escrivaninha com um caminhão de brinquedos e assim por diante).

## <a name="train-the-custom-vision-model"></a>Treinar o modelo de Visão Personalizada

Depois que o aplicativo terminar de capturar as imagens, ele as carregará e alternará para o estado **aguardando o modelo treinado** . Neste ponto, você precisa ir para o portal de [visão personalizada](https://www.customvision.ai/) e criar um modelo com base nas novas imagens de treinamento. A animação a seguir mostra um exemplo desse processo.

![Animação: marcação de várias imagens de bananas](./media/iot-visual-alerts-tutorial/labeling.gif)

Para repetir esse processo com seu próprio cenário:

1. Entre no portal de [visão personalizada](http://customvision.ai).
1. Localize seu projeto de destino, que agora deve ter todas as imagens de treinamento carregadas pelo aplicativo.
1. Para cada Estado do Visual que você deseja identificar, selecione as imagens apropriadas e aplique a marca manualmente.
    * Por exemplo, se seu objetivo for distinguir entre uma sala vazia e uma sala com pessoas, é recomendável marcar cinco ou mais imagens com pessoas como uma nova classe, **pessoas**e marcando cinco ou mais imagens sem pessoas como a marca **negativa** . Isso ajudará o modelo a diferenciar entre os dois Estados.
    * Como outro exemplo, se seu objetivo é aproximar o quão completo é uma prateleira, você pode usar marcas como **EmptyShelf**, **PartiallyFullShelf**e **FullShelf**.
1. Quando tiver terminado, selecione o botão **treinar** .
1. Quando o treinamento for concluído, o aplicativo detectará que uma iteração treinada está disponível. Ele iniciará o processo de exportar o modelo treinado para ONNX e baixá-lo para o dispositivo.

## <a name="use-the-trained-model"></a>Usar o modelo treinado

Depois que o aplicativo baixar o modelo treinado, ele mudará para o estado de **Pontuação** e iniciará as imagens de pontuação da câmera em um loop contínuo.

Para cada imagem capturada, o aplicativo exibirá a marca superior na tela. Se ele não reconhecer o estado do Visual, ele não exibirá **nenhuma correspondência**). O aplicativo também envia essas mensagens para o Hub IoT e, se houver uma classe sendo detectada, a mensagem incluirá o rótulo, a pontuação de confiança e uma propriedade chamada `detectedClassAlert`, que pode ser usada por clientes do Hub IoT interessados em fazer o roteamento rápido de mensagens com base na Prop rties.

Além disso, o exemplo usa uma [biblioteca do Sense Hat](https://github.com/emmellsoft/RPi.SenseHat) para detectar quando ele está em execução em um PI Raspberry com uma unidade do Sense Hat, para que possa usá-lo como uma exibição de saída, definindo todas as luzes de exibição como vermelho sempre que detectar uma classe e em branco quando ela não detectar nada.

## <a name="reuse-the-app"></a>Reutilizar o aplicativo

Se você quiser redefinir o aplicativo de volta para seu estado original, poderá fazer isso clicando no botão no canto superior direito da interface do usuário ou invocando o método `DeleteCurrentModel` por meio do Hub IoT.

A qualquer momento, você pode repetir a etapa de carregar imagens de treinamento clicando no botão de interface do usuário na parte superior direita ou chamando o método de `EnterLearningMode` novamente.

Se você estiver executando o aplicativo em um dispositivo e precisar recuperar o endereço IP novamente (para estabelecer uma conexão remota por meio do [cliente remoto do Windows IOT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), por exemplo), você pode chamar o método `GetIpAddress` por meio do Hub IOT.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua seu projeto Visão Personalizada se não quiser mais mantê-lo. No [site visão personalizada](https://customvision.ai), navegue até **projetos** e selecione a lixeira em seu novo projeto.

![Captura de tela de um painel rotulado meu novo projeto com um ícone de lixeira](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura e executou um aplicativo que detecta informações de estado visual em um dispositivo IoT e envia os resultados para o Hub IoT. Em seguida, explore ainda mais o código-fonte ou faça uma das modificações sugeridas abaixo.

> [!div class="nextstepaction"]
> [Exemplo de IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Adicione um método de Hub IoT para alternar o aplicativo diretamente para o estado **aguardando modelo treinado** . Dessa forma, você pode treinar o modelo com imagens que não são capturadas pelo próprio dispositivo e, em seguida, enviar o novo modelo para o dispositivo no comando.
* Siga o tutorial [Visualizar dados de sensor em tempo real](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) para criar um painel de Power bi para visualizar os alertas do Hub IOT enviados pelo exemplo.
* Siga o tutorial de [monitoramento remoto de IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) para criar um aplicativo lógico que responda aos alertas do Hub IOT quando os Estados visuais forem detectados.