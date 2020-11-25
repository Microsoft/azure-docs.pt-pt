---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990909"
---
## <a name="sample-templates"></a>Modelos de exemplo
Pode encontrar modelos de amostra para personalização da UI aqui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Este projeto contém os seguintes modelos:
- [Azul-do-oceano](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Ardósia Cinza](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para utilizar a amostra:

1. Clone o repo na sua máquina local. Escolha uma pasta de modelo `/ocean_blue` ou `/slate_gray` .
1. Faça o upload de todos os ficheiros sob a pasta do modelo e da `/assets` pasta, para o armazenamento blob, conforme descrito nas secções anteriores.
1. Em seguida, abra cada `\*.html` ficheiro na raiz de qualquer um ou `/ocean_blue` `/slate_gray` substitua todas as instâncias de URLs relativos com os URLs dos ficheiros css, imagens e tipos de letra que carregou no passo 2. Por exemplo:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Para
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Guarde os `\*.html` ficheiros e faça o upload para o armazenamento da Blob.
1. Agora modifique a política, apontando para o seu ficheiro HTML, como mencionado anteriormente.
1. Se vir tipos de letra, imagens ou CSS em falta, verifique as suas referências na política de extensões e nos \* ficheiros .html.
