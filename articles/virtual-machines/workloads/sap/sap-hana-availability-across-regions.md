---
title: Disponibilidade de SAP HANA nas regiões do Azure | Microsoft Docs
description: Uma visão geral das considerações de disponibilidade ao executar SAP HANA em VMs do Azure em várias regiões do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078857"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidade de SAP HANA nas regiões do Azure

Este artigo descreve os cenários relacionados à disponibilidade de SAP HANA em diferentes regiões do Azure. Devido à distância entre as regiões do Azure, a configuração da disponibilidade de SAP HANA em várias regiões do Azure envolve considerações especiais.

## <a name="why-deploy-across-multiple-azure-regions"></a>Por que implantar em várias regiões do Azure

As regiões do Azure geralmente são separadas por grandes distâncias. Dependendo da região geopolítica, a distância entre as regiões do Azure pode ser centenas de milhas ou até mesmo milhares de milhas, como no Estados Unidos. Por causa da distância, o tráfego de rede entre os ativos implantados em duas regiões diferentes do Azure experimenta uma latência significativa de ida e volta da rede. A latência é significativa o suficiente para excluir a troca de dados síncrona entre duas instâncias de SAP HANA em cargas de trabalho típicas do SAP. 

Por outro lado, as organizações geralmente têm um requisito de distância entre o local do datacenter primário e um datacenter secundário. Um requisito de distância ajuda a fornecer disponibilidade se um desastre natural ocorrer em uma localização geográfica mais ampla. Os exemplos incluem os furacões que atingiram o Caribe e a Flórida em setembro e de outubro de 2017. Sua organização pode ter pelo menos um requisito de distância mínima. Para a maioria dos clientes do Azure, uma definição de distância mínima exige que você projete para disponibilidade nas [regiões do Azure](https://azure.microsoft.com/regions/). Como a distância entre duas regiões do Azure é muito grande para usar o modo de replicação síncrona do HANA, os requisitos de RTO e RPO podem forçá-lo a implantar configurações de disponibilidade em uma região e, em seguida, complementar com implantações adicionais em um segundo regionais.

Outro aspecto a ser considerado nesse cenário é o failover e o redirecionamento do cliente. A suposição é que um failover entre SAP HANA instâncias em duas regiões diferentes do Azure sempre seja um failover manual. Como o modo de replicação da replicação do sistema SAP HANA é definido como assíncrono, há um potencial de que os dados confirmados na instância do HANA primário ainda não o fizeram na instância do HANA secundário. Portanto, o failover automático não é uma opção para configurações em que a replicação é assíncrona. Mesmo com o failover controlado manualmente, como em um exercício de failover, você precisa tomar medidas para garantir que todos os dados confirmados no lado primário tenham sido transferidos para a instância secundária antes de você passar manualmente para a outra região do Azure.
 
A rede virtual do Azure usa um intervalo de endereços IP diferente. Os endereços IP são implantados na segunda região do Azure. Portanto, você precisa alterar a configuração do cliente SAP HANA ou preferencialmente, você precisa criar etapas para alterar a resolução do nome. Dessa forma, os clientes são redirecionados para o endereço IP do servidor do novo site secundário. Para obter mais informações, consulte o artigo do SAP [recuperação de conexão do cliente após tomada](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidade simples entre duas regiões do Azure

Você pode optar por não colocar nenhuma configuração de disponibilidade em vigor em uma única região, mas ainda terá a demanda de ter a carga de trabalho atendida se ocorrer um desastre. Casos típicos para esses cenários são sistemas que não são de produção. Embora ter o sistema inoperante por meio dia ou até mesmo um dia seja sustentável, não é possível permitir que o sistema fique indisponível por 48 horas ou mais. Para tornar a configuração menos dispendiosa, execute outro sistema que seja ainda menos importante na VM. O outro sistema funciona como um destino. Você também pode dimensionar a VM na região secundária para que ela seja menor e optar por não pré-carregar os dados. Como o failover é manual e envolve muitas outras etapas para fazer failover da pilha completa do aplicativo, o tempo adicional para desligar a VM, redimensioná-la e reiniciar a VM é aceitável.

Se você estiver usando o cenário de compartilhamento do destino de DR com um sistema de p e r em uma VM, você precisa levar em conta essas considerações:

- Há dois [modos de operação](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) com delta_datashipping e logreplay, que estão disponíveis para esse cenário
- Ambos os modos de operação têm requisitos de memória diferentes sem o pré-carregamento de dados
- O Delta_datashipping pode exigir menos memória, sem a opção de pré-carregamento do que logreplay pode exigir. Consulte o capítulo 4,3 do documento do SAP [como executar a replicação do sistema para SAP Hana](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- O requisito de memória do modo de operação logreplay sem Preload não é determinístico e depende das estruturas columnstore carregadas. Em casos extremos, você pode exigir 50% da memória da instância primária. A memória para o modo de operação logreplay é independente de você optar por ter os dados previamente definidos ou não.


![Diagrama de duas VMs em duas regiões](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Nessa configuração, você não pode fornecer um RPO = 0 porque o modo de replicação do sistema do HANA é assíncrono. Se você precisar fornecer um RPO = 0, essa configuração não será a configuração escolhida.

Uma pequena alteração que você pode fazer na configuração pode ser configurar os dados como o pré-carregamento. No entanto, considerando a natureza manual do failover e o fato de que as camadas de aplicativo também precisam ser migradas para a segunda região, talvez não faça sentido pré-carregar os dados. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinar a disponibilidade dentro de uma região e entre regiões 

Uma combinação de disponibilidade dentro e entre regiões pode ser orientada por esses fatores:

- Um requisito de RPO = 0 em uma região do Azure.
- A organização não está disposta nem pode ter operações globais afetadas por uma grande catástrofe natural que afeta uma região maior. Esse foi o caso de alguns furacões que atingiram o Caribe nos últimos anos.
- Regulamentos que demandam distâncias entre sites primários e secundários que estão claramente além do que as zonas de disponibilidade do Azure podem fornecer.

Nesses casos, você pode configurar o que o SAP chama de uma [configuração de replicação de sistema multicamada SAP Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) usando a replicação de sistema do Hana. A arquitetura teria a seguinte aparência:

![Diagrama de três VMs em duas regiões](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

O SAP introduziu a [replicação de sistema de vários destinos](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) com o Hana 2,0 SPS3. A replicação de sistema de vários destinos traz algumas vantagens em cenários de atualização. Por exemplo, o local de DR (região 2) não é afetado quando o site de HA secundário está inoperante para manutenção ou atualizações. Você pode saber mais sobre a replicação de sistema de vários destinos do HANA [aqui](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
A arquitetura possível com replicação de vários destinos seria semelhante a:

![Diagrama de três VMs em duas regiões milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Se a organização tiver requisitos para prontidão de alta disponibilidade na segunda região do Azure (DR), a arquitetura ficaria assim:

![Diagrama de três VMs em duas regiões milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Usando logreplay como modo de operação, essa configuração fornece um RPO = 0, com RTO baixo, dentro da região primária. A configuração também fornece um RPO razoável se uma mudança para a segunda região estiver envolvida. Os tempos de RTO na segunda região dependem se os dados são pré-carregados. Muitos clientes usam a VM na região secundária para executar um sistema de teste. Nesse caso de uso, os dados não podem ser pré-carregados.

> [!IMPORTANT]
> Os modos de operação entre as diferentes camadas precisam ser homogêneos. Você **não pode** usar logreply como modo de operação entre a camada 1 e a camada 2 e delta_datashipping para fornecer a camada 3. Você só pode escolher um ou outro modo de operação que precise ser consistente para todas as camadas. Como delta_datashipping não é adequado para fornecer um RPO = 0, o único modo de operação razoável para essa configuração de várias camadas permanece logreplay. Para obter detalhes sobre os modos de operação e algumas restrições, consulte os modos de operação de artigo do SAP [para replicação de sistema do SAP Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Passos seguintes

Para obter orientações passo a passo sobre como configurar essas configurações no Azure, consulte:

- [Configurar a replicação do sistema SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA usando a replicação do sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
