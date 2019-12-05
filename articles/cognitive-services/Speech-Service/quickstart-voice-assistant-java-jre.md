---
title: 'Início rápido: Assistente de voz personalizado para Java (Windows, Linux)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a usar o SDK de fala de serviços cognitivas em um aplicativo de console Java. Você aprenderá como é possível conectar seu aplicativo cliente a um bot da estrutura de bot criado anteriormente configurado para usar o canal de fala de linha direta e habilitar uma experiência de assistente de voz.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: bidishac
ms.openlocfilehash: 29f6596408a825a571682d14aceca01fa8108515
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815852"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Início rápido: criar um assistente de voz com o SDK de fala, Java (visualização)

Os guias de início rápido também estão disponíveis para conversão de fala em [texto](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), [texto em fala](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)e [tradução de fala](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

Neste artigo, você cria um aplicativo de console Java usando o [SDK de fala dos serviços cognitivas do Azure](speech-sdk.md). O aplicativo se conecta a um bot criado anteriormente configurado para usar o canal de fala de linha direta, envia uma solicitação de voz e retorna uma atividade de resposta de voz (se configurada). O aplicativo é criado com o pacote do SDK do Speech e o eclipse Java IDE no Windows, Ubuntu Linux ou no macOS. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- Sistema operacional: Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits) ou macOS 10,13 ou posterior.
- [Java IDE Eclipse](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md) ou crie-o no [portal do Azure](https://portal.azure.com).
- Um bot pré-configurado criado usando o bot Framework versão 4,2 ou superior. O bot precisa assinar o novo canal de fala de linha direta para receber entradas de voz.

  > [!NOTE]
  > Consulte [a lista de regiões com suporte para assistentes de voz](regions.md#voice-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

Se você estiver executando o Ubuntu 16.04/18.04, verifique se essas dependências estão instaladas antes de iniciar o eclipse:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se você estiver executando o Windows (64 bits), certifique-se de ter instalado o C++ Microsoft Visual redistribuível para sua plataforma:

- [Baixe o Microsoft C++ Visual redistribuível para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcional: introdução rápida

Este guia de início rápido descreve, passo a passo, como fazer com que um aplicativo cliente simples se conecte ao bot habilitado para fala. Se você quiser mergulhar diretamente no, o código-fonte completo e pronto para compilar usado neste guia de início rápido está disponível nos [exemplos do SDK de fala](https://aka.ms/csspeech/samples) na pasta `quickstart`.

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Além disso, para habilitar o registro em log, atualize o arquivo _pom. xml_ para incluir a seguinte dependência:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela **nova classe Java** , insira _speechsdk. QuickStart_ no campo **pacote** e _principal_ no campo **nome** .

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra a classe de `Main` recém-criada e substitua o conteúdo do arquivo `Main.java` pelo seguinte código inicial:

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

1. No método `main`, primeiro você configura o `DialogServiceConfig` e o usa para criar uma instância `DialogServiceConnector`. Essa instância se conecta ao canal de fala de linha direta para interagir com o bot. Uma instância de `AudioConfig` também é usada para especificar a fonte de entrada de áudio. Neste exemplo, o microfone padrão é usado com `AudioConfig.fromDefaultMicrophoneInput()`.

   - Substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura, que você pode obter deste [site](get-started.md).
   - Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura.

   > [!NOTE]
   > Consulte [a lista de regiões com suporte para assistentes de voz](regions.md#voice-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. O conector `DialogServiceConnector` se baseia em vários eventos para comunicar suas atividades de bot, resultados de reconhecimento de fala e outras informações. Adicione esses ouvintes de evento em seguida.

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

1. Conecte `DialogServiceConnector` para direcionar a fala de linha invocando o método `connectAsync()`. Para testar o bot, você pode invocar o método `listenOnceAsync` para enviar a entrada de áudio do microfone. Além disso, você também pode usar o método `sendActivityAsync` para enviar uma atividade personalizada como uma cadeia de caracteres serializada. Essas atividades personalizadas podem fornecer dados adicionais que o bot usa na conversa.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Salve as alterações no arquivo de `Main`.

1. Para dar suporte à reprodução de resposta, adicione uma classe adicional que transforma o objeto PullAudioOutputStream retornado da API GetAudio () em um InputStream Java para facilitar o manuseio. Essa `ActivityAudioStream` é uma classe especializada que manipula a resposta de áudio do canal de fala de linha direta. Ele fornece aos acessadores para obter informações de formato de áudio necessárias para lidar com a reprodução. Para isso, selecione **arquivo** > **nova** **classe**de > .

1. Na janela **nova classe Java** , insira _speechsdk. QuickStart_ no campo **pacote** e _ActivityAudioStream_ no campo **nome** .

1. Abra a classe de `ActivityAudioStream` recém-criada e substitua-a pelo seguinte código:

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

1. Salve as alterações no arquivo de `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Selecione F11 ou selecione **executar** > **depurar**.
O console exibe a mensagem "Diga algo".
Neste ponto, fale uma frase ou sentença em inglês que seu bot possa entender. Sua fala é transmitida para o bot por meio do canal de fala de linha direta, onde é reconhecido e processado pelo bot. A resposta é retornada como uma atividade. Se o bot retornar a fala como resposta, o áudio será reproduzido usando a classe `AudioPlayer`.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Passos seguintes

Exemplos adicionais, como como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)

## <a name="see-also"></a>Ver também

- [Sobre assistentes de voz](voice-assistants.md)
- [Obtenha uma chave de assinatura do serviço de fala gratuitamente](get-started.md)
- [Palavras-chave personalizadas](speech-devices-sdk-create-kws.md)
- [Conecte a fala de linha direta ao bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)
