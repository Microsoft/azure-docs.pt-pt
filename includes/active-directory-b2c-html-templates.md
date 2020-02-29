---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189034"
---
## <a name="sample-templates"></a>Modelos de exemplo
Você pode encontrar modelos de exemplo para personalização da interface do usuário aqui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Este projeto contém os seguintes modelos:
- [Oceano Azul](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Cinza de ardósia](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para utilizar a amostra:

1. Clone o repositório em seu computador local. Escolha uma pasta de modelo `/ocean_blue` ou `/slate_gray`.
1. Faça upload de todos os ficheiros sob a pasta do modelo e da pasta `/assets`, para o armazenamento Blob, conforme descrito nas secções anteriores.
1. Em seguida, abra cada ficheiro `\*.html` na raiz de `/ocean_blue` ou `/slate_gray`, substitua todas as instâncias de URLs relativos pelos URLs dos ficheiros css, imagens e tipos de letra que tenha carregado no passo 2. Por exemplo:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Para
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Guarde os ficheiros `\*.html` e carregue-os para o armazenamento blob.
1. Agora modifique a política, apontando para o seu ficheiro HTML, como mencionado anteriormente.
1. Se vir fontes, imagens ou CSS em falta, verifique as suas referências na política de extensões e nos ficheiros \*.html.
