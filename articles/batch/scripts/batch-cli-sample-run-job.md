---
title: Exemplo do Script da CLI do Azure - Executar um trabalho do Batch | Microsoft Docs
description: Exemplo do Script do CLI do Azure - Executar um trabalho com o Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: tysonn
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: a5e81393014dd70ae83f66e2a1d41f4de3c14205
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321858"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemplo da CLI: Executar um trabalho e tarefas com o lote do Azure

Este script cria um trabalho do Batch e adiciona uma série de tarefas ao trabalho. Também demonstra como monitorizar um trabalho e as respetivas tarefas. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Cria um conjunto de nós de computação.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-create) | Cria um trabalho do Batch.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az-batch-task-create) | Adiciona uma tarefa ao trabalho do Batch especificado.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-set) | Atualiza as propriedades de um trabalho do Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az-batch-job-show) | Obtém os detalhes de um trabalho do Batch especificado.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az-batch-task-show) | Obtém os detalhes de uma tarefa do trabalho do Batch especificado.  |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).