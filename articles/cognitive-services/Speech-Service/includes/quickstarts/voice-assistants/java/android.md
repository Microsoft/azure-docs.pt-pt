---
title: 'Quickstart: Criar um assistente de voz personalizado, Java (Android) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um assistente de voz personalizado em Java no Android usando o Speech SDK.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: bc27c73fbf9e827ce9a95c8aaaddc82ffb0aec53
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187689"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Criar um bot ligado ao [canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

  > [!NOTE]
  > Por favor, consulte [a lista de regiões apoiadas para assistentes de voz](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e certifique-se de que os seus recursos são utilizados numa dessas regiões.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar a interface de utilizador

Nesta secção, vamos criar uma interface básica de utilizador (UI) para a aplicação. Vamos começar por abrir a atividade principal: `activity_main.xml` . O modelo básico inclui uma barra de título com o nome da aplicação, e uma `TextView` com a mensagem "Olá mundo!".

Em seguida, substitua o conteúdo do `activity_main.xml` seguinte código:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Este XML define uma UI simples para interagir com o seu bot.

- O `button` elemento inicia uma interação e invoca o método quando `onBotButtonClicked` clicado.
- O `recoText` elemento apresentará os resultados do discurso-a-texto enquanto fala com o seu bot.
- O `activityText` elemento apresentará a carga útil do JSON para a mais recente atividade do Bot Framework a partir do seu bot.

O texto e a representação gráfica da sua UI devem agora ser assim:

![Screenshot de como o seu Talk to your bot UI deve parecer.](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `MainActivity.java` , e substitua o conteúdo pelo seguinte código:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * O `onCreate` método inclui código que solicita permissões de microfone e internet.

   * O método `onBotButtonClicked` é, como observado anteriormente, o processador do clique do botão. Uma pressão de botão aciona uma única interação ("turn") com o seu bot.

   * O `registerEventListeners` método demonstra os acontecimentos utilizados pelo tratamento básico das `DialogServiceConnector` atividades de entrada.

1. No mesmo ficheiro, substitua as cadeias de configuração para corresponder aos seus recursos:

    * Substitua `YourSpeechSubscriptionKey` pela sua chave de subscrição.

    * `YourServiceRegion`Substitua-se pela [região](~/articles/cognitive-services/speech-service/regions.md) associada à sua subscrição Apenas um subconjunto de regiões de serviço de fala são atualmente suportados com Discurso de Linha Direta. Para mais informações, consulte [as regiões.](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para construir a aplicação, prima Ctrl+F9 ou escolha **Build**  >  **Make Project** a partir da barra de menu.

1. Para lançar a aplicação, prima Shift+F10 ou escolha **a**  >  **'app'** Run Run.

1. Na janela de destino de implementação que aparece, selecione o seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Assim que a aplicação e a sua atividade tiverem sido lançadas, clique no botão para começar a falar com o seu bot. O texto transcrito aparecerá enquanto fala e a mais recente atividade que recebeu do seu bot aparecerá quando for recebida. Se o seu bot estiver configurado para fornecer respostas faladas, o discurso-texto será reproduzido automaticamente.

![Captura de ecrã da aplicação Android](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]