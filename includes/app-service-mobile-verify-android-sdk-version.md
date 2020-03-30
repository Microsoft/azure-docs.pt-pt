---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184774"
---
Devido ao desenvolvimento em curso, a versão Android SDK instalada no Android Studio pode não corresponder à versão no código. O Android SDK referenciado neste tutorial é a versão 26, a mais recente no momento da escrita. O número da versão pode aumentar à medida que surgem novos lançamentos do SDK, e recomendamos a utilização da versão mais recente disponível.

Dois sintomas de incompatibilidade de versão são:

- Quando construir ou reconstruir o projeto, poderá receber `Gradle sync failed: Failed to find target with hash string 'android-XX'`mensagens de erro gradle como .
- Os objetos Android standard em `import` código que devem ser resolvidos com base em declarações podem estar a gerar mensagens de erro.

Se alguma delas aparecer, a versão do Android SDK instalada no Android Studio pode não corresponder ao alvo SDK do projeto descarregado. Para verificar a versão, efaça as seguintes alterações:

1. No Android Studio, clique em **Ferramentas** > **Android** > **SDK Manager**. Se não tiver instalado a versão mais recente da Plataforma SDK, clique em instalá-la. Tome nota do número da versão.

2. No separador **Project Explorer,** sob **scripts Gradle,** abra o file **build.gradle (Módulo: app)**. Certifique-se de que o **compiladoSdkVersion** e **targetSdkVersion** estão definidos para a versão SDK mais recente instalada. Pode `build.gradle` parecer assim:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
