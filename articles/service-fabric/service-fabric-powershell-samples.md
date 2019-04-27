---
title: Exemplos do Azure PowerShell - Service Fabric | Microsoft Docs
description: Exemplos do Azure PowerShell - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8a3a80fb6ae20eddc3237d986ecda1d4cb5b65a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773253"
---
# <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerem clusters, aplicações e serviços do Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Criar cluster** ||
| [Criar um cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Cria um cluster do Azure Service Fabric. |
| **Gerir o cluster, os nós e a infraestrutura** ||
| [Adicionar um certificado de aplicação](./scripts/service-fabric-powershell-add-application-certificate.md)| Adiciona um certificado X.509 de aplicação a todos os nós num cluster. |
| [Atualizar o intervalo de portas RDP nas VMs do cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de portas RDP nas VMs no nó de cluster num cluster implementado.|
| [Atualizar o utilizador e a palavra-passe de admin das VMs no nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de utilizador e a palavra-passe de admin das VMs no nó de cluster. |
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicação no balanceador de carga do Azure para permitir tráfego de entrada numa porta específica. |
| [Criar uma regra de grupo de segurança de rede de entrada](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crie uma regra de grupo de segurança de rede de entrada para permitir tráfego de entrada para o cluster numa porta específica. |
| **Gerir aplicações** ||
| [Implementar uma aplicação](./scripts/service-fabric-powershell-deploy-application.md)| Implemente uma aplicação num cluster.|
| [Atualizar uma aplicação](./scripts/service-fabric-powershell-upgrade-application.md)| Atualize uma aplicação.|
| [Remover uma aplicação](./scripts/service-fabric-powershell-remove-application.md)| Remova uma aplicação de um cluster.|
