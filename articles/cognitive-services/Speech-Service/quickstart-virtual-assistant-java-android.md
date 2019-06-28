---
title: 'Início rápido: Personalizado voz em primeiro lugar virtual Assistente (pré-visualização), Java (Android) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Saiba como criar uma aplicação de Assistente de voz em primeiro lugar virtual em Java no Android utilizando o SDK de voz
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 5/24/2019
ms.author: travisw
ms.openlocfilehash: 7e82b2ef9500defe0d08351da1e3487e4671155f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467033"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Início rápido: Criar um Assistente de virtual de voz em primeiro lugar em Java no Android com o SDK de voz

Também está disponível para um início rápido [voz em texto](quickstart-java-android.md).

Neste artigo, irá criar um Assistente de voz em primeiro lugar virtual com o Java para Android utilizando o [SDK de voz](speech-sdk.md). Esta aplicação irá ligar a um bot que já tenha criado e configurado com o [canal direto de linha de voz](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Em seguida, enviar um pedido de voz para o bot e apresentar uma atividade de resposta de voz habilitado.

Esta aplicação baseia-se com o pacote Maven do SDK de voz e o Android Studio 3.3. Atualmente, o SDK de Voz é compatível com dispositivos Android com processadores ARM de 32/64 bits ou Intel x86/x64.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição do Azure para serviços de voz. [Obter uma gratuitamente](get-started.md) ou criá-la no [portal do Azure](https://portal.azure.com).
* Um bot criado anteriormente configurado com o [canal direto de linha de voz](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v3.3 ou posterior

    > [!NOTE]
    > Direto de linha de voz (pré-visualização) está atualmente disponível num subconjunto de regiões de serviços de voz. Consulte a [a lista de regiões suportadas para os assistentes de voz em primeiro lugar virtual](regions.md#Voice-first virtual assistants) e certifique-se de que os recursos são implementados nessas regiões.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar a interface de utilizador

Nesta secção, vamos criar uma interface do usuário básica (IU) para a aplicação. Vamos começar abrindo a atividade principal: `activity_main.xml`. O modelo básico inclui uma barra de título com o nome da aplicação e um `TextView` com a mensagem "Hello world!".

Em seguida, substitua o conteúdo do `activity_main.xml` com o código a seguir:

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

Esse XML define uma interface do Usuário simple para interagir com o seu bot.

* O `button` elemento inicia uma interação e invoca o `onBotButtonClicked` método quando clicado.
* O `recoText` elemento exibirá os resultados de voz em texto, como conversa com seu bot.
* O `activityText` elemento apresentará o payload JSON para a atividade de Bot Framework mais recentes do seu bot.

O texto e uma representação gráfica da sua interface do Usuário deverá estar parecido com isto:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `MainActivity.java`e substitua os conteúdos com o código a seguir:

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
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
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

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
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

   * O `onCreate` método inclui o código que solicita as permissões do microfone e internet.

   * O método `onBotButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um pressionamento do botão aciona uma única interação ("desativar") com o seu bot.

   * O `registerEventListeners` método demonstra os eventos utilizados pelo `DialogServiceConnector` e manipulação básica de atividades de entrada.

1. No mesmo ficheiro, substitua as cadeias de caracteres de configuração de acordo com seus recursos:

    * Substitua `YourChannelSecret` com o segredo de canal de voz de linha direta para o bot.

    * Substitua `YourSpeechSubscriptionKey` pela sua chave de subscrição.

    * Substitua `YourServiceRegion` com o [região](regions.md) associados à subscrição apenas um subconjunto de regiões de serviços de voz são atualmente suportadas com a conversão de voz de linha direta. Para obter mais informações, consulte [regiões](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para criar a aplicação, prima Ctrl+F9 ou escolha **Build** (Criar)  > **Make Project** (Criar Projeto) na barra de menus.

1. Para iniciar a aplicação, prima Shift+F10 ou escolha **Run** (Executar)  > **Run 'app'** (Executar 'app').

1. Na janela de destino de implementação que aparece, selecione o seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](media/sdk/qs-java-android-12-deploy.png)

Depois de tem iniciado o aplicativo e sua atividade, clique no botão para começar a falar com o seu bot. Texto transcrito aparecerá como fala e a atividade de mais recente ter que recebeu da sua vontade de bot são apresentadas quando é recebido. Se o seu bot estiver configurado para fornecer respostas faladas, o conversão de voz em texto reproduzirá automaticamente.

![Captura de ecrã da aplicação Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar e implementar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Consulte também
- [Sobre os assistentes de voz em primeiro lugar virtual](voice-first-virtual-assistants.md)
- [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
- [Palavras de reativação personalizado](speech-devices-sdk-create-kws.md)
- [Ligar direto de linha de voz ao seu bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explore exemplos de Java no GitHub](https://aka.ms/csspeech/samples)
