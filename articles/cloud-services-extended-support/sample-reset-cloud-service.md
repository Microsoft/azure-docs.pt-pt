---
title: Amostras Azure PowerShell - Reset Azure Cloud Services (suporte alargado)
description: Scripts de amostra para repor uma implantação do Azure Cloud Service (suporte alargado)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475324"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Repor um Serviço de Nuvem Azure (suporte alargado) 
Estas amostras cobrem várias formas de repor uma implantação existente do Azure Cloud Service (suporte alargado).

## <a name="reimage-role-instances-of-cloud-service"></a>Reimage role instances of cloud service
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando reimagem 2 instâncias de função ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Reimagem todos os papéis do Cloud Service
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando reimagem todas as instâncias de função do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Reimagem uma única instância de um Serviço de Cloud
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Este comando reimagems instance nomeado ContosoFrontEnd_IN_0 do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Reconstruir exemplos de funções de serviço em nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando reconstrói 2 instâncias de função ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 de serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Reconstruir todos os papéis do serviço em nuvem
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando reconstrói todas as instâncias de função do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Reiniciar instâncias de função do serviço em nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando reinicia 2 instâncias de função ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Reinicie todos os papéis do serviço em nuvem
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando reinicia todas as instâncias de função do serviço de nuvem chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre os Serviços de Nuvem Azure (suporte alargado), consulte [a visão geral dos Serviços Azure Cloud (suporte alargado).](overview.md)
- Visite o [repositório](https://github.com/Azure-Samples/cloud-services-extended-support) de amostras cloud services (suporte alargado)
