---
title: 'Início rápido: Assistente de voz personalizado, Java (Android)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como criar um aplicativo de assistente de voz em Java no Android usando o SDK de fala
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 58bc33c54a7bd878d717b2c6e44d9ac8657c245f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111788"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Início rápido: criar um assistente de voz em Java no Android usando o SDK de fala

Um guia de início rápido também está disponível para [conversão de fala em texto](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) e [texto em fala](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

Neste artigo, você criará um assistente de voz com Java para Android usando o [SDK de fala](speech-sdk.md). Este aplicativo se conectará a um bot que você já criou e configurou com o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Em seguida, ele enviará uma solicitação de voz para o bot e apresentará uma atividade de resposta habilitada para voz.

Esse aplicativo é criado com o pacote do SDK do Speech e o Android Studio 3,3. Atualmente, o SDK de Voz é compatível com dispositivos Android com processadores ARM de 32/64 bits ou Intel x86/x64.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura do Azure para serviços de fala. [Obtenha um gratuitamente](get-started.md) ou crie-o no [portal do Azure](https://portal.azure.com).
- Um bot criado anteriormente configurado com o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Android Studio](https://developer.android.com/studio/) v 3.3 ou posterior

    > [!NOTE]
    > Consulte [a lista de regiões com suporte para assistentes de voz](regions.md#voice-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar a interface de utilizador

Nesta seção, vamos criar uma interface do usuário (IU) básica para o aplicativo. Vamos começar abrindo a atividade principal: `activity_main.xml`. O modelo básico inclui uma barra de título com o nome do aplicativo e um `TextView` com a mensagem "Olá, mundo!".

Em seguida, substitua o conteúdo do `activity_main.xml` pelo código a seguir:

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

Esse XML define uma interface do usuário simples para interagir com o bot.

- O elemento `button` inicia uma interação e invoca o método `onBotButtonClicked` quando clicado.
- O elemento `recoText` exibirá os resultados da fala para texto à medida que você conversa com o bot.
- O elemento `activityText` exibirá a carga JSON para a atividade mais recente do bot Framework do bot.

O texto e a representação gráfica da sua interface do usuário agora devem ter esta aparência:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `MainActivity.java`e substitua o conteúdo pelo código a seguir:

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

   * O método `onCreate` inclui código que solicita permissões de microfone e Internet.

   * O método `onBotButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um pressionamento de botão dispara uma única interação ("ativar") com o bot.

   * O método `registerEventListeners` demonstra os eventos usados pelo `DialogServiceConnector` e pela manipulação básica de atividades de entrada.

1. No mesmo arquivo, substitua as cadeias de caracteres de configuração para corresponder aos seus recursos:

    * Substitua `YourChannelSecret` pelo segredo do canal de fala de linha direta para o bot.

    * Substitua `YourSpeechSubscriptionKey` pela sua chave de subscrição.

    * Substitua `YourServiceRegion` pela [região](regions.md) associada à sua assinatura somente um subconjunto de regiões de serviços de fala tem suporte no momento com a Direct line Speech. Para obter mais informações, consulte [regiões](regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para criar a aplicação, prima Ctrl+F9 ou escolha **Build** (Criar)  > **Make Project** (Criar Projeto) na barra de menus.

1. Para iniciar a aplicação, prima Shift+F10 ou escolha **Run** (Executar)  > **Run 'app'** (Executar 'app').

1. Na janela de destino de implementação que aparece, selecione o seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](media/sdk/qs-java-android-12-deploy.png)

Depois que o aplicativo e sua atividade tiverem sido iniciados, clique no botão para começar a conversar com o bot. O texto transcrita será exibido à medida que você falar e a atividade mais recente que você recebeu do bot aparecerá quando ela for recebida. Se o bot estiver configurado para fornecer respostas faladas, a conversão de fala em texto será executada automaticamente.

![Captura de ecrã da aplicação Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)

## <a name="see-also"></a>Ver também
- [Sobre assistentes de voz](voice-assistants.md)
- [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
- [Palavras-chave personalizadas](speech-devices-sdk-create-kws.md)
- [Conecte a fala de linha direta ao bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)
