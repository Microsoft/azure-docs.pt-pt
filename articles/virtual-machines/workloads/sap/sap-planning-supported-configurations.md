---
title: 'SAP on Azure: Cenários apoiados com VMs Azure'
description: Azure Virtual Machines apoiou cenários com carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad1567a3a6cba2c2fbc519ffe5d384aba25ab51d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88648994"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carga de trabalho SAP em cenários de máquinas virtuais do Azure suportados
Projetar a arquitetura de sistemas SAP NetWeaver, Business one `Hybris` ou S/4HANA em Azure abre uma série de oportunidades diferentes para várias arquiteturas e ferramentas para usar para chegar a uma implementação escalável, eficiente e altamente disponível. Embora dependente do sistema operativo ou DBMS utilizado, existem restrições. Além disso, nem todos os cenários que são apoiados no local são apoiados da mesma forma em Azure. Este documento conduzirá através das configurações de não alta disponibilidade suportadas e configurações e arquiteturas de alta disponibilidade utilizando exclusivamente VMs Azure. Para cenários apoiados com [HANA Large Instances](./hana-overview-architecture.md), consulte o artigo [Cenários suportados para HANA Grandes Instâncias](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>Configuração de 2 níveis
Uma configuração SAP 2-Tier é considerada como sendo construída a partir de uma camada combinada do DBMS SAP e camada de aplicação que funciona no mesmo servidor ou unidade VM. O segundo nível é considerado a camada de interface do utilizador. No caso de uma configuração de 2 Níveis, a camada de aplicação DBMS e SAP partilham os recursos do VM Azure. Como resultado, é necessário configurar os diferentes componentes de uma forma que estes não concorram por recursos. Também é preciso ter cuidado para não subscrever excessivamente os recursos do VM. Tal configuração não proporciona qualquer elevada disponibilidade, para além dos acordos de Nível de [Serviço Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes Azure envolvidos.

Uma representação gráfica de tal configuração pode parecer:

![Configuração simples de 2 níveis](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Estas configurações são suportadas com Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS do SQL Server, Oracle, Db2, maxDB e SAP ASE para casos de produção e não produção. Para o SAP HANA como DBMS, este tipo de configurações é suportado apenas para casos de não produção. Isto inclui também o caso de implantação de [Azure HANA Large Instances.](./hana-overview-architecture.md)
Para todas as combinações OS/DBMS suportadas no Azure, este tipo de configuração é suportado. No entanto, é obrigatório definir a configuração dos componentes DBMS e SAP de forma a que os componentes DBMS e SAP não concorram para a memória e os recursos da CPU e, assim, excedam os recursos físicos disponíveis. Isto tem de ser feito restringindo a memória que o DBMS pode atribuir. Também é necessário limitar a Memória Estendida SAP em instâncias de aplicação. É também necessário monitorizar o consumo de CPU do VM em geral para garantir que os componentes não maximizam os recursos da CPU. 

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito mais tarde neste documento


## <a name="3-tier-configuration"></a>Configuração de 3 níveis
Nestas configurações, separa-se a camada de aplicação SAP e a camada DBMS em VMs diferentes. Normalmente faz-se isso por sistemas maiores e por razões de ser mais flexível nos recursos da camada de aplicação SAP. Na configuração mais simples, não há alta disponibilidade para além dos acordos do Nível de [Serviço Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes Azure envolvidos. 

A representação gráfica parece:

![Configuração simples de 2 níveis](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Este tipo de configuração é suportado em Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS de SQL Server, Oracle, Db2, SAP HANA, maxDB e SAP ASE para casos de produção e não produção. Esta é a configuração de implementação padrão para [Azure HANA Grandes Instâncias](./hana-overview-architecture.md). Para a simplificação, não distinguimos entre os serviços centrais SAP e os casos de diálogo SAP na camada de aplicação SAP. Nesta configuração simples de 3 Níveis, não haveria uma proteção de alta disponibilidade para os Serviços Centrais SAP.

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito mais tarde neste documento


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Múltiplas instâncias DBMS por unidade VM ou HANA Large Instance
Neste tipo de configuração, hospeda-se várias instâncias DBMS por unidade Azure VM ou HANA Large Instance. A motivação pode ser ter menos sistemas operativos para manter e com que custos reduzidos. Outras motivações podem ser ter mais flexibilidade e mais eficiência através da partilha de recursos de uma unidade de grande instância VM ou HANA entre várias instâncias DBMS. Até agora, estas configurações aparecendo principalmente para sistemas não produtivos.

Uma configuração como esta pode parecer:

![Múltiplas instâncias DBMS numa unidade](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Este tipo de implantação DBMS é suportado para:

- SQL Server no Windows
- IBM Db2. Encontre detalhes no artigo [Múltiplas instâncias (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Para o Oráculo. Para mais detalhes consulte [a nota de suporte da SAP #1778431](https://launchpad.support.sap.com/#/notes/1778431) e notas SAP relacionadas
- Para o SAP HANA, são suportadas múltiplas instâncias num VM, o SAP chama este método de implantação MCOS. Para mais detalhes consulte o artigo DA SAP [Múltiplos Sistemas SAP HANA em Um Hospedeiro (MCOS)].https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Executando várias instâncias de base de dados num hospedeiro, é necessário certificar-se de que as diferentes instâncias não estão a competir por recursos e, assim, excedem os limites de recursos físicos do VM. Isto é especialmente verdade para a memória onde você precisa tapar a memória qualquer um dos casos que compartilham o VM pode alocar. Isso também pode ser verdade para os recursos da CPU que as diferentes instâncias de base de dados podem alavancar. Todos os DBMS mencionados têm configurações que permitem limitar a alocação de memória e os recursos de CPU a um nível de instância.
Para ter suporte para tal configuração para VMs Azure, espera-se que os discos ou volumes utilizados para os dados e registo/redo ficheiros das bases de dados geridas pelas diferentes instâncias sejam separados. Ou, por outras palavras, os ficheiros de registo/redo de registos de bases de dados geridos por diferentes instâncias DBMS não devem partilhar os mesmos discos ou volumes. 

A configuração do disco para HANA Large Instances é entregue configurada e é detalhada em [cenários suportados para HANA Large Instances](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito mais tarde neste documento. VMs com múltiplas instâncias DBMS não são suportados com as configurações de alta disponibilidade descritas mais tarde neste documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Múltiplas instâncias de diálogo SAP em um VM
Em muitos casos, várias instâncias de diálogo foram implementadas em servidores de metal nus ou mesmo em VMs correndo em nuvens privadas. A razão para tais configurações foi adaptar certos casos de diálogo SAP a determinadas cargas de trabalho, funcionalidade de negócio ou tipos de carga de trabalho. A razão para não isolar essas instâncias em VMs separados foi o esforço de manutenção e operações do sistema operativo. Ou, em inúmeros casos, os custos no caso de o anfitrião ou operador do VM pedir uma taxa mensal por VM operado e administrado. Em Azure, um cenário de hospedagem de múltiplos casos de diálogo SAP dentro de um único VM us suportado para fins de produção e não produção nos sistemas operativos de Windows, Red Hat, SUSE e Oracle Linux. O parâmetro de kernel SAP PHYS_MEMSIZE, disponível no Windows e nos modernos núcleos Linux, deve ser definido se várias instâncias do Servidor de Aplicações SAP estiverem em execução num único VM. É também aconselhável limitar a expansão da MEMÓRIA Estendida SAP em sistemas operativos, como o Windows onde é implementado o crescimento automático da Memória Estendida SAP. Isto pode ser feito com o parâmetro de perfil SAP `em/max_size_MB` .

Na configuração de 3-Tier onde várias instâncias de diálogo SAP são executadas dentro de VMs Azure pode parecer:

![Múltiplas instâncias DBMS numa unidade](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Para a simplificação, não distinguimos entre os serviços centrais SAP e os casos de diálogo SAP na camada de aplicação SAP. Nesta configuração simples de 3 Níveis, não haveria uma proteção de alta disponibilidade para os Serviços Centrais SAP. Para os sistemas de produção, não é aconselhável deixar os Serviços Centrais SAP desprotegidos. Para obter detalhes sobre as chamadas configurações multi-SID em torno de INSTÂNCIAS Centrais SAP e alta disponibilidade de tais configurações multi-SID, consulte secções posteriores deste documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Proteção de Alta Disponibilidade para a camada SAP DBMS
Ao procurar implantar sistemas de produção SAP, tem de considerar o tipo de configurações de alta disponibilidade. Especialmente com o SAP HANA, onde os dados precisam de ser carregados na memória antes de ser capaz de recuperar o desempenho completo e a escalabilidade, a cura do serviço Azure não é uma medida ideal para uma elevada disponibilidade. 

Em geral, a Microsoft suporta apenas configurações de alta disponibilidade e pacotes de software descritos sob a secção de carga de trabalho SAP em docs.microsoft.com. Pode ler a mesma declaração na nota sap [#1928533](https://launchpad.support.sap.com/#/notes/1928533). A Microsoft não fornecerá suporte para outros quadros de software de terceiros de alta disponibilidade que não sejam documentados pela Microsoft em conjunto com a carga de trabalho SAP. Nestes casos, o fornecedor terceiro do quadro de alta disponibilidade é a parte de apoio para a configuração de alta disponibilidade que precisa de ser contratado por si como cliente no processo de suporte. Exceções serão mencionadas neste artigo. 

Em geral, a Microsoft suporta um conjunto limitado de configurações de alta disponibilidade em VMs Azure ou unidades HANA Large Instances. Para os cenários apoiados de HANA Large Instances, leia o documento [Cenários apoiados para HANA Grandes Instâncias](./hana-supported-scenario.md).

Para os VMs Azure, as seguintes configurações de alta disponibilidade são suportadas no nível DBMS:

- Replicação do sistema SAP HANA baseada no Pacemaker Linux em SUSE e Red Hat. Consulte os artigos detalhados:
    - [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- Configurações n+m de escala SAP HANA utilizando [ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) em SUSE e Red Hat. Os detalhes estão listados nestes artigos:
    - [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- Sql Server Failover cluster baseado em Windows Scale-Out File Services. Embora a recomendação para os sistemas de produção seja usar o SQL Server Always On em vez de agrupar. O SQL Server Always On proporciona uma melhor disponibilidade utilizando um armazenamento separado. Os detalhes são descritos neste artigo: 
    - [Configure uma instância de cluster de failover do SQL Server em máquinas virtuais Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- O SQL Server Always On é suportado com o sistema operativo Windows para SQL Server em Azure. Esta é a recomendação padrão para a produção de instâncias do SQL Server no Azure. Os detalhes são descritos nestes artigos:
    - [Apresentando o SQL Server Always On availability groups em máquinas virtuais Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Configure um grupo de disponibilidade Always On em máquinas virtuais Azure em diferentes regiões.](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
    - [Configure um equilibrador de carga para um grupo de disponibilidade Always On em Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle Data Guard para Windows e Oracle Linux. Os detalhes da Oracle Linux podem ser encontrados neste artigo:
    - [Implementar a Oracle Data Guard numa máquina virtual Azure Linux](../oracle/configure-oracle-dataguard.md)
- A IBM Db2 HADR na SUSE e RHEL A documentação detalhada para suse e RHEL utilizando o Pacemaker é fornecida aqui:
    - [Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker](./dbms-guide-ha-ibm.md)
    - [Elevada disponibilidade do IBM DB2 LUW nas VMs do Azure no Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- Configuração MAX ASE e SAP MAXDB conforme detalhado nestes documentos:
    - [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](./dbms_guide_sapase.md)
    - [IMPLANTAÇÃO DE SAP MaxDB, liveCache e Servidor de Conteúdo em VMs Azure](./dbms_guide_maxdb.md)
- HANA Grandes Instâncias os cenários de alta disponibilidade são detalhados em:
    - [Cenários apoiados para HANA Large Instances- HSR com STONITH para alta disponibilidade](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Cenários suportados para HANA Large Instances - Host auto failover (1+1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Para nenhum dos cenários descritos acima, apoiamos configurações de várias instâncias DBMS numa VM. Significa que em cada um dos casos, apenas uma instância de base de dados pode ser implantada por VM e protegida com os métodos de alta disponibilidade descritos. Proteger várias instâncias DBMS sob o mesmo conjunto de falhas do Windows ou pacemaker **NÃO** é suportado neste momento. Também a Oracle Data Guard é suportada apenas por instância única por casos de implantação de VM. 

Vários sistemas de base de dados permitem hospedar várias bases de dados sob uma instância DBMS. Como no caso do SAP HANA, várias bases de dados podem ser aloiadas em vários contentores de base de dados (MDC). Nos casos em que estas configurações multi-base estão a funcionar dentro de um recurso de cluster de falha, estas configurações são suportadas. As configurações que não são suportadas são casos em que seriam necessários múltiplos recursos de cluster. Quanto às configurações em que definiria vários Grupos de Disponibilidade de Servidor SQL, sob uma instância do SQL Server.


![Configuração DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Dependentes dos sistemas DBMS an/ou operativos, componentes como o Esure load balancer podem ou não ser necessários como parte da arquitetura da solução. 

Especificamente para o maxDB, a configuração de armazenamento tem de ser diferente. No maxDB, os ficheiros de dados e registos devem ser localizados em armazenamento partilhado para configurações de alta disponibilidade. Só no caso do maxDB, o armazenamento partilhado é suportado para uma elevada disponibilidade. Para todas as outras pilhas de armazenamento separadas DBMS por nó são as únicas configurações de disco suportadas.

Outros quadros de alta disponibilidade são conhecidos por existirem e são conhecidos por funcionar em Microsoft Azure também. No entanto, a Microsoft não testou esses quadros. Se quiser construir a sua configuração de alta disponibilidade com esses quadros, terá de trabalhar com o fornecedor desse software para:

- Desenvolver uma arquitetura de implantação
- Implantação da arquitetura
- Apoio à arquitetura

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Estes aparelhos macios podem ser utilizados para criar ações NFS, que teoricamente poderiam ser utilizadas nas implementações de escala SAP HANA onde é necessário um nó de espera. Devido a várias razões, nenhum destes aparelhos macios de armazenamento é suportado para qualquer uma das implementações DBMS pela Microsoft e SAP on Azure. As utilizações de DBMS em ações SMB não são suportadas neste momento. As implementações de DBMS em ações NFS estão limitadas a ações NFS 4.1 em [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Alta Disponibilidade para Serviço Central SAP
Os Serviços Centrais SAP são um segundo ponto único de falha da sua configuração SAP. Como resultado, também teria de proteger estes processos dos Serviços Centrais. A oferta apoiada e documentada para a carga de trabalho sap diz como:

- Windows Failover Cluster Server utilizando serviços de ficheiros windows scale-out para o diretório de transportes sapmnt e global. Os detalhes são descritos no artigo:
    - [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando uma partilha de ficheiros no Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Prepare a infraestrutura Azure para a alta disponibilidade do SAP utilizando um cluster de failover do Windows e uma partilha de ficheiros para as instâncias SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows Failover Cluster Server utilizando ações SMB baseadas em [ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) para sapmnt e diretório de transporte global. Os detalhes estão listados no artigo:
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP](./high-availability-guide-windows-netapp-files-smb.md)
- Servidor de cluster de falha do Windows com base no SIOS `Datakeeper` . Apesar de documentado pela Microsoft, precisa de uma relação de suporte com o SIOS, para que possa envolver-se com o suporte sios ao utilizar esta solução. Os detalhes são descritos no artigo:
    - [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado em Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Prepare a infraestrutura Azure para o SAP HA utilizando um cluster de failover do Windows e disco partilhado para SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker no sistema operativo SUSE com a criação de uma quota NFS altamente disponível utilizando dois VMs SUSE e `drdb` para replicação de ficheiros. Os detalhes estão documentados no artigo
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](./high-availability-guide-suse.md)
    - [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Sistema operativo Pacemaker SUSE com alavancagem de ações NFS fornecidas pela [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Os detalhes são documentados em
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-suse-netapp-files.md)
- Pacemaker no sistema operativo Red Hat com a quota NFS hospedado num `glusterfs` cluster. Detalhes podem ser encontrados nos artigos
    - [Azure Virtual Machines alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` em Azure VMs on Red Hat Enterprise Linux for SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker no sistema operativo Red Hat com ações NFS hospedadas em [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). Os detalhes são descritos no artigo
    - [Azure Virtual Machines alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-rhel-netapp-files.md)

Das soluções listadas, precisa de uma relação de apoio com o SIOS para apoiar o `Datakeeper` produto e para se envolver diretamente com o SIOS em caso de problemas. Dependendo da forma como licenciou o Windows, Red Hat e/ou SUSE OS, também poderá ter um contrato de suporte com o seu fornecedor de SO para ter o apoio total das configurações de alta disponibilidade listadas.

A configuração pode muito bem ser exibida como:

![Configuração DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

No lado direito dos gráficos, são mostrados os serviços centrais SAP altamente disponíveis. Além de ter os serviços SAP Central protegidos com um quadro de cluster de failover que pode falhar em caso de problema, existe a necessidade de uma quota NFS ou SMB altamente disponível, ou um disco partilhado do Windows para garantir que o diretório de transportes sapmnt e global esteja disponível independentemente da existência de um único VM. Algumas das soluções adicionais, como o Windows Failover Cluster Server e o Pacemaker, vão necessitar de um equilibrador de carga Azure para direcionar ou redirecionar o tráfego para um nó saudável.

Na lista mostrada, não há qualquer menção ao sistema operativo Oracle Linux. A Oracle Linux não apoia o Pacemaker como uma estrutura de cluster. Se quiser implantar o seu sistema SAP no Oracle Linux e precisar de uma estrutura de alta disponibilidade para o Oracle Linux, tem de trabalhar com fornecedores de terceiros. Um dos fornecedores é o SIOS com a sua Suite de Proteção para o Linux que é apoiada pela SAP em Azure. Para mais informações leia [#1662610 - Suporte detalhes da Suite de Proteção SIOS para o Linux](https://launchpad.support.sap.com/#/notes/1662610) para mais detalhes.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Armazenamento suportado com os cenários dos Serviços Centrais SAP listados acima
Uma vez que apenas um subconjunto de tipos de armazenamento Azure estão fornecendo ações NFS ou SMB altamente disponíveis que a qualidade para o uso nos nossos cenários de cluster sapações centrais uma lista de tipos de armazenamento suportados

- O Windows Failover Cluster Server com o Windows Scale-out File Server pode ser implantado em todos os tipos de armazenamento Azure nativos, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superior em produção e IOPS.
- O Windows Failover Cluster Server com SMB em Ficheiros Azure NetApp é suportado em Ficheiros Azure NetApp. As ações da SMB nos serviços de Ficheiros Azure **NÃO** são suportadas neste momento.
- O Windows Failover Cluster Server com o disco partilhado com janelas com base no SIOS `Datakeeper` pode ser implantado em todos os tipos de armazenamento Azure nativos, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superior em produção e IOPS.
- O SUSE ou o Red Hat Pacemaker que utilizam ações da NFS em Ficheiros Azure NetApp é suportado em Ficheiros Azure NetApp. 
- O Pacemaker SUSE utilizando uma `drdb` configuração entre dois VMs é suportado utilizando tipos de armazenamento Azure nativos, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superior em produção e IOPS.
- O Pacemaker Red Hat que utiliza `glusterfs` para fornecer ações NFS é suportado utilizando tipos de armazenamento nativos Azure, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superior em produção e IOPS.

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Estes aparelhos macios podem ser utilizados para criar ações NFS ou SMB, bem como que teoricamente poderiam ser utilizados nos serviços centrais SAP agrupados. Estas soluções não são suportadas diretamente pela Microsoft para a carga de trabalho SAP. Se decidir utilizar tal solução para criar a sua quota NFS ou SMB, o suporte para a configuração do Serviço Central SAP tem de ser fornecido pelo terceiro que possui o software no aparelho macio de armazenamento.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Múltiplos serviços centrais DASE
Para reduzir o número de VMs que são necessários em grandes paisagens SAP, o SAP permite executar os serviços centrais SAP casos de vários sistemas SAP diferentes na configuração do cluster failover. Imagine casos em que tenha 30 ou mais sistemas de produção NetWeaver ou S/4HANA. Sem o agrupamento multi-SID, estas configurações exigiriam 60 ou mais VMs em 30 ou mais configurações de cluster de falha de Windows ou Pacemaker. Além dos aglomerados de falha dBMS necessários. A implantação de vários serviços centrais SAP em dois nós numa configuração de cluster de failover pode reduzir significativamente o número de VMs. No entanto, a implantação de várias instâncias de serviços SAP Central numa única configuração de cluster de dois nós também tem algumas desvantagens. Problemas em torno de um único VM na configuração do cluster aplicam-se a vários sistemas SAP. A manutenção do so convidado em execução na configuração do cluster requer mais coordenação, uma vez que vários sistemas SAP de produção múltiplas são afetados. Ferramentas como a SAP LaMa não estão a apoiar o agrupamento multi-SID no seu processo de clonagem do sistema.

No Azure, uma configuração de cluster multi-SID é suportada para o sistema operativo Windows com ENSA1 e ENSA2. A recomendação não é combinar a arquitetura mais antiga do Serviço de Replicação enqueue (ENSA1) com a nova arquitetura (ENSA2) num cluster multi-SID. Detalhes sobre tal arquitetura são documentados nos artigos

- [SAP ASCS/SCS exemplo multi-SID alta disponibilidade com Cluster de Failover do Servidor do Windows e disco partilhado em Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [SAP ASCS/SCS exemplo multi-SID alta disponibilidade com Cluster de Falha de Falha do Servidor do Windows e partilha de ficheiros no Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

Para a SUSE, um cluster multi-SID baseado no Pacemaker também é suportado. Até agora a configuração é suportada para:

- Um máximo de cinco instâncias SAP ASCS/SCS
- A arquitetura de gelo do servidor de replicação antiga (ENSA1)
- Duas configurações de cluster pacemaker de nó

A configuração é documentada em [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP multi-SID guide](./high-availability-guide-suse-multi-sid.md)

Um cluster multi-SID com o servidor de replicação enqueue schematicamente parece

![Configuração DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Cenários de escala SAP HANA
Os cenários de escala SAP HANA são suportados para um subconjunto dos VMs Azure certificados HANA como listados no diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Todos os VMs marcados com "Sim" na coluna 'Clustering' podem ser utilizados para a escala de OLAP ou S/4HANA. As configurações sem standby são suportadas com os tipos de Armazenamento Azure de: 

- Armazenamento Azure Premium, incluindo acelerador Azure Write para o volume /hana/log
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

As configurações de escala SAP HANA para OLAP ou S/4HANA com nó(s) de espera são exclusivamente suportadas com NFS partilhadas em Ficheiros Azure NetApp.

Para obter mais informações sobre configurações exatas de armazenamento com ou sem nó de espera, verifique os artigos:

- [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](./hana-vm-operations-storage.md) 
- [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Nota de suporte SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Para detalhes de HANA Large Instances suportado configurações de escala HANA, a seguinte documentação aplica-se:

- [Cenários apoiados para HANA Large Instances escala-out com standby](./hana-supported-scenario.md#scale-out-with-standby)
- [Cenários apoiados para HANA Large Instances escala-out sem standby](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Cenário de recuperação de desastres
Há uma variedade de cenários de recuperação de desastres que são apoiados. Definimos as arquiteturas de desastres como arquiteturas que devem compensar uma completa região de Azure que sai da rede. Isto significa que precisamos que o objetivo de recuperação de desastres seja uma região de Azure diferente como alvo para executar a sua paisagem SAP. Separamos métodos e configurações na camada DBMS e na camada não-DBMS. 

### <a name="dbms-layer"></a>Camada DBMS
Para a camada DBMS, são suportadas configurações utilizando os mecanismos de replicação nativa dBMS, como Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On ou HANA System Replication. É obrigatório que o fluxo de replicação nestes casos seja assíncronos, em vez de sincronizado como em cenários típicos de alta disponibilidade que são implantados dentro de uma única região de Azure. Um exemplo típico de uma configuração de recuperação de desastres DBMS suportada é descrito no artigo DISPONIBILIDADE SAP HANA em todas as [regiões de Azure](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). O segundo gráfico nessa secção descreve um cenário com HANA como um exemplo. As principais bases de dados suportadas para aplicações SAP podem ser implementadas neste cenário.

É apoiado para utilizar um VM menor como instância-alvo na região de recuperação de desastres, uma vez que a VM não experimenta todo o tráfego de carga de trabalho. Ao fazê-lo, tem de ter em conta as seguintes considerações:

- Os tipos de VM mais pequenos não permitem que muitos discos ligados do que VMs menores
- Os VMs mais pequenos têm menos rede e produção de armazenamento
- O redimensionamento entre as famílias VM pode ser um problema quando os VMs diferentes são recolhidos num Conjunto de Disponibilidade de Azure ou quando o redimensionamento deve acontecer entre a família M-Series e a família Mv2 de VMs
- CPU e consumo de memória para a área da base de dados podendo receber o fluxo de alterações com o mínimo de atraso e recursos suficientes de CPU e memória para aplicar estas alterações com o mínimo de atraso nos dados  

Mais detalhes sobre limitações de diferentes tamanhos VM podem ser encontrados [aqui](../../sizes.md) 

Outro método suportado de implantação de um alvo DR é ter uma segunda instância DBMS instalada num VM que executa uma instância DBMS não-produção de um caso SAP não produtivo. Isto pode ser um pouco mais desafiante, uma vez que precisa de descobrir o que na memória, recursos de CPU, largura de banda de rede e largura de banda de armazenamento são necessários para os casos-alvo específicos que devem funcionar como exemplo principal no cenário DR. Especialmente em HANA é altamente recomendado que você esteja configurando o caso que funciona como alvo DR em um hospedeiro compartilhado para que os dados não sejam pré-carregados na instância alvo DR.

Para os cenários DR de grande instância HANA verifique estes documentos:

- [Nó único com DR usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Nó único com DR (multiusos) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Nó único com DR (multiusos) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Alta disponibilidade com HSR e DR com replicação de armazenamento](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Escala com DR usando replicação de armazenamento](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Nó único com DR usando HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [Único nó HSR a DR (otimizado em custos)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Alta disponibilidade e recuperação de desastres com HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Alta disponibilidade e recuperação de desastres com HSR (otimizado em custos)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Escala com DR usando HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> A utilização da Recuperação do [Sítio Azure](https://azure.microsoft.com/services/site-recovery/) não foi testada para implementações DBMS sob a carga de trabalho SAP. Como resultado, não é suportado para a camada DBMS de sistemas SAP neste momento. Outros métodos de replicação por parte da Microsoft e do SAP que não estão listados não são suportados. A utilização de software de terceiros para replicar a camada DBMS de sistemas SAP entre diferentes Regiões de Azure, precisa de ser suportada pelo fornecedor do software e não será suportada através dos canais de suporte microsoft e SAP. 
 
## <a name="non-dbms-layer"></a>Camada não-DBMS
Para a camada de aplicação SAP e eventuais partilhas ou locais de armazenamento necessários, os dois principais cenários são alavancados pelos clientes:

- Os objetivos de recuperação de catástrofes na segunda região de Azure não estão a ser utilizados para qualquer produção ou não produção. Neste cenário, os VMs que funcionam como alvo de recuperação de desastres idealmente não são implantados e a imagem e alterações nas imagens da camada de aplicação SAP de produção é replicada para a região de recuperação de desastres. Uma funcionalidade que pode executar tal tarefa é [a Recuperação do Sítio Azure.](../../../site-recovery/azure-to-azure-move-overview.md) A recuperação do site Azure suporta um cenário de replicação Azure-to-Azure como este. 
- Os objetivos de recuperação de desastres são os VM que são efetivamente utilizados por sistemas não produtivos. Toda a paisagem do SAP está espalhada por duas regiões diferentes de Azure, com sistemas de produção geralmente numa região e sistemas não produtivos noutra região. Em muitas implementações de clientes, o cliente tem um sistema de não produção que é equivalente a um sistema de produção. O cliente tem instâncias de aplicação de produção pré-instaladas nos sistemas de não produção da camada de aplicação. Em caso de incumprimento, os casos de não produção seriam encerrados, os nomes virtuais dos VMs de produção transferidos para os VMs não produtivos (após a atribuição de novos endereços IP em DNS), e os casos de produção pré-instalados estão a começar

### <a name="sap-central-services-clusters"></a>Aglomerados de Serviços Centrais SAP
Os clusters de serviços centrais da SAP que estão a usar discos partilhados (Windows), ações SMB (Windows) ou NFS são um pouco mais difíceis de replicar. Do lado do Windows, a replicação do armazenamento do Windows é uma solução possível. No Linux rsync é uma solução viável.



## <a name="non-supported-scenario"></a>Cenário não apoiado
Há uma lista de cenários, que não são suportados para a carga de trabalho sap em arquiteturas Azure. **Não suportado** significa que a SAP e a Microsoft não serão capazes de suportar estas configurações e precisam de adiar para um eventual terceiro envolvido que forneceu software para estabelecer tais arquiteturas. Duas das categorias são:

- Aparelhos macios de armazenamento: Há uma série de aparelhos macios de armazenamento oferecidos no mercado Azure. Alguns dos fornecedores oferecem documentação própria sobre como usar esses aparelhos macios de armazenamento em Azure relacionados com o software SAP. O suporte a configurações ou implantações que envolvam esses aparelhos macios de armazenamento deve ser fornecido pelo vendedor desses aparelhos macios de armazenamento. Este facto manifesta-se também na [nota de apoio da SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Quadros de alta disponibilidade: Apenas o Pacemaker e o Windows Server Failover Cluster são suportados com quadros de alta disponibilidade para a carga de trabalho SAP em Azure. Como mencionado anteriormente, a solução do SIOS `Datakeeper` é descrita e documentada pela Microsoft. No entanto, os componentes do SIOS `Datakeeper` precisam de ser suportados através do SIOS como o fornecedor que fornece esses componentes. A SAP também enumerou outros quadros certificados de alta disponibilidade em várias notas SAP. Alguns deles foram certificados pelo fornecedor de terceiros para a Azure também. No entanto, o suporte para configurações que utilizam esses produtos deve ser fornecido pelo fornecedor do produto. Diferentes fornecedores têm uma integração diferente nos processos de suporte SAP. Deve esclarecer qual o processo de suporte que funciona melhor para o fornecedor em particular antes de decidir utilizar o produto nas configurações SAP implantadas no Azure.
- Os clusters de discos partilhados onde os ficheiros de base de dados residem nos discos partilhados não são suportados, com exceção do maxDB. Para todas as outras bases de dados, a solução suportada é ter locais de armazenamento separados em vez de uma partilha de SMB ou NFS ou disco partilhado para configurar cenários de alta disponibilidade

Outros cenários, que não são apoiados, são cenários como:

- Cenários de implantação que introduzam uma maior latência de rede entre o nível de aplicação SAP e o nível SAP DBMS na arquitetura comum da SAP, tal como mostrado na NetWeaver, S/4HANA e, por exemplo. `Hybris` O que está incluído:
    - Implantação de um dos níveis no local, enquanto o outro nível é implantado em Azure
    - Implantação do nível de aplicação SAP de um sistema numa região azul diferente do nível DBMS
    - Implantação de um nível em datacenters que são co-localizados em Azure e o outro nível em Azure, exceto quando tais padrões de arquitetura são fornecidos por um serviço nativo Azure
    - Implantação de aparelhos virtuais de rede entre o nível de aplicação SAP e a camada DBMS
    - Utilização do armazenamento que é hospedado em datacenters co-localizados no centro de dados Azure para o nível SAP DBMS ou diretório de transporte global SAP DBMS
    - Implantação das duas camadas com dois fornecedores de nuvem diferentes. Por exemplo, implantação do nível DBMS na Infraestrutura da Nuvem oracle e o nível de aplicação em Azure
- Configurações de cluster HANA Pacemaker multi-instância
- Configurações do Windows Cluster com discos partilhados através de SOFS ou SMB em ANF para bases de dados SAP suportadas no Windows. Em vez disso, recomendamos o uso de replicação de alta disponibilidade nativa das bases de dados específicas e usar pilhas de armazenamento separadas
- Implantação de bases de dados SAP suportadas no Linux com ficheiros de base de dados localizados em ações NFS em cima da ANF, com exceção do SAP HANA
- Implantação de Oracle DBMS em qualquer outro so convidado que não o Windows e o Oracle Linux. Consulte também [a nota de suporte da SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Cenários que não testámos e, portanto, não temos experiência com listas como:

- Recuperação do local de Azure replicando VMs de camada de DBMS. Como resultado, recomendamos aproveitar a funcionalidade de replicação assíncrona nativa da base de dados para a configuração potencial de recuperação de desastres
 

## <a name="next-steps"></a>Passos Seguintes
Leia os próximos passos no planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver](./planning-guide.md)




  