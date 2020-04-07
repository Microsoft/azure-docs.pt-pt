---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673370"
---
Pode ver a fila no [portal Azure](../articles/storage/queues/storage-quickstart-queues-portal.md) ou no [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Pode também ver a fila no Azure CLI, conforme descrito nos seguintes passos:

1. Abra o ficheiro *local.definição.json* do projeto de função e copie o valor da cadeia de ligação. Numa janela de terminais ou comando, execute `AZURE_STORAGE_CONNECTION_STRING`o seguinte comando para criar `<MY_CONNECTION_STRING>`uma variável ambiental chamada, colando a sua cadeia de ligação específica no lugar de . (Esta variável ambiental significa que não precisa de fornecer a `--connection-string` cadeia de ligação a cada comando subsequente usando o argumento.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
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
    
1. (Opcional) Utilize [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) o comando para visualizar as filas de armazenamento na sua conta. A saída deste comando deve `outqueue`incluir uma fila chamada , que foi criada quando a função escreveu a sua primeira mensagem para aquela fila.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Utilize [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) o comando para ler a mensagem desta fila, que deve ser o primeiro nome utilizado ao testar a função anteriormente. O comando lê e remove a primeira mensagem da fila. 

    # <a name="bash"></a>[bash](#tab/bash)
    
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

    Este script utiliza o certutil para descodificar a coleção de mensagens codificada base64 a partir de um ficheiro temporário local. Se não houver saída, `> NUL` tente remover do guião para parar de suprimir a saída certutil, caso haja um erro. 
    
    ---
    
    Como o corpo da mensagem está armazenado na [base64 codificada,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)a mensagem deve ser descodificada antes de ser apresentada. Depois de `az storage message get`executar, a mensagem é removida da fila. Se houvesse apenas `outqueue`uma mensagem, não recuperará uma mensagem quando executar este comando uma segunda vez e, em vez disso, obter um erro.