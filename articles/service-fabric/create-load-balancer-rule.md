---
title: Criar uma regra do balançador de carga Azure para um cluster
description: Configure um Balançador de Carga Azure para abrir portas para o seu cluster de tecido de serviço Azure.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 7e09c7b0b3e2bfa5a5ff834e243f5098cbbd947b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319888"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abrir portas de um cluster do Service Fabric

O equilibrador de carga implantado com o seu cluster Azure Service Fabric direciona o tráfego para a sua aplicação em execução num nó. Se alterar a sua aplicação para utilizar uma porta diferente, tem de expor essa porta (ou encaminhar uma porta diferente) no Balançador de Carga Azure.

Quando implementou o seu cluster de Tecido de Serviço para a Azure, foi automaticamente criado um equilibrador de carga para si. Se não tiver um equilibrador de carga, consulte [configurar um equilibrador de carga virado para a Internet](../load-balancer/quickstart-load-balancer-standard-public-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Tecido de serviço configurar

A sua aplicação Service Fabric **ServiceManifest.xml** ficheiro config define os pontos finais que a sua aplicação espera utilizar. Depois de atualizado o ficheiro config para definir um ponto final, o balançador de carga deve ser atualizado para expor essa (ou outra) porta. Para obter mais informações sobre como criar o ponto final do tecido de serviço, consulte [Configurar um Ponto final](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do Balanceador de Carga abre uma porta virada para a Internet e encaminha o tráfego para a porta do nó interno utilizada pela sua aplicação. Se não tiver um equilibrador de carga, consulte [configurar um equilibrador de carga virado para a Internet](../load-balancer/quickstart-load-balancer-standard-public-portal.md).

Para criar uma regra do Balanceador de Carga, é necessário recolher as seguintes informações:

- Nome do equilibrador de carga.
- Grupo de recursos do balanceador de carga e aglomerado de tecido de serviço.
- Porto externo.
- Porto interno.

## <a name="azure-cli"></a>CLI do Azure
Basta um único comando para criar uma regra do balanceador de carga com o **CLI Azure**. Só precisa de saber o nome do equilibrador de carga e do grupo de recursos para criar uma nova regra.

>[!NOTE]
>Se precisar determinar o nome do equilibrador de carga, utilize este comando para obter rapidamente uma lista de todos os equilibradores de carga e os grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando Azure CLI tem alguns parâmetros descritos na tabela seguinte:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A aplicação "Service Fabric" da porta. |
| `--frontend-port` | A porta que o equilibrador de carga expõe para ligações externas. |
| `-lb-name` | O nome do equilibrador de carga para mudar. |
| `-g`       | O grupo de recursos que tem tanto o balanceador de carga como o cluster de tecido de serviço. |
| `-n`       | O nome desejado da regra. |


>[!NOTE]
>Para obter mais informações sobre como criar um equilibrador de carga com o Azure CLI, consulte [Criar um equilibrador de carga com o Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell é um pouco mais complicado que o Azure CLI. Siga estes passos conceptuais para criar uma regra:

1. Pegue o equilibrador de carga de Azure.
2. Criar uma regra.
3. Adicione a regra ao equilibrador de carga.
4. Atualize o balançador de carga.

>[!NOTE]
>Se precisar determinar o nome do equilibrador de carga, utilize este comando para obter rapidamente uma lista de todos os equilibradores de carga e grupos de recursos associados.
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

No que diz respeito ao `New-AzLoadBalancerRuleConfig` comando, `-FrontendPort` representa a porta que o equilibrador de carga expõe para ligações externas, e `-BackendPort` representa a porta que a aplicação de tecido de serviço está a ouvir.

>[!NOTE]
>Para obter mais informações sobre como criar um equilibrador de carga com PowerShell, consulte [Criar um equilibrador de carga com PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [networking em Service Fabric.](service-fabric-patterns-networking.md)