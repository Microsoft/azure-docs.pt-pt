---
title: 'Quickstart: Assistente de voz personalizado para Java (Windows, Linux) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenderás a usar o Cognitive Services Speech SDK numa aplicação de consola java. Você vai aprender como você pode conectar a sua aplicação de cliente a um bot Bot Framework previamente criado configurado para usar o canal Direct Line Speech e ativar uma experiência de assistente de voz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 45719eebb9cd74b0a5c4278e87b90978dcc3790f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119699"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Quickstart: Criar um assistente de voz com o Speech SDK, Java (Pré-visualização)

Os quickstarts também estão disponíveis para [discurso a texto,](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre) [texto-a-fala,](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)e [tradução da fala.](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre)

Neste artigo, cria-se uma aplicação de consola Java utilizando o [Azure Cognitive Services Speech SDK](speech-sdk.md). A aplicação liga-se a um bot previamente autoria configurado para usar o canal Direct Line Speech, envia um pedido de voz e devolve uma atividade de resposta de voz (se configurado). A aplicação é construída com o pacote Speech SDK Maven e o Eclipse Java IDE no Windows, Ubuntu Linux ou no macOS. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- Sistema operativo: Windows (64-bits), Ubuntu Linux 16.04/18.04 (64-bits) ou macOS 10.13 ou mais tarde.
- [Eclipse Java IDE.](https://www.eclipse.org/downloads/)
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Uma chave de subscrição Azure para o serviço De Fala. [Obtenha um de graça](get-started.md) ou crie-o no [portal Azure.](https://portal.azure.com)
- Um bot pré-reconfigurado criado utilizando a versão Bot Framework 4.2 ou superior. O bot precisa subscrever o novo canal Direct Line Speech para receber inputs de voz.

  > [!NOTE]
  > Consulte [a lista de regiões apoiadas para assistentes](regions.md#voice-assistants) de voz e certifique-se de que os seus recursos são implantados numa dessas regiões.

Se estiver a executar ubuntu 16.04/18.04, certifique-se de que estas dependências estão instaladas antes de iniciar o Eclipse:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se estiver a executar o Windows (64 bits), C++ certifique-se de que instalou o Microsoft Visual Redistribuable para a sua plataforma:

- [Baixar Microsoft C++ Visual Redistribuable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcional: Começar rápido

Este quickstart descreve, passo a passo, como fazer uma aplicação simples do cliente para se conectar ao seu bot ativado pela fala. Se quiser mergulhar, o código fonte completo e pronto a compilar utilizado neste quickstart está disponível nas [amostras do Speech SDK](https://aka.ms/csspeech/samples) sob a pasta `quickstart`.

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Além disso, para permitir a exploração madeireira, atualize o ficheiro _pom.xml_ para incluir a seguinte dependência:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela da **Classe New Java,** insira _speechsdk.quickstart_ no campo **Pacote** e _Main_ no campo **Name.**

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra a classe `Main` recém-criada e substitua o conteúdo do ficheiro `Main.java` pelo seguinte código de partida:

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

1. No método `main`, configura primeiro o seu `DialogServiceConfig` e usa-o para criar uma `DialogServiceConnector` instância. Esta instância liga-se ao canal Direct Line Speech para interagir com o seu bot. Uma instância `AudioConfig` também é usada para especificar a fonte para a entrada de áudio. Neste exemplo, o microfone predefinido é utilizado com `AudioConfig.fromDefaultMicrophoneInput()`.

   - Substitua o `YourSubscriptionKey` de cadeias com a sua chave de subscrição, que pode obter a partir [deste website](get-started.md).
   - Substitua a `YourServiceRegion` de cadeia seleções com a [região](regions.md) associada à sua subscrição.

   > [!NOTE]
   > Consulte [a lista de regiões apoiadas para assistentes](regions.md#voice-assistants) de voz e certifique-se de que os seus recursos são implantados numa dessas regiões.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. O conector `DialogServiceConnector` conta com vários eventos para comunicar as suas atividades bot, resultados de reconhecimento de fala e outras informações. Adicione estes ouvintes do evento a seguir.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Ligue `DialogServiceConnector` ao Discurso da Linha Direta invocando o método `connectAsync()`. Para testar o seu bot, pode invocar o método `listenOnceAsync` para enviar a entrada de áudio do microfone. Além disso, também pode usar o método `sendActivityAsync` para enviar uma atividade personalizada como uma cadeia serializada. Estas atividades personalizadas podem fornecer dados adicionais que o seu bot utiliza na conversação.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Guarde alterações no ficheiro `Main`.

1. Para suportar a reprodução da resposta, adicione uma classe adicional que transforme o objeto PullAudioOutputStream devolvido da API getAudio() a um Java InputStream para facilitar o manuseamento. Este `ActivityAudioStream` é uma classe especializada que lida com a resposta áudio do canal Direct Line Speech. Fornece aos acessórios para obter informações de formato áudio que são necessárias para lidar com a reprodução. Para isso, selecione **File** > **New** > **Class**.

1. Na janela **new java class,** insira _speechsdk.quickstart_ no campo **Pacote** e _ActivityAudioStream_ no campo **Nome.**

1. Abra a classe `ActivityAudioStream` recém-criada e substitua-a pelo seguinte código:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
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
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
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
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
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
         * As this implementation does not buffer, this will be defaulted to 0
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
         * Enum defining the types of audio encoding supported by this stream.
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

1. Guarde alterações no ficheiro `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Selecione F11 ou selecione **Executar** > **Debug**.
A consola exibe a mensagem "Diga alguma coisa".
Neste ponto, diga uma frase ou frase em inglês que o seu bot possa entender. O seu discurso é transmitido ao seu bot através do canal Direct Line Speech, onde é reconhecido e processado pelo seu bot. A resposta é devolvida como uma atividade. Se o seu bot devolver o discurso como resposta, o áudio é reproduzido utilizando a aula de `AudioPlayer`.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Passos seguintes

Amostras adicionais, como ler o discurso a partir de um ficheiro áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)

## <a name="see-also"></a>Veja também

- [Sobre assistentes de voz](voice-assistants.md)
- [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
- [Palavras-chave personalizadas](speech-devices-sdk-create-kws.md)
- [Conecte o discurso da linha direta ao seu bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explore as amostras de Java no GitHub](https://aka.ms/csspeech/samples)
