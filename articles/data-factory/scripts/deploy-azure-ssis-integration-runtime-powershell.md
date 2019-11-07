---
title: Script do PowerShell – implantar o tempo de execução de integração do Azure-SSIS
description: Esse script do PowerShell cria um tempo de execução de integração do Azure-SSIS que pode executar pacotes do SSIS na nuvem.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: beba0a8d01d7d10910716f03602116fe7673ffc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684367"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Script do PowerShell – implantar o tempo de execução de integração do Azure-SSIS

Este script do PowerShell de exemplo cria um tempo de execução de integração do Azure-SSIS que pode executar seus pacotes SSIS no Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, você pode usar o seguinte comando para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Cria um tempo de execução de integração do Azure-SSIS que pode executar pacotes SSIS na nuvem |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Inicia o tempo de execução de integração do Azure-SSIS. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Obtém informações sobre o tempo de execução de integração do Azure-SSIS. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script Azure Data Factory PowerShell podem ser encontrados nos [exemplos do Azure data Factory PowerShell](../samples-powershell.md).
