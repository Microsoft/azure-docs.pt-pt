---
title: Tutorial – instruções passo a passo para criar um novo aplicativo Android usando âncoras espaciais do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um novo aplicativo Android usando âncoras espaciais do Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6386ef775f897ca56d2660adf72a885672c8dfd2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092069"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: instruções passo a passo para criar um novo aplicativo Android usando âncoras espaciais do Azure

Este tutorial mostrará como criar um novo aplicativo Android que integre a funcionalidade ARCore com âncoras espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

- Um computador com Windows ou macOS com o <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado por desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">com ARCore</a> .

## <a name="getting-started"></a>Introdução

Inicie o Android Studio. Na janela **Bem-vindo ao Android Studio** , clique em **Iniciar um novo projeto de Android Studio**. Ou, se você tiver um projeto já aberto, selecione **arquivo**->**novo projeto**.

Na janela **criar novo projeto** , na seção **telefone e Tablet** , escolha **atividade vazia**e clique em **Avançar**. Em seguida, em **nível mínimo de API**, escolha `API 26: Android 8.0 (Oreo)`e verifique se o **idioma** está definido como `Java`. Talvez você queira alterar o nome do projeto & local e o nome do pacote. Deixe as outras opções como estão. Clique em **Concluir**. O **instalador do componente** será executado. Quando terminar, clique em **concluir**. Após algum processamento, Android Studio abrirá o IDE.

## <a name="trying-it-out"></a>Experimentando

Para testar seu novo aplicativo, conecte seu dispositivo habilitado para desenvolvedor à sua máquina de desenvolvimento com um cabo USB. Clique em **executar**->**Executar ' aplicativo '** . Na janela **Selecionar destino de implantação** , selecione o dispositivo e clique em **OK**. Android Studio instala o aplicativo em seu dispositivo conectado e o inicia. Agora você deve ver "Olá, Mundo!" exibido no aplicativo em execução no seu dispositivo. Clique em **executar**->**parar ' aplicativo '** .

## <a name="integrating-_arcore_"></a>Integração do _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> é a plataforma do Google para a criação de experiências de realidade aumentadas, permitindo que seu dispositivo acompanhe sua posição à medida que ele se move e cria sua própria compreensão do mundo real.

Modifique `app\manifests\AndroidManifest.xml` para incluir as entradas a seguir dentro do nó de `<manifest>` raiz. Este trecho de código faz algumas coisas:

- Ele permitirá que seu aplicativo acesse a câmera do dispositivo.
- Ele também garantirá que seu aplicativo só fique visível no Google Play Store para dispositivos que dão suporte a ARCore.
- Ele configurará o Google Play Store para baixar e instalar o ARCore, se ele ainda não estiver instalado, quando seu aplicativo estiver instalado.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada a seguir. Esse código garantirá que seu aplicativo seja direcionado para a versão 1,8 do ARCore. Após essa alteração, você pode receber uma notificação do gradle solicitando a sincronização: clique em **sincronizar agora**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integração do _Sceneform_

