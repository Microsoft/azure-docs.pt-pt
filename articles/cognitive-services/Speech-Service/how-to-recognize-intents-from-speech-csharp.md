---
title: 'Tutorial: Reconhecer intenções de voz com o SDK de Voz para C#'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, vai aprender a reconhecer intenção de voz com o SDK Voz para C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: 7f42d5914a2ec7f479a8b3d1df1b8672f318036b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464621"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Tutorial: Reconhecer intenções de voz com o SDK de Voz para C#

O SDK de [fala](speech-sdk.md) dos serviços cognitivas integra-se ao [Luis (serviço de reconhecimento vocal)](https://www.luis.ai/home) para fornecer **reconhecimento de intenção**. Uma intenção é algo que o utilizador quer fazer, seja reservar um voo, ver tempo ou fazer uma chamada. O utilizador pode utilizar qualquer termo que pareça natural. Usando o Machine Learning, o LUIS mapeia solicitações de usuário para as tentativas que você definiu.

> [!NOTE]
> Uma aplicação do LUIS define as intenções e entidades que quer ver reconhecidas. É separada da aplicação C# que utiliza o serviço Voz. Neste artigo, “aplicação LUIS” significa a aplicação do LUIS e “aplicação” significa o código C#.

Neste tutorial, vai utilizar o SDK Voz para desenvolver uma aplicação de consola C# que deriva intenções das expressões do utilizador através do microfone do seu dispositivo. Vai aprender a:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK Voz
> * Criar uma configuração de fala e obter um reconhecedor de intenção
> * Obter o modelo para a aplicação LUIS e adicionar as intenções de que precisa
> * Especificar o idioma do reconhecimento de voz
> * Reconhecer a voz a partir de um ficheiro
> * Utilizar o reconhecimento contínuo assíncrono e orientado por eventos

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem os seguintes itens antes de iniciar este tutorial:

* Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se com os serviços de fala para reconhecer tentativas de fala. Você não precisa de uma assinatura de serviços de fala, apenas LUIS.

O LUIS usa três tipos de chaves:

|Tipo de chave|Objetivo|
|--------|-------|
|Criação|Permite criar e modificar aplicativos LUIS de forma programática|
|Inicial|Permite testar seu aplicativo LUIS usando somente texto|
|Ponto Final |Autoriza o acesso a um determinado aplicativo LUIS|

Para este tutorial, você precisa do tipo de chave do ponto de extremidade. O tutorial usa o aplicativo de exemplo Home Automation LUIS, que você pode criar seguindo o guia de início rápido [usar aplicativo de automação inicial predefinido](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) . Se você criou um aplicativo LUIS por conta própria, poderá usá-lo em vez disso.

Quando você cria um aplicativo LUIS, o LUIS gera automaticamente uma chave inicial para que você possa testar o aplicativo usando consultas de texto. Esta chave não habilita a integração dos serviços de fala e não funcionará com este tutorial. Crie um recurso LUIS no painel do Azure e atribua-o ao aplicativo LUIS. Para este tutorial, pode utilizar o escalão de subscrição.

Depois de criar o recurso LUIS no painel do Azure, faça logon no [portal do Luis](https://www.luis.ai/home), escolha seu aplicativo na página **meus aplicativos** e, em seguida, alterne para a página **gerenciar** do aplicativo. Por fim, selecione **chaves e pontos de extremidade** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página **configurações de ponto de extremidade e chaves** :

1. Role para baixo até a seção **recursos e chaves** e selecione **atribuir recurso**.
1. Na caixa de diálogo **atribuir uma chave ao seu aplicativo** , faça as seguintes alterações:

   * Em **locatário**, escolha **Microsoft**.
   * Em **nome da assinatura**, escolha a assinatura do Azure que contém o recurso Luis que você deseja usar.
   * Em **chave**, escolha o recurso Luis que você deseja usar com o aplicativo.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página. 

1. Selecione o ícone ao lado de uma chave para copiá-lo para a área de transferência. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de voz no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Em seguida, você adiciona o código ao projeto.

1. Em **Gerenciador de soluções**, abra o arquivo **Program.cs**.

1. Substitua o bloco de instruções `using` no início do arquivo pelas seguintes declarações:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Dentro do método de `Main()` fornecido, adicione o seguinte código:

   ```csharp
   RecognizeIntentAsync().Wait();
   Console.WriteLine("Please press Enter to continue.");
   Console.ReadLine();
   ```

1. Crie um método assíncrono vazio `RecognizeIntentAsync()`, conforme mostrado aqui:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. No corpo desse novo método, adicione este código:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Substitua os marcadores de posição neste método pela chave, a região e a ID de aplicação da sua subscrição do LUIS, da seguinte forma.

   |Marcador de posição|Substituir|
   |-----------|------------|
   |`YourLanguageUnderstandingSubscriptionKey`|Pela chave do ponto final do LUIS. Novamente, você deve obter esse item do seu painel do Azure, não uma "tecla de início". Você pode encontrá-lo na página de **chaves e pontos de extremidade** do aplicativo (em **gerenciar**) no [portal do Luis](https://www.luis.ai/home).|
   |`YourLanguageUnderstandingServiceRegion`|Pelo breve identificador da região na qual a subscrição do LUIS se encontra, como `westus` para E.U.A. Oeste. Veja [Regions](regions.md) (Regiões).|
   |`YourLanguageUnderstandingAppId`|Pelo ID de aplicação do LUIS. Você pode encontrá-lo na página de **configurações** do seu aplicativo no [portal do Luis](https://www.luis.ai/home).|

Com essas alterações feitas, você pode criar (**Control + Shift + B**) e executar (**F5**) o aplicativo tutorial. Quando for solicitado, tente dizer "desligar as luzes" no microfone do seu PC. O aplicativo exibe o resultado na janela do console.

As secções seguintes incluem abordagens ao código.

## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenções

Primeiro, você precisa criar uma configuração de fala da sua chave de ponto de extremidade do LUIS e da região. Você pode usar as configurações de fala para criar reconhecedores para os vários recursos do SDK de fala. A configuração de fala tem várias maneiras de especificar a assinatura que você deseja usar; aqui, usamos `FromSubscription`, que usa a chave de assinatura e a região.

> [!NOTE]
> Use a chave e a região da sua assinatura do LUIS, não de uma assinatura de serviços de fala.

Em seguida, crie um reconhecedor de intenções com `new IntentRecognizer(config)`. Como a configuração já sabe qual assinatura deve ser usada, você não precisa especificar a chave de assinatura e o ponto de extremidade novamente ao criar o reconhecedor.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo do LUIS e adicionar intenções

Agora, utilize `LanguageUnderstandingModel.FromAppId()` para importar o modelo da aplicação LUIS e adicione as intenções que pretende reconhecer através do método `AddIntent()` do reconhecedor. Estes dois passos indicam as palavras que o utilizador irá provavelmente utilizar nos pedidos, o que melhora a precisão do reconhecimento de voz. Você não precisará adicionar todas as intenções do aplicativo se não precisar reconhecê-las em seu aplicativo.

Para adicionar tentativas, você deve fornecer três argumentos: o modelo LUIS (que foi criado e nomeado `model`), o nome da intenção e uma ID de tentativa. A diferença entre o ID e o nome da intenção é a seguinte.

|`AddIntent()`&nbsp;argumento|Objetivo|
|--------|-------|
|intentName|O nome da intenção, conforme definido na aplicação LUIS. Esse valor deve corresponder exatamente ao nome da intenção LUIS.|
|intentID|Um ID que o SDK de Voz atribui a uma intenção reconhecida. Esse valor pode ser o que você desejar; Ele não precisa corresponder ao nome da intenção, conforme definido no aplicativo LUIS. Se o mesmo código processar várias intenções, pode, por exemplo, utilizar o mesmo ID para essas intenções.|

O aplicativo Home Automation LUIS tem duas intenções: uma para ligar um dispositivo e outra para desligar um dispositivo. As linhas abaixo adicionam essas intenções ao reconhecedor. Substitua as três linhas `AddIntent` no método `RecognizeIntentAsync()` por este código.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Em vez de adicionar intenções individuais, você também pode usar o método `AddAllIntents` para adicionar todas as intenções em um modelo ao reconhecedor.

## <a name="start-recognition"></a>Iniciar o reconhecimento

Com o reconhecedor criado e as intenções adicionadas, o reconhecimento pode começar. O SDK Voz suporta o reconhecimento único e o reconhecimento contínuo.

|Modo de reconhecimento|Métodos a chamar|Resultado|
|----------------|-----------------|---------|
|Único|`RecognizeOnceAsync()`|Devolve a intenção reconhecida, se existir, após uma expressão.|
|Contínuo|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Reconhece vários declarações; emite eventos (por exemplo, `IntermediateResultReceived`) quando os resultados estão disponíveis.|

A aplicação do tutorial utiliza o modo único, pelo que chama `RecognizeOnceAsync()` para dar início ao reconhecimento. O resultado é um objeto `IntentRecognitionResult` que inclui informações sobre a intenção reconhecida. Você extrai a resposta JSON LUIS usando a seguinte expressão:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

O aplicativo tutorial não analisa o resultado JSON. Ele exibe apenas o texto JSON na janela do console.

![Resultados do reconhecimento de LUIS único](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar o idioma do reconhecimento

Por predefinição, o LUIS reconhece intenções em inglês dos E.U.A. (`en-us`). Se atribuir um código de região à propriedade `SpeechRecognitionLanguage` da configuração da voz, poderá reconhecer intenções noutros idiomas. Por exemplo, adicione `config.SpeechRecognitionLanguage = "de-de";` à aplicação do tutorial, antes de criar o reconhecedor, para reconhecer intenções em alemão. Para obter mais informações, consulte [idiomas com suporte](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo a partir de um ficheiro

O código seguinte ilustra duas capacidades extra do reconhecimento de intenções quando é utilizado o SDK Voz. O primeiro, já referido antes, é o reconhecimento contínuo, no qual o reconhecedor emite eventos quando há resultados disponíveis. Esses eventos podem, depois, ser processados pelos processadores de eventos que fornecer. Com o reconhecimento contínuo, você chama o método de `StartContinuousRecognitionAsync()` do reconhecedor para iniciar o reconhecimento em vez de `RecognizeOnceAsync()`.

A outra capacidade é ler o áudio que contém a voz que vai ser processada a partir de um ficheiro WAV. A implementação envolve a criação de uma configuração de áudio que pode ser usada ao criar o reconhecedor de intenção. O ficheiro só pode ter um canal (mono) com uma taxa de amostragem de 16 kHz.

Para testar esses recursos, exclua ou comente o corpo do método `RecognizeIntentAsync()` e adicione o código a seguir em seu lugar.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Reveja o código para incluir a chave, a região e o ID de aplicação do ponto final do LUIS e para adicionar as intenções de Home Automation, como fez antes. Altere `whatstheweatherlike.wav` para o nome do arquivo de áudio gravado. Em seguida, compile, copie o arquivo de áudio para o diretório de compilação e execute o aplicativo.

Por exemplo, se você disser "desligar as luzes", pausar e, em seguida, dizer "ativar as luzes" no arquivo de áudio gravado, a saída do console semelhante à seguinte pode ser exibida:

![Resultados do reconhecimento de LUIS do arquivo de áudio](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código deste artigo na pasta **Samples/Csharp/sharedcontent/console** .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [How to recognize speech](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore) (Como reconhecer voz)
