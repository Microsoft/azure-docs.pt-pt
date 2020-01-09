---
title: Exemplos do Azure PowerShell - Service Fabric
description: Saiba mais sobre a criação e o gerenciamento de clusters, aplicativos e serviços do Azure Service Fabric usando o PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645655"
---
# <a name="azure-service-fabric-powershell-samples"></a>Exemplos do PowerShell Service Fabric do Azure

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerem clusters, aplicações e serviços do Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Criar cluster** ||
| [Criar um cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Cria um cluster do Azure Service Fabric. |
| **Gerir o cluster, os nós e a infraestrutura** ||
| [Adicionar um certificado de aplicação](./scripts/service-fabric-powershell-add-application-certificate.md)| Cria um certificado X509 para Key Vault e o implanta em um conjunto de dimensionamento de máquinas virtuais em seu cluster. |
| [Atualizar o intervalo de portas RDP nas VMs do cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de portas RDP nas VMs no nó de cluster num cluster implementado.|
| [Atualizar o utilizador e a palavra-passe de admin das VMs no nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de utilizador e a palavra-passe de admin das VMs no nó de cluster. |
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicação no balanceador de carga do Azure para permitir tráfego de entrada numa porta específica. |
| [Criar uma regra de grupo de segurança de rede de entrada](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crie uma regra de grupo de segurança de rede de entrada para permitir tráfego de entrada para o cluster numa porta específica. |
| **Gerir aplicações** ||
| [Implementar uma aplicação](./scripts/service-fabric-powershell-deploy-application.md)| Implemente uma aplicação num cluster.|
| [Atualizar uma aplicação](./scripts/service-fabric-powershell-upgrade-application.md)| Atualize uma aplicação.|
| [Remover uma aplicação](./scripts/service-fabric-powershell-remove-application.md)| Remova uma aplicação de um cluster.|
