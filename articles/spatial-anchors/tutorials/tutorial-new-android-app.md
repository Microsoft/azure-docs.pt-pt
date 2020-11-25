---
title: 'Tutorial: Criar uma nova aplicação Android'
description: Neste tutorial, aprende-se a criar uma nova aplicação Android utilizando âncoras espaciais Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: af0d01a20728d2332d4a8d71819f73baf68a65a4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998396"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova aplicação Android usando âncoras espaciais Azure

Este tutorial irá mostrar-lhe como criar uma nova aplicação Android que integra a funcionalidade ARCore com âncoras espaciais Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

- Uma máquina Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para o desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>

## <a name="getting-started"></a>Introdução

Inicie o Android Studio. Na janela **Welcome to Android Studio,** clique **em Iniciar um novo projeto Android Studio**. Ou, se tiver um projeto já aberto, selecione **File** -> **New Project**.

Na janela **Criar Novo Projeto,** na secção **Telefone e Tablet,** escolha **Atividade Vazia** e clique em **Seguinte**. Em seguida, ao **nível mínimo de API,** escolha `API 26: Android 8.0 (Oreo)` e certifique-se de que a **língua** está definida para `Java` . Pode querer alterar o Nome do Projeto & Localização e o nome do Pacote. Deixe as outras opções como estão. Clique em **Concluir**. O **Instalador de Componentes** funcionará. Uma vez feito, clique em **Terminar.** Depois de algum processamento, o Android Studio abrirá o IDE.

## <a name="trying-it-out"></a>Experimentando

Para testar a sua nova aplicação, ligue o seu dispositivo ativado pelo programador à sua máquina de desenvolvimento com um cabo USB. Clique **em Executar** executar -> **'app'.** Na janela **'Destino de implementação' Selecione,** selecione o seu dispositivo e clique em **OK**. O Android Studio instala a aplicação no seu dispositivo conectado e inicia-a. Devias ver "Olá Mundo!" exibida na aplicação em execução no seu dispositivo. Clique **em Executar** -> **'app' de 'run'.**

## <a name="integrating-_arcore_"></a>Integração _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_A ARCore_</a> é a plataforma da Google para construir experiências de Realidade Aumentada, permitindo ao seu dispositivo acompanhar a sua posição à medida que se move e constrói a sua própria compreensão do mundo real.

Modificar `app\manifests\AndroidManifest.xml` para incluir as seguintes entradas dentro do `<manifest>` nó raiz. Este código corta algumas coisas:

- Permitirá que a sua aplicação aceda à câmara do seu dispositivo.
- Também irá garantir que a sua aplicação só está visível na Google Play Store para dispositivos que suportem o ARCore.
- Irá configurar a Google Play Store para descarregar e instalar o ARCore, caso ainda não esteja instalado, quando a sua aplicação estiver instalada.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a seguinte entrada. Este código irá garantir que a sua aplicação tem como alvo a versão ARCore 1.8. Após esta alteração, poderá receber uma notificação de Gradle pedindo-lhe para sincronizar: clique **em Sync agora**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integração _da forma_ de cena

