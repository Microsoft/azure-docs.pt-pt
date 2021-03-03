---
title: Quickstart - Adicione voip chamando para uma aplicação Android usando serviços de comunicação Azure
description: Neste tutorial, você aprende a usar a biblioteca de clientes Azure Communication Services Call para Android
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b4719fcf046ce7ef5d74ccf1863b0400c2c52845
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656642"
---
Neste arranque rápido, você vai aprender a iniciar uma chamada usando a biblioteca de clientes Azure Communication Services Call para Android.

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.8 da biblioteca do cliente chamador.

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

Selecione biblioteca mínima de clientes de "API 26: Android 8.0 (Oreo)" ou superior.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Screenshot mostrando a opção 'Atividade vazia' selecionada no ecrã do modelo do projeto 2.":::


### <a name="install-the-package"></a>Instale o pacote

<!-- TODO: update with instructions on how to download, install and add package to project -->
Localize o seu nível de projeto build.gradle e certifique-se de adicionar `mavenCentral()` à lista de repositórios em `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Em seguida, no seu nível de módulo build.gradle adicionar as seguintes linhas às dependências e secções android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Adicionar permissões ao manifesto de aplicação

Para solicitar as permissões necessárias para fazer uma chamada, devem primeiro ser declaradas no Manifesto de Aplicação ( `app/src/main/AndroidManifest.xml` ). Substitua o conteúdo do ficheiro pelo seguinte:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Configurar o layout para a aplicação

São necessárias duas entradas: uma entrada de texto para o ID do callee e um botão para a colocação da chamada. Estes podem ser adicionados através do designer ou editando o layout xml. Crie um botão com um ID de `call_button` e uma entrada de texto de `callee_id` . Navegue para ( `app/src/main/res/layout/activity_main.xml` ) e substitua o conteúdo do ficheiro pelo seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Criar os andaimes e encadernações de atividade principal

Com o layout criado as encadernações podem ser adicionadas, bem como o andaime básico da atividade. A atividade tratará do pedido de permissões de tempo de execução, da criação do agente de chamadas e da colocação da chamada quando o botão for premido. Cada um será coberto pela sua própria secção. O `onCreate` método será ultrapassado para invocar e adicionar as `getAllPermissions` `createAgent` ligações para o botão de chamada. Isto ocorrerá apenas uma vez quando a atividade for criada. Para obter mais `onCreate` informações, consulte o guia Compreender o Ciclo de Vida da [Atividade.](https://developer.android.com/guide/components/activities/activity-lifecycle)

Navegue para **a MainActivity.java** e substitua o conteúdo pelo seguinte código:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.communication.calling.CallAgent;
import com.azure.communication.calling.CallClient;
import com.azure.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Ao desenhar a sua aplicação, considere quando estas permissões devem ser solicitadas. As permissões devem ser solicitadas conforme são necessárias, e não com antecedência. Para mais informações consulte o [Guia de Permissões](https://developer.android.com/training/permissions/requesting) do Android.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.|
| Callagent | O CallAgent é usado para iniciar e gerir chamadas. |
| ComunicadoUserCredential | O CommunicationUserCredential é usado como credencial simbólica para instantaneaizar o CallAgent.|
| Identificador de Comunicação | O Comunicador de Comunicação é usado como diferente tipo de participante que poderia fazer parte de uma chamada.|

## <a name="create-an-agent-from-the-user-access-token"></a>Criar um agente a partir do token de acesso ao utilizador

Com o token do utilizador, um agente de chamadas autenticado pode ser instantâneo. Geralmente este token será gerado a partir de um serviço com autenticação específica para a aplicação. Para obter mais informações sobre fichas de acesso ao utilizador, consulte o guia ["Tokens de acesso ao utilizador".](../../access-tokens.md) Para o arranque rápido, substitua `<User_Access_Token>` por um token de acesso ao utilizador gerado para o seu recurso Azure Communication Service.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Inicie uma chamada usando o agente de chamadas

A colocação da chamada pode ser feita através do agente de chamadas, e apenas requer uma lista de IDs callee e as opções de chamada. Para o arranque rápido, serão utilizadas as opções de chamada padrão sem vídeo e um único ID de callee da entrada de texto.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Lance a app e chame o bot de eco

A aplicação pode agora ser lançada utilizando o botão "Run App" na barra de ferramentas (Shift+F10). Verifique se consegue fazer chamadas ligando `8:echo123` . Uma mensagem pré-gravada reproduzirá a sua mensagem de volta para si.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Screenshot mostrando a aplicação completa.":::

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)
