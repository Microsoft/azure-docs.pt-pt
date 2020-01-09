---
title: Exemplo de script Azure PowerShell – implantar um aplicativo gerenciado
description: Fornece Azure PowerShell exemplo de script de exemplo que implanta uma definição de aplicativo gerenciado para a assinatura.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: e076128a5be453028635b1657bf3f90980863148
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650171"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Implantar um aplicativo gerenciado para um catálogo de serviços com o PowerShell

Este script implementa uma definição da aplicação gerida a partir do catálogo de serviço.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Criar uma aplicação gerida. Dê o ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
