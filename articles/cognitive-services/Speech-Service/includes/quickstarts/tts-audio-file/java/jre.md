---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: 818a7abe6799846470ba60871ba01ea0ced80382
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671855"
---
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **File** > **New** > **Class**.

1. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

   ![Captura de ecrã da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo seguinte fragmento:

   ```Java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";

               // Replace below with your own region identifier from here: https://aka.ms/speech/sdkregion
               String serviceRegion = "YourServiceRegion";

               // Replace below with your own filename.
               String audioFileName = "helloworld.wav";

               // Replace below with your own filename.
               String text = "Hello world!";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioOutput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioOutput != null);

               SpeechSynthesizer synth = new SpeechSynthesizer(config, audioOutput);
               assert(synth != null);

               Future<SpeechSynthesisResult> task = synth.SpeakTextAsync(text);
               assert(task != null);

               SpeechSynthesisResult result = task.get();
               assert(result != null);

               if (result.getReason() == ResultReason.SynthesizingAudioCompleted) {
                   System.out.println("Speech synthesized to [" + audioFilename + "] for text [" + text + "]");
                   exitCode = 0;
               }
               else if (result.getReason() == ResultReason.Canceled) {
                   SpeechSynthesisCancellationDetails cancellation = SpeechSynthesisCancellationDetails.fromResult(result);
                   System.out.println("CANCELED: Reason=" + cancellation.getReason());

                   if (cancellation.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               }

               result.close();
               synth.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Substitua `helloworld.wav` a cadeia pelo seu próprio nome de ficheiro.

1. Substitua `Hello world!` a corda com o seu próprio texto.

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Debug**.
O seu texto é convertido em fala e guardado nos dados áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
