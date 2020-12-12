---
title: Casos de agrupamento de falhas
description: Saiba mais sobre casos de cluster failover (FCIs) com SQL Server em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 33be57832d9364b859042cd38349c2437bcfcb18
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358151"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Instâncias de cluster de failover com servidor SQL em Máquinas Virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo introduz diferenças de funcionalidades quando está a trabalhar com instâncias de cluster failover (FCI) para SQL Server em Azure Virtual Machines (VMs). 

## <a name="overview"></a>Descrição geral

O SQL Server em VMs Azure utiliza a funcionalidade de Clustering failover do Servidor do Windows (WSFC) para fornecer alta disponibilidade local através de redundância ao nível do servidor: uma instância de cluster de failover. Um FCI é uma única instância do SQL Server que é instalado através de nós WSFC (ou simplesmente cluster) e, possivelmente, através de várias sub-redes. Na rede, um FCI parece ser um exemplo de SQL Server a funcionar num único computador. Mas o FCI fornece failover de um nó WSFC para outro se o nó atual ficar indisponível.

O resto do artigo centra-se nas diferenças para casos de cluster de failover quando são usados com SQL Server em VMs Azure. Para saber mais sobre a tecnologia de clustering failover, consulte: 

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quórum

Casos de cluster de failover com SQL Server em Azure Virtual Machines suportam usando uma testemunha de disco, uma testemunha em nuvem ou uma testemunha de partilha de ficheiros para o quórum do cluster.

