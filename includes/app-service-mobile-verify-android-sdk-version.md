---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141195"
---
Por causa de desenvolvimento contínuo, a versão do Android SDK instalada no Android Studio não pode corresponder à versão no código. O Android SDK referenciado neste tutorial é a versão 26, a versão mais recente no momento da escrita. O número de versão pode aumentar conforme novas versões do SDK aparecem e recomendamos que utilize a versão mais recente disponível.

Dois sintomas do erro de correspondência de versão são:

- Quando criar ou reconstruir o projeto, poderá receber mensagens de erro do Gradle como `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Objetos de Android padrão no código que deve resolver com base em `import` instruções podem estar a gerar mensagens de erro.

Se qualquer um destes for apresentada, a versão do SDK Android instalado no Android Studio pode não corresponder ao destino do SDK do projeto transferido. Para verificar a versão, efetue as seguintes alterações:

1. No Android Studio, clique em **ferramentas** > **Android** > **SDK Manager**. Se não tiver instalado a versão mais recente da plataforma SDK, em seguida, clique para instalá-lo. Anote o número de versão.

2. Sobre o **Explorador de projeto** separador, em **Scripts de Gradle**, abra o ficheiro **gradle (módulo: aplicação)** . Certifique-se de que o **compileSdkVersion** e **targetSdkVersion** estão definidas para a versão mais recente do SDK instalada. O `build.gradle` teria esta aparência:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