[_O Sceneform_](https://developers.google.com/sceneform/develop/) torna simples tornar cenas 3D realistas em aplicações de Realidade Aumentada, sem ter de aprender OpenGL.

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir as seguintes entradas. Este código permitirá que a sua aplicação utilize construções linguísticas a partir de Java 8, o que `Sceneform` requer. Também irá garantir que a sua aplicação visa a `Sceneform` versão 1.8, uma vez que deverá corresponder à versão do ARCore que a sua aplicação está a usar. Após esta alteração, poderá receber uma notificação de Gradle pedindo-lhe para sincronizar: clique **em Sync agora**.

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

Abra o seu `app\res\layout\activity_main.xml` , e substitua o elemento Hello Wolrd existente `<TextView>` pelo seguinte ArFragment. Este código fará com que a alimentação da câmara seja exibida no ecrã, permitindo que o ARCore rastree a posição do dispositivo à medida que se move.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Reimplante](#trying-it-out) a sua aplicação para o seu dispositivo para validá-la mais uma vez. Desta vez, devem ser-lhe pedidas permissões de câmara. Uma vez aprovado, deverá ver a sua imagem de câmara a renderizar no ecrã.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real

Vamos criar & colocar um objeto usando a sua aplicação. Em primeiro lugar, adicione as seguintes importações na `app\java\<PackageName>\MainActivity` sua:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Em seguida, adicione as seguintes variáveis de membro na sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Em seguida, adicione o seguinte código no seu `app\java\<PackageName>\MainActivity` `onCreate()` método. Este código irá ligar um ouvinte, chamado `handleTap()` , que irá detetar quando o utilizador toca no ecrã do seu dispositivo. Se a torneira estiver numa superfície real do mundo que já foi reconhecida pelo rastreio da ARCore, o ouvinte irá correr.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Finalmente, adicione o seguinte `handleTap()` método, que vai ligar tudo junto. Criará uma esfera e colocará-a no local sob escuta. A esfera será inicialmente preta, uma vez `this.recommendedSessionProgress` que está definida para zero agora. Este valor será ajustado mais tarde.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

[Reimplante](#trying-it-out) a sua aplicação para o seu dispositivo para validá-la mais uma vez. Desta vez, pode mover-se em torno do seu dispositivo para que o ARCore comece a reconhecer o seu ambiente. Em seguida, toque no ecrã para criar & coloque a sua esfera negra sobre a superfície à sua escolha.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anexar uma âncora espacial local Azure

Modificar `Gradle Scripts\build.gradle (Module: app)` para incluir a seguinte entrada. Este código irá garantir que a sua aplicação visa a versão 2.2.0 da Azure Spatial Anchors. Dito isto, fazer referência a qualquer versão recente de Azure Spatial Anchors deve funcionar. Pode encontrar as notas de lançamento [aqui.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Clique à direita `app\java\<PackageName>` -> **na Classe Nova** -> **Java.** Definir **Nome** para _MyFirstApp_, e **Superclass** para _android.app.Application_. Deixe as outras opções como estão. Clique em **OK**. Será criado um ficheiro `MyFirstApp.java` chamado. Adicione-lhe a seguinte importação:

```java
import com.microsoft.CloudServices;
```

Em seguida, adicione o seguinte código dentro da nova `MyFirstApp` classe, que garantirá que as Âncoras Espaciais Azure são inicializadas com o contexto da sua aplicação.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Agora, modifique `app\manifests\AndroidManifest.xml` para incluir a seguinte entrada dentro do nó `<application>` raiz. Este código irá ligar a classe Aplicação que criou na sua aplicação.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De volta `app\java\<PackageName>\MainActivity` , adicione-lhe as seguintes importações:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Em seguida, adicione as seguintes variáveis de membro na sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Em seguida, vamos adicionar o seguinte método dentro da `initializeSession()` sua `mainActivity` classe. Uma vez convocado, irá garantir que uma sessão de Âncoras Espaciais Azure seja criada e devidamente inicializada durante o arranque da sua app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

Agora, vamos ligar o seu `initializeSession()` método ao seu `onCreate()` método. Além disso, vamos garantir que as molduras do feed da câmara são enviadas para Azure Spatial Anchors SDK para processamento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Por fim, adicione o seguinte código ao seu `handleTap()` método. Vai ligar uma âncora espacial local à esfera negra que estamos colocando no mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

[Reimplante](#trying-it-out) a sua aplicação mais uma vez. Mova-se em torno do seu dispositivo, toque no ecrã e coloque uma esfera preta. Desta vez, porém, o seu código vai criar e anexar uma âncora espacial Azure local à sua esfera.

Antes de prosseguir, terá de criar uma conta Azure Spatial Anchors para obter o Identificador, Chave e Domínio da conta, se ainda não as tiver. Siga a seguinte secção para os obter.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Faça o upload da sua âncora local para a nuvem

Assim que tiver a sua conta Azure Spatial Anchors Identifier, Key e Domain, podemos voltar `app\java\<PackageName>\MainActivity` a entrar, adicione as seguintes importações:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Em seguida, adicione as seguintes variáveis de membro na sua `MainActivity` classe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Agora, adicione o seguinte código no seu `initializeSession()` método. Em primeiro lugar, este código permitirá que a sua aplicação monitorize o progresso que o Azure Spatial Anchors SDK faz à medida que recolhe quadros a partir do feed da câmara. Assim como, a cor da sua esfera começará a mudar do seu preto original, para cinza. Em seguida, ficará branco uma vez que sejam recolhidos quadros suficientes para submeter a sua âncora à nuvem. Segundo, este código fornecerá as credenciais necessárias para comunicar com a nuvem de fundo. Aqui é onde irá configurar a sua aplicação para usar o seu identificador, chave e domínio da sua conta. Copiou-os para um editor de texto ao [configurar o recurso Spatial Anchors](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

Em seguida, adicione o seguinte `uploadCloudAnchorAsync()` método dentro da sua `mainActivity` classe. Uma vez chamado, este método aguardará assincronamente até que sejam recolhidas molduras suficientes do seu dispositivo. Assim que isso acontecer, mudará a cor da sua esfera para amarelo, e então começará a carregar a sua Âncora Espacial Azure local para a nuvem. Uma vez terminado o upload, o código retornará um identificador de âncora.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Finalmente, vamos juntar tudo. No seu `handleTap()` método, adicione o seguinte código. Invocará o seu `uploadCloudAnchorAsync()` método assim que a sua esfera for criada. Uma vez que o método retorna, o código abaixo executará uma última atualização para a sua esfera, alterando a sua cor para azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

[Reimplante](#trying-it-out) a sua aplicação mais uma vez. Mova-se em torno do seu dispositivo, toque no ecrã e coloque a sua esfera. Desta vez, porém, a sua esfera mudará a sua cor de preto para branco, à medida que as molduras das câmaras forem recolhidas. Assim que tivermos quadros suficientes, a esfera transformar-se-á em amarelo, e o upload da nuvem começará. Assim que o upload terminar, a sua esfera ficará azul. Opcionalmente, também pode utilizar a janela dentro do `Logcat` Android Studio para monitorizar as mensagens de registo que a sua aplicação está a enviar. Por exemplo, o progresso da sessão durante a captura do quadro e o identificador de âncora que a nuvem retorna assim que o upload estiver concluído.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize a sua âncora espacial em nuvem

Uma âncora é enviada para a nuvem, estamos prontos para tentar localizá-la novamente. Primeiro, vamos adicionar as seguintes importações no seu código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Então, vamos adicionar o seguinte código no seu `handleTap()` método. Este código:

- Remova a nossa esfera azul existente do ecrã.
- Inicialize a nossa sessão de Âncoras Espaciais Azure novamente. Esta ação vai garantir que a âncora que vamos localizar vem da nuvem em vez da âncora local que criamos.
- Emita uma consulta para a âncora que enviamos para a nuvem.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Agora, vamos ligar o código que será invocado quando a âncora que estamos a pedir está localizada. Dentro do seu `initializeSession()` método, adicione o seguinte código. Este corte irá criar & colocar uma esfera verde uma vez que a âncora espacial nuvem está localizada. Também irá permitir a toscagem do ecrã novamente, para que possa repetir todo o cenário mais uma vez: criar outra âncora local, carregá-la e localizá-la novamente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Já está! [Reimplantar](#trying-it-out) a sua app uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se ao redor do seu dispositivo, e coloque a sua esfera negra. Em seguida, continue a mover o seu dispositivo para capturar as molduras das câmaras até que a esfera fique amarela. A tua âncora local será carregada, e a tua esfera ficará azul. Finalmente, toque no seu ecrã mais uma vez, para que a sua âncora local seja removida, e depois vamos consultar a sua contraparte na nuvem. Continue a mover o seu dispositivo até que a sua âncora espacial em nuvem esteja localizada. Uma esfera verde deve aparecer na localização correta, e pode enxaguar & repetir todo o cenário novamente.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
