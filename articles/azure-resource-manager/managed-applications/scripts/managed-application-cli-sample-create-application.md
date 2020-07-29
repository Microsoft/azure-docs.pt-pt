---
title: Amostra de script Azure CLI - Implementar uma aplicação gerida
description: Fornece o script da amostra do Azure CLI que implementa uma definição de Aplicação Gerida Azure para a subscrição.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 0c2fc3dc4e76f295dff94ebda71d54bad425b621
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056142"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Implementar uma aplicação gerida para catálogo de serviço com a CLI do Azure

Este script implementa uma definição da aplicação gerida a partir do catálogo de serviço. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az-managedapp-create) | Criar uma aplicação gerida. Dê o ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Próximos passos

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
