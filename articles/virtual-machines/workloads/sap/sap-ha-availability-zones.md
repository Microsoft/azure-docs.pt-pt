---
title: Configurações de carga de trabalho SAP com zonas de disponibilidade do Azure | Documentos da Microsoft
description: Arquitetura de elevada disponibilidade e cenários para SAP NetWeaver, através de zonas de disponibilidade do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8e20d617da12aea857308f4020fbd0cfe711430
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651316"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho SAP com zonas de disponibilidade do Azure
[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) é um dos recursos de alta disponibilidade fornecidos pelo Azure. Utilizar zonas de disponibilidade melhora a disponibilidade geral de cargas de trabalho SAP no Azure. Esta funcionalidade já está disponível em alguns [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). No futuro, ele estará disponível em mais regiões.

Este gráfico mostra a arquitetura básica de elevada disponibilidade do SAP:

![Configuração padrão de elevada disponibilidade](./media/sap-ha-availability-zones/standard-ha-config.png)

Camada de aplicação SAP é implementada numa do Azure [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Para elevada disponibilidade do SAP Central Services, pode implementar duas VMs num conjunto de disponibilidade separados. Utilize o Clustering de ativação pós-falha do Windows Server ou Pacemaker (Linux) como uma estrutura de elevada disponibilidade com a ativação pós-falha automática em caso de um problema de infraestrutura ou software. Para saber mais sobre estas implementações, consulte:

- [Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows utilizando um disco partilhado de cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows ao utilizar a partilha de ficheiros](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações de SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Uma arquitetura semelhante aplica-se para a camada do DBMS da SAP NetWeaver, S/4HANA ou Hybris sistemas. Implemente a camada do DBMS em modo ativo/passivo com uma solução de cluster de ativação pós-falha para proteção contra a falha de uma infraestrutura ou software. A solução de cluster de ativação pós-falha pode ser um DBMS específicos da estrutura de ativação pós-falha, Clustering de ativação pós-falha do Windows Server ou Pacemaker.

Para implementar a mesma arquitetura com zonas de disponibilidade do Azure, terá de fazer algumas alterações para a arquitetura descrita anteriormente. Este artigo descreve essas alterações.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações sobre a implementação em zonas de disponibilidade


Quando usar as zonas de disponibilidade, considere o seguinte:

- Não há garantias sobre as distâncias entre várias zonas de disponibilidade numa região do Azure.
- As zonas de disponibilidade não são uma solução de DR ideal. Desastres naturais podem causar danos amplo em regiões do mundo, incluindo danos pesado infra-estruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituem uma solução adequada de DR.
- A latência de rede em zonas de disponibilidade não é o mesmo em todas as regiões do Azure. Em alguns casos, pode implementar e executar a camada de aplicação SAP em zonas diferentes, uma vez que a latência de rede de uma zona para o ativo DBMS VM é aceitável. Mas, em algumas regiões do Azure, a latência entre a VM DBMS Active Directory e a instância da aplicação SAP, quando implementado em diferentes horários, poderá não ser aceitável para os processos de negócios SAP. Nestes casos, a arquitetura de implementação tem de ser diferentes, com uma arquitetura de ativo/ativo para a aplicação ou uma arquitetura de ativo/passivo, onde é demasiado elevada latência de rede entre zonas.
- Ao decidir onde utilizar zonas de disponibilidade, base sua decisão na latência de rede entre as zonas. Latência de rede desempenha um papel importante em duas áreas:
    - Latência entre as duas instâncias do DBMS que tem de ter os replicação síncrona. Quanto maior for a latência de rede, o mais provável que ele irá afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência de rede entre uma VM em execução uma SAP diálogo instância na zona com a instância ativa do DBMS e uma VM semelhante noutra zona. À medida que aumenta essa diferença, a influência sobre o tempo de execução de processos de negócios e batch tarefas também aumenta, dependente independentemente de serem executadas numa zona com o DBMS ou numa zona diferente.

Quando implementar VMs do Azure em zonas de disponibilidade e estabelecer as soluções de ativação pós-falha na mesma região do Azure, aplicam-se algumas restrições:

- Tem de utilizar [Managed Disks do Azure](https://azure.microsoft.com/services/managed-disks/) ao implementar as zonas de disponibilidade do Azure. 
- O mapeamento de enumerações de zona para zonas físicas é corrigido numa base de subscrição do Azure. Se estiver usando a subscrições diferentes para implementar os seus sistemas SAP, terá de definir as zonas ideais para cada subscrição.
- Não é possível implementar conjuntos de disponibilidade do Azure na zona de disponibilidade do Azure. Escolha um ou outro como uma estrutura de implantação para máquinas virtuais.
- Não é possível utilizar um [Balanceador de carga básico do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster com base no Clustering de ativação pós-falha do Windows Server ou Linux Pacemaker de ativação pós-falha. Em vez disso, tem de utilizar o [SKU de Balanceador de carga Standard do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>A combinação ideal de zonas de disponibilidade
Antes de decidir como utilizar as zonas de disponibilidade, terá de determinar:

- A latência de rede entre as três zonas de uma região do Azure. Isso permitirá que escolha as zonas com a menor latência de rede no tráfego de rede entre zonas.
- A diferença entre a latência de VM para VM dentro de uma das zonas, à sua escolha e a latência de rede entre duas zonas à sua escolha.
- Um processo de determinar se os tipos VM que precisa implantar estão disponíveis nas duas zonas que selecionou. Com algumas VMs, especialmente as VMs da série M, pode encontrar situações em que alguns SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência de rede entre os e entre zonas
Para determinar a latência entre as zonas diferentes, terá de:

- Implemente o SKU de VM que pretende utilizar para a sua instância do DBMS em todas as três zonas. Certifique-se [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) é ativada quando pega esta medida.
- Quando encontrar duas zonas com a menor latência de rede, implemente outro três VMs do SKU de VM que pretende utilizar como a VM da camada de aplicativo em três zonas de disponibilidade. Medir a latência de rede contra as duas VMs DBMS em duas zonas DBMS que selecionou. 
- Uso **niping** como uma ferramenta de medição. Essa ferramenta, do SAP, é descrita nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se sobre os comandos documentados para medidas de latência. Uma vez **ping** não funciona por meio de caminhos de código do Azure Accelerated Networking, não recomendamos que usá-lo.

Com base nas suas medidas e a disponibilidade das suas SKUs de VM em zonas de disponibilidade, terá de tomar algumas decisões:

- Defina as zonas ideais para a camada do DBMS.
- Determine se deseja distribuir sua camada de aplicativo do Active Directory SAP através de um, dois ou três regiões, com base nas diferenças de rede latência na zona versus nas zonas.
- Determine se pretende implementar uma configuração de ativo/passivo ou uma configuração de ativo/ativo, de um ponto de vista do aplicativo. (Essas configurações são explicadas neste artigo.)

Tornar essas decisões, também leve em recomendações de latência de rede do SAP de conta, conforme documentado na nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medidas e as decisões que tomar são válidas para a subscrição do Azure que utilizou quando que as medidas de realizar. Se utilizar outra subscrição do Azure, terá de repetir as medidas. O mapeamento de zonas enumerados poderão ser diferente para outra subscrição do Azure.


> [!IMPORTANT]
> Espera-se que as medidas descritas anteriormente fornecerá resultados diferentes em cada região do Azure que suporta [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Mesmo que os seus requisitos de latência de rede são os mesmos, poderá ter de adotar diferentes estratégias de implantação em diferentes regiões do Azure, uma vez que a latência de rede entre zonas pode ser diferente. Em algumas regiões do Azure, a latência de rede entre as três zonas diferentes pode ser diferente. Em outras regiões, a latência de rede entre as três zonas diferentes pode ser mais uniforme. A afirmação de que há sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência de rede em zonas de disponibilidade em regiões do Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implementação de ativo/ativo
Esta arquitetura de implementação é chamada ativo/ativo, porque a implementar as instâncias de caixa de diálogo SAP Active Directory em duas ou três zonas. A instância do SAP Central Services que utiliza a replicação de colocar em fila será implementada entre duas zonas. O mesmo é válido para a camada do DBMS, o que irá ser implementada em zonas da mesmas como serviço Central da SAP.

Ao considerar esta configuração, tem de encontrar duas zonas de disponibilidade na sua região essa latência de rede de entre zonas de oferta que é aceitável para a sua carga de trabalho e a replicação síncrona do DBMS. Também deseje para garantir que o delta entre a latência de rede dentro de zonas que selecionou e a latência de rede entre zonas não é demasiado grande. Isso é porque não deseja grandes variações, dependendo se uma tarefa for executada na zona com o servidor do DBMS ou na zonas, em tempos de execução de processos empresariais ou tarefas de lote. Algumas variações são aceitáveis, mas não os fatores de diferença.

Um esquema simplificado de uma implementação de ativo/ativo entre duas zonas poderia ter esta aparência:

![Implementação de zona ativo/ativo](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações aplicam-se para esta configuração:

- Tratar as zonas de disponibilidade do Azure como domínios de falha e atualização de todas as VMs porque os conjuntos de disponibilidade não podem ser implementados em zonas de disponibilidade do Azure.
- Balanceadores de carga dos clusters de ativação pós-falha do SAP Central Services e a camada do DBMS, tem de utilizar o [Balanceador de carga de Azure de SKU Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Balanceador de carga básico não funcionará em zonas.
- A rede virtual do Azure que implementou para alojar o sistema SAP, juntamente com suas sub-redes, é transferida entre zonas. Não precisar de separar as redes virtuais para cada zona.
- Todas as máquinas virtuais a implementar, precisa usar [Managed Disks do Azure](https://azure.microsoft.com/services/managed-disks/). Discos não geridos não são suportados para implementações zonais.
- Armazenamento Premium do Azure e [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suporta qualquer tipo de replicação de armazenamento em zonas. O aplicativo (DBMS ou SAP Central Services) têm de replicar dados importantes.
- O mesmo é válido para o diretório partilhado /sapmnt, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). Tem de utilizar uma tecnologia que replica esses discos partilhados ou partilhas de entre as zonas. Essas tecnologias são suportadas:
    - Para Windows, uma solução de cluster que utiliza o SIOS DataKeeper, conforme documentado [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar um disco partilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, uma de partilha NFS que baseia-se conforme documentado [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Atualmente, a solução que utiliza o servidor de ficheiros de escalamento horizontal do Microsoft, conforme documentado [infraestrutura de preparar o Azure para elevada disponibilidade SAP através de uma partilha de ficheiros e de cluster de ativação pós-falha do Windows para as instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não é suportado nas zonas.
- A terceiro zona é utilizada para alojar o dispositivo SBD no caso de criar um [cluster de SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicações adicionais.
- Para obter consistência de tempo de execução dos processos empresariais críticos, pode experimentar direcionar determinadas tarefas de lote e os utilizadores para instâncias de aplicações que estão na zona com a instância do DBMS Active Directory com o SAP do batch grupos de servidores, grupos de logon ou grupos RFC. No entanto, no caso de uma ativação pós-falha zonal, seria necessário mover manualmente estes grupos para instâncias em execução em VMs que estão na zona com a VM de DB Active Directory.  
- Pode querer implementar instâncias de caixa de diálogo inativo em cada uma das zonas. Isso é para permitir que um retorno imediato a capacidade do recurso antigo se uma zona utilizada por parte das suas instâncias do aplicativo está fora do serviço.


## <a name="activepassive-deployment"></a>Implementação de ativo/passivo
Se não conseguir encontrar um intervalo aceitável entre a latência de rede dentro de uma zona e a latência entre zonas de tráfego de rede, pode implementar uma arquitetura que tem um caráter de modo ativo/passivo do ponto de vista do SAP aplicação camada. Define um *Active Directory* zona, o que é a zona em que implantar a camada de aplicação completa e sempre que tentar executar o DBMS de Active Directory e a instância do SAP Central Services. Com esta configuração, terá de certificar-se de que não tem tempo de execução extreme variações, dependendo se uma tarefa for executada na zona com a instância ativa do DBMS ou não, em transações empresariais e tarefas do batch.

O layout básico da arquitetura é semelhante a isto:

![Implementação de zona ativo/passivo](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

As seguintes considerações aplicam-se para esta configuração:

- Conjuntos de disponibilidade não podem ser implementados em zonas de disponibilidade do Azure. Então, neste caso, tem um domínio de atualização e com falha para a camada de aplicação. Isso ocorre porque só é implementada numa zona de um. Esta configuração é um pouco menos desejável do que a arquitetura de referência, o que sugere que implemente a camada de aplicativo num conjunto de disponibilidade do Azure.
- Quando utiliza esta arquitetura, terá de monitorizar o estado de perto e tente manter as instâncias ativas do DBMS e SAP Central Services na mesma zona de sua camada de aplicação implementada. Em caso de uma ativação pós-falha do serviço de Central de SAP ou a instância do DBMS, quer certificar-se de que pode manualmente falhar novamente para a zona com a camada de aplicação SAP implementada o mais rapidamente possível.
- Balanceadores de carga dos clusters de ativação pós-falha do SAP Central Services e a camada do DBMS, tem de utilizar o [Balanceador de carga de Azure de SKU Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Balanceador de carga básico não funcionará em zonas.
- A rede virtual do Azure que implementou para alojar o sistema SAP, juntamente com suas sub-redes, é transferida entre zonas. Não precisar de separar as redes virtuais para cada zona.
- Todas as máquinas virtuais a implementar, precisa usar [Managed Disks do Azure](https://azure.microsoft.com/services/managed-disks/). Discos não geridos não são suportados para implementações zonais.
- Armazenamento Premium do Azure e [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suporta qualquer tipo de replicação de armazenamento em zonas. O aplicativo (DBMS ou SAP Central Services) têm de replicar dados importantes.
- O mesmo é válido para o diretório partilhado /sapmnt, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). Tem de utilizar uma tecnologia que replica esses discos partilhados ou partilhas de entre as zonas. Essas tecnologias são suportadas:
    - Para Windows, uma solução de cluster que utiliza o SIOS DataKeeper, conforme documentado [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar um disco partilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, uma de partilha NFS que baseia-se conforme documentado [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Atualmente, a solução que utiliza o servidor de ficheiros de escalamento horizontal do Microsoft, conforme documentado [infraestrutura de preparar o Azure para elevada disponibilidade SAP através de uma partilha de ficheiros e de cluster de ativação pós-falha do Windows para as instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não é suportado nas zonas.
- A terceiro zona é utilizada para alojar o dispositivo SBD no caso de criar um [cluster de SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicações adicionais.
- Deve implementar em VMs inativas no passivo de zona (do ponto de vista do DBMS) para que possa começar a recursos de aplicativos em caso de falha de zona.
    - [O Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) não consegue replicar VMs do Active Directory para VMs Inativas entre zonas. 
- Deve investir na automatização que permite a, em caso de falha de zona, para iniciar automaticamente a camada de aplicação SAP na segunda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Combinados de elevada disponibilidade e a configuração da recuperação após desastre
A Microsoft não partilhar qualquer informação sobre geográficas distâncias entre as instalações esse anfitrião diferente Azure Availability Zones numa região do Azure. Ainda assim, alguns clientes estão a utilizar zonas para uma configuração de HA e DR combinada que promete um objetivo de ponto de recuperação (RPO) igual a zero. Isso significa que não deve perder quaisquer transações consolidadas de base de dados mesmo em caso de recuperação após desastre. 

> [!NOTE]
> Recomendamos que utilize uma configuração semelhante a esta apenas em determinadas circunstâncias. Por exemplo, poderá usá-lo quando os dados não podem deixar a região do Azure por motivos de segurança ou conformidade. 

Eis um exemplo de como essa configuração pode ser:

![Combinados DR de elevada disponibilidade em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

As seguintes considerações aplicam-se para esta configuração:

- É um supondo que existe uma distância significativa entre os recursos que aloja uma zona de disponibilidade ou é forçado a se manter dentro de uma determinada região do Azure. Conjuntos de disponibilidade não podem ser implementados em zonas de disponibilidade do Azure. Então, neste caso, tem um domínio de atualização e com falha para a camada de aplicação. Isso ocorre porque só é implementada numa zona de um. Esta configuração é um pouco menos desejável do que a arquitetura de referência, o que sugere que implemente a camada de aplicativo num conjunto de disponibilidade do Azure.
- Quando utiliza esta arquitetura, terá de monitorizar o estado de perto e tente manter as instâncias ativas do DBMS e SAP Central Services na mesma zona de sua camada de aplicação implementada. Em caso de uma ativação pós-falha do serviço de Central de SAP ou a instância do DBMS, quer certificar-se de que pode manualmente falhar novamente para a zona com a camada de aplicação SAP implementada o mais rapidamente possível.
- Deve ter instâncias de aplicações de produção pré-instaladas nas VMs que executam as Active Directory instâncias da aplicação de controle de qualidade.
- Em caso de falha de zona, encerrar as instâncias do aplicativo de controle de qualidade e iniciar as instâncias de produção em vez disso. Tenha em atenção que tem de utilizar nomes virtual para instâncias da aplicação para fazer isso funcionar.
- Balanceadores de carga dos clusters de ativação pós-falha do SAP Central Services e a camada do DBMS, tem de utilizar o [Balanceador de carga de Azure de SKU Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Balanceador de carga básico não funcionará em zonas.
- A rede virtual do Azure que implementou para alojar o sistema SAP, juntamente com suas sub-redes, é transferida entre zonas. Não precisar de separar as redes virtuais para cada zona.
- Todas as máquinas virtuais a implementar, precisa usar [Managed Disks do Azure](https://azure.microsoft.com/services/managed-disks/). Discos não geridos não são suportados para implementações zonais.
- Armazenamento Premium do Azure e [armazenamento Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não suporta qualquer tipo de replicação de armazenamento em zonas. O aplicativo (DBMS ou SAP Central Services) têm de replicar dados importantes.
- O mesmo é válido para o diretório partilhado /sapmnt, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). Tem de utilizar uma tecnologia que replica esses discos partilhados ou partilhas de entre as zonas. Essas tecnologias são suportadas:
    - Para Windows, uma solução de cluster que utiliza o SIOS DataKeeper, conforme documentado [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar um disco partilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, uma de partilha NFS que baseia-se conforme documentado [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Atualmente, a solução que utiliza o servidor de ficheiros de escalamento horizontal do Microsoft, conforme documentado [infraestrutura de preparar o Azure para elevada disponibilidade SAP através de uma partilha de ficheiros e de cluster de ativação pós-falha do Windows para as instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não é suportado nas zonas.
- A terceiro zona é utilizada para alojar o dispositivo SBD no caso de criar um [cluster de SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicações adicionais.





## <a name="next-steps"></a>Passos Seguintes
Seguem-se alguns passos seguintes para implementar em zonas de disponibilidade do Azure:

- [Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows ao utilizar um disco partilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparar a infraestrutura do Azure para elevada disponibilidade SAP através de uma partilha de ficheiros e de cluster de ativação pós-falha do Windows para as instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






