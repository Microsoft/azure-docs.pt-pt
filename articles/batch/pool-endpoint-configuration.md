---
title: Configurar pontos finais de nó no conjunto do Azure Batch | Documentos da Microsoft
description: Como configurar ou desativar o acesso às portas SSH ou RDP em nós de computação num conjunto do Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616862"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurar ou desativar o acesso remoto a nós num conjunto do Azure Batch de computação

Por predefinição, o Batch permite uma [utilizador de nó](/rest/api/batchservice/computenode/adduser) com conectividade de rede para ligar externamente para um nó de computação no conjunto do Batch. Por exemplo, um utilizador pode ligar-se ao RDP (Remote Desktop) na porta 3389 para um nó de computação num conjunto do Windows. Da mesma forma, por predefinição, um utilizador pode ligar-se por Secure Shell (SSH) na porta 22 para um nó de computação no conjunto do Linux. 

No seu ambiente, poderá ter de restringir ou desativar estas predefinições de acesso externo. Pode modificar essas configurações usando as APIs do Batch para definir o [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) propriedade. 

## <a name="about-the-pool-endpoint-configuration"></a>Sobre a configuração de ponto final do conjunto
A configuração de ponto final consiste num ou mais [conjuntos de tradução (NAT) de endereços de rede](/rest/api/batchservice/pool/add#inboundnatpool) de portas de front-end. (Não confunda um conjunto NAT com o conjunto de nós de computação do Batch.) Configure cada conjunto NAT para substituir as predefinições de ligação em nós de computação do conjunto. 

Cada configuração de conjuntos NAT inclui um ou mais [regras de grupo (NSG) de segurança de rede](/rest/api/batchservice/pool/add#networksecuritygrouprule). Cada regra NSG permite ou nega determinado tráfego de rede para o ponto final. Pode optar por permitir ou negar todo o tráfego, tráfego identificado por uma [etiqueta de serviço](../virtual-network/security-overview.md#service-tags) (por exemplo, "Internet"), ou tráfego a partir de sub-redes ou endereços IP específicos.

### <a name="considerations"></a>Considerações
* A configuração de ponto final do conjunto faz parte do conjunto [configuração de rede](/rest/api/batchservice/pool/add#NetworkConfiguration). A configuração de rede, opcionalmente, pode incluir configurações para ingressar em conjunto para um [rede virtual do Azure](batch-virtual-network.md). Se configurar o conjunto numa rede virtual, pode criar as regras do NSG que utilizam as definições de endereço na rede virtual.
* Pode configurar várias regras NSG quando configurar um conjunto NAT. As regras são verificadas por ordem de prioridade. Quando for aplicada uma regra, não são testadas mais regras para correspondência.


## <a name="example-deny-all-rdp-traffic"></a>Exemplo: Negar todo o tráfego RDP

O seguinte C# trecho de código mostra como configurar o ponto de final RDP em nós de computação num conjunto do Windows para negar todo o tráfego de rede. O ponto de extremidade usa um conjunto de front-end de portas no intervalo *60000 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exemplo: Negar todo o tráfego SSH da internet

O fragmento de Python seguinte mostra como configurar o ponto final SSH em nós de computação de um conjunto do Linux para negar todo o tráfego de internet. O ponto de extremidade usa um conjunto de front-end de portas no intervalo *4000 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemplo: Permitir o tráfego RDP de um endereço IP específico

O seguinte C# trecho de código mostra como configurar o ponto de final RDP em nós de computação num conjunto do Windows para permitir o acesso RDP apenas a partir do endereço IP *198.51.100.7*. A segunda regra NSG recusa o tráfego que não coincide com o endereço IP.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemplo: Permitir o tráfego SSH da sub-rede específica

O fragmento de Python seguinte mostra como configurar o ponto final SSH em nós de computação de um conjunto do Linux para permitir o acesso apenas a partir da sub-rede *192.168.1.0/24*. A segunda regra NSG recusa o tráfego que não corresponde à sub-rede.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
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

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre regras NSG no Azure, consulte [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md).

- Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).

