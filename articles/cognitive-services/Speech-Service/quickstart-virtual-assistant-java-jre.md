---
title: 'Início rápido: Personalizar voz-primeiro assistente virtual (visualização), Java (Windows, Linux)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a usar o SDK (Software Development Kit) de fala de serviços cognitivas em um aplicativo de console do Java. Você aprenderá como é possível conectar seu aplicativo cliente a um bot Framework do bot criado anteriormente configurado para usar o canal de fala de linha direta e habilitar uma experiência de assistente virtual de voz primeiro.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: b1be09a2af712277ccaad827b8e84e24ed9f5c5c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553253"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Início rápido: Criar um assistente virtual de voz-primeiro com o SDK de fala, Java

Os guias de início rápido também estão disponíveis para conversão de [fala em texto](quickstart-java-jre.md) e [fala](quickstart-translate-speech-java-jre.md).

Neste artigo, você cria um aplicativo de console Java usando o [SDK de fala dos serviços cognitivas](speech-sdk.md). O aplicativo se conectará a um bot criado anteriormente configurado para usar o canal de fala de linha direta, enviar uma solicitação de voz e retornar uma atividade de resposta de voz (se configurada). O aplicativo é criado com o pacote do SDK do Speech e o eclipse Java IDE no Windows, Ubuntu Linux ou no macOS. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema Operacional: Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits) ou macOS 10,13 ou posterior
* [Java IDE Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de assinatura do Azure para serviços de fala. [Obtenha um gratuitamente](get-started.md) ou crie-o no [portal do Azure](https://portal.azure.com).
* Um bot pré-configurado criado usando o bot Framework versão 4,2 ou superior. O bot precisaria assinar o novo canal "Direct line Speech" para receber entradas de voz.

    > [!NOTE]
    > A Direct line Speech (visualização) está disponível atualmente em um subconjunto de regiões de serviços de fala. Consulte [a lista de regiões com suporte para assistentes virtuais primeiro de voz](regions.md#Voice-first virtual assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

Se você estiver executando o Ubuntu 16.04/18.04, verifique se essas dependências estão instaladas antes de iniciar o eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se você estiver executando o Windows (64 bits), certifique-se de ter instalado o C++ Microsoft Visual redistribuível para sua plataforma:
* [Baixe o Microsoft C++ Visual redistribuível para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcional: Comece rapidamente

Este guia de início rápido descreverá, passo a passo, como criar um aplicativo cliente simples para se conectar ao bot habilitado para fala. Se você preferir se aprofundar diretamente no, o código-fonte completo e pronto para compilar usado neste guia de início rápido está disponível nos [exemplos](https://aka.ms/csspeech/samples) `quickstart` do SDK de fala na pasta.

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Além disso, para habilitar o registro em log, atualize o arquivo **pom. xml** para incluir a dependência a seguir.

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

1. Abra a classe **principal** recém-criada e substitua o conteúdo do `Main.java` arquivo pelo código inicial a seguir.

    ```java
    package speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
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

1. No método **Main** , primeiro você configurará seu `DialogServiceConfig` e o usará para criar `DialogServiceConnector` uma instância. Isso se conectará ao canal de fala de linha direta para interagir com o bot. Uma `AudioConfig` instância também é usada para especificar a fonte de entrada de áudio. Neste exemplo, o microfone padrão é usado com `AudioConfig.fromDefaultMicrophoneInput()`.

    * Substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura, que pode ser obtida [aqui](get-started.md).
    * Substitua a cadeia `YourServiceRegion` de caracteres pela [região](regions.md) associada à sua assinatura.
    * Substitua a cadeia `YourChannelSecret` de caracteres pelo seu segredo de canal de fala de linha direta.

    > [!NOTE]
    > A Direct line Speech (visualização) está disponível atualmente em um subconjunto de regiões de serviços de fala. Consulte [a lista de regiões com suporte para assistentes virtuais primeiro de voz](regions.md#voice-first-virtual-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector`depende de vários eventos para comunicar suas atividades de bot, resultados de reconhecimento de fala e outras informações. Adicione esses ouvintes de evento em seguida.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Conecte o `DialogServiceConnector` para direcionar a linha de fala invocando o `connectAsync()` método. Para testar o bot, você pode invocar `listenOnceAsync` o método para enviar a entrada de áudio do microfone. Além disso, você também pode usar `sendActivityAsync` o método para enviar uma atividade personalizada como uma cadeia de caracteres serializada. Essas atividades personalizadas podem fornecer dados adicionais que o bot usará na conversa.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Salve as alterações no `Main` arquivo.

1. Para dar suporte à reprodução de resposta, você adicionará uma classe adicional que transformará o objeto PullAudioOutputStream retornado da API GetAudio () em um InputStream Java para facilitar o manuseio. Este ActivityAudioStream é uma classe especializada que manipulará a resposta de áudio do "canal de fala de linha direta". Ele fornecerá aos acessadores para obter as informações de formato de áudio necessárias para lidar com a reprodução: Para esse **arquivo** > , selecione**nova** > **classe**.

1. Na janela **nova classe Java** , insira **speechsdk. QuickStart** no campo **pacote** e **ActivityAudioStream** no campo **nome** .

1. Abra a classe **ActivityAudioStream** recém-criada e substitua pelo código fornecido abaixo.

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

1. Salve as alterações no `ActivityAudioStream` arquivo.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
O console exibirá uma mensagem "Digamos algo" neste ponto, você pode falar uma frase ou sentença em inglês que seu bot entenderá. Sua fala será transmitida para o bot por meio do canal de fala de linha direta, onde será reconhecido, processado pelo bot e a resposta será retornada como uma atividade. Se o bot retornar a fala como resposta, o áudio será reproduzido usando a `AudioPlayer` classe.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)

## <a name="see-also"></a>Consulte também

- [Sobre os assistentes virtuais da primeira voz](voice-first-virtual-assistants.md)
- [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
- [Palavras de ativação personalizadas](speech-devices-sdk-create-kws.md)
- [Conecte a fala de linha direta ao bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)
