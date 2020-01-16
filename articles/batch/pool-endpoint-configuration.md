---
title: Configurar pontos de extremidade de nó no pool do lote do Azure | Microsoft Docs
description: Como configurar ou desabilitar o acesso a portas SSH ou RDP em nós de computação em um pool do lote do Azure.
services: batch
author: ju-shim
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: jushiman
ms.openlocfilehash: 1ac4c7647125cd6164235e98a4a828f6b072cbee
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029460"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurar ou desabilitar o acesso remoto para nós de computação em um pool do lote do Azure

Por padrão, o lote permite que um [usuário do nó](/rest/api/batchservice/computenode/adduser) com conectividade de rede se conecte externamente a um nó de computação em um pool do lote. Por exemplo, um usuário pode se conectar por Área de Trabalho Remota (RDP) na porta 3389 a um nó de computação em um pool do Windows. Da mesma forma, por padrão, um usuário pode se conectar por meio de Secure Shell (SSH) na porta 22 para um nó de computação em um pool do Linux. 

Em seu ambiente, talvez seja necessário restringir ou desabilitar essas configurações de acesso externo padrão. Você pode modificar essas configurações usando as APIs do lote para definir a propriedade [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) . 

## <a name="about-the-pool-endpoint-configuration"></a>Sobre a configuração do ponto de extremidade do pool
A configuração do ponto de extremidade consiste em um ou mais [pools de NAT (conversão de endereços de rede)](/rest/api/batchservice/pool/add#inboundnatpool) de portas de front-end. (Não confunda um pool de NAT com o pool do lote de nós de computação.) Você configura cada pool NAT para substituir as configurações de conexão padrão nos nós de computação do pool. 

Cada configuração de pool de NAT inclui uma ou mais [regras de NSG (grupo de segurança de rede)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Cada regra NSG permite ou nega determinado tráfego de rede para o ponto de extremidade. Você pode optar por permitir ou negar todo o tráfego, o tráfego identificado por uma [marca de serviço](../virtual-network/security-overview.md#service-tags) (como "Internet") ou o tráfego de endereços IP ou sub-redes específicos.

### <a name="considerations"></a>Considerações
* A configuração do ponto de extremidade do pool faz parte da [configuração de rede](/rest/api/batchservice/pool/add#networkconfiguration)do pool. A configuração de rede pode, opcionalmente, incluir configurações para ingressar o pool em uma [rede virtual do Azure](batch-virtual-network.md). Se você configurar o pool em uma rede virtual, poderá criar regras NSG que usam configurações de endereço na rede virtual.
* Você pode configurar várias regras NSG ao configurar um pool de NAT. As regras são verificadas na ordem de prioridade. Quando for aplicada uma regra, não são testadas mais regras para correspondência.


## <a name="example-deny-all-rdp-traffic"></a>Exemplo: negar todo o tráfego RDP

O trecho C# a seguir mostra como configurar o ponto de extremidade RDP em nós de computação em um pool do Windows para negar todo o tráfego de rede. O ponto de extremidade usa um pool de front-end de portas no intervalo de *60000 a 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exemplo: negar todo o tráfego SSH da Internet

O seguinte trecho de código do Python mostra como configurar o ponto de extremidade SSH em nós de computação em um pool do Linux para negar todo o tráfego da Internet. O ponto de extremidade usa um pool de front-end de portas no intervalo de *4000 a 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemplo: permitir o tráfego RDP de um endereço IP específico

O trecho C# a seguir mostra como configurar o ponto de extremidade RDP em nós de computação em um pool do Windows para permitir o acesso RDP somente do endereço IP *198.51.100.7*. A segunda regra NSG nega o tráfego que não corresponde ao endereço IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemplo: permitir o tráfego SSH de uma sub-rede específica

O seguinte trecho de código do Python mostra como configurar o ponto de extremidade SSH em nós de computação em um pool do Linux para permitir o acesso somente da sub-rede *192.168.1.0/24*. A segunda regra NSG nega o tráfego que não corresponde à sub-rede.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre regras NSG no Azure, consulte [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md).

- Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).

