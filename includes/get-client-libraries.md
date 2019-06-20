---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184501"
---
### <a name="install-via-composer"></a>Instalar através do compositor
1. Crie um ficheiro denominado **Composer. JSON** na raiz do seu projeto e adicione o seguinte código ao mesmo:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Baixe **[composer.phar] [ composer-phar]** na raiz do projeto.
3. Abra uma linha de comandos e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

Em alternativa, vá para o [biblioteca de cliente PHP de armazenamento do Azure] [ php-sdk-github] no GitHub para clonar o código-fonte.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
