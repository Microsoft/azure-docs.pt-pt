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
ms.openlocfilehash: a2c10e7915b0d293d3193c710885ded26a791f2c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745136"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Repor um Serviço de Nuvem Azure (suporte alargado) 
Estas amostras cobrem várias formas de repor uma implantação existente do Azure Cloud Service (suporte alargado).

## <a name="reimage-role-instances-of-cloud-service"></a>Reimage role instances of Cloud Service
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Este comando reimageia 2 instâncias de papel **ContosoFrontEnd \_ IN \_ 0** e **ContosoBackEnd \_ IN \_ 1** do Cloud Service chamado ContosoCS que pertence ao grupo de recursos chamado ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Reimagem todos os papéis do Cloud Service
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Reimagem uma única instância de um Serviço de Cloud
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Reinicie uma única instância de função de um Serviço cloud
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os Serviços de Nuvem Azure (suporte alargado), consulte [a visão geral dos Serviços Azure Cloud (suporte alargado).](overview.md)