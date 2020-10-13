---
title: 'Como reconhecer as intenções da fala usando o Discurso SDK C #'
titleSuffix: Azure Cognitive Services
description: Neste guia, aprende-se a reconhecer as intenções da fala usando o Discurso SDK para C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 1138a970bf7c52182f13d0fd14d0178a2d0cfeba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918796"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Como reconhecer as intenções da fala usando o Discurso SDK para C #

O Cognitive Services [Speech SDK](speech-sdk.md) integra-se com o [serviço de compreensão da linguagem (LUIS)](https://www.luis.ai/home) para fornecer **reconhecimento de intenção.** Uma intenção é algo que o utilizador quer fazer, seja reservar um voo, ver tempo ou fazer uma chamada. O utilizador pode utilizar qualquer termo que pareça natural. Utilizando machine learning, o LUIS mapeia os pedidos dos utilizadores para as intenções que definiu.

> [!NOTE]
> Uma aplicação do LUIS define as intenções e entidades que quer ver reconhecidas. É separada da aplicação C# que utiliza o serviço Voz. Neste artigo, “aplicação LUIS” significa a aplicação do LUIS e “aplicação” significa o código C#.

Neste guia, utiliza o Speech SDK para desenvolver uma aplicação de consola C# que deriva das intenções das expressões dos utilizadores através do microfone do seu dispositivo. Vai aprender a:

> [!div class="checklist"]
>
> - Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK Voz
> - Crie uma configuração de discurso e obtenha um reconhecimento de intenção
> - Obter o modelo para a aplicação LUIS e adicionar as intenções de que precisa
> - Especificar o idioma do reconhecimento de voz
> - Reconhecer a voz a partir de um ficheiro
> - Utilizar o reconhecimento contínuo assíncrono e orientado por eventos

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os seguintes itens antes de iniciar este guia:

- Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se no serviço Voz para reconhecer as intenções de voz. Não precisa de uma subscrição do serviço Voz, apenas do LUIS.

LUIS usa três tipos de chaves:

| Tipo de chave  | Objetivo                                               |
| --------- | ----------------------------------------------------- |
| Criação | Permite criar e modificar aplicações LUIS programáticamente |
| Inicial   | Permite testar a sua aplicação LUIS apenas com texto   |
| Ponto final  | Autoriza o acesso a uma determinada aplicação LUIS            |

Para este guia, precisa do tipo de chave de ponta. Este guia utiliza o exemplo da aplicação Home Automation LUIS, que pode criar seguindo o quickstart da [app De domótica pré-construída.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Se criou uma aplicação PRÓPRIA LUIS, pode usá-la em vez disso.

Quando cria uma aplicação LUIS, o LUIS gera automaticamente uma chave de arranque para que possa testar a aplicação utilizando consultas de texto. Esta chave não permite a integração do serviço de discurso e não funcionará com este guia. Crie um recurso LUIS no painel Azure e atribua-o à app LUIS. Pode utilizar o nível de subscrição gratuito para este guia.

Depois de criar o recurso LUIS no painel Azure, inicie sessão no [portal LUIS,](https://www.luis.ai/home)escolha a sua aplicação na página **My Apps** e, em seguida, mude para a página **Manage** da app. Por fim, selecione **Chaves e Pontos finais** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página **de definições de Chaves e Ponto final:**

1. Percorra a secção **Recursos e Chaves e** selecione Recurso de **atribuição**.
1. Na chave Atribuir uma chave para a sua caixa de diálogo **de aplicações,** faça as seguintes alterações:

   - Sob **Inquilino,** escolha **a Microsoft.**
   - Em **Nome de Assinatura,** escolha a subscrição Azure que contém o recurso LUIS que pretende utilizar.
   - Em **Tecla,** escolha o recurso LUIS que pretende utilizar com a app.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página.

1. Selecione o ícone ao lado de uma chave para copiá-lo para a área de transferência. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de voz no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Em seguida, adicione código ao projeto.

1. A partir do **Solution Explorer,** abra o ficheiro **Program.cs**.

1. Substitua o bloco de `using` declarações no início do processo com as seguintes declarações:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Substitua o `Main()` método fornecido, com o seguinte equivalente assíncronos:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Crie um método assíncronos `RecognizeIntentAsync()` vazio, como mostra aqui:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. No corpo deste novo método, adicione este código:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Substitua os marcadores de posição neste método pela chave, a região e a ID de aplicação da sua subscrição do LUIS, da seguinte forma.

   | Marcador de posição | Substituir |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Pela chave do ponto final do LUIS. Mais uma vez, tens de obter este item do teu painel Azure, não uma "chave de arranque". Pode encontrá-lo na página **Chaves e Endpoints** da sua aplicação (em **Gestão)** no [portal LUIS](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Pelo breve identificador da região na qual a subscrição do LUIS se encontra, como `westus` para E.U.A. Oeste. Veja [Regions](regions.md) (Regiões). |
   | `YourLanguageUnderstandingAppId` | Pelo ID de aplicação do LUIS. Pode encontrá-lo na página **de Definições** da sua aplicação no [portal LUIS.](https://www.luis.ai/home) |

Com estas alterações feitas, pode construir **(Control+Shift+B)** e executar **(F5)** a aplicação. Quando for solicitado, tente dizer "Desligue as luzes" no microfone do seu PC. A aplicação apresenta o resultado na janela da consola.

As secções seguintes incluem abordagens ao código.

## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenções

Em primeiro lugar, precisa de criar uma configuração de fala a partir da sua chave e região do ponto final LUIS. Pode utilizar configurações de fala para criar reconhecimentos para as várias capacidades do SDK do discurso. A configuração da fala tem várias formas de especificar a subscrição que pretende utilizar; aqui, `FromSubscription` usamos, que leva a chave de subscrição e região.

> [!NOTE]
> Utilize a chave e a região da sua assinatura LUIS, não uma subscrição de serviço de discurso.

Em seguida, crie um reconhecedor de intenções com `new IntentRecognizer(config)`. Uma vez que a configuração já sabe qual a subscrição a utilizar, não precisa de especificar novamente a chave de subscrição e o ponto final ao criar o reconhecimento.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo do LUIS e adicionar intenções

Agora, utilize `LanguageUnderstandingModel.FromAppId()` para importar o modelo da aplicação LUIS e adicione as intenções que pretende reconhecer através do método `AddIntent()` do reconhecedor. Estes dois passos indicam as palavras que o utilizador irá provavelmente utilizar nos pedidos, o que melhora a precisão do reconhecimento de voz. Não precisa adicionar todas as intenções da aplicação se não precisar de as reconhecer todas na sua aplicação.

Para adicionar intenções, deve apresentar três argumentos: o modelo LUIS (que foi criado e `model` nomeado), o nome de intenção, e um ID de intenção. A diferença entre o ID e o nome da intenção é a seguinte.

| `AddIntent()`&nbsp;argumento | Objetivo |
| --------------------------- | ------- |
| `intentName` | O nome da intenção, conforme definido na aplicação LUIS. Este valor deve corresponder exatamente ao nome de intenção do LUIS. |
| `intentID` | Um ID que o SDK de Voz atribui a uma intenção reconhecida. Este valor pode ser o que quiser; não precisa de corresponder ao nome de intenção definido na app LUIS. Se o mesmo código processar várias intenções, pode, por exemplo, utilizar o mesmo ID para essas intenções. |

A aplicação Home Automation LUIS tem duas intenções: uma para ligar um dispositivo e outra para desligar um dispositivo. As linhas abaixo adicionam essas intenções ao reconhecedor. Substitua as três linhas `AddIntent` no método `RecognizeIntentAsync()` por este código.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Em vez de adicionar intenções individuais, também pode usar o `AddAllIntents` método para adicionar todas as intenções de um modelo ao reconhecedor.

## <a name="start-recognition"></a>Iniciar o reconhecimento

Com o reconhecedor criado e as intenções adicionadas, o reconhecimento pode começar. O SDK Voz suporta o reconhecimento único e o reconhecimento contínuo.

| Modo de reconhecimento | Métodos a chamar | Resultado |
| ---------------- | --------------- | ------ |
| Único | `RecognizeOnceAsync()` | Devolve a intenção reconhecida, se existir, após uma expressão. |
| Contínuo | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Reconhece múltiplas expressões; emite eventos (por exemplo, `IntermediateResultReceived` ) quando os resultados estão disponíveis. |

A aplicação utiliza o modo single-shot e assim chamadas `RecognizeOnceAsync()` para começar o reconhecimento. O resultado é um objeto `IntentRecognitionResult` que inclui informações sobre a intenção reconhecida. Extrai a resposta LUIS JSON utilizando a seguinte expressão:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

A aplicação não analisa o resultado do JSON. Só exibe o texto JSON na janela da consola.

![Resultados únicos do reconhecimento LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar o idioma do reconhecimento

Por predefinição, o LUIS reconhece intenções em inglês dos E.U.A. (`en-us`). Se atribuir um código de região à propriedade `SpeechRecognitionLanguage` da configuração da voz, poderá reconhecer intenções noutros idiomas. Por exemplo, adicione `config.SpeechRecognitionLanguage = "de-de";` a nossa aplicação antes de criar o reconhecedor para reconhecer as intenções em alemão. Para mais informações, consulte [o suporte linguístico LUIS.](../LUIS/luis-language-support.md#languages-supported)

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo a partir de um ficheiro

O código seguinte ilustra duas capacidades extra do reconhecimento de intenções quando é utilizado o SDK Voz. O primeiro, já referido antes, é o reconhecimento contínuo, no qual o reconhecedor emite eventos quando há resultados disponíveis. Esses eventos podem, depois, ser processados pelos processadores de eventos que fornecer. Com reconhecimento contínuo, você chama o método do reconhecedor para iniciar o `StartContinuousRecognitionAsync()` reconhecimento em vez de `RecognizeOnceAsync()` .

A outra capacidade é ler o áudio que contém a voz que vai ser processada a partir de um ficheiro WAV. A implementação envolve a criação de uma configuração áudio que pode ser usada ao criar o reconhecimento de intenções. O ficheiro só pode ter um canal (mono) com uma taxa de amostragem de 16 kHz.

Para experimentar estas funcionalidades, elimine ou comente o corpo do `RecognizeIntentAsync()` método e adicione o seguinte código no seu lugar.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Reveja o código para incluir a chave, a região e o ID de aplicação do ponto final do LUIS e para adicionar as intenções de Home Automation, como fez antes. Mude `whatstheweatherlike.wav` para o nome do seu ficheiro áudio gravado. Em seguida, construa, copie o ficheiro áudio para o diretório de construção e execute a aplicação.

Por exemplo, se disser "Desligue as luzes", faça uma pausa e diga "Ligue as luzes" no seu ficheiro áudio gravado, pode aparecer a saída da consola semelhante à seguinte:

![Resultados do reconhecimento do ficheiro áudio LUIS](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código deste artigo na pasta **samples/csharp/sharedcontent/console.**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Reconhecer a fala a partir de um microfone](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
