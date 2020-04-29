---
title: Criar uma regra de Equilíbrio de Carga Azure para um cluster
description: Configure um Balancer de Carga Azure para abrir portas para o seu cluster Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025389"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abrir portas de um cluster do Service Fabric

O equilibrador de carga implantado com o seu cluster Azure Service Fabric direciona o tráfego para a sua aplicação em funcionamento num nó. Se alterar a sua aplicação para utilizar uma porta diferente, tem de expor essa porta (ou encaminhar uma porta diferente) no Equilíbrio de Carga Azure.

Quando implantou o seu cluster De Tecido de Serviço para o Azure, foi criado automaticamente um equilibrador de carga para si. Se não tiver um equilibrador de carga, consulte [Configure um equilibrador de carga virado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Configurar tecido de serviço

O ficheiro de config **ServiceManifesto.xml** do seu serviço de tecido define os pontos finais que a sua aplicação espera utilizar. Depois de atualizado o ficheiro config para definir um ponto final, o equilibrador de carga deve ser atualizado para expor essa (ou uma outra) porta. Para obter mais informações sobre como criar o ponto final do tecido de serviço, consulte [Configurar um Ponto final](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do Balancer de Carga abre uma porta virada para a Internet e encaminha o tráfego para a porta do nó interno utilizada pela sua aplicação. Se não tiver um equilibrador de carga, consulte [Configure um equilibrador de carga virado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md).

Para criar uma regra do Balancer de Carga, é necessário recolher as seguintes informações:

- Nome do equilibrador de carga.
- Grupo de recursos do balanceor de carga e cluster de tecido de serviço.
- Porto externo.
- Porto interno.

## <a name="azure-cli"></a>CLI do Azure
É preciso apenas um único comando para criar uma regra de equilíbrio de carga com o **Azure CLI**. Basta saber o nome do equilibrador de carga e do grupo de recursos para criar uma nova regra.

>[!NOTE]
>Se precisar determinar o nome do equilibrista de carga, use este comando para obter rapidamente uma lista de todos os equilibradores de carga e dos grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando Azure CLI tem alguns parâmetros descritos na tabela seguinte:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A porta que a aplicação Service Fabric está a ouvir. |
| `--frontend-port` | A porta que o equilibrador de carga expõe para ligações externas. |
| `-lb-name` | O nome do equilibrador de carga para mudar. |
| `-g`       | O grupo de recursos que tem tanto o balanceador de carga como o cluster Service Fabric. |
| `-n`       | O nome desejado da regra. |


>[!NOTE]
>Para obter mais informações sobre como criar um equilibrador de carga com o Azure CLI, consulte [Criar um equilibrador de carga com o Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell é um pouco mais complicado que o Azure CLI. Siga estes passos conceptuais para criar uma regra:

1. Pegue o equilibrador de carga do Azure.
2. Criar uma regra.
3. Adicione a regra ao equilibrador de carga.
4. Atualize o equilibrador de carga.

>[!NOTE]
>Se precisar determinar o nome do equilibrista de carga, use este comando para obter rapidamente uma lista de todos os equilibradores de carga e grupos de recursos associados.
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

No que `New-AzLoadBalancerRuleConfig` diz `-FrontendPort` respeito ao comando, o porta que o equilibrador de carga expõe para ligações externas, e o `-BackendPort` que representa a porta que a aplicação de tecido de serviço está a ouvir.

>[!NOTE]
>Para obter mais informações sobre como criar um equilibrador de carga com a PowerShell, consulte Criar um equilibrador de [carga com powerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [networking em Tecido de Serviço.](service-fabric-patterns-networking.md)
