---
title: 'Tutorial: Amostra de alertas visuais IoT'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usa Custom Vision com um dispositivo IoT para reconhecer e reportar estados visuais a partir de um feed de vídeo de uma câmara.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ac7609d49631fb2ed16fa129f8dc4099cc166247
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769878"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Tutorial: Use visão personalizada com um dispositivo IoT para reportar estados visuais

Esta aplicação de amostras ilustra como usar a Custom Vision para treinar um dispositivo com uma câmara para detetar estados visuais. Pode executar este cenário de deteção num dispositivo IoT utilizando um modelo ONNX exportado.

Um estado visual descreve o conteúdo de uma imagem: um quarto vazio ou um quarto com pessoas, uma garagem vazia ou uma garagem com um caminhão, e assim por diante. Na imagem abaixo, pode ver a aplicação detetar quando uma banana ou uma maçã são colocadas em frente à câmara.

![Animação de um UI rotulando fruta em frente à câmera](./media/iot-visual-alerts-tutorial/scoring.gif)

Este tutorial irá mostrar-lhe como:
> [!div class="checklist"]
> * Configure a aplicação de amostra para utilizar os seus próprios recursos Custom Vision e IoT Hub.
> * Utilize a aplicação para treinar o seu projeto Custom Vision.
> * Utilize a aplicação para marcar novas imagens em tempo real e envie os resultados para o Azure.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Este projeto tem de ser um projeto de classificação de imagem **Compacto,** porque mais tarde vamos exportar o modelo para a ONNX.
* Também terá de [criar um recurso IoT Hub](https://ms.portal.azure.com/#create/Microsoft.IotHub) no Azure.
* [Estúdio Visual 2015 ou mais tarde](https://www.visualstudio.com/downloads/)
* Opcionalmente, um dispositivo IoT que executa a versão 17763 do Windows 10 IoT Core. Também pode executar a aplicação diretamente a partir do seu PC.
   * Para Raspberry Pi 2 e 3, pode configurar o Windows 10 diretamente a partir da aplicação IoT Dashboard. Para outros dispositivos como o DrangonBoard, terá de o mostrar utilizando o [método eMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Se precisar de ajuda para configurar um novo dispositivo, consulte a [configuração](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) do seu dispositivo na documentação do Windows IoT.

## <a name="about-the-visual-alerts-app"></a>Sobre a aplicação Alertas Visuais

A aplicação IoT Visual Alerts funciona em ciclo contínuo, alternando entre quatro estados diferentes, conforme apropriado:

* **No Model**: Um estado sem op. A aplicação irá continuar a dormir por um segundo e verificar a câmara.
* **Capturando Imagens**de Formação : Neste estado, a aplicação captura uma imagem e envia-a como uma imagem de treino para o projeto target Custom Vision. A aplicação dorme então durante 500 ms e repete a operação até que o número definido de imagens seja capturado. Depois despoleta o treino do modelo Visão Personalizada.
* **Waiting For Trained Model**: Neste estado, a aplicação chama a API de Visão Personalizada a cada segundo para verificar se o projeto-alvo contém uma iteração treinada. Quando encontra um, descarrega o modelo ONNX correspondente para um ficheiro local e muda para o estado **de Pontuação.**
* **Pontuação**: Neste estado, a aplicação utiliza o Windows ML para avaliar uma única moldura da câmara contra o modelo ONNX local. A classificação de imagem resultante é exibida no ecrã e enviada como mensagem para o IoT Hub. A aplicação dorme por um segundo antes de marcar uma nova imagem.

## <a name="understand-the-code-structure"></a>Compreender a estrutura do código

Os seguintes ficheiros tratam da funcionalidade principal da aplicação.

| Ficheiro | Descrição |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Este ficheiro define a interface de utilizador XAML. Acolhe o controlo da câmara web e contém as etiquetas utilizadas para atualizações de estado.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Este código controla o comportamento do XAML UI. Contém o código de processamento de máquinas do Estado.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Esta aula é um invólucro que lida com a integração com o Serviço de Visão Personalizada.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Esta classe é um invólucro que lida com a integração com o Windows ML para carregar o modelo ONNX e marcar imagens contra ele.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Esta classe é um invólucro que lida com a integração com o IoT Hub para carregar resultados de pontuação para o Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Configurar a aplicação Alertas Visuais

Siga estes passos para que a aplicação IoT Visual Alerts funcionasse no seu dispositivo PC ou IoT.

1. Clone ou descarregue a [amostra IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) no GitHub.
1. Abra a solução _IoTVisualAlerts.sln_ no Estúdio Visual
1. Integre o seu projeto Visão Personalizada:
    1. No script _CustomVision\CustomVisionServiceWrapper.cs,_ `ApiKey` atualize a variável com a sua chave de treino.
    1. Em seguida, atualize a `Endpoint` variável com o URL do ponto final associado à sua chave.
    1. Atualize `targetCVSProjectGuid` a variável com o ID correspondente do projeto Custom Vision que pretende utilizar. 
1. Configurar o recurso IoT Hub:
    1. No script _IoTHub\IotHubWrapper.cs,_ `s_connectionString` atualize a variável com a corda de ligação adequada para o seu dispositivo. 
    1. No portal Azure, carregue a sua instância IoT Hub, clique em **dispositivos IoT** em **Explorers,** selecione no seu dispositivo-alvo (ou crie um, se necessário), e encontre a cadeia de ligação sob a cadeia de **ligação primária**. A cadeia conterá o nome ioT hub, identificação do dispositivo e chave de acesso partilhada; tem o seguinte `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`formato: .

## <a name="run-the-app"></a>Executar a aplicação

Se estiver a executar a aplicação no seu PC, selecione **Local Machine** para o dispositivo-alvo no Estúdio Visual e selecione **x64** ou **x86** para a plataforma alvo. Em seguida, pressione F5 para executar o programa. A aplicação deve iniciar e exibir o feed ao vivo a partir da câmara e uma mensagem de estado.

Se estiver a implantar um dispositivo IoT com um processador ARM, terá de selecionar o **ARM** como plataforma-alvo e **a Máquina Remota** como dispositivo alvo. Forneça o endereço IP do seu dispositivo quando solicitado (deve estar na mesma rede que o seu PC). Pode obter o Endereço IP da aplicação predefinida do Windows IoT assim que iniciar o dispositivo e ligá-lo à rede. Pressione F5 para executar o programa.

Quando executar a aplicação pela primeira vez, não terá qualquer conhecimento de estados visuais. Mostrará uma mensagem de estado que não existe nenhum modelo disponível. 

## <a name="capture-training-images"></a>Capturar imagens de treino

Para configurar um modelo, é necessário colocar a app no estado de Imagens de **Formação de Captura.** Tome um dos seguintes passos:
* Se estiver a executar a aplicação no PC, utilize o botão no canto superior direito da UI.
* Se estiver a executar a aplicação num dispositivo `EnterLearningMode` IoT, ligue para o método do dispositivo através do IoT Hub. Pode chamá-lo através da entrada do dispositivo no menu IoT Hub no portal Azure, ou com uma ferramenta como [o IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
Quando a aplicação entrar no estado de Imagens de **Formação de Captura,** capturará cerca de duas imagens a cada segundo até atingir o número de imagens alvo. Por padrão, o alvo é de 30 imagens, mas pode definir este `EnterLearningMode` parâmetro passando o número desejado como argumento para o método IoT Hub. 

Enquanto a aplicação está a captar imagens, deve expor a câmara aos tipos de estados visuais que pretende detetar (por exemplo, um quarto vazio, um quarto com pessoas, uma mesa vazia, uma secretária com um caminhão de brinquedo, e assim por diante).

## <a name="train-the-custom-vision-model"></a>Treine o modelo Visão Personalizada

Uma vez que a aplicação termine de capturar imagens, irá carregá-las e depois mudar para o estado **do Modelo À Espera de Formação.** Neste ponto, você precisa ir ao [portal Custom Vision](https://www.customvision.ai/) e construir um modelo baseado nas novas imagens de treino. A seguinte animação mostra um exemplo deste processo.

![Animação: marcar várias imagens de bananas](./media/iot-visual-alerts-tutorial/labeling.gif)

Para repetir este processo com o seu próprio cenário:

1. Inscreva-se no [portal Visão Personalizada.](http://customvision.ai)
1. Encontre o seu projeto-alvo, que deverá agora ter todas as imagens de treino que a aplicação carregou.
1. Para cada estado visual que pretende identificar, selecione as imagens apropriadas e aplique manualmente a etiqueta.
    * Por exemplo, se o seu objetivo é distinguir entre uma sala vazia e uma sala com pessoas nela, recomendamos marcar cinco ou mais imagens com pessoas como uma nova classe, **People**, e marcar cinco ou mais imagens sem pessoas como a etiqueta **Negativa.** Isto ajudará o modelo a diferenciar entre os dois Estados.
    * Como outro exemplo, se o seu objetivo é aproximar o quão cheia está uma prateleira, então poderá utilizar tags como **EmptyShelf,** **ParcialmenteFullShelf**e **FullShelf**.
1. Quando terminar, selecione o botão **Train.**
1. Uma vez concluído o treino, a aplicação irá detetar que uma iteração treinada está disponível. Iniciará o processo de exportação do modelo treinado para a ONNX e transferi-lo-á para o dispositivo.

## <a name="use-the-trained-model"></a>Use o modelo treinado

Assim que a aplicação descarregar o modelo treinado, irá mudar para o estado **de Pontuação** e começar a marcar imagens a partir da câmara num ciclo contínuo.

Para cada imagem capturada, a aplicação apresentará a etiqueta superior no ecrã. Se não reconhecer o estado visual, não apresentará **Fósforos**. A aplicação também envia estas mensagens para o IoT Hub, e se houver uma classe a ser `detectedClassAlert`detetada, a mensagem incluirá o rótulo, a pontuação de confiança e uma propriedade chamada , que pode ser usada por clientes do IoT Hub interessados em fazer o encaminhamento de mensagens rápidas com base em propriedades.

Além disso, a amostra utiliza uma [biblioteca Sense HAT](https://github.com/emmellsoft/RPi.SenseHat) para detetar quando está a funcionar numa Raspberry Pi com uma unidade Sense HAT, para que possa usá-la como um visor de saída, definindo todas as luzes de exibição a vermelho sempre que deteta uma classe e em branco quando não deteta nada.

## <a name="reuse-the-app"></a>Reutilizar a app

Se quiser redefinir a aplicação para o seu estado original, pode fazê-lo clicando no botão no canto `DeleteCurrentModel` superior direito da UI, ou invocando o método através do IoT Hub.

Em qualquer momento, pode repetir o passo de carregar imagens de treino clicando no botão UI superior direito ou chamando novamente o `EnterLearningMode` método.

Se estiver a executar a aplicação num dispositivo e precisar de recuperar novamente o endereço IP (para estabelecer uma `GetIpAddress` ligação remota através do Cliente Remoto Windows [IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), por exemplo), pode ligar para o método através do IoT Hub.

## <a name="clean-up-resources"></a>Limpar recursos

Elimine o seu projeto Custom Vision se já não quiser mantê-lo. No site da [Custom Vision,](https://customvision.ai)navegue para **Projetos** e selecione o caixote do lixo no âmbito do seu novo projeto.

![Screenshot de um painel rotulado My New Project com um ícone de lata de lixo](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou e executou uma aplicação que deteta informações do estado visual num dispositivo IoT e envia os resultados para o IoT Hub. Em seguida, explore ainda mais o código de origem ou faça uma das modificações sugeridas abaixo.

> [!div class="nextstepaction"]
> [Amostra IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Adicione um método IoT Hub para mudar a aplicação diretamente para o estado do **Modelo De Espera treinado.** Desta forma, pode treinar o modelo com imagens que não são captadas pelo próprio dispositivo e, em seguida, empurrar o novo modelo para o dispositivo em comando.
* Siga o tutorial de dados de [sensores visualizar em tempo real](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) para criar um Power BI Dashboard para visualizar os alertas do IoT Hub enviados pela amostra.
* Siga o tutorial de [monitorização remota IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) para criar uma Aplicação Lógica que responda aos alertas do IoT Hub quando os estados visuais são detetados.
