---
title: Configurações de carga de trabalho SAP com Zonas de Disponibilidade Azure [ Microsoft Docs
description: Arquitetura de alta disponibilidade e cenários para SAP NetWeaver usando zonas de disponibilidade Azure
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
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78675615"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho de SAP com Zonas de Disponibilidade do Azure
As Zonas de [Disponibilidade Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) são uma das funcionalidades de alta disponibilidade que o Azure fornece. A utilização de Zonas de Disponibilidade melhora a disponibilidade global de cargas de trabalho SAP no Azure. Esta funcionalidade já está disponível em [algumas regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/) No futuro, estará disponível em mais regiões.

Este gráfico mostra a arquitetura básica da alta disponibilidade do SAP:

![Configuração padrão de alta disponibilidade](./media/sap-ha-availability-zones/standard-ha-config.png)

A camada de aplicação SAP é implantada através de um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)Azure . Para uma elevada disponibilidade de Serviços Centrais SAP, pode implementar dois VMs num conjunto de disponibilidade separado. Utilize o Clusterde Failover do Windows Server ou o Pacemaker (Linux) como uma estrutura de alta disponibilidade com falha automática em caso de um problema de infraestrutura ou software. Para saber mais sobre estas implementações, consulte:

- [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando a partilha de ficheiros](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Uma arquitetura semelhante aplica-se à camada DBMS dos sistemas SAP NetWeaver, S/4HANA ou Hybris. Implanta a camada DBMS num modo ativo/passivo com uma solução de cluster failover para proteger contra infraestruturas ou falha de software. A solução de cluster failover pode ser uma estrutura de failover específica do DBMS, Clustering de Failover do Servidor windows ou Pacemaker.

Para implementar a mesma arquitetura utilizando zonas de disponibilidade Azure, é necessário fazer algumas alterações na arquitetura delineada anteriormente. Este artigo descreve estas mudanças.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações para implantação em todas as Zonas de Disponibilidade


Considere o seguinte quando utilizar Zonas de Disponibilidade:

- Não existem garantias relativas às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- As Zonas de Disponibilidade não são uma solução DE DR ideal. As catástrofes naturais podem causar danos generalizados nas regiões mundiais, incluindo danos pesados nas infraestruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituir uma solução DEDR adequada.
- A latência da rede em todas as Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, pode implantar e executar a camada de aplicação SAP em diferentes zonas porque a latência da rede de uma zona para o VM DBMS ativo é aceitável. Mas em algumas regiões do Azure, a latência entre o Ativo DBMS VM e a instância de aplicação SAP, quando implantada em diferentes zonas, pode não ser aceitável para os processos de negócio sap. Nestes casos, a arquitetura de implantação tem de ser diferente, com uma arquitetura ativa/ativa para a aplicação ou uma arquitetura ativa/passiva onde a latência da rede transversal é demasiado elevada.
- Ao decidir onde utilizar Zonas de Disponibilidade, baseie a sua decisão na latência da rede entre as zonas. A latência da rede desempenha um papel importante em duas áreas:
    - Latência entre os dois casos de DBMS que precisam de ter replicação sincronizada. Quanto maior for a latência da rede, maior é a probabilidade de afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência da rede entre um VM que executa um dialog sap caso em zona com a instância DBMS ativa e um VM semelhante em outra zona. À medida que esta diferença aumenta, a influência no tempo de funcionamento dos processos de negócio e dos postos de trabalho em lotes também aumenta, dependendo se funcionam em zona com o DBMS ou numa zona diferente.

Quando implementa VMs Azure em zonas de disponibilidade e estabelece soluções de failover dentro da mesma região azure, aplicam-se algumas restrições:

- Deve utilizar [discos geridos azure](https://azure.microsoft.com/services/managed-disks/) quando se deslocar para zonas de disponibilidade azure. 
- O mapeamento de enumerações de zonas para as zonas físicas é fixado numa base de subscrição Azure. Se estiver a utilizar diferentes subscrições para implementar os seus sistemas SAP, precisa definir as zonas ideais para cada subscrição.
- Não é possível implementar conjuntos de disponibilidade azure dentro de uma Zona de Disponibilidade Azure a menos que utilize o [Azure Closey Placement Group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). A forma como pode implantar a camada SAP DBMS e os serviços centrais em todas as zonas e, ao mesmo tempo, implementar a camada de aplicação SAP utilizando conjuntos de disponibilidade e ainda alcançar a proximidade dos VMs está documentada no artigo [Azure Closey Placement Groups para uma latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md). Se não estiver a alavancar os grupos de colocação de proximidade do Azure, tem de escolher um ou outro como quadro de implantação para máquinas virtuais.
- Não é possível utilizar um [Balancer de Carga Básico Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) para criar soluções de cluster failover baseadas no Cluster Failover do Windows Server ou no Pacemaker Linux. Em vez disso, precisa de utilizar o [Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>A combinação ideal de Zonas de Disponibilidade
Antes de decidir como utilizar as Zonas de Disponibilidade, tem de determinar:

- A latência da rede entre as três zonas de uma região de Azure. Isto permitir-lhe-á escolher as zonas com menor latência de rede no tráfego de rede de zona cruzada.
- A diferença entre a latência VM-to-VM dentro de uma das zonas, à sua escolha, e a latência da rede em duas zonas à sua escolha.
- A determinação de se os tipos vM que precisa de implementar estão disponíveis nas duas zonas que selecionou. Com alguns VMs, especialmente VMs da Série M, você pode encontrar situações em que algumas SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência da rede entre e dentro de zonas
Para determinar a latência entre as diferentes zonas, é necessário:

- Desloque o VM SKU que pretende utilizar para a sua instância DBMS nas três zonas. Certifique-se de que [o Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado quando efetuar esta medição.
- Quando encontrar as duas zonas com menor latência de rede, desloque outros três VMs do VM SKU que pretende utilizar como camada de aplicação VM através das três Zonas de Disponibilidade. Meça a latência da rede contra os dois VMs DBMS nas duas zonas DBMS que selecionou. 
- Utilize **o niping** como uma ferramenta de medição. Esta ferramenta, da SAP, é descrita nas notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona através dos caminhos do código de rede acelerada Azure, não recomendamos que o utilize.

Não precisas de fazer estes testes manualmente. Pode encontrar um teste de [latência](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) da zona de disponibilidade powerShell que automatiza os testes de latência descritos. 

Com base nas suas medições e na disponibilidade das suas VM SKUs nas Zonas de Disponibilidade, precisa de tomar algumas decisões:

- Defina as zonas ideais para a camada DBMS.
- Determine se pretende distribuir a sua camada de aplicação SAP ativa através de uma, duas ou todas as três zonas, com base em diferenças de latência da rede na zona versus em zonas.
- Determine se pretende implementar uma configuração ativa/passiva ou uma configuração ativa/ativa, do ponto de vista da aplicação. (Estas configurações são explicadas mais tarde neste artigo.)

Ao tomar estas decisões, tenha igualmente em conta as recomendações de latência da rede da SAP, tal como documentado na nota sap [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medições e decisões que tomar são válidas para a subscrição Azure que utilizou quando efetuou as medições. Se utilizar outra subscrição Do Azure, tem de repetir as medições. O mapeamento de zonas enumeradas pode ser diferente para outra subscrição do Azure.


> [!IMPORTANT]
> Espera-se que as medições descritas anteriormente fornecerão resultados diferentes em todas as regiões do Azure que suportam [zonas de disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview) Mesmo que os requisitos de latência da rede sejam os mesmos, poderá ter de adotar diferentes estratégias de implantação em diferentes regiões do Azure, porque a latência da rede entre zonas pode ser diferente. Em algumas regiões de Azure, a latência da rede entre as três zonas diferentes pode ser muito diferente. Noutras regiões, a latência da rede entre as três zonas diferentes pode ser mais uniforme. A alegação de que há sempre uma latência da rede entre 1 e 2 milissegundos não está correta. A latência da rede através das Zonas de Disponibilidade nas regiões de Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Esta arquitetura de implementação é chamada ativa/ativa porque você implementa os seus servidores de aplicação SAP ativos em duas ou três zonas. A instância sap Central Services que utiliza a replicação da fila será implantada entre duas zonas. O mesmo acontece com a camada DBMS, que será implantada nas mesmas zonas que o Serviço Central SAP.

Ao considerar esta configuração, você precisa encontrar as duas Zonas de Disponibilidade na sua região que oferecem latência de rede transversal que é aceitável para a sua carga de trabalho e sua replicação sincronia DBMS. Também quer ter a certeza de que o delta entre a latência da rede dentro das zonas selecionadas e a latência da rede de cross-zone não é muito grande. Isto porque não quer grandes variações, dependendo se um trabalho funciona em zona com o servidor DBMS ou em zonas, nos tempos de funcionamento dos seus processos de negócio ou em trabalhos de lote. Algumas variações são aceitáveis, mas não fatores de diferença.

Um esquema simplificado de uma implantação ativa/ativa em duas zonas poderia parecer assim:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Aplicam-se as seguintes considerações para esta configuração:

- Não utilizando o [Azure Closey Placement Group,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)trata as Zonas de Disponibilidade Do Azure como domínios de avaria e atualização para todos os VMs, uma vez que os conjuntos de disponibilidade não podem ser implementados em Zonas de Disponibilidade Do Azure.
- Se quiser combinar implantações zonais para a camada DBMS e serviços centrais, mas quer utilizar conjuntos de disponibilidade azure para a camada de aplicação, precisa de utilizar grupos de proximidade Azure, como descrito no artigo [Azure Closey Placement Groups para uma latência ótima da rede com aplicações SAP.](sap-proximity-placement-scenarios.md)
- Para os equilibradores de carga dos clusters failover dos Serviços Centrais SAP e da camada DBMS, você precisa usar o Equilíbrio de [Carga Standard SKU Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Equilibrista básico de carga não funcionará através de zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas subredes, é estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, precisa de utilizar [discos geridos azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implantações zonais.
- O Armazenamento Azure Premium e o [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha nFS (Linux). É preciso usar uma tecnologia que reproduz estes discos ou partilhas partilhadas entre as zonas. Estas tecnologias são apoiadas:
  - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - Para o SUSE Linux, uma parte da NFS que é construída como documentada em [alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, tal como documentado na [infraestrutura Prepare Azure para uma elevada disponibilidade do SAP, utilizando um cluster de failover windows e partilha de ficheiros para instâncias SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)não é suportada em todas as zonas.
- A terceira zona é utilizada para alojar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.
- Para alcançar a consistência do tempo de execução para processos de negócio críticos, pode tentar direcionar certos trabalhos de lote e utilizadores para instâncias de aplicação que estão em zona com a instância dbmS ativa utilizando grupos de servidores de lote SAP, grupos de logon SAP ou grupos RFC. No entanto, no caso de uma falha zonal, seria necessário mover manualmente estes grupos para instâncias em execução em VMs que estão em zona com o DB VM ativo.  
- Talvez queira colocar casos de diálogo dormente em cada uma das zonas. Isto é para permitir um retorno imediato à capacidade de recursos anteriores se uma zona utilizada por parte das suas instâncias de aplicação estiver fora de serviço.

> [!IMPORTANT]
> Neste cenário ativo/ativo, são anunciados pela Microsoft taxas adicionais adicionais para a largura de banda a partir de 04/01/2020. Verifique os detalhes de preços da [largura de banda do](https://azure.microsoft.com/pricing/details/bandwidth/)documento . A transferência de dados entre a camada de aplicação SAP e a camada SAP DBMS é bastante intensiva. Portanto, o cenário ativo/ativo pode contribuir bastante para os custos. Continue verificando este artigo para obter os custos exatos 


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Se não encontrar um delta aceitável entre a latência da rede dentro de uma zona e a latência do tráfego de rede de cross-zone, pode implementar uma arquitetura que tenha um caráter ativo/passivo do ponto de vista da camada de aplicação SAP. Você define uma zona *ativa,* que é a zona onde você implanta a camada completa de aplicação e onde você tenta executar tanto o DBMS ativo como a instância De Serviços Centrais SAP. Com tal configuração, você precisa ter certeza de que você não tem variações de tempo de execução extremas, dependendo se um trabalho corre em zona com a instância dbms ativa ou não, em transações comerciais e trabalhos de lote.

O layout básico da arquitetura é assim:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Aplicam-se as seguintes considerações para esta configuração:

- Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade azure como documentado no artigo [Azure Closey Placement Groups para uma ótima latência da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
- Quando utiliza esta arquitetura, é necessário monitorizar o estado de perto e tentar manter as instâncias ativas de DBMS e SAP Central Services na mesma zona que a sua camada de aplicação implantada. Em caso de falha no Serviço Central SAP ou na instância DBMS, pretende certificar-se de que pode voltar a falhar manualmente na zona com a camada de aplicação SAP implementada o mais rapidamente possível.
- Para os equilibradores de carga dos clusters failover dos Serviços Centrais SAP e da camada DBMS, você precisa usar o Equilíbrio de [Carga Standard SKU Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Equilibrista básico de carga não funcionará através de zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas subredes, é estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, precisa de utilizar [discos geridos azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implantações zonais.
- O Armazenamento Azure Premium e o [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha nFS (Linux). É preciso usar uma tecnologia que reproduz estes discos ou partilhas partilhadas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, uma parte da NFS que é construída como documentada em [alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, tal como documentado na [infraestrutura Prepare Azure para uma elevada disponibilidade do SAP, utilizando um cluster de failover windows e partilha de ficheiros para instâncias SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)não é suportada em todas as zonas.
- A terceira zona é utilizada para alojar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.
- Deve colocar VMs dormentes na zona passiva (do ponto de vista do DBMS) para que possa iniciar os recursos de aplicação em caso de falha de zona.
    - [A Recuperação](https://azure.microsoft.com/services/site-recovery/) do Site Azure é atualmente incapaz de replicar VMs ativos a VMs dormentes entre zonas. 
- Deve investir em automação que lhe permita, em caso de falha de zona, iniciar automaticamente a camada de aplicação SAP na segunda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuração combinada de alta disponibilidade e recuperação de desastres
A Microsoft não partilha nenhuma informação sobre distâncias geográficas entre as instalações que acolhem diferentes Zonas de Disponibilidade Azure numa região do Azure. Ainda assim, alguns clientes estão a usar zonas para uma configuração combinada de HA e DR que promete um objetivo de ponto de recuperação (RPO) de zero. Isto significa que não deve perder nenhuma transação de base de dados cometida mesmo em caso de recuperação de desastres. 

> [!NOTE]
> Recomendamos que utilize uma configuração como esta apenas em determinadas circunstâncias. Por exemplo, pode usá-lo quando os dados não podem sair da região de Azure por razões de segurança ou conformidade. 

Aqui está um exemplo de como tal configuração pode parecer:

![DR de alta disponibilidade combinada em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Aplicam-se as seguintes considerações para esta configuração:

- Ou assumindo que há uma distância significativa entre as instalações que acolhem uma Zona de Disponibilidade ou é forçado a permanecer dentro de uma determinada região de Azure. Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade azure como documentado no artigo [Azure Closey Placement Groups para uma ótima latência da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
- Quando utiliza esta arquitetura, é necessário monitorizar o estado de perto e tentar manter as instâncias ativas de DBMS e SAP Central Services na mesma zona que a sua camada de aplicação implantada. Em caso de falha no Serviço Central SAP ou na instância DBMS, pretende certificar-se de que pode voltar a falhar manualmente na zona com a camada de aplicação SAP implementada o mais rapidamente possível.
- Deve ter instâncias de aplicação de produção pré-instaladas nos VMs que executam as instâncias ativas de aplicação qA.
- Em caso de falha de zona, encerre as instâncias de aplicação qA e inicie as instâncias de produção. Tenha em atenção que necessita de utilizar nomes virtuais para as instâncias de aplicação para que isto funcione.
- Para os equilibradores de carga dos clusters failover dos Serviços Centrais SAP e da camada DBMS, você precisa usar o Equilíbrio de [Carga Standard SKU Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Equilibrista básico de carga não funcionará através de zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas subredes, é estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, precisa de utilizar [discos geridos azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implantações zonais.
- O Armazenamento Azure Premium e o [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha nFS (Linux). É preciso usar uma tecnologia que reproduz estes discos ou partilhas partilhadas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, uma parte da NFS que é construída como documentada em [alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, tal como documentado na [infraestrutura Prepare Azure para uma elevada disponibilidade do SAP, utilizando um cluster de failover windows e partilha de ficheiros para instâncias SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)não é suportada em todas as zonas.
- A terceira zona é utilizada para alojar o dispositivo SBD no caso de construir um [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicação adicionais.





## <a name="next-steps"></a>Passos seguintes
Aqui estão alguns próximos passos para implantar em zonas de disponibilidade Azure:

- [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Prepare a infraestrutura Azure para uma alta disponibilidade do SAP utilizando um cluster de falhas do Windows e partilha de ficheiros para instâncias SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






