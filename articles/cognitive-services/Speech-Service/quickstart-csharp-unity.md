---
title: 'Início rápido: Reconhecer a conversão de voz, Unity - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de voz em texto com o Unity e o SDK de voz para Unity (Beta). Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 3cedfaf1ae16c17026314fc24dbdc7bb11494caf
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020941"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Início rápido: Reconhecer a conversão de voz com o SDK de voz para Unity (Beta)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilize este guia para criar um aplicativo de voz em texto usando [Unity](https://unity3d.com/) e o SDK de voz para Unity (Beta).
Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
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
* Acesso ao microfone do seu computador.

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

Adicionamos uma interface de Usuário mínimo ao nosso cena, consiste num botão para disparar o reconhecimento de fala e um campo de texto para exibir o resultado.

* Na [janela de hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por predefinição no lado esquerdo), uma cena de exemplo é mostrada que Unity criado com o novo projeto.
* Clique nas **Create** botão na parte superior da janela de hierarquia e selecione **interface do Usuário** > **botão**.
* Esta ação cria três objetos do jogo que pode ver na janela de hierarquia: uma **botão** objeto aninhado dentro de um **tela** objeto e um **EventSystem** objeto.
* [Navegue até a exibição de cenas](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que tenha uma boa visão da tela e o botão no [modo de exibição de cenas](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Clique nas **botão** objeto na janela de hierarquia para apresentar as definições no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
* Definir o **X de Pos** e **Pos Y** propriedades para **0**, por isso, o botão centra-se no meio da tela.
* Clique nas **Create** botão na parte superior da janela de hierarquia novamente e selecione **interface do Usuário** > **texto** para criar um campo de texto.
* Clique nas **texto** objeto na janela de hierarquia para apresentar as definições no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
* Definir o **X de Pos** e **Pos Y** propriedades para **0** e **120**e defina a **largura** e **Altura** as propriedades para **240** e **120** para se certificar de que o campo de texto e o botão não se sobrepõem.

Quando tiver terminado, a interface do Usuário deve ter um aspeto semelhante a esta captura de ecrã:

[![Captura de ecrã da interface do usuário de início rápido no Editor do Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Na [janela Project](https://docs.unity3d.com/Manual/ProjectView.html) (por predefinição no canto inferior esquerdo), clique no **Create** botão e, em seguida, selecione  **C# script**. Nomeie o script `HelloWorld`.

1. Edite o script clicando nela.

   > [!NOTE]
   > Pode configurar o editor de código será iniciado sob **editar** > **preferências**, consulte o [Manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código com o seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localizar e substituir a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição de serviços de voz.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações no script.

1. Novamente no Unity Editor, o script tem de ser adicionado como um componente para um dos seus objetos do jogo.

   * Clique nas **tela** objeto na janela da hierarquia. Isso abrirá a definição no [Inspetor de janela](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por predefinição no lado direito).
   * Clique nas **Add Component** botão no Inspetor de janela, em seguida, procure o script de HelloWorld criamos acima e adicioná-lo.
   * Tenha em atenção que o componente de Hello World tem duas propriedades não inicializadas, **saída de texto** e **botão de Uperação iniciar**, que correspondam a propriedades públicas do `HelloWorld` classe.
     Para conectá-las, clique o selecionador de objetos (o ícone pequeno círculo para a direita da propriedade) e escolha os objetos de texto e o botão que criou anteriormente.

     > [!NOTE]
     > O botão também tem um objeto de texto aninhado. Certifique-se de que não acidentalmente escolhê-la para a saída de texto (ou mudar o nome de um dos objetos de texto usando o campo de nome na janela de Inspetor para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar a aplicação no Editor do Unity

* Prima a **reproduzir** botão da barra de ferramentas do Editor do Unity (abaixo da barra de menu).

* Depois da aplicação é iniciada, clique no botão e fala uma frase em inglês ou frase no microfone do seu computador. Sua voz é transmitido para os serviços de voz e transcrito para texto, que aparece na janela.

  [![Captura de ecrã do início rápido em execução na janela de jogo Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Verifique os [janela de consola](https://docs.unity3d.com/Manual/Console.html) para mensagens de depuração.

* Quando tiver terminado de reconhecimento de fala, clique nas **reproduzir** botão da barra de ferramentas do Editor do Unity para parar a aplicação.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar esta aplicação

Esta aplicação também pode ser implementada para o Android, como uma aplicação autónoma do Windows ou uma aplicação de UWP.
Consulte a nossa [repositório de exemplo](https://aka.ms/csspeech/samples) na pasta de início rápido/csharp-unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
