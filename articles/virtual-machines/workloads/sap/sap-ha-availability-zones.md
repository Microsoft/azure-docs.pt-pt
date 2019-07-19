---
title: Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para SAP NetWeaver usando Zonas de Disponibilidade do Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234240"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho de SAP com Zonas de Disponibilidade do Azure
O [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) é um dos recursos de alta disponibilidade que o Azure fornece. O uso de Zonas de Disponibilidade melhora a disponibilidade geral das cargas de trabalho do SAP no Azure. Esse recurso já está disponível em algumas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). No futuro, ele estará disponível em mais regiões.

Este gráfico mostra a arquitetura básica da alta disponibilidade do SAP:

![Configuração de alta disponibilidade padrão](./media/sap-ha-availability-zones/standard-ha-config.png)

A camada de aplicativo SAP é implantada em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)do Azure. Para alta disponibilidade dos serviços centrais do SAP, você pode implantar duas VMs em um conjunto de disponibilidade separado. Use o Windows Server failover clustering ou o pacemaker (Linux) como uma estrutura de alta disponibilidade com failover automático no caso de um problema de infraestrutura ou software. Para saber mais sobre essas implantações, consulte:

- [Cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o compartilhamento de arquivos](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Alta disponibilidade para SAP NetWeaver em VMs do Azure em SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Uma arquitetura semelhante se aplica à camada DBMS dos sistemas SAP NetWeaver, S/4HANA ou Hybris. Você implanta a camada DBMS em um modo ativo/passivo com uma solução de cluster de failover para proteger contra falhas de software ou infraestrutura. A solução de cluster de failover pode ser uma estrutura de failover específica de DBMS, clustering de failover do Windows Server ou pacemaker.

Para implantar a mesma arquitetura usando Zonas de Disponibilidade do Azure, você precisa fazer algumas alterações na arquitetura descrita anteriormente. Este artigo descreve essas alterações.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações sobre a implantação em Zonas de Disponibilidade


Considere o seguinte ao usar Zonas de Disponibilidade:

- Não há nenhuma garantia em relação às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- Zonas de Disponibilidade não são uma solução de DR ideal. Desastres naturais podem causar danos amplos em regiões mundiais, incluindo danos pesados em infra-estruturas de energia. As distâncias entre várias zonas podem não ser grandes o suficiente para constituir uma solução de DR adequada.
- A latência de rede em Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, você pode implantar e executar a camada de aplicativo SAP em diferentes zonas, pois a latência de rede de uma zona para a VM do DBMS ativo é aceitável. Mas, em algumas regiões do Azure, a latência entre a VM do DBMS ativo e a instância do aplicativo SAP, quando implantada em diferentes zonas, pode não ser aceitável para processos de negócios do SAP. Nesses casos, a arquitetura de implantação precisa ser diferente, com uma arquitetura ativa/ativa para o aplicativo ou uma arquitetura ativa/passiva em que a latência de rede entre zonas é muito alta.
- Ao decidir onde usar Zonas de Disponibilidade, baseie sua decisão sobre a latência de rede entre as zonas. A latência de rede desempenha um papel importante em duas áreas:
    - Latência entre as duas instâncias de DBMS que precisam ter replicação síncrona. Quanto maior a latência da rede, mais provável será que ela afete a escalabilidade da carga de trabalho.
    - A diferença na latência de rede entre uma VM que executa uma instância da caixa de diálogo SAP na zona com a instância do DBMS ativa e uma VM semelhante em outra zona. À medida que essa diferença aumenta, a influência sobre o tempo de execução dos processos de negócios e trabalhos em lotes também aumenta, dependendo se eles são executados em zona com o DBMS ou em uma zona diferente.

Quando você implanta VMs do Azure em Zonas de Disponibilidade e estabelece soluções de failover na mesma região do Azure, algumas restrições se aplicam:

- Você deve usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) ao implantar no zonas de disponibilidade do Azure. 
- O mapeamento de enumerações de zona para as zonas físicas é corrigido em uma base de assinatura do Azure. Se você estiver usando assinaturas diferentes para implantar seus sistemas SAP, precisará definir as zonas ideais para cada assinatura.
- Você não pode implantar conjuntos de disponibilidade do Azure em uma zona de disponibilidade do Azure, a menos que use o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). A maneira como você pode implantar a camada do DBMS do SAP e os serviços centrais entre as zonas e, ao mesmo tempo, implantar a camada de aplicativo SAP usando conjuntos de disponibilidade e ainda atingir a proximidade das VMs está documentada no artigo [posicionamento de proximidade do Azure Grupos para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md). Se não estiver aproveitando os grupos de posicionamento de proximidade do Azure, você precisará escolher um ou outro como uma estrutura de implantação para máquinas virtuais.
- Você não pode usar um [Load Balancer básico do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster de failover com base no clustering de failover do Windows Server ou no pacemaker do Linux. Em vez disso, você precisa usar o [SKU Standard Load Balancer do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>A combinação ideal de Zonas de Disponibilidade
Antes de decidir como usar Zonas de Disponibilidade, você precisa determinar:

- A latência de rede entre as três zonas de uma região do Azure. Isso permitirá que você escolha as zonas com a menor latência de rede no tráfego de rede entre zonas.
- A diferença entre a latência de VM para VM dentro de uma das zonas, de sua escolha e a latência de rede em duas zonas de sua escolha.
- Uma determinação de se os tipos de VM que você precisa implantar estão disponíveis nas duas zonas que você selecionou. Com algumas VMs, especialmente as VMs da série M, você pode encontrar situações em que algumas SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência de rede entre e dentro de zonas
Para determinar a latência entre as diferentes zonas, você precisa:

- Implante a SKU da VM que você deseja usar para a instância do DBMS em todas as três zonas. Verifique se a [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada quando você faz essa medição.
- Quando você encontrar as duas zonas com a menor latência de rede, implante outras três VMs da SKU de VM que você deseja usar como a VM da camada de aplicativo entre as três Zonas de Disponibilidade. Meça a latência de rede em relação às duas VMs DBMS nas duas zonas DBMS que você selecionou. 
- Use **niping** como uma ferramenta de medição. Essa ferramenta, do SAP, é descrita nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona nos caminhos de código de rede acelerada do Azure, não recomendamos que você o use.

Com base em suas medições e na disponibilidade de suas SKUs de VM no Zonas de Disponibilidade, você precisa tomar algumas decisões:

- Defina as zonas ideais para a camada DBMS.
- Determine se você deseja distribuir sua camada de aplicativo SAP ativa entre uma, duas ou todas as três zonas, com base nas diferenças de latência de rede na zona versus entre zonas.
- Determine se você deseja implantar uma configuração ativa/passiva ou uma configuração ativa/ativa, de um ponto de vista do aplicativo. (Essas configurações são explicadas posteriormente neste artigo.)

Ao tomar essas decisões, também leve em conta as recomendações de latência de rede do SAP, conforme documentado no SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medidas e decisões que você faz são válidas para a assinatura do Azure que você usou quando realizou as medições. Se você usar outra assinatura do Azure, precisará repetir as medidas. O mapeamento de zonas enumeradas pode ser diferente para outra assinatura do Azure.


> [!IMPORTANT]
> Espera-se que as medidas descritas anteriormente forneçam resultados diferentes em todas as regiões do Azure que dão suporte a [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Mesmo que os requisitos de latência de rede sejam os mesmos, talvez seja necessário adotar diferentes estratégias de implantação em diferentes regiões do Azure, pois a latência de rede entre as zonas pode ser diferente. Em algumas regiões do Azure, a latência de rede entre as três zonas diferentes pode ser imensamente diferente. Em outras regiões, a latência de rede entre as três zonas diferentes pode ser mais uniforme. A declaração de que há sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência de rede entre Zonas de Disponibilidade nas regiões do Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Essa arquitetura de implantação é chamada ativa/ativa porque você implanta seus servidores de aplicativos SAP ativos em duas ou três zonas. A instância do SAP central Services que usa a replicação de enfileiramento será implantada entre duas zonas. O mesmo é verdadeiro para a camada DBMS, que será implantada nas mesmas zonas que o serviço SAP central.

Ao considerar essa configuração, você precisa encontrar as duas Zonas de Disponibilidade em sua região que oferecem latência de rede entre zonas aceitável para sua carga de trabalho e a replicação de DBMS síncrona. Você também deve ter certeza de que o Delta entre a latência de rede dentro das zonas selecionadas e a latência de rede entre zonas não é muito grande. Isso ocorre porque você não deseja grandes variações, dependendo se um trabalho é executado em uma zona com o servidor DBMS ou entre zonas, nos tempos de execução de seus processos de negócios ou trabalhos em lotes. Algumas variações são aceitáveis, mas não fatores de diferença.

Um esquema simplificado de uma implantação ativa/ativa em duas zonas poderia ser assim:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não usando o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), você trata o zonas de disponibilidade do Azure como domínios de falha e atualização para todas as VMs porque os conjuntos de disponibilidade não podem ser implantados no zonas de disponibilidade do Azure.
- Se você quiser combinar implantações zonais para a camada DBMS e os serviços centrais, mas quiser usar os conjuntos de disponibilidade do Azure para a camada de aplicativo, será necessário usar os grupos de proximidade do Azure, conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para o ideal latência de rede com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Para os balanceadores de carga dos clusters de failover dos serviços centrais do SAP e da camada DBMS, você precisa usar o [Azure Load BALANCER SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funcionará entre zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP, junto com suas sub-redes, é ampliada entre as zonas. Você não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados para implantações zonais.
- O armazenamento Premium do Azure e o [armazenamento de SSD ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou serviços centrais do SAP) deve replicar dados importantes.
- O mesmo é verdadeiro para o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que Replica esses discos compartilhados ou compartilhamentos entre as zonas. Essas tecnologias têm suporte:
  - Para o Windows, uma solução de cluster que usa o SIOS datakeeper, conforme documentado em [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [alta disponibilidade para NFS em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Atualmente, a solução que usa o servidor de arquivos de escalabilidade horizontal da Microsoft, conforme documentado em [preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e o compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não tem suporte entre zonas.
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster SuSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.
- Para obter a consistência do tempo de execução para processos comerciais críticos, você pode tentar direcionar determinados trabalhos e usuários do lote para instâncias de aplicativo que estão em zona com a instância do DBMS ativa usando grupos de servidores do lote SAP, grupos de logon SAP ou grupos de RFC. No entanto, no caso de um failover de zonal, você precisaria mover manualmente esses grupos para instâncias em execução em VMs que estão na zona com a VM do BD ativo.  
- Talvez você queira implantar as instâncias de diálogo inativos em cada uma das zonas. Isso é para habilitar um retorno imediato à capacidade do recurso anterior se uma zona usada por parte de suas instâncias do aplicativo estiver fora do serviço.


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Se você não encontrar um Delta aceitável entre a latência de rede em uma zona e a latência de tráfego de rede entre zonas, poderá implantar uma arquitetura que tenha um caractere ativo/passivo do ponto de vista da camada de aplicativo SAP. Você define uma zona *ativa* , que é a zona em que você implanta a camada de aplicativo completa e em que você tenta executar o DBMS ativo e a instância dos serviços centrais do SAP. Com essa configuração, você precisa ter certeza de que não tem variações de tempo de execução extremas, dependendo se um trabalho é executado em uma zona com a instância de DBMS ativa ou não, em transações de negócios e trabalhos em lotes.

O layout básico da arquitetura tem esta aparência:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Os conjuntos de disponibilidade não podem ser implantados no Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, você precisa monitorar o status de forma minuciosa e tentar manter as instâncias ativas DBMS e serviços centrais do SAP na mesma zona que a camada de aplicativo implantada. No caso de um failover do serviço SAP central ou da instância do DBMS, você deseja garantir que você possa fazer failback manualmente na zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Para os balanceadores de carga dos clusters de failover dos serviços centrais do SAP e da camada DBMS, você precisa usar o [Azure Load BALANCER SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funcionará entre zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP, junto com suas sub-redes, é ampliada entre as zonas. Você não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados para implantações zonais.
- O armazenamento Premium do Azure e o [armazenamento de SSD ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou serviços centrais do SAP) deve replicar dados importantes.
- O mesmo é verdadeiro para o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que Replica esses discos compartilhados ou compartilhamentos entre as zonas. Essas tecnologias têm suporte:
    - Para o Windows, uma solução de cluster que usa o SIOS datakeeper, conforme documentado em [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [alta disponibilidade para NFS em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Atualmente, a solução que usa o servidor de arquivos de escalabilidade horizontal da Microsoft, conforme documentado em [preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e o compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não tem suporte entre zonas.
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster SuSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.
- Você deve implantar VMs inativas na zona passiva (de um ponto de vista DBMS) para poder iniciar recursos do aplicativo no caso de uma falha de zona.
    - No momento, o [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) não pode replicar VMs ativas para VMs inativas entre zonas. 
- Você deve investir na automação que permite, no caso de uma falha de zona, iniciar automaticamente a camada de aplicativo SAP na segunda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuração de recuperação de desastre e alta disponibilidade combinada
A Microsoft não compartilha nenhuma informação sobre distâncias geográficas entre os recursos que hospedam diferentes Zonas de Disponibilidade do Azure em uma região do Azure. Ainda assim, alguns clientes estão usando zonas para uma configuração combinada de HA e DR que promete um RPO (objetivo de ponto de recuperação) de zero. Isso significa que você não deve perder nenhuma transação de banco de dados confirmada mesmo no caso de recuperação de desastre. 

> [!NOTE]
> Recomendamos que você use uma configuração como esta somente em determinadas circunstâncias. Por exemplo, você pode usá-lo quando os dados não podem sair da região do Azure por motivos de segurança ou conformidade. 

Veja um exemplo de como essa configuração pode parecer:

![DR de alta disponibilidade combinada em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

As seguintes considerações se aplicam a essa configuração:

- Você está presumindo que há uma distância significativa entre as instalações que hospedam uma zona de disponibilidade ou que você é forçado a permanecer dentro de uma determinada região do Azure. Os conjuntos de disponibilidade não podem ser implantados no Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, você precisa monitorar o status de forma minuciosa e tentar manter as instâncias ativas DBMS e serviços centrais do SAP na mesma zona que a camada de aplicativo implantada. No caso de um failover do serviço SAP central ou da instância do DBMS, você deseja garantir que você possa fazer failback manualmente na zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Você deve ter instâncias do aplicativo de produção pré-instaladas nas VMs que executam as instâncias de aplicativo de QA ativas.
- No caso de uma falha de zona, desligue as instâncias do aplicativo de QA e inicie as instâncias de produção em vez disso. Observe que você precisa usar nomes virtuais para que as instâncias do aplicativo façam esse trabalho.
- Para os balanceadores de carga dos clusters de failover dos serviços centrais do SAP e da camada DBMS, você precisa usar o [Azure Load BALANCER SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funcionará entre zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP, junto com suas sub-redes, é ampliada entre as zonas. Você não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados para implantações zonais.
- O armazenamento Premium do Azure e o [armazenamento de SSD ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou serviços centrais do SAP) deve replicar dados importantes.
- O mesmo é verdadeiro para o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que Replica esses discos compartilhados ou compartilhamentos entre as zonas. Essas tecnologias têm suporte:
    - Para o Windows, uma solução de cluster que usa o SIOS datakeeper, conforme documentado em [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [alta disponibilidade para NFS em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Atualmente, a solução que usa o servidor de arquivos de escalabilidade horizontal da Microsoft, conforme documentado em [preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e o compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), não tem suporte entre zonas.
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster SuSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.





## <a name="next-steps"></a>Passos Seguintes
Aqui estão algumas próximas etapas para a implantação em Zonas de Disponibilidade do Azure:

- [Cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