Para saber mais, consulte as [melhores práticas do Quorum com VMs sql server em Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Armazenamento

Em ambientes tradicionais agrupados no local, um cluster de failover do Windows utiliza uma rede de área de armazenamento (SAN) acessível por ambos os nós como o armazenamento partilhado. Os ficheiros SQL Server estão alojados no armazenamento partilhado, e apenas o nó ativo pode aceder aos ficheiros de uma só vez. 

O SQL Server em VMs Azure oferece várias opções como solução de armazenamento partilhado para uma implementação de instâncias de cluster de failover do SQL Server: 

||[Discos partilhados do Azure](../../../virtual-machines/disks-shared.md)|[Ações de ficheiros premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Espaços de armazenamento Direto (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Versão mínima do SO**| Todos |Windows Server 2012|Windows Server 2016|
|**Versão mínima do SqL Server**|Todos|SQL Server 2012|SQL Server 2016|
|**Disponibilidade de VM suportada** |Conjuntos de disponibilidade com grupos de colocação de proximidade (Para Premium SSD) </br> Mesma zona de disponibilidade (Para Ultra SSD) |Conjuntos de disponibilidade e zonas de disponibilidade|Conjuntos de disponibilidade |
|**Suporta FileStream**|Sim|Não|Sim |
|**Cache de bolha de Azure**|Não|Não|Sim|

O resto desta secção lista os benefícios e limitações de cada opção de armazenamento disponível para O SQL Server em VMs Azure. 

### <a name="azure-shared-disks"></a>Discos partilhados do Azure

[Os discos partilhados Azure](../../../virtual-machines/disks-shared.md) são uma característica dos [discos geridos pela Azure.](../../../virtual-machines/managed-disks-overview.md) O Windows Server Failover Clustering suporta a utilização de discos partilhados do Azure com uma instância de cluster de falha. 

**Os suportados**: Todos   
**Versão SQL suportada**: Todos     

**Benefícios:** 
- Útil para aplicações que procuram migrar para Azure, mantendo a sua arquitetura de alta disponibilidade e recuperação de desastres (HADR) como está. 
- Pode migrar aplicações agrupadas para Azure, como é devido ao suporte de Reservas Persistentes SCSI (SCSI PR). 
- Suporta armazenamento Azure Premium SSD e Azure Ultra Disk.
- Pode usar um único disco partilhado ou riscar vários discos partilhados para criar uma piscina de armazenamento partilhada. 
- Suporta o Filestream.
- Conjuntos de disponibilidade de suporte Premium SSDs. 


**Limitações:** 
- Recomenda-se colocar as máquinas virtuais no mesmo conjunto de disponibilidade e grupo de colocação de proximidade.
- Os discos ultra não suportam conjuntos de disponibilidade. 
- As zonas de disponibilidade são suportadas para Discos Ultra, mas os VMs devem estar na mesma zona de disponibilidade, o que reduz a disponibilidade da máquina virtual. 
- Independentemente da solução de disponibilidade de hardware escolhida, a disponibilidade do cluster failover é sempre de 99,9% quando se utiliza Discos Partilhados Azure. 
- O cache de disco SSD premium não é suportado.

 
Para começar, consulte o [exemplo de cluster failover do SQL Server com discos partilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Direto de Espaços de Armazenamento

[Storage Spaces Direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) é uma funcionalidade do Windows Server que é suportada com agrupamento de falhas em Máquinas Virtuais Azure. Fornece uma SAN virtual baseada em software.

**SISTEMA Suportado**: Windows Server 2016 e posteriormente   
**Versão SQL suportada**: SQL Server 2016 e posterior   


**Benefícios:** 
- A largura de banda de rede suficiente permite uma solução de armazenamento partilhado robusto e altamente performante. 
- Suporta cache de bolha azure, para que as leituras possam ser servidas localmente a partir da cache. (As atualizações são replicadas simultaneamente em ambos os nós.) 
- Suporta o FileStream. 

**Limitações:**
- Disponível apenas para Windows Server 2016 e posteriormente. 
- As zonas de disponibilidade não são suportadas.
- Requer a mesma capacidade de disco ligada a ambas as máquinas virtuais. 
- A largura de banda de alta rede é necessária para obter um alto desempenho devido à replicação contínua do disco. 
- Requer um tamanho VM maior e pagamento duplo para armazenamento, porque o armazenamento é anexado a cada VM. 

Para começar, consulte a [instância de cluster failover do SQL Server com espaços de armazenamento direto.](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 

### <a name="premium-file-share"></a>Partilha de ficheiros premium

[As ações de ficheiros premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) são uma característica dos [Ficheiros Azure.](../../../storage/files/index.yml) As ações de ficheiros premium são apoiadas por SSD e têm uma latência consistentemente baixa. São totalmente suportados para utilização com casos de cluster failover para SQL Server 2012 ou mais tarde no Windows Server 2012 ou mais tarde. As ações de ficheiros premium dão-lhe maior flexibilidade, pois pode redimensionar e escalar uma partilha de ficheiros sem qualquer tempo de inatividade.

**SISTEMA Suportado**: Windows Server 2012 e posteriormente   
**Versão SQL suportada**: SQL Server 2012 e mais tarde   

**Benefícios:** 
- Apenas solução de armazenamento partilhada para máquinas virtuais espalhadas por várias zonas de disponibilidade. 
- Sistema de ficheiros totalmente gerido com latências de um dígito e desempenho de I/S resmado. 

**Limitações:**
- Disponível apenas para Windows Server 2012 e posteriormente. 
- O FileStream não é suportado. 


Para começar, consulte a [instância de cluster failover do SQL Server com a partilha de ficheiros Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Parceiro

Existem soluções de agrupamento de parceiros com armazenamento suportado. 

**Os suportados**: Todos   
**Versão SQL suportada**: Todos   

Um exemplo utiliza o SIOS DataKeeper como o armazenamento. Para obter mais informações, consulte o clustering failover de entrada no blog [e sios DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI e ExpressRoute

Também pode expor um armazenamento de bloco compartilhado iSCSI através do Azure ExpressRoute. 

**Os suportados**: Todos   
**Versão SQL suportada**: Todos   

Por exemplo, o NetApp Private Storage (NPS) expõe um alvo iSCSI via ExpressRoute com Equinix a Azure VMs.

Para soluções de armazenamento partilhado e replicação de dados dos parceiros da Microsoft, contacte o fornecedor para quaisquer problemas relacionados com o acesso a dados sobre falha.

## <a name="connectivity"></a>Conectividade

Casos de cluster de failover com SQL Server em Azure Virtual Machines usam um [nome de rede distribuído (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou um nome de rede virtual [(VNN) com Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) para encaminhar o tráfego para a instância do SQL Server, independentemente do qual o nó detém atualmente os recursos agrupados. Existem considerações adicionais ao utilizar certas funcionalidades e o DNN com um SQL Server FCI. Consulte [a interoperabilidade do DNN com o SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) para saber mais. 

Para obter mais detalhes sobre as opções de conectividade do cluster, consulte [as ligações Route HADR ao SQL Server em VMs Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

Considere as seguintes limitações para casos de cluster de failover com SQL Server em Azure Virtual Machines. 

### <a name="lightweight-extension-support"></a>Suporte de extensão leve   

Neste momento, as instâncias de cluster de failover do SQL Server nas máquinas virtuais Azure são suportadas apenas com o modo de [gestão leve](sql-server-iaas-agent-extension-automate-management.md#management-modes) da Extensão do Agente IAAS do SQL Server. Para mudar do modo de extensão total para leve, elimine o recurso **de máquina virtual SQL** para os VMs correspondentes e registe-os com a extensão SQL IaaS Agent em modo leve. Quando estiver a eliminar o recurso da **máquina virtual SQL** utilizando o portal Azure, limpe a caixa de verificação ao lado da máquina virtual correta para evitar a eliminação da máquina virtual. 

A extensão completa suporta funcionalidades como backup automatizado, patching e gestão avançada do portal. Estas funcionalidades não funcionarão para VMs sql server registados em modo de gestão leve.

### <a name="msdtc"></a>MSDTC 

As Máquinas Virtuais Azure suportam o Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com armazenamento em Volumes Compartilhados Agrupados (CSV) e [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) ou em VMs do SERVIDOR SQL que estão a usar discos partilhados Azure. 

Nas Máquinas Virtuais Azure, o MSDTC não é suportado para Windows Server 2016 ou anteriormente com volumes compartilhados agrupados porque:

- O recurso MSDTC agrupado não pode ser configurado para usar armazenamento partilhado. No Windows Server 2016, se criar um recurso MSDTC, não apresentará nenhum armazenamento partilhado disponível para utilização, mesmo que o armazenamento esteja disponível. Este problema foi corrigido no Windows Server 2019.
- O equilibrador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Passos seguintes

Rever [as melhores práticas](hadr-cluster-best-practices.md)do cluster, e depois preparar [o seu SQL Server VM para a FCI](failover-cluster-instance-prepare-vm.md). 

Para obter mais informações, veja: 

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)