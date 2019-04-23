---
title: Criar uma regra de Balanceador de carga do Azure para um cluster
description: Configure um balanceador de carga do Azure para abrir portas para o seu cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: d95d2802398a61b948ff6c59fb3eab0e1ddddbc5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786138"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abra portas para um cluster do Service Fabric

O Balanceador de carga implementado com o seu cluster do Azure Service Fabric direciona o tráfego para a aplicação em execução num nó. Se alterar a aplicação para utilizar uma porta diferente, deve expor essa porta (ou encaminhar uma porta diferente) no balanceador de carga do Azure.

Quando implementou o cluster do Service Fabric para o Azure, um balanceador de carga foi criado automaticamente para. Se não tiver um balanceador de carga, veja [configurar um balanceador de carga com acesso à Internet](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Configurar o service fabric

A aplicação do Service Fabric **servicemanifest. XML** ficheiro de configuração define os pontos finais de seu aplicativo espera usar. Depois do ficheiro de configuração foi atualizado para definir um ponto final, o Balanceador de carga tem de ser atualizado para expor que (ou outro) porta. Para obter mais informações sobre como criar o ponto de final do service fabric, veja [configurar um ponto de extremidade](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

Uma regra de Balanceador de carga abre uma porta de acesso à internet e encaminha o tráfego para a porta do nó interno utilizado pela sua aplicação. Se não tiver um balanceador de carga, veja [configurar um balanceador de carga com acesso à Internet](../load-balancer/load-balancer-get-started-internet-portal.md).

Para criar uma regra de Balanceador de carga, terá de recolher as seguintes informações:

- Nome do Balanceador de carga.
- Grupo de recursos do cluster de recursos de infraestrutura de serviço e o Balanceador de carga.
- Porta externa.
- Porta interna.

## <a name="azure-cli"></a>CLI do Azure
Leva apenas um único comando para criar uma regra de Balanceador de carga com o **CLI do Azure**. Precisa apenas saber o nome do grupo de recursos e o Balanceador de carga para criar uma nova regra de.

>[!NOTE]
>Se precisar de saber o nome do Balanceador de carga, utilize este comando para obter rapidamente uma lista de todos os balanceadores de carga e os grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando da CLI do Azure tem alguns parâmetros que são descritos na tabela a seguir:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A porta da aplicação do Service Fabric está monitorando. |
| `--frontend-port` | Expõe a porta do Balanceador de carga para ligações externas. |
| `-lb-name` | O nome do Balanceador de carga para alterar. |
| `-g`       | O grupo de recursos que tenha o Balanceador de carga e o cluster do Service Fabric. |
| `-n`       | O nome da regra desejado. |


>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com a CLI do Azure, consulte [criar um balanceador de carga com a CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

O PowerShell é um pouco mais complicado do que a CLI do Azure. Siga estes passos conceituais para criar uma regra:

1. Obtenha o Balanceador de carga do Azure.
2. Crie uma regra.
3. Adicione a regra para o Balanceador de carga.
4. Atualize o Balanceador de carga.

>[!NOTE]
>Se precisar de saber o nome do Balanceador de carga, utilize este comando para obter rapidamente uma lista de todos os balanceadores de carga e grupos de recursos associados.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Relativamente à `New-AzLoadBalancerRuleConfig` comando, o `-FrontendPort` representa a porta do Balanceador de carga expõe para ligações externas, e o `-BackendPort` representa a porta da aplicação do service fabric está a escutar.

>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com o PowerShell, consulte [criar um balanceador de carga com o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [nos recursos de infraestrutura do serviço de rede](service-fabric-patterns-networking.md).rvice-fabric-padrões-networking.md).