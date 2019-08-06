---
title: 'Início rápido: Reconhecer fala, serviço Unity-Speech'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de fala a texto com o Unity e o SDK de fala para Unity (beta). Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: jhakulin
ms.openlocfilehash: 1b6e60edd86cff2d657b562f05351e20571c0909
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815366"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Início rápido: Reconhecer a fala com o SDK de fala para Unity (beta)

Os guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de conversão de fala em texto usando o [Unity](https://unity3d.com/) e o SDK de fala para Unity (beta).
Quando terminar, você poderá conversar com seu dispositivo para transcrever a fala para o texto em tempo real.
Se você for novo no Unity, sugerimos que você estude o [manual do usuário do Unity](https://docs.unity3d.com/Manual/UnityManual.html) antes de desenvolver seu aplicativo.

> [!NOTE]
> O SDK de fala para o Unity está atualmente em beta.
> Ele dá suporte à área de trabalho do Windows (x86 e x64) ou Plataforma Universal do Windows (x86, x64, ARM/ARM64) e Android (x86, ARM32/64).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

- O [unity 2018,3 ou posterior](https://store.unity.com/) com o [Unity 2019,1 adicionando suporte para UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A versão 15,9 ou superior do Visual Studio 2017 também é aceitável.
  - Para obter suporte a ARM64, instale as [ferramentas de compilação opcionais para ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
- Acesso ao microfone do seu computador.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

1. Abra o Unity. Se você estiver usando o Unity pela primeira vez, a janela **Hub** *<version number>* do Unity será exibida. (Você também pode abrir o Hub do Unity diretamente para chegar a essa janela.)

   [![Janela do Hub do Unity](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecione **Novo**. A janela **criar um novo projeto com o Unity** *<version number>* é exibida.

   [![Criar um novo projeto no Hub do Unity](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Em **nome do projeto**, digite **Csharp-Unity**.
1. Em **modelos**, se **3D** ainda não estiver selecionado, selecione-o.
1. Em **local**, selecione ou crie uma pasta na qual salvar o projeto.
1. Selecione **Criar**.

Após um pouco de tempo, a janela do editor do Unity é exibida.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Para instalar o SDK de fala para o Unity, siga estas etapas:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Baixe e abra o [SDK de fala para Unity (beta)](https://aka.ms/csspeech/unitypackage), que é empacotado como um pacote de ativos de Unity (. unitypackage). Quando o pacote de ativos é aberto, a caixa de diálogo **Importar pacote do Unity** é exibida.

   [![Caixa de diálogo Importar pacote do Unity no editor do Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Verifique se todos os arquivos estão selecionados e selecione **importar**. Depois de alguns instantes, o pacote de ativos do Unity é importado para seu projeto.

Para obter mais informações sobre como importar pacotes de ativos para o Unity, consulte a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Adicionar interface do usuário

Agora, vamos adicionar uma interface do usuário mínima à nossa cena. Essa interface do usuário consiste em um botão para disparar o reconhecimento de fala e um campo de texto para exibir o resultado. Na janela [ **hierarquia** ](https://docs.unity3d.com/Manual/Hierarchy.html), uma cena de exemplo é mostrada como Unity criado com o novo projeto.

1. Na parte superior da janela **hierarquia** , selecione o**botão** **criar** > **interface do usuário** > .

   Essa ação cria três objetos de jogo que você pode ver na janela **hierarquia** : um objeto de **botão** , um objeto **Canvas** contendo o botão e um objeto **EventSystem** .

   [![Ambiente do editor do Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navegue pela exibição de **cena** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que você tenha uma boa exibição da tela e do botão na [exibição de **cena** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Na janela [ **Inspetor** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita), defina as propriedades **pos X** e **pos Y** como **0**, para que o botão seja centralizado no meio da tela.

1. Na janela **hierarquia** , selecione **criar** > **texto** **da interface do usuário** > para criar um objeto de **texto** .

1. Na janela **Inspetor** , defina as propriedades **pos X** e **pos Y** como **0** e **120**e defina as propriedades **Width** e **Height** como **240** e **120**. Esses valores garantem que o campo de texto e o botão não se sobreponham.

Quando terminar, a exibição de **cena** deverá ser semelhante a esta captura de tela:

[![Exibição de cena no editor do Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

Para adicionar o código de script de exemplo para o projeto do Unity, siga estas etapas:

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html), selecione **criar**  >   **C# script** para adicionar um novo C# script.

   [![Janela do projeto no editor do Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nomeie o script `HelloWorld`.

1. Clique `HelloWorld` duas vezes para editar o script recém-criado.

   > [!NOTE]
   > Para configurar o editor de código a ser usado pelo Unity para edição, selecione **Editar** > **preferências**e vá para as preferências de **Ferramentas externas** . Para obter mais informações, consulte o [manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua o script existente pelo código a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura dos serviços de fala.

1. Localize e substitua a cadeia `YourServiceRegion` de caracteres pela [região](regions.md) associada à sua assinatura. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no script.

Agora, retorne ao editor do Unity e adicione o script como um componente a um dos seus objetos de jogo:

1. Na janela **hierarquia** , selecione o objeto **Canvas** .

1. Na janela **Inspetor** , selecione o botão **Adicionar componente** .

   [![Janela do Inspetor no editor do Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na lista suspensa, pesquise o script que criamos `HelloWorld` acima e adicione-o. Uma seção **Olá, mundo (script)** aparece na janela **Inspetor** , listando duas propriedades não inicializadas, **texto de saída** e o **botão iniciar recup**. Essas propriedades do componente do Unity correspondem às propriedades `HelloWorld` públicas da classe.

1. Selecione o seletor de objetos da propriedade **botão iniciar recup** (o pequeno ícone de círculo à direita da propriedade) e escolha o objeto de **botão** que você criou anteriormente.

1. Selecione o seletor de objetos da propriedade **texto de saída** e escolha o objeto de **texto** que você criou anteriormente.

   > [!NOTE]
   > O botão também tem um objeto de texto aninhado. Verifique se você não o selecionou acidentalmente para saída de texto (ou renomeie um dos objetos de texto usando o campo **nome** na janela **Inspetor** para evitar confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no editor do Unity

Agora você está pronto para executar o aplicativo no editor do Unity.

1. Na barra de ferramentas do editor do Unity (abaixo da barra de menus), selecione o botão **reproduzir** (um triângulo apontando para a direita).

1. Vá para o modo de exibição de [ **jogo** ](https://docs.unity3d.com/Manual/GameView.html)e aguarde até que o objeto de **texto** seja exibido **clique no botão para reconhecer a fala**. (Ele exibe o **novo texto** quando o aplicativo não foi iniciado ou não está pronto para responder.)

1. Selecione o botão e fale uma frase ou sentença em inglês no microfone do seu computador. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na exibição do **jogo** .

   [![Exibição de jogo no editor do Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verifique a [janela do **console** ](https://docs.unity3d.com/Manual/Console.html) em busca de mensagens de depuração. Se a janela do **console** não estiver sendo exibida, vá para a barra de menus e selecione**console** **geral** > do **Windows** > para exibi-la.

1. Quando você terminar de reconhecer a fala, selecione o botão **reproduzir** na barra de ferramentas do editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar este aplicativo

Esse aplicativo também pode ser implantado como um aplicativo Android, um aplicativo autônomo do Windows ou um aplicativo UWP.
Para obter mais informações, consulte nosso [repositório de exemplo](https://aka.ms/csspeech/samples). A `quickstart/csharp-unity` pasta descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)
