---
title: Pontos finais de nó de configuração na piscina Azure Batch
description: Como configurar ou desativar o acesso às portas SSH ou RDP em nós computativos numa piscina do Azure Batch.
ms.topic: how-to
ms.date: 02/13/2018
ms.openlocfilehash: 1713637a9aba937525e64e1c4146589fca443461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83780295"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configure ou desative o acesso remoto aos nóns computacional numa piscina do Azure Batch

Por predefinição, o Batch permite que um [utilizador de nó](/rest/api/batchservice/computenode/adduser) com conectividade de rede se conecte externamente a um nó de computação num pool de Lote. Por exemplo, um utilizador pode ligar-se por Remote Desktop (RDP) na porta 3389 a um nó de computação numa piscina do Windows. Da mesma forma, por padrão, um utilizador pode ligar-se por Secure Shell (SSH) na porta 22 a um nó computacional numa piscina Linux. 

No seu ambiente, poderá ser necessário restringir ou desativar estas definições de acesso externo predefinido. Pode modificar estas definições utilizando as APIs do lote para definir a propriedade [PoolEndpointConfiguration.](/rest/api/batchservice/pool/add#poolendpointconfiguration) 

## <a name="about-the-pool-endpoint-configuration"></a>Sobre a configuração do ponto final da piscina
A configuração do ponto final consiste em uma ou mais piscinas de tradução de endereços de [rede (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) de portas frontend. (Não confunda uma piscina NAT com o lote de nós computacional.) Configurar cada piscina NAT para anular as definições de ligação predefinidos nos nós de cálculo da piscina. 

Cada configuração da piscina NAT inclui uma ou mais [regras do grupo de segurança de rede (NSG).](/rest/api/batchservice/pool/add#networksecuritygrouprule) Cada regra NSG permite ou nega determinado tráfego de rede para o ponto final. Pode optar por permitir ou negar todo o tráfego, tráfego identificado por uma etiqueta de [serviço](../virtual-network/security-overview.md#service-tags) (como "Internet"), ou tráfego a partir de endereços IP específicos ou sub-redes.

### <a name="considerations"></a>Considerações
* A configuração do ponto final da piscina faz parte da [configuração](/rest/api/batchservice/pool/add#networkconfiguration)de rede da piscina. A configuração da rede pode opcionalmente incluir configurações para juntar a piscina a uma [rede virtual Azure.](batch-virtual-network.md) Se configurar o pool numa rede virtual, pode criar regras NSG que utilizam definições de endereços na rede virtual.
* Você pode configurar várias regras NSG quando configurar uma piscina NAT. As regras são verificadas na ordem de prioridade. Quando for aplicada uma regra, não são testadas mais regras para correspondência.


## <a name="example-deny-all-rdp-traffic"></a>Exemplo: Negar todo o tráfego RDP

O seguinte corte C# mostra como configurar o ponto final RDP em nós de computação numa piscina do Windows para negar todo o tráfego de rede. O ponto final utiliza uma piscina frontal de portas na gama *60000 - 60099*. 

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

O seguinte snippet Python mostra como configurar o ponto final SSH em nós computativos em uma piscina Linux para negar todo o tráfego de internet. O ponto final utiliza uma piscina frontal de portas na gama *4000 - 4100*. 

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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemplo: Permitir o tráfego rdp a partir de um endereço IP específico

O seguinte corte C# mostra como configurar o ponto final RDP em nós computacional numa piscina windows para permitir o acesso rdp apenas a partir do endereço IP *198.51.100.7*. A segunda regra NSG nega o tráfego que não corresponde ao endereço IP.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemplo: Permitir o tráfego SSH a partir de uma sub-rede específica

O seguinte snippet Python mostra como configurar o ponto final SSH em nós computativos numa piscina Linux para permitir o acesso apenas a partir da sub-rede *192.168.1.0/24*. A segunda regra NSG nega o tráfego que não corresponde à sub-rede.

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

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Para obter mais informações sobre as regras da NSG em Azure, consulte [o tráfego da rede Filter com grupos de segurança de rede.](../virtual-network/security-overview.md)
