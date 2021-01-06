---
title: Monte uma partilha de ficheiros para uma aplicação de função Python - Azure CLI
description: Crie uma aplicação de função Python sem servidor e monte uma partilha de ficheiros existente utilizando o Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89cf1422c70a49497a1bc43e42248612e0c8dd83
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930910"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Monte uma partilha de ficheiros para uma aplicação de função Python usando O Azure CLI

Este script de amostra de Azure Functions cria uma aplicação de função e cria uma partilha em Ficheiros Azure. É a parte que monta a partilha para que os dados possam ser acedidos pelas suas funções.  

>[!NOTE]
>A aplicação de função criada é criada na versão Python 3.7. A azure Functions também [suporta as versões Python 3.6 e 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Script de exemplo

Este script cria uma aplicação Azure Function utilizando o [plano de consumo.](../consumption-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma Conta de armazenamento do Azure. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de funções. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Cria uma partilha de Ficheiros Azure na conta de armazenamento. | 
| [diretório de armazenamento az criar](/cli/azure/storage/directory#az-storage-directory-create) | Cria um diretório na parte. |
| [az webapp config armazenamento-conta adicionar](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monta a partilha na aplicação de função. |
| [az webapp config lista de conta de armazenamento](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Mostra partilhas de ficheiros montadas na aplicação de função. | 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
