---
title: Introdução à API de reconhecimento de Fala do Bing usando a biblioteca C# de desktops | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Desenvolva aplicativos básicos do Windows que usam a API de reconhecimento de Fala do Bing para converter áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966907"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Início rápido: Usar a API de reconhecimento de Fala do Bing&#35; no C para .net no Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página mostra como desenvolver um aplicativo básico do Windows que usa a API de reconhecimento de fala para converter áudio falado em texto. O uso da biblioteca de cliente permite streaming em tempo real, o que significa que quando o aplicativo cliente envia áudio para o serviço, ele recebe de forma assíncrona os resultados de reconhecimento parciais.

Os desenvolvedores que desejam usar o serviço de fala de aplicativos executados em qualquer dispositivo C# podem usar a biblioteca de desktops. Para usar a biblioteca, instale o [pacote NuGet Microsoft. ProjectOxford. SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) para uma plataforma de 32 bits e o [pacote NuGet Microsoft. ProjectOxford. SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) para uma plataforma de 64 bits. Para obter a referência de API da biblioteca de cliente, consulte [Microsoft Speech C# desktop library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

As seções a seguir descrevem como instalar, compilar e executar o aplicativo C# de exemplo usando a biblioteca C# de desktops.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para Windows 8 + e .NET Framework 4.5 + usando o [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter o aplicativo de exemplo

Clone o exemplo do repositório [de C# exemplo do Speech desktop library](https://github.com/microsoft/cognitive-speech-stt-windows) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Assine a API de reconhecimento de fala e obtenha uma chave de assinatura de avaliação gratuita

O Speech API faz parte de serviços cognitivas (anteriormente Oxford de projeto). Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de usar as bibliotecas de cliente de fala, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo C# de exemplo da área de trabalho fornecido, Cole sua chave de assinatura na caixa de texto quando você executa o exemplo. Para obter mais informações, consulte [executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passo 1: Instalar o aplicativo de exemplo

1. Inicie o Visual Studio 2015 e selecione **arquivo** > **abrir** > **projeto/solução**.

2. Navegue até a pasta em que você salvou os arquivos de API de reconhecimento de fala baixados. Selecione**janelas**de **fala** > e, em seguida, selecione a pasta exemplo-wp.

3. Clique duas vezes para abrir o arquivo de solução (. sln) do Visual Studio 2015 chamado SpeechToText-WPF-Samples. sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passo 2: Compilar o aplicativo de exemplo

1. Se você quiser usar o *reconhecimento com a intenção*, primeiro será necessário inscrever-se para o [serviço reconhecimento vocal inteligente (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Em seguida, use a URL do ponto de extremidade do seu aplicativo Luis para definir `LuisEndpointUrl` o valor da chave no arquivo app. config na pasta Samples/SpeechRecognitionServiceExample. Para obter mais informações sobre a URL do ponto de extremidade do aplicativo LUIS, consulte [publicar seu aplicativo](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Substitua o `&` caractere na URL do ponto de extremidade `&amp;` Luis por para garantir que a URL seja interpretada corretamente pelo analisador XML.

2. Pressione Ctrl + Shift + B ou selecione **criar** no menu da faixa de opções. Em seguida, selecione **Compilar solução**.

## <a name="step-3-run-the-sample-application"></a>Passo 3: Executar o exemplo de aplicação

1. Depois que a compilação for concluída, pressione F5 ou selecione **Iniciar** no menu faixa de opções para executar o exemplo.

2. Vá para a janela de **exemplo do Project Oxford Speech to Text** . Cole sua chave de assinatura na caixa de texto **colar sua chave de assinatura aqui para iniciar** , conforme mostrado. Para persistir sua chave de assinatura em seu PC ou laptop, selecione **salvar chave**. Para excluir a chave de assinatura do sistema, selecione **Excluir chave** para removê-la do seu PC ou laptop.

   ![Chave de colagem de reconhecimento de fala](../Images/SpeechRecog_paste_key.PNG)

3. Em **fonte de reconhecimento de fala**, escolha uma das seis fontes de fala, que se enquadram em duas categorias de entrada principais:

   * Use o microfone do computador ou um microfone conectado para capturar a fala.
   * Reproduzir um arquivo de áudio.

   Cada categoria tem três modos de reconhecimento:

    * **Modo de ShortPhrase**: Um expressão de até 15 segundos de comprimento. À medida que os dados são enviados para o servidor, o cliente recebe vários resultados parciais e um resultado final com várias opções n-melhores.
    * **Modo de LongDictation**: Um expressão de até dois minutos de duração. À medida que os dados são enviados para o servidor, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.
    * **Detecção de intenção**: O servidor retorna informações estruturadas adicionais sobre a entrada de fala. Para usar a detecção de intenção, primeiro você precisa treinar um modelo usando [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Use arquivos de áudio de exemplo com este aplicativo de exemplo. Localize os arquivos no repositório que você baixou com este exemplo na pasta Samples/SpeechRecognitionServiceExample. Esses arquivos de áudio de exemplo são executados automaticamente se nenhum outro arquivo for escolhido quando você selecionar **usar arquivo WAV para o modo Shortphrase** ou **usar o arquivo WAV para o modo Longdictation** como sua entrada de fala. Atualmente, há suporte apenas para o formato de áudio WAV.

![Fala para interface de texto](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exemplos explicados

### <a name="recognition-events"></a>Eventos de reconhecimento

* **Eventos de resultados parciais**: Esse evento é chamado sempre que o serviço de fala prevê o que você deve dizer, mesmo antes de terminar de falar (se `MicrophoneRecognitionClient`você usar) ou terminar de enviar dados ( `DataRecognitionClient`se você usar).
* **Eventos de erro**: Chamado quando o serviço detecta um erro.
* **Eventos de intenção**: Chamado em clientes "WithIntent" (somente no modo ShortPhrase) depois que o resultado do reconhecimento final é analisado em uma intenção estruturada de JSON.
* **Eventos de resultado**:
  * No `ShortPhrase` modo, esse evento é chamado e retorna os n-melhores resultados depois de terminar de falar.
  * No `LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas de sentença.
  * **Para cada uma das n melhores opções**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

Manipuladores de eventos já são apontados no código na forma de comentários de código.

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da Microsoft Speech desktop library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Introdução à API de reconhecimento de fala da Microsoft em Java no Android](GetStartedJavaAndroid.md)
* [Introdução à API de reconhecimento de fala da Microsoft em Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Introdução à API de reconhecimento de fala da Microsoft em JavaScript](GetStartedJSWebsockets.md)
* [Introdução à API de reconhecimento de fala da Microsoft via REST](GetStartedREST.md)
