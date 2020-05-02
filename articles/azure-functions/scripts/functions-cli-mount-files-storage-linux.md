---
title: Monte uma partilha de ficheiros numa aplicação de função Python - Azure CLI
description: Crie uma aplicação de função Python sem servidor e monte uma partilha de ficheiros existente utilizando o Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79086461"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Monte uma partilha de ficheiros numa aplicação de função Python usando o Azure CLI

Este script de amostra de Funções Azure cria uma aplicação de função e cria uma participação em Ficheiros Azure. Monta a parte para que os dados possam ser acedidos pelas suas funções.  

>[!NOTE]
>A aplicação de funções criada funciona na versão 3.7 da Python. As Funções Azure também suportam as [versões Python 3.6 e 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). Exemplos são escritos para a concha bash e devem ser modificados para serem executados num pedido de comando windows. 

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
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Cria uma quota de Ficheiros Azure na conta de armazenamento. | 
| [az diretório de armazenamento criar](/cli/azure/storage/directory#az-storage-directory-create) | Cria um diretório na parte. |
| [az webapp config conta de armazenamento adicionar](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monta a parte para a aplicação de funções. |
| [az webapp config lista de conta de armazenamento](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Mostra as partilhas de ficheiros montadas na aplicação de função. | 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
