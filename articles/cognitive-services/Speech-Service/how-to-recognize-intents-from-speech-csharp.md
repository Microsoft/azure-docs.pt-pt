---
title: 'Como reconhecer as intenções da fala usando o Discurso SDK C #'
titleSuffix: Azure Cognitive Services
description: Neste guia, aprende-se a reconhecer as intenções da fala usando o SDK de Fala para C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: 41ebcb7b44ea88af06a30a611960fd8bb0ceddee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402227"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Como reconhecer as intenções da fala usando o SDK do Discurso para C #

O [SDK de Serviços](speech-sdk.md) Cognitivos integra-se com o serviço de Compreensão da [Língua (LUIS)](https://www.luis.ai/home) para fornecer **reconhecimento de intenções.** Uma intenção é algo que o utilizador quer fazer, seja reservar um voo, ver tempo ou fazer uma chamada. O utilizador pode utilizar qualquer termo que pareça natural. Utilizando machine learning, o LUIS mapeia os pedidos dos utilizadores às intenções que definiu.

> [!NOTE]
> Uma aplicação do LUIS define as intenções e entidades que quer ver reconhecidas. É separada da aplicação C# que utiliza o serviço Voz. Neste artigo, “aplicação LUIS” significa a aplicação do LUIS e “aplicação” significa o código C#.

Neste guia, utiliza o SDK de Speech para desenvolver uma aplicação de consola C# que obtém intenções de declarações do utilizador através do microfone do seu dispositivo. Vai aprender a:

> [!div class="checklist"]
>
> - Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK Voz
> - Crie uma configuração de fala e obtenha um reconhecimento de intenção
> - Obter o modelo para a aplicação LUIS e adicionar as intenções de que precisa
> - Especificar o idioma do reconhecimento de voz
> - Reconhecer a voz a partir de um ficheiro
> - Utilizar o reconhecimento contínuo assíncrono e orientado por eventos

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os seguintes itens antes de iniciar este guia:

- Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).
- [Estúdio Visual 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se no serviço Voz para reconhecer as intenções de voz. Não precisa de uma subscrição do serviço Voz, apenas do LUIS.

Luis usa três tipos de chaves:

| Tipo de chave  | Objetivo                                               |
| --------- | ----------------------------------------------------- |
| Criação | Permite criar e modificar as aplicações do LUIS programáticamente |
| Inicial   | Permite testar a sua aplicação LUIS usando apenas texto   |
| Ponto Final  | Autoriza o acesso a uma determinada app LUIS            |

Para este guia, precisa do tipo de chave de ponto final. Este guia utiliza o exemplo da aplicação Home Automation LUIS, que pode criar seguindo a [aplicação de domótica pré-construída Utilização.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Se criou uma aplicação LUIS própria, pode usá-la.

Ao criar uma aplicação LUIS, o LUIS gera automaticamente uma chave de arranque para que possa testar a aplicação usando consultas de texto. Esta chave não permite a integração do serviço da Fala e não funcionará com este guia. Crie um recurso LUIS no painel azure e atribua-o à aplicação LUIS. Pode utilizar o nível de subscrição gratuito para este guia.

Depois de criar o recurso LUIS no painel de instrumentos Do Azure, inicie sessão no [portal LUIS,](https://www.luis.ai/home)escolha a sua aplicação na página **My Apps** e, em seguida, mude para a página **'Gerir'** da aplicação. Por fim, selecione **Keys e Endpoints** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página de **definições de Teclas e ponto final:**

1. Desloque-se até à secção **Recursos e Teclas** e selecione **recurso atribuir**.
1. Na placa de atribuição de uma chave para a sua caixa de diálogo de **aplicações,** epreça as seguintes alterações:

   - Under **Tenant,** escolha **Microsoft**.
   - Em **nome de subscrição,** escolha a subscrição Azure que contém o recurso LUIS que pretende utilizar.
   - Em **Chave,** escolha o recurso LUIS que pretende utilizar com a app.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página.

1. Selecione o ícone ao lado de uma tecla para copiá-lo para a área de recortes. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de voz no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Em seguida, adicione código ao projeto.

1. Do **Solution Explorer,** abra o ficheiro **Program.cs**.

1. Substitua o `using` bloco de declarações no início do processo pelas seguintes declarações:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Substitua `Main()` o método fornecido, com o seguinte equivalente assíncrono:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Crie um método `RecognizeIntentAsync()`assíncrono vazio, como mostrado aqui:

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
   | `YourLanguageUnderstandingSubscriptionKey` | Pela chave do ponto final do LUIS. Mais uma vez, você deve obter este item do seu painel Azure, não uma "chave de arranque". Pode encontrá-lo na página **Chaves e Pontos Finais** da sua aplicação (em **Gerir)** no [portal LUIS](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Pelo breve identificador da região na qual a subscrição do LUIS se encontra, como `westus` para E.U.A. Oeste. Veja [Regions](regions.md) (Regiões). |
   | `YourLanguageUnderstandingAppId` | Pelo ID de aplicação do LUIS. Pode encontrá-lo na página **Definições** da sua aplicação no [portal LUIS](https://www.luis.ai/home). |

Com estas alterações feitas, pode construir (**Control+Shift+B)** e executar **(F5**) a aplicação. Quando for solicitado, tente dizer "apagam as luzes" no microfone do seu pc. A aplicação apresenta o resultado na janela da consola.

As secções seguintes incluem abordagens ao código.

## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenções

Em primeiro lugar, é necessário criar uma configuração de fala a partir da sua chave e região de ponto final LUIS. Pode usar configurações de fala para criar reconhecíveis para as várias capacidades do SDK de Fala. A configuração da fala tem várias formas de especificar a subscrição que pretende utilizar; aqui, usamos `FromSubscription`, que leva a chave de subscrição e região.

> [!NOTE]
> Utilize a chave e região da sua assinatura LUIS, não uma subscrição do serviço Speech.

Em seguida, crie um reconhecedor de intenções com `new IntentRecognizer(config)`. Uma vez que a configuração já sabe qual subscrição usar, não precisa de especificar novamente a chave de subscrição e o ponto final ao criar o reconhecedor.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo do LUIS e adicionar intenções

Agora, utilize `LanguageUnderstandingModel.FromAppId()` para importar o modelo da aplicação LUIS e adicione as intenções que pretende reconhecer através do método `AddIntent()` do reconhecedor. Estes dois passos indicam as palavras que o utilizador irá provavelmente utilizar nos pedidos, o que melhora a precisão do reconhecimento de voz. Não precisa de adicionar todas as intenções da aplicação se não precisar de as reconhecer todas na sua aplicação.

Para adicionar intenções, deve apresentar três argumentos: o modelo LUIS `model`(que foi criado e nomeado), o nome da intenção e um ID de intenção. A diferença entre o ID e o nome da intenção é a seguinte.

| `AddIntent()`&nbsp;argumento | Objetivo |
| --------------------------- | ------- |
| `intentName` | O nome da intenção, conforme definido na aplicação LUIS. Este valor deve corresponder exatamente ao nome de intenção do LUIS. |
| `intentID` | Um ID que o SDK de Voz atribui a uma intenção reconhecida. Este valor pode ser o que quiser; não precisa de corresponder ao nome de intenção definido na aplicação LUIS. Se o mesmo código processar várias intenções, pode, por exemplo, utilizar o mesmo ID para essas intenções. |

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
| Contínuo | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Reconhece várias expressões; emite eventos `IntermediateResultReceived`(por exemplo, ) quando os resultados estão disponíveis. |

A aplicação utiliza o modo `RecognizeOnceAsync()` single-shot e assim chamadas para iniciar o reconhecimento. O resultado é um objeto `IntentRecognitionResult` que inclui informações sobre a intenção reconhecida. Extrai a resposta LUIS JSON utilizando a seguinte expressão:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

A aplicação não analisa o resultado da JSON. Apenas exibe o texto JSON na janela da consola.

![Resultados únicos do reconhecimento luis](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar o idioma do reconhecimento

Por predefinição, o LUIS reconhece intenções em inglês dos E.U.A. (`en-us`). Se atribuir um código de região à propriedade `SpeechRecognitionLanguage` da configuração da voz, poderá reconhecer intenções noutros idiomas. Por exemplo, `config.SpeechRecognitionLanguage = "de-de";` adicione a nossa aplicação antes de criar o reconhecível para reconhecer as intenções em alemão. Para mais informações, consulte o [suporte linguístico da LUIS.](../LUIS/luis-language-support.md#languages-supported)

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo a partir de um ficheiro

O código seguinte ilustra duas capacidades extra do reconhecimento de intenções quando é utilizado o SDK Voz. O primeiro, já referido antes, é o reconhecimento contínuo, no qual o reconhecedor emite eventos quando há resultados disponíveis. Esses eventos podem, depois, ser processados pelos processadores de eventos que fornecer. Com reconhecimento contínuo, chama-se `StartContinuousRecognitionAsync()` método do reconhecedor `RecognizeOnceAsync()`para iniciar o reconhecimento em vez de .

A outra capacidade é ler o áudio que contém a voz que vai ser processada a partir de um ficheiro WAV. A implementação envolve a criação de uma configuração áudio que pode ser usada na criação do reconhecedor de intenções. O ficheiro só pode ter um canal (mono) com uma taxa de amostragem de 16 kHz.

Para experimentar estas funcionalidades, elimine ou `RecognizeIntentAsync()` comente o corpo do método e adicione o seguinte código no seu lugar.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Reveja o código para incluir a chave, a região e o ID de aplicação do ponto final do LUIS e para adicionar as intenções de Home Automation, como fez antes. Mude `whatstheweatherlike.wav` para o nome do seu ficheiro áudio gravado. Em seguida, construir, copiar o ficheiro áudio para o diretório de construção, e executar a aplicação.

Por exemplo, se disser "Desligue as luzes", faça uma pausa e diga "Acenda as luzes" no seu ficheiro áudio gravado, pode aparecer a saída da consola semelhante à seguinte:

![Resultados do reconhecimento do ficheiro áudio LUIS](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código deste artigo na **pasta de amostras/csharp/compartilhado/consola.**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Reconhecer o discurso a partir de um microfone](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
