---
title: 'Tutorial: Criar uma nova aplicação Android'
description: Neste tutorial, aprende-se a criar uma nova aplicação Android utilizando âncoras espaciais Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c02595ebdb2f011dcc94b517771a79a4b2df754d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161725"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova aplicação Android usando âncoras espaciais Azure

Este tutorial irá mostrar-lhe como criar uma nova aplicação Android que integra a funcionalidade ARCore com âncoras espaciais Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

- Uma máquina Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- Um <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativado</a> e dispositivo Android <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>

## <a name="getting-started"></a>Introdução

Inicie o Android Studio. Na janela **Welcome to Android Studio,** clique **em Iniciar um novo projeto Android Studio**. Ou, se tiver um projeto já aberto, selecione **File**->**New Project**.

Na janela **Criar Novo Projeto,** sob a secção **Telefone e Tablet,** escolha **Atividade Vazia**, e clique **em Seguinte**. Em seguida, sob o **nível mínimo de API,** escolha `API 26: Android 8.0 (Oreo)`, e certifique-se de que a **língua** está definida para `Java`. Pode querer alterar o Nome e Localização do Projeto e o nome do pacote. Deixe as outras opções como estão. Clique em **Concluir**. O **Instalador** de Componentes funcionará. Uma vez feito, clique em **Terminar**. Depois de algum processamento, o Android Studio abrirá o IDE.

## <a name="trying-it-out"></a>Experimentando-o

Para testar a sua nova aplicação, ligue o seu dispositivo ativado pelo desenvolvedor à sua máquina de desenvolvimento com um cabo USB. Clique em **Executar**->**Executar 'app'** . Na janela **Select Deployment Target,** selecione o seu dispositivo e clique em **OK**. O Android Studio instala a aplicação no seu dispositivo conectado e inicia-a. Agora devias ver "Olá Mundo!" exibido na aplicação em execução no seu dispositivo. Clique em **Executar**->**Parar 'app'.**

## <a name="integrating-_arcore_"></a>Integração _arCore_

A <a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> é a plataforma da Google para construir experiências de Realidade Aumentada, permitindo ao seu dispositivo acompanhar a sua posição à medida que se move e constrói a sua própria compreensão do mundo real.

Modifique `app\manifests\AndroidManifest.xml` para incluir as seguintes entradas dentro do nó de raiz `<manifest>`. Este código de corte faz algumas coisas:

- Permitirá que a sua aplicação aceda à câmara do seu dispositivo.
- Também garantirá que a sua aplicação só é visível na Google Play Store para dispositivos que suportem arCore.
- Configurará a Google Play Store para descarregar e instalar o ARCore, caso ainda não esteja instalado, quando a sua aplicação estiver instalada.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a seguinte entrada. Este código irá garantir que a sua aplicação tem como alvo a versão ARCore 1.8. Após esta alteração, poderá receber uma notificação de Gradle pedindo-lhe que sincronize: clique **em Sync agora**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integração _de formas_ de cena

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_O Sceneform_</a> torna simples tornar cenas 3D realistas em aplicações de Realidade Aumentada, sem ter de aprender OpenGL.

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir as seguintes entradas. Este código permitirá que a sua aplicação utilize construções linguísticas a partir de Java 8, o que `Sceneform` requer. Também garantirá que os seus alvos de aplicação `Sceneform` versão 1.8, uma vez que deve corresponder à versão do ARCore que a sua aplicação está a utilizar. Após esta alteração, poderá receber uma notificação de Gradle pedindo-lhe que sincronize: clique **em Sync agora**.

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

