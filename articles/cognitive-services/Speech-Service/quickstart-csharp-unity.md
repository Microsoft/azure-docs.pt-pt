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
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 06831fa933c04827c966e8f6e12aa817f5008b88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554143"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Início rápido: Reconhecer a fala com o SDK de fala para Unity (beta)

Os guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de conversão de fala em texto usando o [Unity](https://unity3d.com/) e o SDK de fala para Unity (beta).
Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
Se você não estiver familiarizado com o Unity, é recomendável estudar o [manual do usuário do Unity](https://docs.unity3d.com/Manual/UnityManual.html) antes de iniciar o desenvolvimento do aplicativo.

> [!NOTE]
> O SDK de fala para o Unity está atualmente em beta.
> Ele dá suporte à área de trabalho do Windows (x86 e x64) ou Plataforma Universal do Windows (x86, x64, ARM/ARM64) e Android (x86, ARM32/64).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Unity 2018,3 ou posterior](https://store.unity.com/) com o [Unity 2019,1 adicionando suporte para UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Para obter suporte a ARM64, instale as [ferramentas de compilação opcionais para ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Acesso ao microfone do seu computador.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

* Inicie o Unity e, na guia **projetos** , selecione **novo**.
* Especifique o **nome do projeto** como **Csharp-Unity**, **modelo** como **3D** e escolha um local.
  Em seguida, selecione **criar projeto**.
* Após um pouco de tempo, a janela do editor do Unity deve aparecer.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* O SDK de fala para Unity (beta) é empacotado como um pacote de ativos de Unity (. unitypackage).
  Baixe-o a partir [aqui](https://aka.ms/csspeech/unitypackage).
* Importe o SDK de fala selecionando **ativos** > **importação de pacote** > pacote**personalizado**.
  Confira a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html) para obter detalhes.
* No seletor de arquivos, selecione o arquivo SDK. unitypackage de fala que você baixou acima.
* Verifique se todos os arquivos estão selecionados e clique em **importar**:

  ![Captura de tela do editor do Unity ao importar o pacote de ativos da Unity do SDK de fala](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Adicionar interface do usuário

Adicionamos uma interface do usuário mínima à nossa cena, que consiste em um botão para disparar o reconhecimento de fala e um campo de texto para exibir o resultado.

* Na [janela hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por padrão à esquerda), uma cena de exemplo é mostrada como Unity criado com o novo projeto.
* Clique no botão **criar** na parte superior da janela hierarquia e selecione o**botão** **da interface do usuário** > .
* Isso cria três objetos de jogo que você pode ver na janela hierarquia: um objeto de **botão** aninhado em um objeto **Canvas** e um objeto **EventSystem** .
* [Navegue pela exibição de cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que você tenha uma boa exibição da tela e do botão na [exibição de cena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Clique no objeto **Button** na janela hierarquia para exibir suas configurações na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
* Defina as propriedades de **pos X** e **pos Y** como **0**, portanto, o botão é centralizado no meio da tela.
* Clique no botão **criar** na parte superior da janela hierarquia novamente e selecione**texto** **da interface do usuário** > para criar um campo de texto.
* Clique no objeto de **texto** na janela hierarquia para exibir suas configurações na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
* Defina as propriedades de **pos X** e **pos Y** como **0** e **120**e defina as propriedades **Width** e **Height** como **240** e **120** para garantir que o campo de texto e o botão não se sobreponham.

Quando você terminar, a interface do usuário deverá ser semelhante a esta captura de tela:

[![Captura de tela da interface do usuário do guia de início rápido no editor do Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html) (por padrão na parte inferior esquerda), clique no botão **criar** e selecione  **C# script**. Nomeie o script `HelloWorld`.

1. Edite o script clicando duas vezes nele.

   > [!NOTE]
   > Você pode configurar qual editor de código será iniciado em**preferências**de **edição** > , consulte o [manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código pelo seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura dos serviços de fala.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no script.

1. De volta ao editor do Unity, o script precisa ser adicionado como um componente a um dos seus objetos de jogo.

   * Clique no objeto **Canvas** na janela hierarquia. Isso abre a configuração na janela do [Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
   * Clique no botão **Adicionar componente** na janela Inspetor e procure o script HelloWorld que criamos acima e adicione-o.
   * Observe que o componente Olá, mundo tem duas propriedades não inicializadas, o **texto de saída** e o **botão iniciar recup**, que correspondem às `HelloWorld` propriedades públicas da classe.
     Para conectá-los, clique no seletor de objetos (o ícone de círculo pequeno à direita da propriedade) e escolha o texto e os objetos de botão criados anteriormente.

     > [!NOTE]
     > O botão também tem um objeto de texto aninhado. Verifique se você não o selecionou acidentalmente para saída de texto (ou renomeie um dos objetos de texto usando o campo nome na janela Inspetor para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no editor do Unity

* Pressione o botão **reproduzir** na barra de ferramentas do editor do Unity (abaixo da barra de menus).

* Depois que o aplicativo for iniciado, clique no botão e fale uma frase em inglês ou frase no microfone do seu computador. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na janela.

  [![Captura de tela do guia de início rápido em execução na janela de jogos do Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Verifique a [janela do console](https://docs.unity3d.com/Manual/Console.html) em busca de mensagens de depuração.

* Quando você terminar de reconhecer a fala, clique no botão **reproduzir** na barra de ferramentas do editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar este aplicativo

Esse aplicativo também pode ser implantado no Android, como um aplicativo autônomo do Windows, ou aplicativos UWP.
Consulte nosso [repositório de exemplo](https://aka.ms/csspeech/samples) na pasta QuickStart/Csharp-Unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
