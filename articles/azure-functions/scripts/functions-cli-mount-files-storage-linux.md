---
title: Monte uma partilha de ficheiros para uma aplicação de função Python - Azure CLI
description: Crie uma aplicação de função Python sem servidor e monte uma partilha de ficheiros existente utilizando o Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498213"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Monte uma partilha de ficheiros para uma aplicação de função Python usando O Azure CLI

Este script de amostra de Azure Functions cria uma aplicação de função e cria uma partilha em Ficheiros Azure. É a parte que monta a partilha para que os dados possam ser acedidos pelas suas funções.  

>[!NOTE]
>A aplicação de função criada é criada na versão Python 3.7. A azure Functions também [suporta as versões Python 3.6 e 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). Os exemplos são escritos para a concha bash e devem ser modificados para funcionar em uma solicitação de comando do Windows. 

## <a name="sample-script"></a>Script de exemplo

Este script cria uma aplicação Azure Function utilizando o [plano de consumo.](../functions-scale.md#consumption-plan)

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
