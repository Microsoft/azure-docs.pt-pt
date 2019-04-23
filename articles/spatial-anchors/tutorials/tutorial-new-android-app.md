---
title: Tutorial - instruções passo a passo para criar uma nova aplicação Android utilizando as âncoras espaciais do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como criar uma nova aplicação Android utilizando as âncoras espaciais do Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9838add4f83434848d61f3ae86db71765efdc59a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995732"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova aplicação Android utilizando as âncoras espaciais do Azure

Neste tutorial mostram-lhe como criar uma nova aplicação Android que se integra a funcionalidade de ARCore âncoras espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

- Um Windows ou macOS do computador com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
- R <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativada</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore com capacidade</a> dispositivo Android.

## <a name="getting-started"></a>Introdução

Inicie o Android Studio. Na **bem-vindo ao Android Studio** janela, clique em **inicie um novo projeto do Android Studio**. Ou, se tiver um projeto já aberto, selecione **arquivo**->**novo projeto**.

Na **criar novo projeto** janela, no **telemóveis e tablets** secção, escolha **atividade vazia**e clique em **seguinte**. Em seguida, em **nível mínimo de API**, escolha `API 26: Android 8.0 (Oreo)`e certifique-se a **linguagem** está definido como `Java`. Convém alterar o nome do projeto e localização e o nome do pacote. Deixe as outras opções, como estão. Clique em **Concluir**. O **Component Installer** será executado. Quando terminar, clique em **concluir**. Depois de algum processamento, o Android Studio abrirá o IDE.

## <a name="trying-it-out"></a>Experimentá-lo

Para testar a nova aplicação, ligue o seu dispositivo habilitado para desenvolvedor para o seu computador de desenvolvimento com um cabo USB. Clique em **execute**->**executar "aplicação"**. Na **selecione o destino de implementação** janela, selecione o seu dispositivo e clique em **OK**. Android Studio instala a aplicação no seu dispositivo ligado e inicia-o. Agora, deverá ver "Hello World!" apresentado na aplicação em execução no seu dispositivo. Clique em **execute**->**Stop "aplicação"**.

## <a name="integrating-arcore"></a>Integrar _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_ </a> é a plataforma da Google para a criação de experiências de realidade aumentada, permitindo que o seu dispositivo controlar a sua posição como ele se move e cria o seu próprio conhecimento do mundo real.

Modificar `app\manifests\AndroidManifest.xml` para incluir as seguintes entradas dentro da raiz `<manifest>` nó. Este fragmento de código faz algumas coisas:

