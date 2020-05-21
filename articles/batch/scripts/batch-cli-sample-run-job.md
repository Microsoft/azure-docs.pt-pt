---
title: Exemplo de script azure CLI - Executar um trabalho de lote
description: Este script cria um trabalho do Batch e adiciona uma série de tarefas ao trabalho. Também demonstra como monitorizar um trabalho e as respetivas tarefas.
ms.topic: sample
ms.date: 12/12/2019
ms.openlocfilehash: ce3c06b4631095df54371ca2417dc45d493a437d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681835"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemplo da CLI: Executar um trabalho e tarefas com o Azure Batch

Este script cria um trabalho do Batch e adiciona uma série de tarefas ao trabalho. Também demonstra como monitorizar um trabalho e as respetivas tarefas. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

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

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).