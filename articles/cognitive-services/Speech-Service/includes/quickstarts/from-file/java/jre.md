---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 0dbfab9b1886b39f166b94447fed8dc6707404bd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796115"
---
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=jre)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

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
               // Replace below with your own service region (e.g., "westus").
               String serviceRegion = "YourServiceRegion";
               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               if (result.getReason() == ResultReason.RecognizedSpeech) {
                   System.out.println("We recognized: " + result.getText());
                   exitCode = 0;
               }
               else if (result.getReason() == ResultReason.NoMatch) {
                   System.out.println("NOMATCH: Speech could not be recognized.");
               }
               else if (result.getReason() == ResultReason.Canceled) {
                   CancellationDetails cancellation = CancellationDetails.fromResult(result);
                   System.out.println("CANCELED: Reason=" + cancellation.getReason());

                   if (cancellation.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               }

               reco.close();

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

1. Substitua a cadeia de caracteres `whatstheweatherlike.wav` pelo seu próprio nome de arquivo.

1. Guarde as alterações ao projeto.

> [!NOTE]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
Os primeiros 15 segundos de entrada de fala do arquivo de áudio serão reconhecidos e registrados na janela do console.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]