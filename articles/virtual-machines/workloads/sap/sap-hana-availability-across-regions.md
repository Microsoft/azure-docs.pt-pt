---
title: Disponibilidade do SAP HANA em regiões do Azure | Documentos da Microsoft
description: Uma descrição geral das considerações de disponibilidade quando a execução do SAP HANA em VMs do Azure em várias regiões do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95ada2cb146bdbc972afee883a1d174c95aa67d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60650305"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidade do SAP HANA em regiões do Azure

Este artigo descreve os cenários relacionados com a disponibilidade do SAP HANA em diferentes regiões do Azure. Devido a distância entre regiões do Azure, a configuração de disponibilidade do SAP HANA em várias regiões do Azure envolve considerações especiais.

## <a name="why-deploy-across-multiple-azure-regions"></a>Por que motivo implementar em várias regiões do Azure

Regiões do Azure, muitas vezes, são separadas por grandes distâncias. Consoante a região geopolítica, a distância entre regiões do Azure pode ser centenas de quilómetros de distância, ou até mesmo léguas, como nos Estados Unidos. Devido a distância, o tráfego de rede entre os recursos que são implementadas em duas regiões do Azure diferentes experiência latência de ida e volta de rede significativo. A latência é significativa o suficiente para excluir a troca de dados síncrona entre duas instâncias do SAP HANA em cargas de trabalho SAP típicas. 

