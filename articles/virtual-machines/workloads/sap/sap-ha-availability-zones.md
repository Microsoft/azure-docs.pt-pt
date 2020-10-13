---
title: Configurações de carga de trabalho SAP com Zonas de Disponibilidade Azure / Microsoft Docs
description: Arquitetura de alta disponibilidade e cenários para SAP NetWeaver usando Zonas de Disponibilidade Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f9fc34a85f8a858adea2161e1734ead589180ea4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978242"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho de SAP com Zonas de Disponibilidade do Azure
[Azure Availability Zones](../../../availability-zones/az-overview.md) é uma das funcionalidades de alta disponibilidade que o Azure fornece. A utilização de Zonas de Disponibilidade melhora a disponibilidade global de cargas de trabalho SAP no Azure. Esta funcionalidade já está disponível em algumas [regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/) No futuro, estará disponível em mais regiões.

Este gráfico mostra a arquitetura básica da alta disponibilidade do SAP:

![Configuração padrão de alta disponibilidade](./media/sap-ha-availability-zones/standard-ha-config.png)

A camada de aplicação SAP é implantada através de um [conjunto de disponibilidades](../../manage-availability.md)Azure . Para uma elevada disponibilidade de Serviços Centrais SAP, pode implantar dois VMs num conjunto de disponibilidade separado. Utilize o Cluster de Failover do Servidor do Windows ou o Pacemaker (Linux) como uma estrutura de alta disponibilidade com falha automática em caso de problema de infraestrutura ou software. Para saber mais sobre estas implementações, consulte:

- [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado de cluster](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Cluster uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando a partilha de ficheiros](./sap-high-availability-guide-wsfc-file-share.md)
- [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](./high-availability-guide-suse.md)
- [Azure Virtual Machines alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux](./high-availability-guide-rhel.md)

Uma arquitetura semelhante aplica-se à camada DBMS dos sistemas SAP NetWeaver, S/4HANA ou Hybris. Implanta a camada DBMS num modo ativo/passivo com uma solução de cluster de falha para proteger contra infraestruturas ou falhas de software. A solução de cluster de failover pode ser uma estrutura de falha específica do DBMS, cluster de falha do servidor do Windows ou Pacemaker.

Para implementar a mesma arquitetura utilizando zonas de disponibilidade do Azure, você precisa fazer algumas alterações na arquitetura delineada anteriormente. Este artigo descreve estas alterações.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações para implantação em zonas de disponibilidade


Considere o seguinte quando utilizar Zonas de Disponibilidade:

- Não existem garantias quanto às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- As Zonas de Disponibilidade não são uma solução DR ideal. As catástrofes naturais podem causar danos generalizados nas regiões mundiais, incluindo graves danos nas infraestruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituir uma solução DR adequada.
- A latência da rede em todas as Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, pode implementar e executar a camada de aplicação SAP em diferentes zonas porque a latência da rede de uma zona para a DBMS VM ativa é aceitável. Mas em algumas regiões do Azure, a latência entre o DBMS VM ativo e a instância de aplicação SAP, quando implantada em diferentes zonas, pode não ser aceitável para os processos comerciais da SAP. Nestes casos, a arquitetura de implantação tem de ser diferente, com uma arquitetura ativa/ativa para a aplicação ou uma arquitetura ativa/passiva onde a latência da rede transversal é demasiado elevada.
- Ao decidir onde utilizar Zonas de Disponibilidade, baseie a sua decisão na latência da rede entre as zonas. A latência da rede desempenha um papel importante em duas áreas:
    - Latência entre os dois casos de DBMS que precisam de ter replicação sincronizada. Quanto maior for a latência da rede, maior é a probabilidade de afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência da rede entre um VM que executa uma instância de diálogo SAP em zona com a instância DBMS ativa e um VM semelhante noutra zona. À medida que esta diferença aumenta, a influência no tempo de funcionamento dos processos empresariais e dos postos de trabalho em lote também aumenta, dependendo se funcionam em zona com o DBMS ou numa zona diferente.

Quando implementa VMs Azure em zonas de disponibilidade e estabelece soluções de failover na mesma região do Azure, algumas restrições aplicam-se:

- Tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/) quando se implanta em Zonas de Disponibilidade Azure. 
- O mapeamento das enumerações de zona para as zonas físicas é fixado numa base de subscrição Azure. Se estiver a utilizar diferentes subscrições para implantar os seus sistemas SAP, tem de definir as zonas ideais para cada subscrição.
- Não é possível implantar conjuntos de disponibilidades Azure dentro de uma Zona de Disponibilidade Azure, a menos que utilize [o Grupo de Colocação de Proximidade Azure](../../linux/co-location.md). A forma como pode implantar a camada SAP DBMS e os serviços centrais através de zonas e, ao mesmo tempo, implementar a camada de aplicação SAP utilizando conjuntos de disponibilidade e ainda alcançar uma proximidade próxima dos VMs está documentada no artigo Grupos de Colocação de [Proximidade Azure para a latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md). Se não estiver a utilizar grupos de colocação de proximidade do Azure, tem de escolher um ou outro como estrutura de implantação para máquinas virtuais.
- Não é possível utilizar um [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) para criar soluções de cluster de falha com base no Cluster de Falha de Falha do Servidor do Windows ou no Linux Pacemaker. Em vez disso, tem de utilizar o SKU do [Balanceador de Carga Padrão Azure](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>A combinação de Zonas de Disponibilidade ideal
Antes de decidir como utilizar zonas de disponibilidade, tem de determinar:

- A latência da rede entre as três zonas de uma região de Azure. Isto permitir-lhe-á escolher as zonas com menos latência de rede no tráfego de rede de zonas cruzadas.
- A diferença entre a latência VM-vM dentro de uma das zonas, à sua escolha, e a latência da rede em duas zonas à sua escolha.
- A determinação de se os tipos de VM que precisa de implantar estão disponíveis nas duas zonas que selecionou. Com alguns VMs, especialmente VMs da Série M, você pode encontrar situações em que alguns SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência da rede entre e dentro de zonas
Para determinar a latência entre as diferentes zonas, é necessário:

- Desdobre o VM SKU que pretende utilizar para a sua instância DBMS nas três zonas. Certifique-se de que [o Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado quando efetuar esta medição.
- Quando encontrar as duas zonas com menos latência de rede, coloque mais três VMs do VM SKU que pretende utilizar como a camada de aplicação VM através das três Zonas de Disponibilidade. Meça a latência da rede contra os dois VMS DBMS nas duas zonas DBMS que selecionou. 
- Utilize **niping** como uma ferramenta de medição. Esta ferramenta, da SAP, é descrita em notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona através dos caminhos de código de rede acelerado do Azure, não recomendamos que o utilize.

Não precisa fazer estes testes manualmente. Pode encontrar um teste [de latência do](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedimento PowerShell que automatiza os testes de latência descritos. 

Com base nas suas medições e na disponibilidade dos seus SKUs VM nas Zonas de Disponibilidade, tem de tomar algumas decisões:

- Defina as zonas ideais para a camada DBMS.
- Determine se deseja distribuir a sua camada de aplicação SAP ativa por uma, duas ou todas as três zonas, com base em diferenças de latência da rede na zona versus entre zonas.
- Determine se pretende implementar uma configuração ativa/passiva ou uma configuração ativa/ativa, do ponto de vista da aplicação. (Estas configurações são explicadas mais tarde neste artigo.)

Ao tomar estas decisões, tenha também em conta as recomendações de latência da rede da SAP, tal como documentado na nota [sap #1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medições e decisões que tomar são válidas para a subscrição Azure que utilizou quando tomou as medições. Se utilizar outra subscrição do Azure, tem de repetir as medições. O mapeamento de zonas enumeradas pode ser diferente para outra subscrição do Azure.


> [!IMPORTANT]
> Espera-se que as medições descritas anteriormente proporcionem resultados diferentes em todas as regiões do Azure que suportam [Zonas de Disponibilidade.](../../../availability-zones/az-overview.md) Mesmo que os requisitos de latência da sua rede sejam os mesmos, poderá ter de adotar diferentes estratégias de implantação em diferentes regiões do Azure, porque a latência da rede entre zonas pode ser diferente. Em algumas regiões de Azure, a latência da rede entre as três zonas diferentes pode ser muito diferente. Noutras regiões, a latência da rede entre as três zonas diferentes poderia ser mais uniforme. A alegação de que existe sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência da rede em zonas de disponibilidade nas regiões de Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Esta arquitetura de implementação é chamada ativa/ativa porque implementa os seus servidores de aplicações SAP ativos em duas ou três zonas. A instância dos Serviços Centrais da SAP que utiliza a replicação do enqueue será implantada entre duas zonas. O mesmo acontece com a camada DBMS, que será implantada nas mesmas zonas do Serviço Central SAP.

Ao considerar esta configuração, você precisa encontrar as duas Zonas de Disponibilidade na sua região que oferecem latência de rede transversal que é aceitável para a sua carga de trabalho e sua replicação sincronizada DBMS. Também quer ter a certeza de que o delta entre a latência da rede dentro das zonas selecionadas e a latência da rede transversal não é muito grande. Isto porque não quer grandes variações, dependendo se um trabalho funciona em zona com o servidor DBMS ou através de zonas, nos tempos de funcionamento dos seus processos de negócio ou em lotes. Algumas variações são aceitáveis, mas não fatores de diferença.

Um esquema simplificado de uma implantação ativa/ativa em duas zonas poderia ser assim:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Aplicam-se as seguintes considerações a esta configuração:

- Não utilizando o [Grupo de Colocação de Proximidade Azure,](../../linux/co-location.md)trata as Zonas de Disponibilidade Azure como domínios de falha e atualização para todos os VMs porque os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure.
- Se pretender combinar implementações zonais para a camada DBMS e serviços centrais, mas pretender utilizar conjuntos de disponibilidade do Azure para a camada de aplicação, precisa de utilizar grupos de proximidade Azure, conforme descrito no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ótima com aplicações SAP](sap-proximity-placement-scenarios.md).
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O armazenamento Azure Premium e [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
  - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.
- Para obter a consistência do tempo de execução para processos de negócio críticos, você pode tentar direcionar certos trabalhos de lote e utilizadores para instâncias de aplicação que estão em zona com a instância DBMS ativa, usando grupos de servidores de lote SAP, grupos de logon SAP ou grupos RFC. No entanto, no caso de um failover zonal, você precisaria mover manualmente estes grupos para instâncias em execução em VMs que estão em zona com o DB VM ativo.  
- É melhor implementar instâncias de diálogo dormentes em cada uma das zonas. Isto é para permitir um retorno imediato à antiga capacidade de recursos se uma zona utilizada por parte das suas instâncias de aplicação estiver fora de serviço.

> [!IMPORTANT]
> Neste cenário ativo/ativo, os custos adicionais para a largura de banda são anunciados pela Microsoft a partir de 04/01/2020. Consulte o documento [Detalhes de Preços da Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). A transferência de dados entre a camada de aplicação SAP e a camada DBMS SAP é bastante intensiva. Portanto, o cenário ativo/ativo pode contribuir bastante para os custos. Continue a verificar este artigo para obter os custos exatos 


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Se não encontrar um delta aceitável entre a latência da rede dentro de uma zona e a latência do tráfego de rede de zonas cruzadas, pode implementar uma arquitetura que tenha um caráter ativo/passivo do ponto de vista da camada de aplicação SAP. Você define uma zona *ativa,* que é a zona onde você implanta a camada completa de aplicação e onde você tenta executar tanto o DBMS ativo como a instância dos Serviços Centrais SAP. Com esta configuração, você precisa ter certeza de que você não tem variações de tempo de execução extrema, dependendo se um trabalho funciona em zona com a instância DBMS ativa ou não, em transações de negócios e trabalhos de lote.

O layout básico da arquitetura é assim:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Aplicam-se as seguintes considerações a esta configuração:

- Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade Azure, conforme documentado no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
- Ao utilizar esta arquitetura, tem de monitorizar de perto o estado e tentar manter as instâncias ativas dos Serviços Centrais DBMS e SAP na mesma zona que a sua camada de aplicação implantada. Em caso de falha do Serviço Central SAP ou da instância DBMS, deseja certificar-se de que pode falhar manualmente na zona com a camada de aplicação SAP implantada o mais rapidamente possível.
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O armazenamento Azure Premium e [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.
- Deve implantar VMs dormentes na zona passiva (do ponto de vista DBMS) para que possa iniciar os recursos de aplicação em caso de falha de zona.
    - [A recuperação do local de Azure](https://azure.microsoft.com/services/site-recovery/) é atualmente incapaz de replicar VMs ativos para VMs dormentes entre zonas. 
- Deve investir na automatização que lhe permite, em caso de falha de zona, iniciar automaticamente a camada de aplicação SAP na segunda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Combinação de alta disponibilidade e configuração de recuperação de desastres
A Microsoft não partilha qualquer informação sobre distâncias geográficas entre as instalações que acolhem diferentes Zonas de Disponibilidade Azure numa região do Azure. Ainda assim, alguns clientes estão a usar zonas para uma configuração combinada de HA e DR que promete um objetivo de ponto de recuperação (RPO) de zero. Isto significa que não deve perder nenhuma transação de base de dados comprometida, mesmo em caso de recuperação de desastres. 

> [!NOTE]
> Recomendamos que utilize uma configuração como esta apenas em determinadas circunstâncias. Por exemplo, pode usá-lo quando os dados não podem sair da região de Azure por razões de segurança ou conformidade. 

Aqui está um exemplo de como tal configuração pode parecer:

![Dr combinado de alta disponibilidade em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Aplicam-se as seguintes considerações a esta configuração:

- Ou está assumindo que há uma distância significativa entre as instalações que hospedam uma Zona de Disponibilidade ou é forçado a permanecer dentro de uma certa região de Azure. Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade Azure, conforme documentado no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
- Ao utilizar esta arquitetura, tem de monitorizar de perto o estado e tentar manter as instâncias ativas dos Serviços Centrais DBMS e SAP na mesma zona que a sua camada de aplicação implantada. Em caso de falha do Serviço Central SAP ou da instância DBMS, deseja certificar-se de que pode falhar manualmente na zona com a camada de aplicação SAP implantada o mais rapidamente possível.
- Deve ter instâncias de aplicação de produção pré-instaladas nos VMs que executam as instâncias de aplicação ativas da QA.
- Em caso de falha de zona, encerre as instâncias de aplicação da QA e inicie as instâncias de produção. Note que precisa de usar nomes virtuais para as instâncias de aplicação para que isto funcione.
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O armazenamento Azure Premium e [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.





## <a name="next-steps"></a>Passos seguintes
Aqui estão alguns próximos passos para implantação em todas as Zonas de Disponibilidade Azure:

- [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado em Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Prepare a infraestrutura Azure para a alta disponibilidade do SAP utilizando um cluster de failover do Windows e uma partilha de ficheiros para as instâncias SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)