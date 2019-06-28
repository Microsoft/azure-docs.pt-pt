---
title: 'Início rápido: Sintetizar a conversão de voz, Unity - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de texto para voz com o Unity e o SDK de voz para Unity (Beta). Quando terminar, pode sintetizar a conversão de voz de texto em tempo real em orador do seu dispositivo.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467489"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>Início rápido: Sintetizar fala com o SDK de voz para Unity (Beta)

Também estão disponíveis para inícios Rápidos [reconhecimento de fala](quickstart-csharp-unity.md).

Utilize este guia para criar um aplicativo de texto para discurso usando [Unity](https://unity3d.com/) e o SDK de voz para Unity (Beta).
Quando terminar, pode sintetizar a conversão de voz de texto em tempo real em orador do seu dispositivo.
Se não estiver familiarizado com o Unity, é recomendado para estudar a [Manual do usuário do Unity](https://docs.unity3d.com/Manual/UnityManual.html) antes de iniciar o desenvolvimento de aplicativos.

> [!NOTE]
> O SDK de voz para Unity está atualmente na versão beta.
> Suporta a área de trabalho do Windows (x86 e x64) ou a plataforma Universal do Windows (x86, x64, ARM/ARM64) e Android (x86, ARM32/64).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Unity 2018.3 ou posterior](https://store.unity.com/) com [2019.1 Unity, adicionando suporte a UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Para obter suporte ARM64, instalar o [ferramentas de compilação opcional para ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* Uma chave de subscrição para o serviço de voz. [Obter uma gratuitamente](get-started.md).

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

* Iniciar o Unity e, no **projetos** separador select **New**.
* Especifique **nome do projeto** como **csharp unity**, **modelo** como **3D** e escolher um local.
  Em seguida, selecione **criar projeto**.
* Depois de um pouco de tempo, a janela do Editor do Unity deve ser exibido.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de voz

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* O SDK de voz para Unity (Beta) é empacotado como um pacote de recurso do Unity (.unitypackage).
  Baixe-o a partir [aqui](https://aka.ms/csspeech/unitypackage).
* Importar o SDK de voz, selecionando **ativos** > **importar pacote** > **pacote personalizado**.
  Veja a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html) para obter detalhes.
* No Seletor de ficheiros, selecione o ficheiro de .unitypackage do SDK de voz que transferiu anteriormente.
* Certifique-se de que todos os ficheiros estão selecionados e clique em **importação**:

  ![Captura de ecrã do Unity Editor ao importar o pacote de recurso do Unity do SDK de voz](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Adicionar a interface do Usuário

Adicionamos uma interface de Usuário mínimo ao nosso cena, consiste num campo de entrada para introduzir o texto para síntese, um botão para síntese de fala do acionador e um campo de texto para exibir o resultado.

* Na [janela de hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por predefinição no lado esquerdo), uma cena de exemplo é mostrada que Unity criado com o novo projeto.
* Clique nas **Create** botão na parte superior da janela de hierarquia e selecione **interface do Usuário** > **campo de entrada**.
* Esta ação cria três objetos do jogo que pode ver na janela de hierarquia: uma **campo de entrada** objeto aninhado dentro de um **tela** objeto e um **EventSystem** objeto.
* [Navegue até a exibição de cenas](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que tenha uma boa visão da tela e o campo de entrada no [modo de exibição de cenas](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Clique nas **campo de entrada** objeto na janela de hierarquia para apresentar as definições no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
* Definir o **X de Pos** e **Pos Y** propriedades para **0**, por isso, o campo de entrada centra-se no meio da tela.
* Clique nas **Create** botão na parte superior da janela de hierarquia novamente e selecione **interface do Usuário** > **botão** para criar um botão.
* Clique nas **botão** objeto na janela de hierarquia para apresentar as definições no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
* Definir o **X de Pos** e **Pos Y** propriedades para **0** e **-48**e defina a **largura** e **Altura** as propriedades para **160** e **30** para se certificar de que o botão e o campo de entrada não se sobrepõem.
* Clique nas **Create** botão na parte superior da janela de hierarquia novamente e selecione **interface do Usuário** > **texto** para criar um campo de texto.
* Clique nas **texto** objeto na janela de hierarquia para apresentar as definições no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
* Definir o **X de Pos** e **Pos Y** propriedades para **0** e **80**e defina a **largura** e  **Altura** propriedades a serem **320** e **80** para se certificar de que o campo de texto e o campo de entrada não se sobrepõem.
* Clique nas **Create** botão na parte superior da janela de hierarquia novamente e selecione **áudio** > **origem áudio** para criar uma origem de áudio.

Quando tiver terminado, a interface do Usuário deve ter um aspeto semelhante a esta captura de ecrã:

[![Captura de ecrã da interface do usuário de início rápido no Editor do Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Na [janela Project](https://docs.unity3d.com/Manual/ProjectView.html) (por predefinição no canto inferior esquerdo), clique no **Create** botão e, em seguida, selecione  **C# script**. Nomeie o script `HelloWorld`.

1. Edite o script clicando nela.

   > [!NOTE]
   > Pode configurar o editor de código será iniciado sob **editar** > **preferências**, consulte o [Manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código com o seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localizar e substituir a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição de serviços de voz.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações no script.

1. Novamente no Unity Editor, o script tem de ser adicionado como um componente para um dos seus objetos do jogo.

   * Clique nas **tela** objeto na janela da hierarquia. Isso abrirá a definição no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
   * Clique nas **Add Component** botão no Inspetor de janela, em seguida, procure o script de HelloWorld criamos acima e adicioná-lo.
   * Tenha em atenção que o componente de Hello World tem quatro propriedades não inicializadas, **saída de texto**, **campo de entrada**, **falar botão** e **áudio origem**, que correspondam a propriedades públicas do `HelloWorld` classe.
     Para conectá-las, clique o selecionador de objetos (o ícone pequeno círculo para a direita da propriedade) e escolha os objetos de texto e o botão que criou anteriormente.

     > [!NOTE]
     > O campo de entrada e um botão também tem um objeto de texto aninhado. Certifique-se de que não acidentalmente escolhê-la para a saída de texto (ou mudar o nome os objetos de texto usando o campo de nome na janela de Inspetor para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar a aplicação no Editor do Unity

* Prima a **reproduzir** botão da barra de ferramentas do Editor do Unity (abaixo da barra de menu).

* Depois da aplicação é iniciada, introduza algum texto no campo de entrada e clique no botão. O texto é transmitido para os serviços de voz e sintetizado para conversão de voz, que desempenha na sua orador.

  [![Captura de ecrã do início rápido em execução na janela de jogo Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Verifique os [janela de consola](https://docs.unity3d.com/Manual/Console.html) para mensagens de depuração.

* Quando tiver terminado a voz para resumir, clique nas **reproduzir** botão da barra de ferramentas do Editor do Unity para parar a aplicação.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar esta aplicação

Esta aplicação também pode ser implementada para o Android, como uma aplicação autónoma do Windows ou uma aplicação de UWP.
Consulte a nossa [repositório de exemplo](https://aka.ms/csspeech/samples) na pasta de início rápido/csharp-unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar os tipos de voz](how-to-customize-voice-font.md)
- [Exemplos de voz de registo](record-custom-voice-samples.md)
