---
title: Configure pontos finais do nó na piscina do Lote Azure [ Microsoft Docs
description: Como configurar ou desativar o acesso às portas SSH ou RDP em nódeos de computação numa piscina de Lote Azure.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017229"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configure ou desative o acesso remoto aos nódosos de computação numa piscina de Lote Azure

Por padrão, o Batch permite que um utilizador de [nó](/rest/api/batchservice/computenode/adduser) com conectividade de rede se conectem externamente a um nó computacional numa piscina de Lote. Por exemplo, um utilizador pode ligar-se por Remote Desktop (RDP) na porta 3389 a um nó de computação numa piscina do Windows. Da mesma forma, por padrão, um utilizador pode ligar-se por Secure Shell (SSH) na porta 22 a um nó de cálculo numa piscina de Linux. 

No seu ambiente, poderá ser necessário restringir ou desativar estas definições de acesso externo predefinidas. Pode modificar estas definições utilizando as APIs do Lote para definir a propriedade [PoolEndpointConfiguration.](/rest/api/batchservice/pool/add#poolendpointconfiguration) 

## <a name="about-the-pool-endpoint-configuration"></a>Sobre a configuração do ponto final da piscina
A configuração do ponto final consiste em um ou mais conjuntos de tradução de endereços de [rede (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) de portas frontendas. (Não confunda uma piscina NAT com o conjunto de nós de computação.) Configura cada piscina NAT para anular as definições de ligação padrão nos nós de computação da piscina. 

Cada configuração de piscina NAT inclui uma ou mais regras do grupo de segurança de [rede (NSG).](/rest/api/batchservice/pool/add#networksecuritygrouprule) Cada regra NSG permite ou nega determinado tráfego de rede até ao ponto final. Pode optar por permitir ou negar todo o tráfego, tráfego identificado por uma etiqueta de [serviço](../virtual-network/security-overview.md#service-tags) (como "Internet"), ou tráfego a partir de endereços IP ou subredes específicos.

### <a name="considerations"></a>Considerações
* A configuração do ponto final da piscina faz parte da [configuração](/rest/api/batchservice/pool/add#networkconfiguration)da rede da piscina. A configuração da rede pode incluir opcionalmente configurações para se juntar à piscina a uma [rede virtual Azure.](batch-virtual-network.md) Se configurar a piscina numa rede virtual, pode criar regras NSG que utilizam definições de endereços na rede virtual.
* Pode configurar várias regras de NSG quando configurar uma piscina NAT. As regras são verificadas na ordem de prioridade. Quando for aplicada uma regra, não são testadas mais regras para correspondência.


## <a name="example-deny-all-rdp-traffic"></a>Exemplo: Negar todo o tráfego de RDP

O seguinte c# snippet mostra como configurar o ponto final rdp em nós de computação numa piscina do Windows para negar todo o tráfego da rede. O ponto final utiliza uma piscina frontal de portas na gama *60000 - 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exemplo: Negar todo o tráfego sSH da internet

O seguinte snippet Python mostra como configurar o ponto final do SSH em nós de computação numa piscina linux para negar todo o tráfego de internet. O ponto final utiliza uma piscina frontal de portas na gama *4000 - 4100*. 

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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemplo: Permitir tráfego rdp a partir de um endereço IP específico

O seguinte c# snippet mostra como configurar o ponto final rdp em nóes de computação numa piscina windows para permitir o acesso rdp apenas a partir do endereço IP *198.51.100.7*. A segunda regra do NSG nega o tráfego que não corresponde ao endereço IP.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemplo: Permitir o tráfego de SSH a partir de uma subrede específica

O seguinte snippet Python mostra como configurar o ponto final sSH em nós de computação numa piscina linux para permitir o acesso apenas a partir da subnet *192.168.1.0/24*. A segunda regra da NSG nega o tráfego que não corresponde à sub-rede.

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

- Para obter mais informações sobre as regras da NSG em Azure, consulte o tráfego da [rede Filter com grupos](../virtual-network/security-overview.md)de segurança da rede .

- Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).

