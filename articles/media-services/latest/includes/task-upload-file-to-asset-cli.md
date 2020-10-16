---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88658439"
---
<!-- ### Upload files with the CLI -->

O seguinte comando carrega um único ficheiro.  

Alterar os seguintes valores:

1. Mude `/path/to/file` para o seu caminho local do arquivo.  
1. Altere `MyContainer` para um ativo que criou anteriormente usando o ID do ativo (não o nome).
1. Mude `MyBlob` para o nome que pretende utilizar para o ficheiro carregado.
1. Altere `MyStorageAccountName` para o nome da conta de armazenamento que está a usar.
1. Altere `MyStorageAccountKey` a chave de acesso para a sua conta de armazenamento.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
