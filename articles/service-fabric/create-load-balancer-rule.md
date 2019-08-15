---
title: Criar uma regra de Azure Load Balancer para um cluster
description: Configure um Azure Load Balancer para abrir portas para o cluster de Service Fabric do Azure.
services: service-fabric
documentationcenter: na
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 2c96c421d768b1e75169615d81a03387602a83ac
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036072"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abrir portas para um Cluster Service Fabric

O balanceador de carga implantado com o cluster de Service Fabric do Azure direciona o tráfego para seu aplicativo em execução em um nó. Se você alterar seu aplicativo para usar uma porta diferente, deverá expor essa porta (ou rotear uma porta diferente) no Azure Load Balancer.

Quando você implantou o Cluster Service Fabric no Azure, um balanceador de carga foi criado automaticamente para você. Se você não tiver um balanceador de carga, consulte [configurar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Configurar o Service Fabric

Seu arquivo de configuração do Service Fabric Application **inmanifest. xml** define os pontos de extremidade que seu aplicativo espera usar. Depois que o arquivo de configuração tiver sido atualizado para definir um ponto de extremidade, o balanceador de carga deverá ser atualizado para expor essa porta (ou outra). Para obter mais informações sobre como criar o ponto de extremidade do Service Fabric, consulte [configurar um ponto de extremidade](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

Uma regra de Load Balancer abre uma porta voltada para a Internet e encaminha o tráfego para a porta do nó interno usada pelo seu aplicativo. Se você não tiver um balanceador de carga, consulte [configurar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md).

Para criar uma regra de Load Balancer, você precisa coletar as seguintes informações:

- Nome do balanceador de carga.
- Grupo de recursos do balanceador de carga e cluster do Service Fabric.
- Porta externa.
- Porta interna.

## <a name="azure-cli"></a>CLI do Azure
Ele usa apenas um único comando para criar uma regra de balanceador de carga com o **CLI do Azure**. Você só precisa saber o nome do balanceador de carga e do grupo de recursos para criar uma nova regra.

>[!NOTE]
>Se você precisar determinar o nome do balanceador de carga, use este comando para obter rapidamente uma lista de todos os balanceadores de carga e os grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando CLI do Azure tem alguns parâmetros descritos na tabela a seguir:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A porta à qual o aplicativo Service Fabric está escutando. |
| `--frontend-port` | A porta que o balanceador de carga expõe para conexões externas. |
| `-lb-name` | O nome do balanceador de carga a ser alterado. |
| `-g`       | O grupo de recursos que tem o balanceador de carga e o cluster de Service Fabric. |
| `-n`       | O nome desejado da regra. |


>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com o CLI do Azure, consulte [criar um balanceador de carga com o CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

O PowerShell é um pouco mais complicado do que o CLI do Azure. Siga estas etapas conceituais para criar uma regra:

1. Obtenha o balanceador de carga do Azure.
2. Crie uma regra.
3. Adicione a regra ao balanceador de carga.
4. Atualize o balanceador de carga.

>[!NOTE]
>Se você precisar determinar o nome do balanceador de carga, use este comando para obter rapidamente uma lista de todos os balanceadores de carga e grupos de recursos associados.
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

Em relação `New-AzLoadBalancerRuleConfig` ao comando, `-FrontendPort` o representa a porta que o balanceador de carga expõe para conexões `-BackendPort` externas e representa a porta que o aplicativo do Service Fabric está ouvindo.

>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com o PowerShell, consulte [criar um balanceador de carga com o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [rede em Service Fabric](service-fabric-patterns-networking.md). Rvice-Fabric-Patterns-Networking.MD).