---
title: PowerShell script - implementar o runtime de integração Azure-SSIS | Documentos da Microsoft
description: Este script do PowerShell cria um runtime de integração Azure-SSIS que pode executar pacotes do SSIS na cloud.
services: data-factory
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: douglasl
ms.openlocfilehash: 437b8df66bca2923a342584c4deec5b2947365c5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443754"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell script - implementar o runtime de integração Azure-SSIS

Este script do PowerShell de exemplo cria um runtime de integração Azure-SSIS que pode executar os seus pacotes do SSIS no Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover a fábrica de dados do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Cria um runtime de integração Azure-SSIS que pode executar pacotes do SSIS na cloud |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Inicia o runtime de integração Azure-SSIS. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Obtém informações sobre o runtime de integração Azure-SSIS. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos do script do PowerShell de fábrica de dados do Azure adicionais podem ser encontrados no [exemplos do PowerShell do Azure Data Factory](../samples-powershell.md).
