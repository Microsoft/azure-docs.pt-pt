---
title: 'PowerShell: Ligue-se a uma conta de armazenamento'
description: Saiba como utilizar o Azure PowerShell para automatizar a implementação e gestão do Serviço de Aplicações. Esta amostra mostra como ligar uma aplicação a uma conta de armazenamento.
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9c182c797515288038ae6a23d6d939d5ec855011
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073918"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>Conecte uma aplicação do Serviço de Aplicações a uma conta de armazenamento

Neste cenário, você vai aprender como criar uma conta de armazenamento Azure e uma app App Service. Em seguida, ligará a conta de armazenamento à aplicação utilizando as definições de aplicações.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução da amostra de script, o seguinte comando pode ser usado para remover o grupo de recursos, app App Service e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação de Serviço de Aplicações. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma Conta de armazenamento. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtém as chaves de acesso para uma conta de Armazenamento do Azure. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica a configuração de uma aplicação do Serviço de Aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Amostras adicionais de Azure Powershell para o Azure App Service podem ser encontradas nas [amostras Azure PowerShell](../samples-powershell.md).