O <a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> simplifica a renderização de cenas 3D realísticas em aplicativos de realidade aumentada, sem a necessidade de aprender OpenGL.

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir as entradas a seguir. Esse código permitirá que seu aplicativo use construções de linguagem do Java 8, o que `Sceneform` requer. Ele também garantirá que seu aplicativo se destina `Sceneform` versão 1,8, já que ela deve corresponder à versão do ARCore que seu aplicativo está usando. Após essa alteração, você pode receber uma notificação do gradle solicitando a sincronização: clique em **sincronizar agora**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Abra o `app\res\layout\activity_main.xml`e substitua o elemento Hello Wolrd `<TextView>` existente pelo seguinte ArFragment. Esse código fará com que o feed de câmera seja exibido na tela, permitindo que o ARCore acompanhe a posição do dispositivo à medida que ele se move.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Reimplante](#trying-it-out) seu aplicativo em seu dispositivo para validá-lo mais uma vez. Desta vez, você deve ser solicitado a fornecer permissões de câmera. Depois de aprovado, você deverá ver a renderização do feed de câmera na tela.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real

Vamos criar & Coloque um objeto usando seu aplicativo. Primeiro, adicione as seguintes importações em seu `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Em seguida, adicione as seguintes variáveis de membro à sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Em seguida, adicione o código a seguir ao seu método `app\java\<PackageName>\MainActivity` `onCreate()`. Esse código conectará um ouvinte, chamado `handleTap()`, que detectará quando o usuário tocar a tela em seu dispositivo. Se o Tap estiver em uma superfície do mundo real que já foi reconhecida pelo acompanhamento do ARCore, o ouvinte será executado.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Por fim, adicione o seguinte método de `handleTap()`, que vinculará tudo. Ele criará uma esfera e a posicionará no local tocado. A esfera inicialmente será preta, pois `this.recommendedSessionProgress` está definida como zero no momento. Esse valor será ajustado posteriormente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Reimplante](#trying-it-out) seu aplicativo em seu dispositivo para validá-lo mais uma vez. Desta vez, você pode se mover em seu dispositivo para obter ARCore para começar a reconhecer seu ambiente. Em seguida, toque na tela para criar & Coloque sua esfera preta sobre a superfície de sua escolha.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anexar uma âncora espacial local do Azure

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada a seguir. Esse código garantirá que seu aplicativo seja direcionado para as âncoras espaciais do Azure versão 1.3.0. Dito isso, fazer referência a qualquer versão recente das âncoras espaciais do Azure deve funcionar.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.3.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.3.0]"
    ...
}
```

Clique com o botão direito do mouse em `app\java\<PackageName>`->**nova** **classe Java**->. Defina o **nome** como _meuprimeiroapp_e a **superclasse** como _Android. app. Application_. Deixe as outras opções como estão. Clique em **OK**. Um arquivo chamado `MyFirstApp.java` será criado. Adicione a seguinte importação a ele:

```java
import com.microsoft.CloudServices;
```

Em seguida, adicione o código a seguir dentro da nova classe `MyFirstApp`, que garantirá que as âncoras espaciais do Azure sejam inicializadas com o contexto do seu aplicativo.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Agora, modifique `app\manifests\AndroidManifest.xml` para incluir a entrada a seguir dentro do nó de `<application>` raiz. Esse código irá conectar a classe de aplicativo que você criou em seu aplicativo.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De volta ao `app\java\<PackageName>\MainActivity`, adicione as seguintes importações a ele:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Em seguida, adicione as seguintes variáveis de membro à sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Em seguida, vamos adicionar o seguinte método `initializeSession()` dentro de sua classe `mainActivity`. Uma vez chamado, ele garantirá que uma sessão de âncoras espaciais do Azure seja criada e inicializada corretamente durante a inicialização do seu aplicativo.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Agora, vamos conectar seu método `initializeSession()` ao seu método `onCreate()`. Além disso, vamos garantir que os quadros de seu feed de câmera sejam enviados para o SDK de âncoras espaciais do Azure para processamento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Por fim, adicione o código a seguir ao seu método `handleTap()`. Ele anexará uma âncora espacial local do Azure à esfera preta que estamos colocando no mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Reimplante](#trying-it-out) seu aplicativo mais uma vez. Mova ao seu dispositivo, toque na tela e coloque uma esfera preta. Desta vez, no entanto, seu código estará criando e anexando uma âncora espacial local do Azure à sua esfera.

Antes de continuar, você precisará criar um identificador e uma chave de conta de âncoras espaciais do Azure, se você ainda não os tiver. Siga a seção a seguir para obtê-los.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregue sua âncora local na nuvem

Depois que você tiver o identificador e a chave da conta das âncoras espaciais do Azure, podemos voltar ao `app\java\<PackageName>\MainActivity`, adicionar as seguintes importações a ela:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Em seguida, adicione as seguintes variáveis de membro à sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Agora, adicione o código a seguir ao seu método `initializeSession()`. Primeiro, esse código permitirá que seu aplicativo monitore o progresso que o SDK de âncoras espaciais do Azure faz à medida que coleta quadros do seu feed de câmera. Como faz, a cor da esfera começará a mudar de seu preto original para o cinza. Em seguida, ele deixará o branco quando forem coletados quadros suficientes para enviar sua âncora para a nuvem. Em segundo lugar, esse código fornecerá as credenciais necessárias para se comunicar com o back-end de nuvem. Aqui está onde você configurará seu aplicativo para usar o identificador de conta e a chave. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Em seguida, adicione o seguinte método `uploadCloudAnchorAsync()` dentro de sua classe `mainActivity`. Uma vez chamado, esse método irá aguardar de forma assíncrona até que quadros suficientes sejam coletados do seu dispositivo. Assim que isso acontecer, ele mudará a cor da sua esfera para amarelo e, em seguida, começará a carregar sua âncora espacial local do Azure na nuvem. Quando o carregamento for concluído, o código retornará um identificador de âncora.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Por fim, vamos conectar tudo isso. Em seu método de `handleTap()`, adicione o código a seguir. Ele invocará seu método `uploadCloudAnchorAsync()` assim que sua esfera for criada. Depois que o método retornar, o código a seguir executará uma atualização final para sua esfera, alterando sua cor para azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Reimplante](#trying-it-out) seu aplicativo mais uma vez. Mova-se pelo dispositivo, toque na tela e coloque a esfera. Desta vez, no entanto, sua esfera mudará sua cor de preto para branco, pois os quadros da câmera são coletados. Assim que tivermos quadros suficientes, a esfera será transformada em amarelo e o carregamento da nuvem será iniciado. Quando o carregamento for concluído, sua esfera ficará azul. Opcionalmente, você também pode usar a janela `Logcat` dentro de Android Studio para monitorar as mensagens de log que seu aplicativo está enviando. Por exemplo, o andamento da sessão durante capturas de quadros e o identificador de âncora que a nuvem retorna quando o carregamento é concluído.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize sua âncora espacial de nuvem

Uma sua âncora é carregada na nuvem, estamos prontos para tentar localizá-la novamente. Primeiro, vamos adicionar as importações a seguir ao seu código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Em seguida, vamos adicionar o código a seguir ao seu método `handleTap()`. Esse código irá:

- Remova nossa esfera azul existente da tela.
- Inicialize nossa sessão de âncoras espaciais do Azure novamente. Essa ação garantirá que a âncora que vamos localizar venha da nuvem em vez da âncora local que criamos.
- Emita uma consulta para a âncora que carregamos na nuvem.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Agora, vamos conectar o código que será invocado quando a âncora que estamos consultando estiver localizada. Dentro de seu método de `initializeSession()`, adicione o código a seguir. Esse trecho de código criará & posicionar uma esfera verde quando a âncora espacial da nuvem estiver localizada. Ele também habilitará o toque da tela novamente, para que você possa repetir o cenário inteiro mais uma vez: crie outra âncora local, carregue-a e localize-a novamente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Já está! [Reimplante](#trying-it-out) seu aplicativo uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se pelo dispositivo e coloque a esfera preta. Em seguida, continue movendo seu dispositivo para capturar quadros de câmera até que a esfera fique amarela. Sua âncora local será carregada e sua esfera ficará azul. Por fim, toque na tela mais uma vez, para que sua âncora local seja removida e, em seguida, consultaremos seu equivalente em nuvem. Continue movendo seu dispositivo até que a âncora espacial da nuvem esteja localizada. Uma esfera verde deve aparecer no local correto e você pode aplique & repetir o cenário inteiro novamente.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
