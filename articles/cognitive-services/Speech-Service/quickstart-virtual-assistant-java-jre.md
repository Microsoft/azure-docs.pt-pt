---
title: 'Início rápido: Personalizado voz em primeiro lugar virtual Assistente (pré-visualização), o Java (Windows, Linux) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como utilizar o SDK dos serviços cognitivos voz Software Development Kit () numa aplicação de consola Java. Ficará a saber como pode ligar a aplicação de cliente para um bot de Bot Framework criado anteriormente configurado para utilizar o canal direto de linha de voz e permitir uma experiência de Assistente de voz em primeiro lugar virtual.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027020"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Início rápido: Criar um Assistente de virtual de voz em primeiro lugar, com o SDK de voz, o Java

Neste artigo, vai criar uma aplicação de consola Java utilizando o [SDK de voz dos serviços cognitivos](speech-sdk.md). O aplicativo se conectará um bot criado anteriormente configurado para utilizar o canal direto de linha de voz, enviar um pedido de voz e retornar uma atividade de resposta de voz (se configurada). A aplicação baseia-se com o pacote Maven do SDK de voz e o Java de Eclipse IDE no Windows, Ubuntu Linux, ou no macOS. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operativo: (64-bit) do Windows, Ubuntu Linux 16.04/18.04 (64 bits) ou macOS 10.13 ou posterior
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).
* Um bot pré-configuradas criado com o Bot Framework versão 4.2 ou superior. O bot têm de subscrever o novo canal de "Fala de linha direta" para receber as entradas de voz.

    > [!NOTE]
    > Em pré-visualização, o canal direto de linha de voz atualmente, só suporta o **westus2** região.

    > [!NOTE]
    > A versão de avaliação de 30 dias para o padrão descrito em do escalão de preço [experimentar gratuitamente os serviços de voz](get-started.md) é restringido aos **westus** (não **westus2**) e, portanto, não é compatível com o Direct Voz da linha. Escalão gratuito e standard **westus2** subscrições são compatíveis.

Se estiver a executar o Ubuntu 16.04/18.04, certifique-se de que estas dependências estão instaladas antes de iniciar o Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se estiver a executar Windows (64-bit), certifique-se de que instalou o Microsoft Visual C++ redistribuível para a sua plataforma:
* [Baixe o Microsoft Visual C++ Redistributable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcional: Começar rapidamente

Neste início rápido descreve, passo a passo, como criar um aplicativo cliente simples para ligar ao seu bot habilitado para fala. Se preferir começar imediatamente, o código-fonte completo e pronto para compilação utilizado neste início rápido está disponível na [amostras do SDK de voz](https://aka.ms/csspeech/samples) sob a `quickstart` pasta.

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Além disso, para ativar o registo, atualize o **pom** arquivo para incluir a seguinte dependência.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra o recém-criado **Main** de classe e substitua os conteúdos do `Main.java` ficheiro com o seguinte código de partida.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. Na **principal** método, primeiro irá configurar seu `BotConnectorConfig` e utilizá-lo para criar um `SpeechBotConnector` instância. Isto irá ligar para o canal de voz de linha direta para interagir com o seu bot. Um `AudioConfig` instância também é utilizada para especificar a origem de entrada de áudio. Neste exemplo, o microfone do padrão é utilizado com `AudioConfig.fromDefaultMicrophoneInput()`.

    * Substitua a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição, o que pode ser obtido a partir de [aqui](get-started.md).
    * Substitua a cadeia de caracteres `YourServiceRegion` com o [região](regions.md) associados à subscrição.
    * Substitua a cadeia de caracteres `YourChannelSecret` com seu segredo de canal de voz de linha direta.

    > [!NOTE]
    > Em pré-visualização, o canal direto de linha de voz atualmente, só suporta o **westus2** região.

    > [!NOTE]
    > A versão de avaliação de 30 dias para o padrão descrito em do escalão de preço [experimentar gratuitamente os serviços de voz](get-started.md) é restringido aos **westus** (não **westus2**) e, portanto, não é compatível com o Direct Voz da linha. Escalão gratuito e standard **westus2** subscrições são compatíveis.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` depende de vários eventos para comunicar suas atividades de bot, resultados de reconhecimento de voz e outras informações. Adicione estes serviços de escuta de eventos a seguir.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Ligar o `SpeechBotConnector` para direto de linha de voz, invocando o `connectAsync()` método. Para testar o seu bot, é possível invocar o `listenOnceAsync` método para enviar a entrada de áudio do microfone. Além disso, também pode utilizar o `sendActivityAsync` método para enviar uma atividade personalizada como uma cadeia de caracteres serializada. Essas atividades personalizadas podem fornecer dados adicionais que o seu bot irá utilizar na conversação.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Guardar as alterações para o `Main` ficheiro.

1. Para dar suporte a reprodução de resposta, adicionará uma classe adicional que irão incluir métodos de utilitário para oferecer suporte a áudio. Para ativar o áudio, adicione outra nova classe de vazio ao seu projeto Java: selecione **arquivo** > **New** > **classe**.

1. Na **nova classe de Java** janela, introduza **speechsdk.quickstart** para o **pacote** campo, e **AudioPlayer** no **Nome** campo.

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra o recém-criado **AudioPlayer** de classe e substitua o código fornecido abaixo.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Guardar as alterações para o `AudioPlayer` ficheiro.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
A consola irá apresentar uma mensagem "Diga algo" neste momento, pode falar uma frase em inglês ou frase em que o seu bot vai entender. A conversão de voz será transmitido ao seu bot através do canal direto de linha de voz em que ela será reconhecida, processados pelo seu bot e a resposta é devolvida como uma atividade. Se o seu bot retorna a voz como resposta, o áudio será executado novamente com o `AudioPlayer` classe.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como a leitura de voz de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
