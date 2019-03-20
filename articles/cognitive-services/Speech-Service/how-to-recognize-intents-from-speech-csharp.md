---
title: 'Tutorial: Reconhecer intenções de voz com o SDK de voz para c#'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, vai aprender a reconhecer intenção de voz com o SDK Voz para C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: a9b3d8a2670a0b4e6bed2d5e9a9b64e597adcb16
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855729"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Tutorial: Reconhecer intenções de voz com o SDK de voz paraC#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

O [SDK Voz](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos integra-se no [serviço Language Understanding (LUIS)](https://www.luis.ai/home) para proporcionar o **reconhecimento de intenções**. Uma intenção é algo que o utilizador quer fazer, seja reservar um voo, ver tempo ou fazer uma chamada. O utilizador pode utilizar qualquer termo que pareça natural. Ao utilizar machine learning, o LUIS mapeia os pedidos do utilizador para as intenções que você definiu.

> [!NOTE]
> Uma aplicação do LUIS define as intenções e entidades que quer ver reconhecidas. É separada da aplicação C# que utiliza o serviço Voz. Neste artigo, “aplicação LUIS” significa a aplicação do LUIS e “aplicação” significa o código C#.

Neste tutorial, vai utilizar o SDK Voz para desenvolver uma aplicação de consola C# que deriva intenções das expressões do utilizador através do microfone do seu dispositivo. Vai aprender a:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK Voz
> * Criar uma configuração de voz e obter um reconhecedor de intenções
> * Obter o modelo para a aplicação LUIS e adicionar as intenções de que precisa
> * Especificar o idioma do reconhecimento de voz
> * Reconhecer a voz a partir de um ficheiro
> * Utilizar o reconhecimento contínuo assíncrono e orientado por eventos

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar o tutorial, confirme que tem o seguinte.

* Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).
* Visual Studio 2017 (qualquer edição).

## <a name="luis-and-speech"></a>O LUIS e a voz

LUIS integra-se com os serviços de voz para reconhecer intenções de voz. Não precisa de uma subscrição de serviços de voz, apenas o LUIS.

O LUIS utiliza dois tipos de chaves:

|Tipo de chave|Objetivo|
|--------|-------|
|criação|permite-lhe criar e modificar as aplicações LUIS programaticamente|
|endpoint |autoriza o acesso a uma determinada aplicação LUIS|

A chave necessária para este tutorial é a chave de LUIS do ponto final. O tutorial utiliza a aplicação LUIS Home Automation de exemplo, que pode criar ao seguir [Utilizar a aplicação Home Automation pré-concebida](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Se tiver criado a sua própria aplicação LUIS, pode utilizá-la.

Quando cria uma aplicação LUIS, é gerada automaticamente uma chave de arranque para que a possa testar com consultas de texto. Esta chave não permitir a integração de serviços de voz e não funcionará com este tutorial. Tem de criar um recurso do LUIS no dashboard do Azure e atribuí-lo à aplicação LUIS. Para este tutorial, pode utilizar o escalão de subscrição.

Depois de criar o recurso do LUIS no dashboard do Azure, inicie sessão no [portal do LUIS](https://www.luis.ai/home), escolha a sua aplicação na página My Apps (As Minhas Aplicações) e mude para a página Manage (Gerir) da aplicação. Por fim, clique em **Keys and Endpoints** (Chaves e Pontos Finais), na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página de definições Keys and Endpoint (Chaves e Pontos Finais):

1. Desloque-se para a secção Resources and Keys (Recursos e Chaves) e clique em **Assign resource** (Atribuir recurso).
1. Na caixa de diálogo **Assign a key to your app** (Atribuir uma chave à aplicação), escolha o seguinte:

    * Escolha Microsoft como Tenant (Inquilino).
    * Em Subscription Name (Nome da Subscrição), escolha a subscrição do Azure que contém o recurso do LUIS que pretende utilizar.
    * Em Key (Chave), escolha o recurso do LUIS que quer utilizar com a aplicação.

Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página. Clique no ícone junto a uma chave para a copiar para a área de transferência. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de voz no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Abra o ficheiro `Program.cs` no projeto do Visual Studio e substitua o bloco de declarações `using` no início do ficheiro pelas declarações abaixo.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

Dentro do método `Main()` fornecido, adicione o código seguinte.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Crie um método `RecognizeIntentAsync()` assíncrono vazio, conforme mostrado aqui.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

No corpo deste método novo, adicione este código.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Substitua os marcadores de posição neste método pela chave, a região e a ID de aplicação da sua subscrição do LUIS, da seguinte forma.

|Marcador de posição|Substituir|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Pela chave do ponto final do LUIS. Conforme mencionado anteriormente, tem de ser uma chave obtida a partir do dashboard do Azure, não uma “chave de arranque”. Pode localizá-la na página Keys and Endpoints (Chaves e Pontos Finais) da aplicação (em Manage [Gerir]), no [portal do LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Pelo breve identificador da região na qual a subscrição do LUIS se encontra, como `westus` para E.U.A. Oeste. Veja [Regions](regions.md) (Regiões).|
|`YourLanguageUnderstandingAppId`|Pelo ID de aplicação do LUIS. Pode localizá-lo na página Settings (Definições) da aplicação, no [portal do LUIS](https://www.luis.ai/home).|

Depois de feitas estas alterações, pode compilar (Control-Shift-B) a aplicação do tutorial e executá-la (F5). Quando lhe for pedido, experimente dizer “Desligar as luzes” para o microfone do PC. O resultado é apresentado na janela da consola.

As secções seguintes incluem abordagens ao código.


## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenções

O primeiro passo para reconhecer intenções na voz é criar uma configuração de voz na chave do ponto final e na região do LUIS. As configurações de voz podem ser utilizadas para criar reconhecedores para as diversas capacidades do SDK Voz. A configuração de voz pode especificar a subscrição que pretende utilizar de várias formas. Aqui, utilizamos `FromSubscription`, que requer a chave e a região da subscrição.

> [!NOTE]
> Utilize a chave e a região da sua subscrição do LUIS, não de uma subscrição de serviços de voz.

Em seguida, crie um reconhecedor de intenções com `new IntentRecognizer(config)`. Uma vez que a configuração já sabe que subscrição utilizar, não é preciso especificar a chave e o ponto final da mesma novamente quando criar o reconhecedor.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo do LUIS e adicionar intenções

Agora, utilize `LanguageUnderstandingModel.FromAppId()` para importar o modelo da aplicação LUIS e adicione as intenções que pretende reconhecer através do método `AddIntent()` do reconhecedor. Estes dois passos indicam as palavras que o utilizador irá provavelmente utilizar nos pedidos, o que melhora a precisão do reconhecimento de voz. Se não precisar de reconhecer todas as intenções na aplicação, não é necessário adicioná-las todas.

Adicionar intenções requer três argumentos: o modelo do LUIS (que acabou de ser criado e é denominado `model`), o nome da intenção e um ID de intenção. A diferença entre o ID e o nome da intenção é a seguinte.

|Argumento `AddIntent()`|Objetivo|
|--------|-------|
|intentName |O nome da intenção, conforme definido na aplicação LUIS. Tem de corresponder exatamente ao nome da intenção do LUIS.|
|intentID    |Um ID que o SDK de Voz atribui a uma intenção reconhecida. Pode ser o que quiser e não tem de corresponder ao nome da intenção definido na aplicação LUIS. Se o mesmo código processar várias intenções, pode, por exemplo, utilizar o mesmo ID para essas intenções.|

A aplicação LUIS Home Automation tem duas intenções. Uma para ligar um dispositivo e outra para o desligar. As linhas abaixo adicionam essas intenções ao reconhecedor. Substitua as três linhas `AddIntent` no método `RecognizeIntentAsync()` por este código.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

## <a name="start-recognition"></a>Iniciar o reconhecimento

Com o reconhecedor criado e as intenções adicionadas, o reconhecimento pode começar. O SDK Voz suporta o reconhecimento único e o reconhecimento contínuo.

|Modo de reconhecimento|Métodos a chamar|Resultado|
|----------------|-----------------|---------|
|Único|`RecognizeOnceAsync()`|Devolve a intenção reconhecida, se existir, após uma expressão.|
|Contínuo|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Reconhece múltiplas expressões. Emite eventos (por exemplo, `IntermediateResultReceived`) se houver resultados disponíveis.|

A aplicação do tutorial utiliza o modo único, pelo que chama `RecognizeOnceAsync()` para dar início ao reconhecimento. O resultado é um objeto `IntentRecognitionResult` que inclui informações sobre a intenção reconhecida. A resposta JSON do LUIS é extraída com a expressão seguinte:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

A aplicação de tutorial não analisa o resultado JSON, só o apresenta na janela de consola.

![Resultados do reconhecimento do LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar o idioma do reconhecimento

Por predefinição, o LUIS reconhece intenções em inglês dos E.U.A. (`en-us`). Se atribuir um código de região à propriedade `SpeechRecognitionLanguage` da configuração da voz, poderá reconhecer intenções noutros idiomas. Por exemplo, adicione `config.SpeechRecognitionLanguage = "de-de";` à aplicação do tutorial, antes de criar o reconhecedor, para reconhecer intenções em alemão. Veja [Supported Languages](language-support.md#speech-to-text) (Idiomas Suportados).

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo a partir de um ficheiro

O código seguinte ilustra duas capacidades extra do reconhecimento de intenções quando é utilizado o SDK Voz. O primeiro, já referido antes, é o reconhecimento contínuo, no qual o reconhecedor emite eventos quando há resultados disponíveis. Esses eventos podem, depois, ser processados pelos processadores de eventos que fornecer. Com o reconhecimento contínuo, é chamado o `StartContinuousRecognitionAsync()` do reconhecedor para iniciar o reconhecimento, em vez de `RecognizeOnceAsync()`.

A outra capacidade é ler o áudio que contém a voz que vai ser processada a partir de um ficheiro WAV. Esta capacidade envolve criar uma configuração de áudio que pode ser utilizada quando for criado o reconhecedor de intenções. O ficheiro só pode ter um canal (mono) com uma taxa de amostragem de 16 kHz.

Para experimentar estas funcionalidades, substitua o corpo do método `RecognizeIntentAsync()` pelo código seguinte.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Reveja o código para incluir a chave, a região e o ID de aplicação do ponto final do LUIS e para adicionar as intenções de Home Automation, como fez antes. Altere `whatstheweatherlike.wav` para o nome do ficheiro de áudio. Em seguida, compile e execute.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código deste artigo na pasta samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [How to recognize speech](how-to-recognize-speech-csharp.md) (Como reconhecer voz)
