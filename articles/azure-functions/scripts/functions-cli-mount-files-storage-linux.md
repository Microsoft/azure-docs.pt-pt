---
title: Monte uma partilha de ficheiros para uma aplicação de função Python - Azure CLI
description: Crie uma aplicação de função Python sem servidor e monte uma partilha de ficheiros existente utilizando o Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762286"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Monte uma partilha de ficheiros para uma aplicação de função Python usando O Azure CLI

Este script de amostra de Azure Functions cria uma aplicação de função e cria uma partilha em Ficheiros Azure. É a parte que monta a partilha para que os dados possam ser acedidos pelas suas funções.  

>[!NOTE]
>A aplicação de função criada é criada na versão Python 3.7. A azure Functions também [suporta as versões Python 3.6 e 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Script de exemplo

Este script cria uma aplicação de função em Funções Azure utilizando o [plano de consumo.](../consumption-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma Conta de armazenamento do Azure. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria uma aplicação de funções. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Cria uma partilha de Ficheiros Azure na conta de armazenamento. | 
| [diretório de armazenamento az criar](/cli/azure/storage/directory#az_storage_directory_create) | Cria um diretório na parte. |
| [az webapp config armazenamento-conta adicionar](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Monta a partilha na aplicação de função. |
| [az webapp config lista de conta de armazenamento](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Mostra partilhas de ficheiros montadas na aplicação de função. | 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
