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
ms.openlocfilehash: 83149a8422db25106a97b1711c0ae9ce3c6603eb
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465670"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Início rápido: Criar um Assistente de virtual de voz em primeiro lugar, com o SDK de voz, o Java

Também estão disponíveis para inícios Rápidos [voz em texto](quickstart-java-jre.md) e [tradução de voz](quickstart-translate-speech-java-jre.md).

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

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
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

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
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

1. Para dar suporte a reprodução de resposta, adicionará uma classe adicional que irá transformar o objeto de PullAudioOutputStream devolvido do getAudio() API a um java InputStream para facilitar a do processamento. Este ActivityAudioStream é uma classe especializada que irá processar a resposta de áudio do "canal direto de linha de voz". Ele fornecerá acessadores para obter informações de formato de áudio necessárias para a manipulação de reprodução: Para isso, selecione **arquivo** > **New** > **classe**.

1. Na **nova classe de Java** janela, introduza **speechsdk.quickstart** para o **pacote** campo, e **ActivityAudioStream** no  **Nome** campo.

1. Abra o recém-criado **ActivityAudioStream** de classe e substitua o código fornecido abaixo.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Guardar as alterações para o `ActivityAudioStream` ficheiro.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
A consola irá apresentar uma mensagem "Diga algo" neste momento, pode falar uma frase em inglês ou frase em que o seu bot vai entender. A conversão de voz será transmitido ao seu bot através do canal direto de linha de voz em que ela será reconhecida, processados pelo seu bot e a resposta é devolvida como uma atividade. Se o seu bot retorna a voz como resposta, o áudio será executado novamente com o `AudioPlayer` classe.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como a leitura de voz de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
