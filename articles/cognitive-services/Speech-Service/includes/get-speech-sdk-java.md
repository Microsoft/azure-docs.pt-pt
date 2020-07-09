---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637507"
---
:::row:::
    :::column span="3":::
        O Java SDK para Android é embalado como <a href="https://developer.android.com/studio/projects/android-library" target="_blank">um AAR <span class="docon docon-navigate-external x-hidden-focus"></span> (Android Library) </a>, que inclui as bibliotecas necessárias e permissões necessárias para Android. Está hospedado num repositório de Maven `https://csspeechstorage.blob.core.windows.net/maven/` como `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1` pacote.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Para consumir o pacote do seu projeto Android Studio, faça as seguintes alterações:

1. No ficheiro *build.gradle* de nível de projeto, adicione o seguinte à `repository` secção:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. No ficheiro *build.gradle* de nível de módulo, adicione o seguinte à `dependencies` secção:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

O Java SDK também faz parte do [SDK dos Dispositivos de Fala.](../speech-devices-sdk.md)

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Código fonte de arranque rápido java específico do Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) código fonte de arranque rápido<span class="docon docon-navigate-external x-hidden-focus"></span></a>