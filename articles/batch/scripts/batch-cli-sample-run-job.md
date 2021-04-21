---
title: Exemplo de script do Azure CLI - Executar um trabalho de lote
description: Este script cria um trabalho do Batch e adiciona uma série de tarefas ao trabalho. Também demonstra como monitorizar um trabalho e as respetivas tarefas.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768173"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemplo da CLI: Executar um trabalho e tarefas com o Azure Batch

Este script cria um trabalho do Batch e adiciona uma série de tarefas ao trabalho. Também demonstra como monitorizar um trabalho e as respetivas tarefas. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

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
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Cria um conjunto de nós de computação.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Cria um trabalho do Batch.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Adiciona uma tarefa ao trabalho do Batch especificado.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Atualiza as propriedades de um trabalho do Batch.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Obtém os detalhes de um trabalho do Batch especificado.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Obtém os detalhes de uma tarefa do trabalho do Batch especificado.  |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
