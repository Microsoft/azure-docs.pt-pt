---
title: As melhores práticas de configuração do cluster
description: Saiba mais sobre as configurações suportadas do cluster quando configurar alta disponibilidade e recuperação de desastres (HADR) para SQL Server em Azure Virtual Machines, tais como quórums suportados ou opções de encaminhamento de ligação.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: e98bfbf58c179fe9df0d99e0522e5747d220ae52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317026"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Melhores práticas de configuração do cluster (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Um cluster é utilizado para alta disponibilidade e recuperação de desastres (HADR) com SQL Server em Azure Virtual Machines (VMs). 

Este artigo fornece as melhores práticas de configuração do cluster para ambos os [casos de cluster failover (FCIs)](failover-cluster-instance-overview.md) e [grupos de disponibilidade](availability-group-overview.md) quando os utiliza com SQL Server em VMs Azure. 


## <a name="networking"></a>Redes

Utilize um único NIC por servidor (nó de cluster) e uma única sub-rede. O networking Azure tem redundância física, o que torna niCs adicionais e sub-redes desnecessários num cluster de hóspedes virtuais Azure. O relatório de validação do cluster irá avisá-lo de que os nós são alcançáveis apenas numa única rede. Pode ignorar este aviso em aglomerados de failover de hóspedes virtuais da Azure.

## <a name="quorum"></a>Quórum

Embora um cluster de dois nós funcione sem um recurso de [quórum,](/windows-server/storage/storage-spaces/understand-quorum)os clientes são estritamente obrigados a usar um recurso quórum para ter suporte de produção. A validação do cluster não passará por nenhum cluster sem um recurso de quórum. 

Tecnicamente, um aglomerado de três nós pode sobreviver a uma única perda de nó (até dois nós) sem um recurso de quórum. Mas depois que o cluster está reduzido a dois nós, há o risco de os recursos agrupados ficarem offline no caso de uma perda de nó ou falha de comunicação para evitar um cenário de cérebro dividido.

Configurar um recurso de quórum permitirá que o cluster continue on-line com apenas um nó on-line.

A tabela a seguir enumera as opções de quórum disponíveis na ordem recomendada para a utilização com um Azure VM, sendo a testemunha de disco a escolha preferida: 


