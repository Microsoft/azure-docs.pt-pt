---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656640"
---
:::row:::
    :::column span="3":::
        O Java SDK para Android está embalado como <a href="https://developer.android.com/studio/projects/android-library" target="_blank">Um Ar (Biblioteca Android), <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>que inclui as bibliotecas necessárias e as permissões necessárias para android. Está hospedado num repositório Maven `https://csspeechstorage.blob.core.windows.net/maven/` como `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`pacote.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Para consumir o pacote do seu projeto Android Studio, efaça as seguintes alterações:

1. No ficheiro *build.gradle* ao nível do `repository` projeto, adicione o seguinte à secção:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. No ficheiro *build.gradle* de nível de `dependencies` módulo, adicione o seguinte à secção:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

O Java SDK também faz parte do [SDK dispositivos](../speech-devices-sdk.md)de fala.

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Código fonte de início rápido de Java específico do Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Código fonte de arranque rápido java Runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>