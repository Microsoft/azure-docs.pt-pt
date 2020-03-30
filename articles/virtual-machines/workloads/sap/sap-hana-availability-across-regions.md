---
title: Disponibilidade do SAP HANA nas regiões de Azure [ Microsoft Docs
description: Uma visão geral das considerações de disponibilidade ao executar SAP HANA em VMs Azure em várias regiões de Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078857"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidade do SAP HANA nas regiões de Azure

Este artigo descreve cenários relacionados com a disponibilidade do SAP HANA em diferentes regiões do Azure. Devido à distância entre as regiões de Azure, a criação da disponibilidade do SAP HANA em várias regiões de Azure envolve considerações especiais.

## <a name="why-deploy-across-multiple-azure-regions"></a>Por que implantar em várias regiões azure

As regiões de Azure são frequentemente separadas por grandes distâncias. Dependendo da região geopolítica, a distância entre as regiões de Azure pode ser de centenas de milhas, ou mesmo de milhares de milhas, como nos Estados Unidos. Devido à distância, o tráfego de rede entre os ativos que são implantados em duas regiões diferentes do Azure experimentam uma latência significativa de ida e volta em rede. A latência é significativa o suficiente para excluir a troca de dados sincronizada entre duas instâncias SAP HANA sob cargas de trabalho típicas do SAP. 

Por outro lado, as organizações têm frequentemente um requisito de distância entre a localização do centro de dados primário e um centro de dados secundário. Um requisito de distância ajuda a fornecer disponibilidade se uma catástrofe natural ocorrer em uma localização geográfica mais ampla. Exemplos incluem os furacões que atingiram as Caraíbas e a Florida em setembro e outubro de 2017. A sua organização pode ter pelo menos um requisito mínimo de distância. Para a maioria dos clientes Azure, uma definição de distância mínima requer que você desenhe para disponibilidade em [todas as regiões de Azure.](https://azure.microsoft.com/regions/) Como a distância entre duas regiões azure é muito grande para usar o modo de replicação sincronizada HANA, os requisitos de RTO e RPO podem forçá-lo a implementar configurações de disponibilidade numa região, e depois complementar com implementações adicionais em um segundo região.

Outro aspeto a ter em conta neste cenário é o failover e o redirecionamento do cliente. O pressuposto é que uma falha entre os casos do SAP HANA em duas regiões azure diferentes é sempre uma falha manual. Como o modo de replicação da replicação do sistema SAP HANA está definido como assíncrono, há um potencial que os dados cometidos na instância primária de HANA ainda não chegaram à instância secundária de HANA. Portanto, a falha automática não é uma opção para configurações onde a replicação é assíncrona. Mesmo com uma falha controlada manualmente, como num exercício de failover, é necessário tomar medidas para garantir que todos os dados comprometidos do lado primário chegaram à instância secundária antes de se deslocar manualmente para a outra região do Azure.
 
A Rede Virtual Azure utiliza uma gama de endereços IP diferente. Os endereços IP são implantados na segunda região de Azure. Por isso, ou precisa de alterar a configuração do cliente SAP HANA, ou de preferência, precisa de criar passos para alterar a resolução de nomes. Desta forma, os clientes são redirecionados para o endereço IP do novo site secundário. Para mais informações, consulte o artigo SAP [recuperação de ligação ao cliente após aquisição](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidade simples entre duas regiões azure

Você pode optar por não colocar qualquer configuração de disponibilidade em lugar dentro de uma única região, mas ainda tem a exigência de ter a carga de trabalho servida se um desastre ocorrer. Casos típicos para tais cenários são sistemas de não produção. Embora ter o sistema para baixo durante meio dia ou mesmo um dia seja sustentável, não pode permitir que o sistema esteja indisponível por 48 horas ou mais. Para tornar a configuração menos dispendiosa, gerenum outro sistema que seja ainda menos importante no VM. O outro sistema funciona como um destino. Também pode dimensionar o VM na região secundária para ser menor, e optar por não pré-carregar os dados. Como a falha é manual e implica muitos mais passos para falhar sobre a pilha completa de aplicações, o tempo adicional para desligar o VM, redimensioná-lo e, em seguida, reiniciar o VM é aceitável.

Se estiver a usar o cenário de partilhar o alvo DR com um sistema QA num VM, tem de ter em conta estas considerações:

- Existem dois modos de [funcionamento](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) com delta_datashipping e logreplay, que estão disponíveis para tal cenário
- Ambos os modos de funcionamento têm diferentes requisitos de memória sem dados de pré-carregamento
- Delta_datashipping pode exigir uma memória drasticamente menor sem a opção de pré-carga do que a repetição de logreplay poderia exigir. Ver capítulo 4.3 do documento SAP Como Executar Replicação do [Sistema para SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- O requisito de memória do modo de funcionamento da reprodução de logreplay sem pré-carga não é determinístico e depende das estruturas de loja de colunas carregadas. Em casos extremos, pode exigir 50% da memória da instância primária. A memória do modo de funcionamento de reprodução de logreplay é independente sobre se optou por ter ou não os dados pré-carregados.


![Diagrama de duas VMs sobre duas regiões](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Nesta configuração, não é possível fornecer um RPO=0 porque o seu modo de replicação do sistema HANA é assíncrono. Se precisar de fornecer um RPO=0, esta configuração não é a configuração de escolha.

Uma pequena alteração que pode fazer na configuração pode ser configurar dados como pré-carregamento. No entanto, dada a natureza manual da falha e o facto de as camadas de aplicação também precisarem de se deslocar para a segunda região, pode não fazer sentido pré-carregar dados. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combine disponibilidade numa região e em todas as regiões 

Uma combinação de disponibilidade dentro e em todas as regiões pode ser impulsionada por estes fatores:

- Uma exigência de RPO=0 dentro de uma região de Azure.
- A organização não está disposta ou capaz de ter operações globais afetadas por uma grande catástrofe natural que afeta uma região maior. Foi o caso de alguns furacões que atingiram as Caraíbas nos últimos anos.
- Regulamentos que exigem distâncias entre locais primários e secundários que estão claramente para além do que as zonas de disponibilidade do Azure podem fornecer.

Nestes casos, pode configurar o que o SAP chama de uma configuração de [replicação multimais do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) utilizando a replicação do sistema HANA. A arquitetura seria como:

![Diagrama de três VMs em duas regiões](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

O SAP introduziu [a replicação do sistema multi-alvo](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) com HANA 2.0 SPS3. A replicação do sistema multi-alvo traz algumas vantagens em cenários de atualização. Por exemplo, o sítio DR (Região 2) não é afetado quando o local secundário de HA está em baixo para manutenção ou atualizações. Pode saber mais sobre a replicação do sistema multi-alvo DA HANA [aqui](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Uma possível arquitetura com replicação multi-alvo seria como:

![Diagrama de três VMs sobre duas regiões milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Se a organização tiver requisitos para uma prontidão de elevada disponibilidade na segunda região (DR) Azure, então a arquitetura seria como:

![Diagrama de três VMs sobre duas regiões milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Utilizando a repetição de logreplay como modo de funcionamento, esta configuração fornece um RPO=0, com RTO baixo, dentro da região primária. A configuração também fornece RPO decente se uma mudança para a segunda região estiver envolvida. Os tempos de RTO na segunda região dependem da pré-carga dos dados. Muitos clientes usam o VM na região secundária para executar um sistema de teste. Nesse caso, os dados não podem ser pré-carregados.

> [!IMPORTANT]
> Os modos de funcionamento entre os diferentes níveis têm de ser homogéneos. **Não é possível** utilizar o logreply como modo de funcionamento entre o nível 1 e o nível 2 e delta_datashipping para fornecer o nível 3. Só é possível escolher o modo de funcionamento de um ou outro modo de funcionamento que tem de ser consistente para todos os níveis. Uma vez que delta_datashipping não é adequado para lhe dar um RPO=0, o único modo de funcionamento razoável para uma configuração de vários níveis permanece a repetição de logreplay. Para mais detalhes sobre os modos de funcionamento e algumas restrições, consulte os modos de funcionamento do artigo [SAP HANA para a replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Passos seguintes

Para obter orientações passo a passo sobre a configuração destas configurações em Azure, consulte:

- [Configurar a replicação do sistema SAP HANA em VMs Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA utilizando replicação do sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
