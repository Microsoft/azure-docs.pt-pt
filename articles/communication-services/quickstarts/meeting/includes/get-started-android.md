---
title: Quickstart - Adicione juntar uma reunião de equipas a uma aplicação Android usando os Serviços de Comunicação Azure
description: Neste arranque rápido, aprende-se a usar a biblioteca Azure Communication Services Teams Embed para Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e9069b5d43044ef0d0341717a12fcce7c4a72dc7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104804068"
---
Neste quickstart, você vai aprender a se juntar a uma reunião de equipas usando a biblioteca Azure Communication Services Teams Embed para Android.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Android Studio](https://developer.android.com/studio), para criar a sua aplicação Android.
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um [Token de Acesso ao Utilizador para](../../access-tokens.md) o seu Serviço de Comunicação Azure.

## <a name="setting-up"></a>Configuração

### <a name="create-an-android-app-with-an-empty-activity"></a>Criar uma aplicação Android com atividade vazia

A partir do Android Studio, selecione Iniciar um novo projeto Android Studio.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Screenshot mostrando o botão 'Iniciar um novo Android Studio Project' selecionado no Android Studio.":::

Selecione o modelo de projeto "Atividade Vazia" em "Telefone e Tablet".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Screenshot mostrando a opção &quot;Atividade Vazia&quot; selecionada no ecrã do modelo do projeto.":::

Nomeie o projeto `TeamsEmbedAndroidGettingStarted` , desempate o idioma para java e selecione biblioteca mínima de clientes de "API 21: Android 5.0 (Lollipop)" ou superior.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Screenshot mostrando a opção 'Atividade vazia' selecionada no ecrã do modelo do projeto 2.":::


### <a name="install-the-azure-package"></a>Instale o pacote Azure

No seu nível de aplicativo build.gradle adicione as seguintes linhas às dependências e secções android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Instale o pacote Teams Embed

Descarregue o `MicrosoftTeamsSDK` pacote.

Em seguida, desaperte a pasta MicrosoftTeamsSDK na pasta de aplicações do seu projeto. Por exemplo: `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Adicione pacote de incorporação de equipas à sua build.gradle

No nível da sua aplicação `build.gradle` adicione a seguinte linha no final do ficheiro:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Projeto sincronizado com ficheiros gradle.

### <a name="create-application-class"></a>Criar classe de aplicação

Crie um novo ficheiro da classe Java chamado `TeamsEmbedAndroidGettingStarted` . Esta será a classe de candidatura que deve `TeamsSDKApplication` estender-se. [Documentação do Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Screenshot mostrando onde criar classe de aplicações no Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Atualizar temas

Desa estarça o nome de estilo `AppTheme` em ambos os seus e `theme.xml` `theme.xml (night)` ficheiros.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Screenshot mostrando os ficheiros theme.xml no Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Adicionar permissões ao manifesto de aplicação

Adicione a `RECORD_AUDIO` permissão ao seu Manifesto de Inscrição `app/src/main/AndroidManifest.xml` ( ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Adicione o nome da aplicação e o tema ao manifesto da aplicação

Adicione 'xmlns:tools=' http://schemas.android.com/tools ao manifesto.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Adicione `.TeamsEmbedAndroidGettingStarted` a , para , e alterar o `android:name` `android:name` `tools:replace` `android:theme` para `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Configurar o layout para a aplicação

Criar um botão com um ID de `join_meeting` . Navegue para ( `app/src/main/res/layout/activity_main.xml` ) e substitua o conteúdo do ficheiro pelo seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Criar os andaimes e encadernações de atividade principal

Com o layout criado, o andaime básico da atividade juntamente com as ligações necessárias podem ser adicionados. A atividade tratará de solicitar permissões de tempo de execução, criar o cliente da reunião e juntar-se a uma reunião quando o botão for premido. Cada um será coberto pela sua própria secção. 

O `onCreate` método será ultrapassado para invocar e adicionar as `getAllPermissions` `createAgent` ligações para o `Join Meeting` botão. Isto ocorrerá apenas uma vez quando a atividade for criada. Para obter mais `onCreate` informações, consulte o guia Compreender o Ciclo de Vida da [Atividade.](https://developer.android.com/guide/components/activities/activity-lifecycle)

Navegue para **a MainActivity.java** e substitua o conteúdo pelo seguinte código:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Solicitar permissões no tempo de execução

Para o Android 6.0 e superior (nível API 23) e `targetSdkVersion` 23 ou superior, as permissões são concedidas em tempo de execução em vez de quando a aplicação está instalada. Para apoiá-lo, `getAllPermissions` pode ser implementado para pedir e para cada `ActivityCompat.checkSelfPermission` `ActivityCompat.requestPermissions` permissão necessária.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Ao desenhar a sua aplicação, considere quando estas permissões devem ser solicitadas. As permissões devem ser solicitadas conforme são necessárias, e não com antecedência. Para mais informações consulte o [Guia de Permissões](https://developer.android.com/training/permissions/requesting) do Android.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca Azure Communication Services Teams Embed:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| O MeetingUIClient é o principal ponto de entrada para a biblioteca Teams Embed. |
| Reuniões | Reuniões As Opções são utilizadas para opções configuráveis, como o nome do visor. |
| Estado de Chamada | O CallState está habituado a reportar alterações do estado de chamada. As opções são as seguintes: `connecting` `waitingInLobby` , , e `connected` `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Criar um MeetingClient a partir do token de acesso ao utilizador

Um cliente de reunião autenticado pode ser instantâneo com um token de acesso ao utilizador. Este token é normalmente gerado por um serviço com autenticação específica à aplicação. Para saber mais sobre fichas de acesso ao utilizador, consulte o guia ["Tokens de Acesso ao Utilizador".](../../access-tokens.md) Para o arranque rápido, substitua `<USER_ACCESS_TOKEN>` por um token de acesso ao utilizador gerado para o seu recurso Azure Communication Service.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Configuração Token refrescante

Crie um `tokenRefresher` método callable. Em seguida, crie um `fetchToken` método para obter o token do utilizador. [Pode encontrar instruções sobre como fazê-lo aqui](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Obtenha o link de reunião das equipas

O link de encontro das equipas pode ser recuperado usando APIs de gráfico. Isto é detalhado na [documentação do Gráfico.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
A biblioteca de clientes de serviços de comunicação aceita um link completo de reuniões de equipas. Este link é devolvido como parte do `onlineMeeting` recurso, acessível sob a [ `joinWebUrl` propriedade](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Também pode obter as informações de reunião necessárias do URL de Encontro de **Adesão** na reunião das Equipas.

## <a name="start-a-meeting-using-the-meeting-client"></a>Inicie uma reunião usando o cliente da reunião

Juntar-se a uma reunião pode ser feito através do `MeetingClient` , e apenas requer um `meetingURL` e o `JoinOptions` . Substitua `<MEETING_URL>` por uma url de reunião de equipas.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Lance a app e junte-se a uma reunião

A aplicação pode agora ser lançada utilizando o botão "Run App" na barra de ferramentas (Shift+F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Screenshot mostrando a aplicação completa.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Screenshot mostrando a aplicação completa após a reunião foi acompanhado.":::

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