- Isso permitirá que a aplicação aceda à sua câmara do dispositivo.
- Também irá garantir a que sua aplicação só é visível na Store de Play Google em dispositivos que suportam ARCore.
- Ele irá configurar o Store de Play Google para transferir e instalar ARCore, se não estiver instalado, quando a aplicação está instalada.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada seguinte. Esse código irá assegurar que sua versão da aplicação destinos ARCore 1.7. Após esta alteração, poderá obter uma notificação do Gradle que lhe pede para sincronizar: clique em **sincronizar agora**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.7.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrar _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_ </a> simplifica a composição de cenas 3D realistas em aplicações de realidade aumentada, sem ter de aprender o OpenGL.

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir as seguintes entradas. Este código permitirá que a aplicação para utilizar construções de linguagem do Java 8, que `Sceneform` requer. Também irá garantir que as aplicações têm como destino `Sceneform` versão 1.7, uma vez que ele deve corresponder à versão de ARCore seu aplicativo está usando. Após esta alteração, poderá obter uma notificação do Gradle que lhe pede para sincronizar: clique em **sincronizar agora**.

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
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.7.0'
    ...
}
```

Abra sua `app\res\layout\activity_main.xml`e substituir o existente Hello Wolrd `<TextView>` elemento com o seguinte ArFragment. Este código fará com que o feed a apresentar no ecrã ativar ARCore controlar a posição de dispositivo, pois ela se move da câmara.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Reimplementar](#trying-it-out) seu aplicativo para o seu dispositivo para validá-la mais uma vez. Desta vez, deve solicitado para permissões de câmara. Quando for aprovado, deverá ver o que processamento de feed a câmara no seu ecrã.

## <a name="place-an-object-in-the-real-world"></a>Colocar um objeto no mundo real

Vamos criar & colocar um objeto com a sua aplicação. Em primeiro lugar, adicione as seguintes importações em sua `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Em seguida, adicione as seguintes variáveis de membro em sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Em seguida, adicione o seguinte código no seu `app\java\<PackageName>\MainActivity` `onCreate()` método. Esse código será conectar um serviço de escuta, chamado `handleTap()`, que irá detetar quando o usuário toca na tela no seu dispositivo. Se ocorrer o toque estar numa superfície de mundo real que já foi reconhecida pelo controlo do ARCore, será executado o serviço de escuta.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Por fim, adicione o seguinte `handleTap()` método, que irão juntar tudo. Irá criar uma esfera e coloque-o na localização de tocado. A sphere será inicialmente o preto, desde `this.recommendedSessionProgress` está definido como zero neste momento. Este valor será ajustado mais tarde.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Reimplementar](#trying-it-out) seu aplicativo para o seu dispositivo para validá-la mais uma vez. Desta vez, pode mover em torno do seu dispositivo para obter ARCore para começar a reconhecer o seu ambiente. Em seguida, toque no ecrã para criar e colocar seu esfera preta sobre a superfície da sua preferência.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anexar uma âncora de geográficos local do Azure

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada seguinte. Esse código irá assegurar que sua versão de âncoras espaciais do Azure de destinos de aplicação 1.0.2. Dito isso, qualquer versão recente do Azure âncoras de geográficos a referenciar deve funcionar.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Com o botão direito `app\java\<PackageName>` -> **novo**->**classe Java**. Definir **Name** ao _Minhaprimeiraaplicação_, e **sobre a superclasse** para _android.app.Application_. Deixe as outras opções, como estão. Clique em **OK**. Um arquivo chamado `MyFirstApp.java` será criado. Adicione a seguinte importação ao mesmo:

```java
import com.microsoft.CloudServices;
```

Em seguida, adicione o seguinte código dentro do novo `MyFirstApp` classe, que garante que as âncoras espaciais do Azure é inicializado com o contexto da sua aplicação.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Agora, modifique `app\manifests\AndroidManifest.xml` para incluir a seguinte entrada dentro da raiz `<application>` nó. Esse código irá vincular a classe de aplicação que criou na sua aplicação.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De volta `app\java\<PackageName>\MainActivity`, adicione as seguintes importações nele:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Em seguida, adicione as seguintes variáveis de membro em sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Em seguida, vamos adicionar o seguinte procedimento `initializeSession()` método dentro de seu `mainActivity` classe. Quando chamado, ele garantirá uma sessão de âncoras espaciais do Azure é criada e corretamente inicializada durante o arranque da sua aplicação.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Agora, vamos vincular sua `initializeSession()` método em seu `onCreate()` método. Além disso, podemos irá Certifique-se de que quadros de sua câmera feed são enviados ao SDK de âncoras espaciais do Azure para processamento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Por fim, adicione o código seguinte para sua `handleTap()` método. Ele se ligará uma âncora de geográficos Azure local esfera preta que podemos estiver colocando no mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Reimplementar](#trying-it-out) mais uma vez a sua aplicação. Mover em torno do seu dispositivo, toque no ecrã e colocar uma esfera preta. Desta vez, porém, seu código for criar e anexar uma âncora de geográficos local do Azure à sua área.

Antes de prosseguir qualquer, terá de criar um âncoras espaciais do Azure conta identificador e a chave, se ainda não tivê-los. Siga a secção seguinte para obtê-las.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregar sua âncora local para a cloud

Assim que tiver o identificador de conta de âncoras espaciais do Azure e a chave, podemos voltar `app\java\<PackageName>\MainActivity`, adicione as seguintes importações nele:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Em seguida, adicione as seguintes variáveis de membro em sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Agora, adicione o código seguinte para sua `initializeSession()` método. Em primeiro lugar, este código permitirá que a sua aplicação monitorizar o progresso que o SDK do Azure geográficos âncoras faz, que recolhe quadros de sua câmera feed. Como ele faz, a cor do seu esfera começará a alteração de uma black original, em cinzento. Em seguida, ele desativará branco depois de suficiente quadros são recolhidos para submeter a sua âncora para a cloud. Em segundo lugar, esse código irá fornecer as credenciais necessárias para comunicar com o back-end de cloud. Aqui é onde irá configurar a sua aplicação para utilizar o identificador de conta e a chave. Copiou num editor de texto quando [como configurar o recurso de âncoras geográficos](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Em seguida, adicione o seguinte procedimento `uploadCloudAnchorAsync()` método dentro de seu `mainActivity` classe. Quando chamado, este método de forma assíncrona aguardará pela suficiente quadros são recolhidos a partir do seu dispositivo. Assim que isso acontece, ele muda a cor do seu sphere para amarelo e, em seguida, irá começar a carregar o seu local âncora geográficos do Azure para a cloud. Depois do carregamento estiver concluído, o código retornará um identificador de âncora.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Por fim, vamos conectar tudo. No seu `handleTap()` método, adicione o seguinte código. Ele invocará o `uploadCloudAnchorAsync()` método assim que a esfera é criada. Assim que o método retorna, o código abaixo irá efetuar uma atualização final para sua esfera, alterar a cor como azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Reimplementar](#trying-it-out) mais uma vez a sua aplicação. Mover em torno do seu dispositivo, toque no ecrã e colocar seu esfera. Desta vez, no entanto, a sphere será alterado sua cor de preto para branco, à medida que são recolhidos quadros de câmara. Assim que tivermos suficiente quadros, esfera irá transformar em amarelo e o carregamento de nuvem é iniciado. Depois do carregamento estiver concluído, seu esfera ativará azul. Opcionalmente, também pode usar o `Logcat` janela dentro do Android Studio para monitorizar as mensagens de registo que a aplicação está a enviar. Por exemplo, o progresso da sessão durante o período de captura e o identificador de âncora que a nuvem retorna, uma vez, o carregamento é concluído.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize sua âncora geográficos de cloud

Um sua âncora é carregado para a cloud, estamos prontos para tentar localizá-lo novamente. Em primeiro lugar, vamos adicionar as seguintes importações em seu código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Em seguida, vamos adicionar o código seguinte para sua `handleTap()` método. Este código poderá:

- Remova nossa esfera azul existente a partir do ecrã.
- Inicialize nossa sessão de âncoras de geográficos Azure novamente. Esta ação irá garantir que a âncora, vamos localizar vem a partir da cloud em vez da âncora de local que criámos.
- Emita uma consulta para a âncora carregado para a cloud.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Agora, vamos conectar o código que será chamado quando a âncora que estiver a consultar para está localizada. Dentro de seu `initializeSession()` método, adicione o seguinte código. Este trecho de código irá criar e colocar uma esfera verde quando a âncora de geográficos de cloud está localizada. Ele também ativará o ecrã tocar novamente, para que pode repetir o cenário completo de uma só mais uma vez: criar outro âncora local, carregá-la e localizá-la novamente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Já está! [Reimplementar](#trying-it-out) seu aplicativo uma última vez para experimentar o cenário completo ponta a ponta. Mover o seu dispositivo e colocar seu esfera preta. Em seguida, mantenha mover o seu dispositivo para capturar os quadros de câmera, até que a esfera fica amarela. Sua âncora local será carregada, e seu esfera ativará azul. Por fim, toque em sua tela mais uma vez, para que sua âncora local é removida e, em seguida, podemos vai consultar para o respetivo homólogo na cloud. Continue a movimentar o dispositivo até que a sua âncora geográficos de cloud está localizada. Uma esfera verde deve aparecer na localização correta, e pode gravá & Repetir novamente o cenário completo.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
