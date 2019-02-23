---
title: Comece com a API de reconhecimento de voz do Bing utilizando a ambiente de trabalho biblioteca de c# | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Desenvolva aplicativos básicos do Windows que utilizam a API de reconhecimento de voz do Bing para converter áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5f3b70a2dd9816210ed61280be38504a3980d205
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670099"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Início rápido: Utilizar o reconhecimento de voz do Bing API em C&#35; para .NET no Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página mostra como desenvolver uma aplicação básica do Windows que utiliza a API de reconhecimento de voz para converter áudio falado em texto. Usando a biblioteca de cliente permite a transmissão em fluxo em tempo real, que significa que quando a aplicação cliente envia áudio para o serviço, em simultâneo e de forma assíncrona recebe novamente os resultados de reconhecimento parcial.

Os programadores que pretendem utilizar o serviço de voz a partir de aplicações que são executados em qualquer dispositivo podem utilizar a ambiente de trabalho biblioteca de c#. Para usar a biblioteca, instale o [pacote de NuGet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) de uma plataforma de 32 bits e o [pacote NuGet Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) para um plataforma de 64 bits. Para a biblioteca de cliente, referência da API, consulte [Microsoft fala c# a área de trabalho biblioteca](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

As secções seguintes descrevem como instalar, criar e executar o aplicativo de exemplo do c# com a ambiente de trabalho biblioteca de c#.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para o Windows 8 e posteriores e .NET Framework 4.5 + usando [Visual Studio 2015, edição de Comunidade](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter a aplicação de exemplo

Clonar o exemplo a partir da [exemplo de ambiente de trabalho biblioteca fala c#](https://github.com/microsoft/cognitive-speech-stt-windows) repositório.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de reconhecimento de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos (anteriormente o projeto Oxford). Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

> [!IMPORTANT]
> * Obter uma chave de subscrição. Antes de utilizar as bibliotecas de cliente de voz, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilize a sua chave de subscrição. Com o fornecido c# aplicativo de exemplo de ambiente de trabalho, cole a chave de subscrição na caixa de texto quando executar o exemplo. Para obter mais informações, consulte [executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passo 1: Instalar a aplicação de exemplo

1. Inicie o Visual Studio 2015 e selecione **arquivo** > **abrir** > **projeto/solução**.

2. Procure a pasta onde guardou os ficheiros transferidos da API de reconhecimento de voz. Selecione **voz** > **Windows**e, em seguida, selecione a pasta de exemplo WP.

3. Faça duplo clique para abrir o ficheiro de solução de 2015 do Visual Studio (. sln) com o nome SpeechToText-WPF-Samples.sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passo 2: Criar a aplicação de exemplo

1. Se quiser usar *reconhecimento com a intenção*, tem primeiro de inscrever-se a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Em seguida, utilize o URL de ponto final da sua aplicação LUIS para definir o valor da chave `LuisEndpointUrl` no ficheiro App. config na pasta de exemplos/SpeechRecognitionServiceExample. Para obter mais informações sobre o URL de ponto final da aplicação LUIS, consulte [publicar a sua aplicação](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Substitua a `&` caráter no URL de ponto final do LUIS com `&amp;` para se certificar de que o URL é corretamente interpretado pelo analisador XML.

2. Prima Ctrl + Shift + B ou selecione **criar** no menu da faixa de opções. Em seguida, selecione **compilar solução**.

## <a name="step-3-run-the-sample-application"></a>Passo 3: Executar o exemplo de aplicação

1. Depois de concluída a compilação, prima F5 ou selecione **iniciar** no menu da faixa de opções para executar o exemplo.

2. Vá para o **conversão de voz do projeto Oxford em texto de exemplo** janela. Cole a chave de subscrição para o **colar a chave de subscrição aqui para iniciar** caixa de texto, como mostrado. Para manter a sua chave de subscrição no seu PC ou portátil, selecione **Guardar chave**. Para eliminar a chave de subscrição do sistema, selecione **eliminar chave** removê-lo a partir do seu PC ou computador portátil.

   ![Chave de colar da reconhecimento de voz](../Images/SpeechRecog_paste_key.PNG)

3. Sob **origem de reconhecimento de voz**, escolha uma das origens de seis voz, enquadram-se em duas categorias principais de entrada:

   * Utilize o microfone do seu computador ou um microfone anexado para capturar voz.
   * Reproduza um arquivo de áudio.

   Cada categoria tem três modos de reconhecimento:

    * **Modo de ShortPhrase**: Uma expressão até 15 segundos de tempo. Enquanto os dados são enviados para o servidor, o cliente recebe vários resultados parciais e um resultado final com várias opções de múltipla.
    * **Modo de LongDictation**: Uma expressão até dois minutos longa. Enquanto os dados são enviados para o servidor, o cliente recebe vários resultados parciais e finais, com base em onde o servidor indica as pausas das frases.
    * **Deteção de intenção**: O servidor devolve informações estruturadas adicionais sobre a conversão de voz de entrada. Para utilizar a deteção de intenção, precisa primeiro preparar um modelo com o [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Utilize arquivos de áudio de exemplo com este aplicativo de exemplo. Encontre os ficheiros no repositório que transferiu com este exemplo sob a pasta de exemplos/SpeechRecognitionServiceExample. Esses arquivos de áudio de exemplo são executados automaticamente se não existem outros ficheiros são escolhidos Quando seleciona **arquivo wav de utilização para o modo de Shortphrase** ou **arquivo wav de utilização para o modo de Longdictation** como sua voz de entrada. Atualmente, apenas WAV formato de áudio é suportado.

![Conversão de voz em interface de texto](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exemplos de explicado

### <a name="recognition-events"></a>Eventos de reconhecimento

* **Eventos de resultados parciais**: Este evento é chamado sempre que o serviço de voz prevê o que poderá ser dizendo, até mesmo antes de acabar de fala (se usar `MicrophoneRecognitionClient`) ou de concluir o envio de dados (se usar `DataRecognitionClient`).
* **Eventos de erro**: Chamado quando o serviço Deteta um erro.
* **Eventos de intenção**: Chamado nos clientes de "WithIntent" (apenas no modo de ShortPhrase) após o final reconhecimento o resultado é analisado numa intenção do JSON estruturada.
* **Resultar eventos**:
  * No `ShortPhrase` modo, este evento é chamado e retorna os resultados de múltipla depois de concluir a falar.
  * No `LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas das frases.
  * **Para cada uma das opções múltipla**, são devolvidas um valor de confiança e algumas maneiras diferentes do texto reconhecido. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

Manipuladores de eventos são já ter mencionados que o código no formulário de comentários do código.

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da biblioteca de área de trabalho Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Comece com a API de reconhecimento de voz de Microsoft em Java no Android](GetStartedJavaAndroid.md)
* [Comece com a API de reconhecimento de voz de Microsoft no Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Comece com a API de reconhecimento de voz de Microsoft em JavaScript](GetStartedJSWebsockets.md)
* [Comece com a API de reconhecimento de voz de Microsoft através de REST](GetStartedREST.md)
