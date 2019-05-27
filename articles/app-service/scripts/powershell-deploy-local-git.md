---
title: Script do Azure PowerShell de exemplo - criar aplicação e implemente a partir do repositório de Git local | Documentos da Microsoft
description: Exemplo do Script do Azure PowerShell - Crie uma aplicação Web e implemente o código a partir de um repositório Git local
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 80d1b295db7d1eb1e0ea0dda9522aa3875ea882d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136430"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Criar uma aplicação Web e implementar código a partir de um repositório do Git local

Este script de exemplo cria uma aplicação Web no Serviço de Aplicações com os respetivos recursos relacionados e, em seguida, implementa o seu código da aplicação Web num repositório do Git local.

Se for necessário, atualize para o Azure PowerShell mais recente utilizando a instrução encontrada no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. Além disso, o código da aplicação tem de ser consolidado para um repositório de Git local.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação Web com o grupo de recursos necessário e o grupo de Serviço de Aplicações. Quando o diretório atual contém um repositório de Git, adicione também um `azure` remoto. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).