||[Testemunho de disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Testemunha de cloud](/windows-server/failover-clustering/deploy-cloud-witness)  |[Testemunho de partilha de ficheiros](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**SoA apoiado**| Todos |Windows Server 2016+| Todos|




### <a name="disk-witness"></a>Testemunho de disco

Uma testemunha em disco é um pequeno disco agrupado no grupo de armazenamento disponível do Cluster. Este disco está altamente disponível e pode falhar entre nós. Contém uma cópia da base de dados do cluster, com um tamanho padrão que normalmente é inferior a 1 GB. A testemunha em disco é a opção de quórum preferida para qualquer cluster que utilize Discos Partilhados Azure (ou qualquer solução de disco partilhado como SCSI compartilhado, iSCSI ou canal de fibra SAN).  Um volume partilhado agrupado não pode ser usado como testemunha de disco.

Configure um disco partilhado do Azure como testemunha do disco. 

Para começar, consulte [a Configure uma testemunha de disco.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)


**Os suportados**: Todos   


### <a name="cloud-witness"></a>Testemunha de cloud

Uma testemunha em nuvem é um tipo de testemunha de quórum de cluster que usa o Microsoft Azure para fornecer uma votação no quórum do cluster. O tamanho predefinido é de cerca de 1 MB e contém apenas o carimbo de tempo. Uma testemunha em nuvem é ideal para implantações em vários locais, várias zonas e várias regiões.

Para começar, consulte [Configure uma testemunha em nuvem.](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)


**SISTEMA Suportado**: Windows Server 2016 e posteriormente   


### <a name="file-share-witness"></a>Testemunho de partilha de ficheiros

Uma testemunha de partilha de ficheiros é uma partilha de ficheiros SMB que é tipicamente configurada num servidor de ficheiros que executa o Windows Server. Mantém informações de agrupamento num ficheiro witness.log, mas não armazena uma cópia da base de dados do cluster. Em Azure, pode configurar uma [partilha de ficheiros Azure](../../../storage/files/storage-how-to-create-file-share.md) para usar como testemunha de partilha de ficheiros, ou pode usar uma partilha de ficheiros numa máquina virtual separada.

Se vai utilizar uma partilha de ficheiros Azure, pode montá-la com o mesmo processo usado para [montar a partilha de ficheiros Premium.](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share) 

Para começar, consulte [a Configure uma testemunha de partilha de ficheiros.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)


**SISTEMA Suportado**: Windows Server 2012 e posteriormente   

## <a name="connectivity"></a>Conectividade

Num ambiente tradicional de rede no local, um exemplo de cluster de falha do SQL Server parece ser um único exemplo de SQL Server a funcionar num único computador. Como a instância do cluster de failover falha do nó para o nó, o nome de rede virtual (VNN) para o caso fornece um ponto de ligação unificado e permite que as aplicações se conectem à instância do SQL Server sem saber qual nó está atualmente ativo. Quando ocorre uma falha, o nome da rede virtual é registado no novo nó ativo após o seu início. Este processo é transparente para o cliente ou aplicação que está conectando-se ao SQL Server, e isso minimiza o tempo de inatividade que o cliente ou aplicação experimenta durante uma falha. 

Utilize um VNN com Azure Load Balancer ou um nome de rede distribuído (DNN) para encaminhar o tráfego para o VNN da instância de cluster failover com o SQL Server em Azure VMs. A funcionalidade DNN está atualmente disponível apenas para SQL Server 2019 CU2 e mais tarde numa máquina virtual do Windows Server 2016 (ou posteriormente). 

O quadro a seguir compara a capacidade de suporte à ligação HADR: 

| |**Nome de Rede Virtual (VNN)**  |**Nome de Rede Distribuída (DNN)**  |
|---------|---------|---------|
|**Versão mínima do SO**| Todos | Todos |
|**Versão mínima do SqL Server** |Todos |SQL Server 2019 CU2|
|**Solução HADR suportada** | Instância de cluster de ativação pós-falha <br/> Grupo de disponibilidade | Instância de cluster de ativação pós-falha|


### <a name="virtual-network-name-vnn"></a>Nome de Rede Virtual (VNN)

Como o ponto de acesso IP virtual funciona de forma diferente no Azure, é necessário configurar [o Azure Load Balancer](../../../load-balancer/index.yml) para encaminhar o tráfego para o endereço IP dos nós FCI. Nas máquinas virtuais Azure, um equilibrador de carga detém o endereço IP para o VNN em que os recursos do SQL Server agrupados dependem. O equilibrador de carga distribui fluxos de entrada que chegam à parte frontal e, em seguida, encaminha esse tráfego para as instâncias definidas pela piscina traseira. Configura o fluxo de tráfego utilizando regras de equilíbrio de carga e sondas de saúde. Com o SQL Server FCI, as instâncias de back-end da piscina são as máquinas virtuais Azure que executam o SQL Server. 

Há um ligeiro atraso de incumprimento quando se está a usar o equilibrador de carga, porque a sonda de saúde realiza verificações vivas a cada 10 segundos por defeito. 

Para começar, aprenda a [configurar o Azure Load Balancer para uma FCI](hadr-vnn-azure-load-balancer-configure.md). 

**Os suportados**: Todos   
**Versão SQL suportada**: Todos   
**Solução HADR suportada**: Instância de cluster de failover e grupo de disponibilidade   


### <a name="distributed-network-name-dnn"></a>Nome de Rede Distribuída (DNN)

O nome de rede distribuído é uma nova funcionalidade Azure para SQL Server 2019 CU2. O DNN fornece uma forma alternativa para os clientes do SQL Server ligarem-se à instância de cluster de falha do SQL Server sem utilizarem um equilibrador de carga. 

Quando um recurso DNN é criado, o cluster liga o nome DNS aos endereços IP de todos os nós do cluster. O cliente SQL tentará ligar-se a cada endereço IP desta lista para encontrar o nó onde a instância do cluster de failover está atualmente em execução. Pode acelerar este processo especificando `MultiSubnetFailover=True` na cadeia de ligação. Esta definição diz ao fornecedor para experimentar todos os endereços IP em paralelo, para que o cliente possa ligar-se ao FCI instantaneamente. 

Recomenda-se um nome de rede distribuído sobre um equilibrador de carga, quando possível, porque: 
- A solução de ponta a ponta é mais robusta, uma vez que já não é preciso manter o recurso do balanceador de carga. 
- A eliminação das sondas do balançador de carga minimiza a duração de falha. 
- O DNN simplifica o fornecimento e a gestão da instância de cluster failover com o SQL Server em Azure VMs. 

A maioria das funcionalidades do SQL Server funcionam de forma transparente com a FCI. Nesses casos, pode simplesmente substituir o nome DNS VNN existente pelo nome DNN DNS, ou definir o valor DNN com o nome DNS VNN existente. No entanto, alguns componentes do lado do servidor requerem um pseudónimo de rede que mapeie o nome VNN para o nome DNN. Casos específicos podem requerer a utilização explícita do nome DNN DNS, como quando está a definir certos URLs numa configuração do lado do servidor. 

Para começar, aprenda a [configurar um recurso DNN para um FCI](hadr-distributed-network-name-dnn-configure.md). 

**SISTEMA Suportado**: Windows Server 2016 e posteriormente   
**Versão SQL suportada**: SQL Server 2019 e mais tarde   
**Solução HADR suportada**: Instância de cluster de failover apenas


## <a name="limitations"></a>Limitações

Considere as seguintes limitações quando estiver a trabalhar com fci ou grupos de disponibilidade e SQL Server em Azure Virtual Machines. 

### <a name="msdtc"></a>MSDTC 

As Máquinas Virtuais Azure suportam o Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com armazenamento em Volumes Compartilhados Agrupados (CSV) e [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) ou em VMs do SERVIDOR SQL que estão a usar discos partilhados Azure. 

Nas Máquinas Virtuais Azure, o MSDTC não é suportado para Windows Server 2016 ou anteriormente com volumes compartilhados agrupados porque:

- O recurso MSDTC agrupado não pode ser configurado para usar armazenamento partilhado. No Windows Server 2016, se criar um recurso MSDTC, não apresentará nenhum armazenamento partilhado disponível para utilização, mesmo que o armazenamento esteja disponível. Este problema foi corrigido no Windows Server 2019.
- O equilibrador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Passos seguintes

Depois de ter determinado as melhores práticas adequadas para a sua solução, começa por [preparar o seu SQL Server VM para a FCI](failover-cluster-instance-prepare-vm.md). Também pode criar o seu grupo de disponibilidade utilizando os modelos [Azure CLI](availability-group-az-cli-configure.md), ou [Azure quickstart](availability-group-quickstart-template-configure.md). 

