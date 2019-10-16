---
title: Exemplo do script da CLI do Azure - Implementar uma aplicação gerida | Microsoft Docs
description: Fornece CLI do Azure script de exemplo que implanta uma definição de aplicativo gerenciado do Azure para a assinatura.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 18dbd46df3e5e1f7dc0368b2c007e0c15d76decc
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330164"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Implementar uma aplicação gerida para catálogo de serviço com a CLI do Azure

Este script implementa uma definição da aplicação gerida a partir do catálogo de serviço. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Criar uma aplicação gerida. Dê o ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
