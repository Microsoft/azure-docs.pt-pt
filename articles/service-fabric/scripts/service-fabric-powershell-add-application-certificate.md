---
title: Exemplo do Script do Azure PowerShell - Adicionar o certificado de aplicação a um cluster | Microsoft Docs
description: Exemplo do Script do Azure PowerShell - Adicionar o certificado de aplicação a um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: d5e1540a347bb14e7479bc445afa98e220b5475a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497066"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Adicionar um certificado de aplicação a um cluster do Service Fabric

Este script de exemplo cria um certificado autoassinado no cofre de chaves do Azure especificado e instala-o em todos os nós do cluster do Service Fabric. O certificado também transfere para uma pasta local. O nome do certificado transferido é igual ao nome do certificado no cofre de chaves. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Adicione um novo certificado de aplicação para o conjunto de dimensionamento de máquina virtual que compõem o cluster.  |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
