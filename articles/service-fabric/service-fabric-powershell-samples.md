---
title: Exemplos do Azure PowerShell - Service Fabric
description: Conheça a criação e gestão de clusters, apps e serviços da Azure Service Fabric utilizando o Powershell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86187103"
---
# <a name="azure-service-fabric-powershell-samples"></a>Amostras de PowerShell de tecido de serviço Azure

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerem clusters, aplicações e serviços do Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| Script | Description |
|-|-|
| **Criar cluster** ||
| [Criar um cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Cria um cluster do Azure Service Fabric. |
| **Gerir o cluster, os nós e a infraestrutura** ||
| [Adicionar um certificado de aplicação](./scripts/service-fabric-powershell-add-application-certificate.md)| Cria um certificado X509 para o Key Vault e implanta-o numa escala de máquina virtual definida no seu cluster. |
| [Atualizar o intervalo de portas RDP nas VMs do cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de portas RDP nas VMs no nó de cluster num cluster implementado.|
| [Atualizar o utilizador e a palavra-passe de admin das VMs no nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de utilizador e a palavra-passe de admin das VMs no nó de cluster. |
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicação no balanceador de carga do Azure para permitir tráfego de entrada numa porta específica. |
| [Criar uma regra de grupo de segurança de rede de entrada](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crie uma regra de grupo de segurança de rede de entrada para permitir tráfego de entrada para o cluster numa porta específica. |
| **Gerir aplicações** ||
| [Implementar uma aplicação](./scripts/service-fabric-powershell-deploy-application.md)| Implemente uma aplicação num cluster.|
| [Atualizar uma aplicação](./scripts/service-fabric-powershell-upgrade-application.md)| Atualize uma aplicação.|
| [Remover uma aplicação](./scripts/service-fabric-powershell-remove-application.md)| Remova uma aplicação de um cluster.|
