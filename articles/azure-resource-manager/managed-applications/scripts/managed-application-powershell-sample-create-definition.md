---
title: Criar definição de aplicação gerida - Azure PowerShell
description: Fornece uma amostra de script Azure PowerShell que cria uma definição de aplicação gerida na subscrição Azure.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 09fe1455f0dba57fd23aeeb66acb1f99fc2513d4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650206"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Criar uma definição de aplicação gerida com a PowerShell

Este script publica uma definição da aplicação gerida num catálogo de serviço.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar a definição da aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Definição de aplicação nova-az-gerida](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Criar uma definição da aplicação gerida. Forneça o pacote que contém os ficheiros necessários. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