Abra o seu `app\res\layout\activity_main.xml`e substitua o elemento `<TextView>` Hello Wolrd existente pelo seguinte ArFragment. Este código fará com que o feed da câmara seja exibido no ecrã, permitindo ao ARCore rastrear a posição do dispositivo à medida que se move.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Recoloque](#trying-it-out) a sua aplicação para o seu dispositivo para validá-la mais uma vez. Desta vez, deviam pedir-lhe permissões de câmara. Uma vez aprovado, deve ver a sua transmissão de câmara no ecrã.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real

Vamos criar e colocar um objeto usando a sua aplicação. Em primeiro lugar, adicione as seguintes importações no seu `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Em seguida, adicione as seguintes variáveis membros na sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Em seguida, adicione o seguinte código no seu método `app\java\<PackageName>\MainActivity` `onCreate()`. Este código ligará um ouvinte, chamado `handleTap()`, que detetará quando o utilizador tocar no ecrã no seu dispositivo. Se a torneira estiver numa superfície do mundo real que já foi reconhecida pelo rastreio da ARCore, o ouvinte correrá.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Finalmente, adicione o seguinte método `handleTap()`, que irá ligar tudo. Criará uma esfera e a colocará no local sob escuta. A esfera será inicialmente negra, uma vez que `this.recommendedSessionProgress` está definida para zero agora. Este valor será ajustado mais tarde.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Recoloque](#trying-it-out) a sua aplicação para o seu dispositivo para validá-la mais uma vez. Desta vez, pode mover-se em torno do seu dispositivo para que a ARCore comece a reconhecer o seu ambiente. Em seguida, toque no ecrã para criar e colocar a sua esfera preta sobre a superfície à sua escolha.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anexe uma âncora espacial azure local

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a seguinte entrada. Este código irá garantir que a sua aplicação tem como alvo a versão 2.2.0 do Azure Spatial Anchors. Dito isto, a referência a qualquer versão recente das Âncoras Espaciais Azure deve funcionar. Pode encontrar as notas de lançamento [aqui.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Clique à direita `app\java\<PackageName>`->**Classe** **Java de Nova**->. Detete **o nome** para _MyFirstApp_e **Superclass** para _android.app.Application._ Deixe as outras opções como estão. Clique em **OK**. Será criado um ficheiro chamado `MyFirstApp.java`. Adicione-lhe a seguinte importação:

```java
import com.microsoft.CloudServices;
```

Em seguida, adicione o seguinte código dentro da nova classe `MyFirstApp`, que garantirá que as Âncoras Espaciais Azure são inicializadas com o contexto da sua aplicação.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Agora, modifique `app\manifests\AndroidManifest.xml` para incluir a seguinte entrada dentro do nó de `<application>` raiz. Este código irá ligar a classe Aplicação que criou na sua aplicação.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De volta `app\java\<PackageName>\MainActivity`, adicione-lhe as seguintes importações:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Em seguida, adicione as seguintes variáveis membros na sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Em seguida, vamos adicionar o seguinte método `initializeSession()` dentro da sua aula de `mainActivity`. Uma vez chamado, irá garantir que uma sessão de Âncoras Espaciais Azure seja criada e devidamente inicializada durante o arranque da sua app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Agora, vamos ligar o seu método `initializeSession()` ao seu método `onCreate()`. Além disso, vamos garantir que as molduras do seu feed de câmara são enviadas para O SDK de Âncoraespacial Espacial Azure para processamento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Por fim, adicione o seguinte código ao seu método `handleTap()`. Vai ligar uma âncora espacial azure local à esfera negra que estamos colocando no mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Recoloque](#trying-it-out) a sua aplicação mais uma vez. Mova-se em torno do seu dispositivo, toque no ecrã e coloque uma esfera preta. Desta vez, porém, o seu código será criar e anexar uma Âncora Espacial Azure local à sua esfera.

Antes de prosseguir mais, terá de criar uma conta Deâncoras Espaciais Azure Identifier e Key, se ainda não as tiver. Siga a seguinte secção para as obter.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Faça upload da sua âncora local para a nuvem

Assim que tiver a sua conta Deâncoras Espaciais Azure Identifier e Key, podemos voltar a `app\java\<PackageName>\MainActivity`, adicionar as seguintes importações:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Em seguida, adicione as seguintes variáveis membros na sua classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Agora, adicione o seguinte código no seu método `initializeSession()`. Em primeiro lugar, este código permitirá que a sua aplicação monitorize o progresso que o Azure Spatial Anchors SDK faz à medida que recolhe quadros do feed da câmara. Assim como, a cor da sua esfera começará a mudar do seu preto original, para cinzento. Em seguida, ficará branco uma vez que forem recolhidas molduras suficientes para submeter a sua âncora à nuvem. Em segundo lugar, este código fornecerá as credenciais necessárias para comunicar com a nuvem de fundo. Aqui é onde configurará a sua aplicação para usar a sua conta Identificador e Chave. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Em seguida, adicione o seguinte método `uploadCloudAnchorAsync()` dentro da sua aula de `mainActivity`. Uma vez chamado, este método aguardará assincronicamente até que sejam recolhidas quadros suficientes do seu dispositivo. Assim que isso acontecer, mudará a cor da sua esfera para amarelo, e depois começará a carregar a sua Âncora Espacial Azure local para a nuvem. Assim que o upload terminar, o código devolverá um identificador de âncora.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Finalmente, vamos juntar tudo. No seu método `handleTap()`, adicione o seguinte código. Invocará o seu método `uploadCloudAnchorAsync()` assim que a sua esfera for criada. Assim que o método voltar, o código abaixo realizará uma atualização final para a sua esfera, alterando a sua cor para azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Recoloque](#trying-it-out) a sua aplicação mais uma vez. Mova-se em torno do seu dispositivo, toque no ecrã e coloque a sua esfera. Desta vez, porém, a sua esfera mudará a sua cor de preto para branco, à medida que os quadros das câmaras são recolhidos. Assim que tivermos quadros suficientes, a esfera transformar-se-á em amarelo, e o carregamento da nuvem começará. Assim que o upload terminar, a tua esfera ficará azul. Opcionalmente, também pode utilizar a janela `Logcat` dentro do Android Studio para monitorizar as mensagens de registo que a sua aplicação está a enviar. Por exemplo, o progresso da sessão durante a captura do quadro e o identificador de âncora que a nuvem retorna assim que o upload estiver concluído.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize a sua âncora espacial em nuvem

Uma a tua âncora está carregada para a nuvem, estamos prontos para tentar localizá-la outra vez. Primeiro, vamos adicionar as seguintes importações no seu código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Em seguida, vamos adicionar o seguinte código no seu método `handleTap()`. Este código irá:

- Retire a nossa esfera azul existente do ecrã.
- Inicialize novamente a nossa sessão de Âncoras Espaciais Azure. Esta ação vai garantir que a âncora que vamos localizar vem da nuvem em vez da âncora local que criamos.
- Emita uma consulta para a âncora que enviamos para a nuvem.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Agora, vamos ligar o código que será invocado quando a âncora que estamos pedindo está localizada. Dentro do seu método `initializeSession()`, adicione o seguinte código. Este corte criará e colocará uma esfera verde assim que a âncora espacial da nuvem estiver localizada. Também permitirá a escuta do ecrã novamente, para que possa repetir todo o cenário mais uma vez: criar outra âncora local, carregá-la e localizá-la novamente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Já está! [Recoloque](#trying-it-out) a sua aplicação uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se em torno do seu dispositivo, e coloque a sua esfera preta. Em seguida, continue a mover o seu dispositivo para capturar as molduras das câmaras até que a esfera fique amarela. A tua âncora local será carregada, e a tua esfera ficará azul. Por fim, toque no ecrã mais uma vez, para que a sua âncora local seja removida, e depois vamos consultar a sua congénere de nuvem. Continue a mover o seu dispositivo até que a sua âncora espacial em nuvem esteja localizada. Uma esfera verde deve aparecer no local correto, e pode enxaguar e repetir todo o cenário novamente.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
