---
title: Cluster SAP ASCS/SCS instância no WSFC usando disco partilhado em Azure | Microsoft Docs
description: Aprenda a agrupar uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675349"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado em Azure

> ![SO Windows][Logo_Windows] Windows
>

O cluster de falha do Windows Server é a base de uma instalação SAP ASCS/SCS de alta disponibilidade e de DBMS no Windows.

Um cluster failover é um grupo de 1+n-independentes servidores (nós) que trabalham em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha no nó, o cluster de falha do Windows Server calcula o número de falhas que podem ocorrer e ainda mantém um cluster saudável para fornecer aplicações e serviços. Pode escolher entre diferentes modos de quórum para obter o agrupamento de falhas.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as tarefas neste artigo, reveja o seguinte artigo:

* [Azure Virtual Machines arquitetura de alta disponibilidade e cenários para SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Cluster de falha do Windows Server em Azure

O agrupamento de falhas do Windows Server com máquinas virtuais Azure requer passos de configuração adicionais. Ao construir um cluster, precisa de definir vários endereços IP e nomes de anfitriões virtuais para a instância SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Resolução de nomes em Azure e o nome de hospedeiro virtual do cluster

A plataforma cloud Azure não oferece a opção de configurar endereços IP virtuais, como endereços IP flutuantes. Precisa de uma solução alternativa para configurar um endereço IP virtual para chegar ao recurso de cluster na nuvem. 

O serviço Azure Load Balancer fornece um *equilibrador de carga interno* para o Azure. Com o equilibrador de carga interno, os clientes chegam ao cluster sobre o endereço IP virtual do cluster. 

Coloque o equilibrador de carga interno no grupo de recursos que contém os nós de cluster. Em seguida, configure todas as regras necessárias para o encaminhamento da porta utilizando as portas de sonda do esquilibrador interno de carga. Os clientes podem ligar-se através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster e o equilibrador de carga interno manuseia a porta reencaminhando para o nó ativo do cluster.

> [!IMPORTANT]
> O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.  

![Figura 1: Configuração de clustering failover do Windows no Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_Configuração de clustering de falha do Windows Server no Azure sem um disco partilhado_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA com discos partilhados de cluster
No Windows, uma instância SAP ASCS/SCS contém serviços centrais SAP, o servidor de mensagens SAP, processos de servidor de enquecimento e ficheiros de anfitriões globais SAP. Os ficheiros anfitriões globais da SAP armazenam ficheiros centrais para todo o sistema SAP.

Uma instância SAP ASCS/SCS tem os seguintes componentes:

* Serviços centrais DAE:
    * Dois processos, um servidor de mensagem e enqueue, e um \<ASCS/SCS virtual host name> , que é usado para aceder a estes dois processos.
    * Estrutura de arquivo: S:\usr\sap \\ &lt; SID &gt; \ASCS/SCS\<instance number\>


* Ficheiros de anfitriões globais DA SAP:
  * Estrutura de ficheiro: S:\usr\sap \\ &lt; SID &gt; \SYS \. ..
  * A partilha de ficheiros sapmnt, que permite o acesso a estes ficheiros S:\usr\sap \\ &lt; SID &gt; \SYS \. .. utilizando o seguinte caminho da UNC:

    \\\\<nome de anfitrião virtual ASCS/SCS \> \sapmnt \\ &lt; SID &gt; \SYS \. ..


![Figura 2: Processos, estrutura de ficheiros e partilha global de ficheiros sapmnt de um caso SAP ASCS/SCS][sap-ha-guide-figure-8001]

_Processos, estrutura de ficheiros e partilha global de ficheiros sapmnt de um caso SAP ASCS/SCS_

Numa definição de alta disponibilidade, agrupa as instâncias SAP ASCS/SCS. Utilizamos *discos partilhados agrupados* (unidade S, no nosso exemplo), para colocar os ficheiros de anfitriões globais SAP ASCS/SCS e SAP.

![Figura 3: SAP ASCS/SCS HA arquitetura com disco partilhado][sap-ha-guide-figure-8002]

_Arquitetura SAP ASCS/SCS HA com disco partilhado_


Com a replicação do servidor Enqueue 1 arquitetura:
* O mesmo \<ASCS/SCS virtual host name> é usado para aceder aos processos de servidor de mensagens SAP e enqueue, e os ficheiros anfitriões globais da SAPmnt através da partilha de ficheiros sapmnt.
* O mesmo cluster partilhado disco drive S é partilhado entre eles.  

Com a replicação do servidor Enqueue 2 arquitetura: 
* O mesmo \<ASCS/SCS virtual host name> é usado para aceder ao processo do servidor de mensagens SAP, e os ficheiros anfitriões globais SAP através da partilha de ficheiros sapmnt.
* O mesmo cluster partilhado disco drive S é partilhado entre eles.
* Há separado \<ERS virtual host name> para aceder ao processo do servidor de enqueue  

![Figura 4: SAP ASCS/SCS HA arquitetura com disco partilhado][sap-ha-guide-figure-8003]

_Arquitetura SAP ASCS/SCS HA com disco partilhado_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Servidor de replicação de disco partilhado e enqueue 

1. O disco partilhado é suportado com a replicação do servidor Enqueue 1 arquitetura, onde o servidor de replicação enqueue (ERS) é o exemplo:   

   - não está agrupado
   - usa `localhost` nome
   - é implantado em discos locais em cada um dos nós de cluster

2. O disco partilhado também é suportado com a arquitetura de replicação do servidor Enqueue 2, onde o caso do Servidor de Replicação enqueue 2 (ERS2):  

   - está agrupado
   - usa nome de anfitrião virtual/rede dedicado
   - precisa que o endereço IP do nome de anfitrião virtual ERS seja configurado no Balançador de Carga Interna Azure, além do endereço IP (A)SCS
   - é implantado em **discos locais** em cada um dos nós agrupados, portanto não há necessidade de disco compartilhado

   > [!TIP]
   > Pode encontrar mais informações sobre o Servidor de Replicação enqueue 1 e 2 (ERS1 e ERS2) aqui:  
   > [Enqueue o servidor de replicação num cluster de falha da Microsoft](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Novo replicador enqueue em ambientes de cluster failover](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Opções para disco partilhado em Azure para cargas de trabalho SAP

Existem duas opções para o disco partilhado num aglomerado de falhas de janelas em Azure:

- [Discos partilhados Azure](../../disks-shared.md) - recurso, que permite anexar o disco gerido Azure a vários VMs simultaneamente. 
- Utilizando software sios de terceiros [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) para criar um armazenamento espelhado que simula armazenamento compartilhado de cluster. 

Ao selecionar a tecnologia para o disco partilhado, tenha em mente as seguintes considerações:

**Disco compartilhado Azure para cargas de trabalho SAP**
- Permite-lhe anexar o disco gerido a Azure a vários VMs simultaneamente sem a necessidade de software adicional para manter e operar 
- Você estará operando com um único disco compartilhado Azure em um cluster de armazenamento. Isso tem impacto na fiabilidade da sua solução SAP.
- Atualmente, a única implementação suportada é com o disco Premium partilhado Azure no conjunto Availability. O Disco Partilhado Azure não é suportado na implementação zonal.     
- Certifique-se de que fornece o disco Azure Premium com um tamanho mínimo de disco, conforme especificado nas [gamas Premium SSD,](../../disks-shared.md#disk-sizes) para poder anexar simultaneamente o número exigido de VMs (normalmente 2 para o cluster de falha do Windows ASCS SAP ASCS). 
- O disco Ultra compartilhado Azure não é suportado para cargas de trabalho SAP, uma vez que não suporta a implementação em disponibilidade definida ou implementação zonal.  
 
**SIOS**
- A solução SIOS fornece replicação de dados sincronizados em tempo real entre dois discos
- Com a solução SIOS opera-se com dois discos geridos, e se utilizar conjuntos de disponibilidade ou zonas de disponibilidade, os discos geridos aterrarão em diferentes clusters de armazenamento. 
- A implantação em zonas de disponibilidade é suportada
- Requer a instalação e operação de software de terceiros, que terá de comprar adicionalmente

### <a name="shared-disk-using-azure-shared-disk"></a>Disco compartilhado usando disco compartilhado Azure

A Microsoft está a oferecer [discos partilhados Azure](../../disks-shared.md), que podem ser usados para implementar a ALTA Disponibilidade SAP ASCS/SCS com uma opção de disco partilhado.

#### <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

Atualmente pode utilizar discos SSD Azure Premium como disco partilhado Azure para a instância SAP ASCS/SCS. Estão atualmente em vigor as seguintes limitações:

-  [O disco Azure Ultra](../../disks-types.md#ultra-disk) não é suportado como Disco Partilhado Azure para cargas de trabalho SAP. Atualmente não é possível colocar VMs Azure, usando Azure Ultra Disk em Conjunto de Disponibilidade
-  [O disco Azure Shared](../../disks-shared.md) com discos SSD Premium só é suportado com VMs no Conjunto de Disponibilidade. Não é suportado na implantação de Zonas de Disponibilidade. 
-  O [maxShares,](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) valor do disco partilhado Azure, determina quantos nós de cluster podem usar o disco partilhado. Normalmente para a instância SAP ASCS/SCS, irá configurar dois nós no Windows Failover Cluster, pelo que o valor para `maxShares` deve ser definido para dois.
-  Todos os VMs do cluster SAP ASCS/SCS devem ser implantados no mesmo grupo de colocação de [proximidade Azure](../../windows/proximity-placement-groups.md).   
   Embora possa implementar VMs de cluster Windows em Conjunto de Disponibilidade com disco partilhado Azure sem PPG, o PPG garantirá uma proximidade física próxima dos discos partilhados do Azure e dos VMs do cluster, conseguindo assim uma menor latência entre os VMs e a camada de armazenamento.    

Para mais detalhes sobre as limitações para o disco partilhado Azure, reveja cuidadosamente a secção [limitações](../../disks-shared.md#limitations) da documentação do Disco Partilhado Azure.

> [!IMPORTANT]
> Ao implementar o cluster SAP ASCS/SCS Windows Failover com o disco partilhado Azure, esteja ciente de que a sua implementação estará a funcionar com um único disco partilhado num único cluster de armazenamento. A sua instância SAP ASCS/SCS seria impactada, em caso de problemas com o cluster de armazenamento, onde o disco partilhado Azure é implantado.    

> [!TIP]
> Reveja o [SAP Netweaver no guia de planeamento da Azure](./planning-guide.md) e o [guia de armazenamento Azure para cargas de trabalho SAP](./planning-guide-storage.md) para considerações importantes, ao planear a sua implantação sap.

### <a name="supported-os-versions"></a>Versões de SO suportadas

Tanto o Windows Server 2016 como o 2019 são suportados (utilize as imagens mais recentes do data center).

Recomendamos vivamente a utilização do **Datacenter 2019 do Windows Server 2019,** como:
- Windows 2019 Failover Cluster Service está ciente de Azure
- Há uma maior integração e consciencialização da Manutenção do Anfitrião Azure e uma melhor experiência através da monitorização de eventos de agenda do Azure.
- É possível utilizar o nome de rede distribuído (é a opção predefinida). Portanto, não há necessidade de ter um endereço IP dedicado para o nome da rede de cluster. Além disso, não há necessidade de configurar este endereço IP no Azure Internal Load Balancer. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Discos partilhados em Azure com SIOS DataKeeper

Outra opção para o disco partilhado é usar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula armazenamento compartilhado de cluster. A solução SIOS fornece replicação de dados sincronizados em tempo real.

Para criar um recurso de disco partilhado para um cluster:

1. Fixe um disco adicional a cada uma das máquinas virtuais numa configuração de cluster Windows.
2. Executar a Edição do Cluster SIOS DataKeeper em ambos os nós de máquinas virtuais.
3. Configure o SIOS DataKeeper Cluster Edition de modo a espelhar o conteúdo do volume adicional do disco anexado da máquina virtual de origem ao volume adicional ligado ao disco da máquina virtual alvo. SiOS DataKeeper abstrata a fonte e os volumes locais alvo, e, em seguida, apresenta-os ao cluster de failover do Windows Server como um disco partilhado.

Obtenha mais informações sobre [sios DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 5: Configuração de clustering de failover do Windows Server em Azure com sios DataKeeper][sap-ha-guide-figure-1002]

_Windows falha na configuração de clustering em Azure com sios dataKeeper_

> [!NOTE]
> Não precisa de discos partilhados para uma alta disponibilidade com alguns produtos DBMS, como o SQL Server. SQL Server AlwaysOn replica dados DBMS e regista ficheiros do disco local de um nó de cluster para o disco local de outro nó de cluster. Neste caso, a configuração do cluster do Windows não necessita de um disco partilhado.
>

## <a name="next-steps"></a>Passos seguintes

* [Prepare a infraestrutura Azure para o SAP HA utilizando um cluster de failover do Windows e disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Instale o SAP NetWeaver HA num cluster de falha do Windows e disque partilhe o disco para uma instância SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade DE SAP multi-SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md