---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 921127ffdd35007cc3fa2eaaa95cdb3fac8bbe15
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782292"
---
Pode ver a fila no [portal Azure](../articles/storage/queues/storage-quickstart-queues-portal.md) ou no  [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Também pode ver a fila no CLI Azure, conforme descrito nos seguintes passos:

1. Abra alocal.setting.jsdo projeto de função *no* ficheiro e copie o valor da cadeia de ligação. Numa janela de terminal ou comando, executar o seguinte comando para criar uma variável ambiental `AZURE_STORAGE_CONNECTION_STRING` chamada, colando a sua cadeia de ligação específica no lugar de  `<MY_CONNECTION_STRING>` . (Esta variável ambiental significa que não precisa de fornecer a cadeia de ligação a cada comando subsequente utilizando o `--connection-string` argumento.)

    # <a name="bash"></a>[festa](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Opcional) Utilize o [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) comando para visualizar as filas de armazenamento na sua conta. A saída deste comando deve incluir uma fila chamada `outqueue` , que foi criada quando a função escreveu a sua primeira mensagem para essa fila.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Utilize o [`az storage message get`](/cli/azure/storage/message#az_storage_message_get) comando para ler a mensagem desta fila, que deve ser o primeiro nome que usou ao testar a função anteriormente. O comando lê e remove a primeira mensagem da fila. 

    # <a name="bash"></a>[festa](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Este script utiliza certutil para descodificar a recolha de mensagens codificadas base64 a partir de um ficheiro temporário local. Se não houver saída, tente remover `> NUL` do script para parar de suprimir a saída de certutil, no caso de haver um erro. 
    
    ---
    
    Como o corpo da mensagem está armazenado [na base64 codificado,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)a mensagem deve ser descodificado antes de ser exibida. Depois de `az storage message get` executar, a mensagem é removida da fila. Se houvesse apenas uma `outqueue` mensagem, não recuperará uma mensagem quando executar este comando uma segunda vez e, em vez disso, terá um erro.