Por outro lado, as organizações têm, muitas vezes, um requisito de distância entre a localização do datacenter primário e um datacenter secundário. Um requisito de distância ajuda a fornecer disponibilidade se um desastre natural ocorrer numa localização geográfica mais amplo. Os exemplos incluem os furacões que atingem o Caribe e Florida em Setembro e de Outubro de 2017. Sua organização pode ter, pelo menos, um requisito de distância mínima. Para clientes de mais do Azure, uma definição de distância mínima requer a criação de disponibilidade no [regiões do Azure](https://azure.microsoft.com/regions/). Uma vez a distância entre duas regiões do Azure é demasiado grande para utilizar o modo de replicação síncrona do HANA, requisitos de RTO e RPO podem forçá-lo a implantar configurações de disponibilidade numa única região e, em seguida, complementar com implementações adicionais num segundo região.

Outro aspecto a considerar neste cenário é a ativação pós-falha e redirecionar o cliente. A suposição é que uma ativação pós-falha entre instâncias do SAP HANA em duas diferentes regiões do Azure sempre é uma ativação pós-falha manual. Porque o modo de replicação de replicação do sistema de SAP HANA está definido como assíncrono, é possível que dados na instância HANA primária ainda não ainda chegaram até a segunda instância do HANA. Por conseguinte, a ativação pós-falha automática não é uma opção para configurações em que a replicação é assíncrona. Mesmo com ativação pós-falha manualmente controlada, tal como um exercício de ativação pós-falha, terá de tomar medidas para garantir que todos os dados confirmados no lado do primário chegaram até a instância secundária antes de manualmente mudar para outro região do Azure.
 
Rede Virtual do Azure utiliza um intervalo de endereços IP diferente. Os endereços IP são implementados na segunda região do Azure. Portanto, qualquer um terá de alterar a configuração de cliente do SAP HANA ou, de preferência, precisa criar passos para alterar a resolução do nome. Dessa forma, os clientes são redirecionados para o endereço IP do servidor do site secundário de novo. Para obter mais informações, consulte o artigo SAP [recuperação de ligação de cliente após a obtenção de controlo](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidade Simple entre duas regiões do Azure

Pode optar por não implementar qualquer configuração de disponibilidade numa única região, mas continua com a demanda para que a carga de trabalho servida se ocorrer um desastre. Casos típicos para estes cenários são os sistemas de não produção. Apesar de ter o sistema para baixo para metade de um dia ou até mesmo um dia for sustentável, não é possível permitir que o sistema fique indisponível para 48 horas ou mais. Para fazer com que a configuração mais econômica, execute outro sistema que é até mesmo menos importante na VM. O outro sistema funciona como um destino. Também pode dimensionar a VM na região secundária para ser mais pequena e optar por não pré-carregar os dados. Como a ativação pós-falha é manual e envolve muitas etapas para a ativação pós-falha a pilha de aplicação completa, o tempo adicional para encerrar a VM, redimensioná-la e, em seguida, reinicie a VM é aceitável.

Se estiver a utilizar o cenário de compartilhamento de destino de DR com um sistema de controle de qualidade numa VM, tem de ter em conta estas considerações:

- Existem duas [modos de operação](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) com delta_datashipping e logreplay, que estão disponível para um cenário desse tipo
- Ambos os modos de operação têm requisitos diferentes de memória sem pré-carregamento de dados
- Delta_datashipping exijam significativamente menos memória sem a opção de pré-carregamento que logreplay poderia exigir. Consulte o capítulo 4.3 do documento SAP [como para efetuar a replicação do sistema para o SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- O requisito de memória do modo de operação logreplay sem pré-carregamento não é determinístico e depende das estruturas de columnstore carregá-lo. Em casos extremos, poderá necessitar de 50% da memória da instância primária. A memória para o modo de operação logreplay é independente em se optou por ter os dados pré-carregada ou não.


![Diagrama de duas VMs através de duas regiões](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Nesta configuração, não é possível fornecer um RPO = 0, como o modo de replicação de sistema HANA é assíncrono. Se tiver de fornecer um RPO = 0, esta configuração não é a configuração de preferência.

Pode ser uma pequena alteração que pode fazer na configuração de configurar os dados como o pré-carregamento. No entanto, devido à natureza manual de ativação pós-falha e o fato de que camadas da aplicação também precisam de mover para a segunda região, poderá não fazer sentido para pré-carregar dados. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinar disponibilidade numa região e em várias regiões 

Uma combinação de disponibilidade nas regiões pode ser orientada por esses fatores:

- Um requisito de RPO = 0 dentro de uma região do Azure.
- A organização não é disposto ou ter operações globais afetadas por uma catástrofe natural principais que afete uma região maior. Esse era o caso para alguns furacões que atingem o Caribe nos últimos anos.
- Regulamentações que a pedido distâncias entre sites primários e secundários que estão claramente além de disponibilidade do Azure que podem fornecer zonas.

Nestes casos, pode configurar as chamadas SAP uma [a configuração de replicação de várias camadas do sistema de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) utilizando a replicação de sistema do HANA. A arquitetura teria o seguinte aspeto:

![Diagrama de três VMs através de duas regiões](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introduzida [replicação de sistema de destino Multi](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) com HANA 2.0 SPS3. Replicação do sistema de destino multi traz algumas vantagens em cenários de atualização. Por exemplo, o site de DR (região 2) não é afetado quando o site secundário do HA está inativo para manutenção ou atualizações. Pode encontrar mais informações sobre a replicação do sistema de destino multi HANA [aqui](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Arquitetura possível com a replicação de destino multi teria o seguinte aspeto:

![Diagrama de três VMs através de duas regiões milti-destino](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Se a organização tem requisitos para a preparação de elevada disponibilidade no second(DR) região do Azure, em seguida, a arquitetura teria o seguinte aspeto:

![Diagrama de três VMs através de duas regiões milti-destino](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Utilizar logreplay como modo de operação, esta configuração fornece um RPO = 0, com RTO baixo, dentro da região primária. A configuração também fornece o RPO decente, se uma mudança para a segunda região estiver envolvida. Os tempos de RTO da segunda região são dependentes do se os dados é pré-carregado. Muitos clientes utilizam a VM na região secundária para executar um sistema de teste. Nesse caso de utilização, os dados não podem ser pré-carregado.

> [!IMPORTANT]
> Os modos de operação entre as diferentes camadas precisam homogeneidade entre elas. **Não é possível** utilizar logreply como modo de operação entre a camada 1 e de camada 2 e delta_datashipping fornecer de camada 3. Só pode escolher um ou o modo de operação que precisa para ser consistente para todos os escalões. Uma vez que não é adequado para que tenha um RPO delta_datashipping = 0, o modo de operação apenas razoável para uma configuração de várias camada permanece logreplay. Para obter detalhes sobre os modos de operação e algumas restrições, consulte o artigo SAP [modos de operação de replicação do sistema de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Passos Seguintes

Para obter orientações passo a passo sobre como configurar estas configurações no Azure, consulte:

- [Configurar a replicação de sistema de SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Elevada disponibilidade para SAP HANA através de replicação de sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
