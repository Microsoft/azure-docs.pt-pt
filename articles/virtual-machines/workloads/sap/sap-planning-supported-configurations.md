---
title: 'SAP on Azure: Cenários suportados com VMs Azure'
description: Máquinas Virtuais Azure apoiaram cenários com carga de trabalho SAP
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
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137633"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carga de trabalho SAP em cenários de máquinas virtuais do Azure suportados
Conceber a arquitetura de sistemas SAP NetWeaver, Business one, `Hybris` ou S/4HANA em Azure abre muitas oportunidades diferentes para várias arquiteturas e ferramentas para usar para chegar a uma implementação escalável, eficiente e altamente disponível. Embora dependente do sistema operativo ou DBMS utilizados, existem restrições. Além disso, nem todos os cenários que são apoiados no local são apoiados da mesma forma em Azure. Este documento irá liderar através das configurações suportadas de não alta disponibilidade e configurações e arquiteturas de alta disponibilidade usando VMs Azure exclusivamente. Para cenários suportados com [grandes instâncias HANA,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)consulte o artigo [Cenários suportados para grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>Configuração de 2 níveis
Uma configuração SAP 2-Tier é considerada para ser construída a partir de uma camada combinada do DBMS SAP e camada de aplicação que funcionam no mesmo servidor ou unidade VM. O segundo nível é considerado a camada de interface do utilizador. No caso de uma configuração de 2 níveis, a camada de aplicação DBMS e SAP partilham os recursos do VM Azure. Como resultado, é necessário configurar os diferentes componentes de uma forma que não concorra por recursos. Também precisa de ter cuidado para não subscrever demasiado os recursos do VM. Tal configuração não proporciona nenhuma grande disponibilidade, para além dos acordos de [Nível de Serviço Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes Azure envolvidos.

Uma representação gráfica de tal configuração pode parecer:

![Configuração simples de 2 camadas](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Tais configurações são suportadas com Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS de SQL Server, Oracle, Db2, maxDB e SAP ASE para casos de produção e não produção. Para o SAP HANA como DBMS, este tipo de configurações é suportado apenas para casos de não produção. Isto inclui o caso de implantação de [Grandes Instâncias Azure HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) também.
Para todas as combinações DE/DBMS suportadas no Azure, este tipo de configuração é suportado. No entanto, é obrigatório definir a configuração dos componentes DBMS e SAP de forma a que os componentes DBMS e SAP não concorram pelos recursos de memória e CPU e excedam assim os recursos físicos disponíveis. Isto tem de ser feito restringindo a memória que o DBMS está autorizado a atribuir. Também precisa de limitar o SAP Extended Memory em instâncias de aplicação. Também é necessário monitorizar o consumo de CPU do VM em geral para garantir que os componentes não estão a maximizar os recursos da CPU. 

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, como descrito mais tarde neste documento


## <a name="3-tier-configuration"></a>Configuração de 3 níveis
Nestas configurações, separa-se a camada de aplicação SAP e a camada DBMS em diferentes VMs. Normalmente faz-se isso para sistemas maiores e por razões de ser mais flexível nos recursos da camada de aplicação SAP. Na configuração mais simples, não existe uma grande disponibilidade para além dos acordos de [Nível de Serviço Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes Azure envolvidos. 

A representação gráfica parece:

![Configuração simples de 2 camadas](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Este tipo de configuração é suportado no Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS de SQL Server, Oracle, Db2, SAP HANA, maxDB e SAP ASE para casos de produção e não produção. Esta é a configuração de implementação padrão para [As Grandes Instâncias Azure HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para simplificar, não distinguimos entre os serviços centrais da SAP e os casos de diálogo SAP na camada de aplicação SAP. Nesta configuração simples de 3 Níveis, não haveria proteção de alta disponibilidade para os Serviços Centrais SAP.

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, como descrito mais tarde neste documento


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Múltiplas instâncias DBMS por VM ou unidade de grande instância HANA
Neste tipo de configuração, acolhe várias instâncias DBMS por unidade Azure VM ou HANA Large Instance. A motivação pode ser ter menos sistemas operativos para manter e com isso custos reduzidos. Outras motivações podem ser ter mais flexibilidade e mais eficiência através da partilha de recursos de uma unidade de Grande Instância VM ou HANA maior entre várias instâncias de DBMS. Até agora, estas configurações apareciam principalmente para sistemas não produtivos.

Uma configuração como esta pode parecer:

![Múltiplas instâncias de DBMS numa unidade](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Este tipo de implantação de DBMS é suportado para:

- Servidor SQL no Windows
- IBM Db2. Encontre detalhes no artigo [Várias instâncias (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Para o oráculo. Para mais detalhes consulte a nota de [suporte sap #1778431](https://launchpad.support.sap.com/#/notes/1778431) e notas SAP relacionadas
- Para o SAP HANA, são suportados vários casos num VM, o SAP chama a este método de implantação MCOS. Para mais detalhes consulte o artigo da SAP [Múltiplos Sistemas SAP HANA em Um Anfitrião (MCOS)], https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Executando várias instâncias de base de dados num hospedeiro, é necessário certificar-se de que as diferentes instâncias não estão a competir por recursos e, assim, exceder os limites de recursos físicos do VM. Isto é especialmente verdade para a memória onde você precisa tapar a memória qualquer um dos casos que partilham o VM pode alocar. Isso também pode ser verdade para os recursos da CPU que as diferentes instâncias de base de dados podem alavancar. Todos os DBMS mencionados têm configurações que permitem limitar a atribuição de memória e recursos cpu a nível de instância.
Para ter suporte para tal configuração para VMs Azure, espera-se que os discos ou volumes utilizados para os dados e registo de registo/redo das bases de dados geridas pelas diferentes instâncias sejam separados. Ou, por outras palavras, os dados ou ficheiros de registo de registo/redo de bases de dados geridos por diferentes instâncias de DBMS não devem partilhar os mesmos discos ou volumes. 

A configuração do disco para grandes instâncias HANA é entregue configurada e é detalhada em [cenários suportados para grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> Para a produção de sistemas SAP, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito mais tarde neste documento. VMs com múltiplas instâncias de DBMS não são suportados com as configurações de alta disponibilidade descritas mais tarde neste documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Múltiplas instâncias de diálogo SAP em um VM
Em muitos casos, vários casos de diálogo foram implantados em servidores de metal nus ou mesmo em VMs correndo em nuvens privadas. A razão para tais configurações foi a adaptação de certos casos de diálogo SAP a certos tipos de carga de trabalho, funcionalidade de negócio ou trabalho de carga de trabalho. A razão para não isolar esses casos em VMs separados foi o esforço de manutenção e operações do sistema operativo. Ou, em numerosos casos, os custos no caso de o anfitrião ou operador da VM pedir uma mensalidade por VM operado e administrado. No Azure, um cenário de hospedar várias instâncias de diálogo SAP dentro de um único VM que nós suportamos para fins de produção e não produção nos sistemas operativos do Windows, Red Hat, SUSE e Oracle Linux. O parâmetro de kernel SAP PHYS_MEMSIZE, disponível no Windows e nos modernos kernels Linux, deve ser definido se várias instâncias do Servidor de Aplicações SAP estiverem em execução num único VM. É também aconselhável limitar a expansão da Memória Estendida SAP em sistemas operativos, como o Windows onde é implementado o crescimento automático da Memória Estendida SAP. Isto pode ser feito com o parâmetro de perfil SAP `em/max_size_MB`.

Na configuração de 3 níveis onde várias instâncias de diálogo SAP são executadas dentro de VMs Azure pode parecer:

![Múltiplas instâncias de DBMS numa unidade](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Para simplificar, não distinguimos entre os serviços centrais da SAP e os casos de diálogo SAP na camada de aplicação SAP. Nesta configuração simples de 3 Níveis, não haveria proteção de alta disponibilidade para os Serviços Centrais SAP. Para os sistemas de produção, não é aconselhável deixar os Serviços Centrais da SAP desprotegidos. Para obter especificações sobre as chamadas configurações multi-SID em torno de Instâncias Centrais SAP e alta disponibilidade de tais configurações multi-SID, consulte secções posteriores deste documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Proteção de alta disponibilidade para a camada DBMS SAP
À medida que procura implementar sistemas de produção SAP, você precisa considerar o tipo de configurações de alta disponibilidade de espera. Especialmente com o SAP HANA, onde os dados precisam de ser carregados na memória antes de poderem recuperar o desempenho completo e a escalabilidade, a cura do serviço Azure não é uma medida ideal para uma elevada disponibilidade. 

Em geral, a Microsoft suporta apenas configurações de alta disponibilidade e pacotes de software que são descritos na secção de carga de trabalho SAP em docs.microsoft.com. Pode ler a mesma declaração na nota sap [#1928533](https://launchpad.support.sap.com/#/notes/1928533). A Microsoft não fornecerá suporte para outros quadros de software de alta disponibilidade que não sejam documentados pela Microsoft em conjunto com a carga de trabalho do SAP. Nestes casos, o fornecedor de terceiros do quadro de alta disponibilidade é o grupo de apoio para a configuração de alta disponibilidade que precisa de ser contratado por si como cliente no processo de suporte. As exceções serão mencionadas neste artigo. 

Em geral, a Microsoft suporta um conjunto limitado de configurações de alta disponibilidade em VMs Azure ou unidades HANA Large Instances. Para os cenários suportados de Grandes Instâncias HANA, leia o documento [Cenários Suportados para Grandes Instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Para os VMs Azure, as seguintes configurações de alta disponibilidade são suportadas a nível DBMS:

- Replicação do sistema SAP HANA baseada no Pacemaker Linux no SUSE e no Chapéu Vermelho. Consulte os artigos detalhados:
    - [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- Configurações n+m de escala SAP HANA utilizando [ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) em SUSE e Chapéu Vermelho. Os detalhes estão listados nestes artigos:
    - [Implemente um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- Cluster Failover do servidor SQL baseado em serviços de ficheiros Windows Scale-Out. Embora a recomendação para sistemas de produção seja usar o Servidor SQL Always On em vez de agrupar. O Servidor SQL Always On proporciona uma melhor disponibilidade utilizando armazenamento separado. Os detalhes são descritos neste artigo: 
    - [Configure uma instância de cluster de falha do Servidor SQL em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- O Servidor SQL Always On é suportado com o sistema operativo Windows para o SQL Server no Azure. Esta é a recomendação padrão para a produção de casos de Servidor SQL no Azure. Os detalhes são descritos nestes artigos:
    - [Apresentando o Servidor SQL Sempre em grupos de disponibilidade em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
    - [Configure um grupo sempre em disponibilidade em máquinas virtuais Azure em diferentes regiões.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
    - [Configure um balanceor de carga para um grupo sempre em funcionação em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Guarda de Dados Oracle para Janelas e Oracle Linux. Detalhes para oracle Linux podem ser encontrados neste artigo:
    - [Implementar a Oracle Data Guard numa máquina virtual Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR sobre SUSE e RHEL Documentação detalhada para SUSE e RHEL utilizando pacemaker é fornecida aqui:
    - [Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Alta disponibilidade de IBM Db2 LUW em VMs Azure no Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Configuração maxDB SAP ASE e SAP conforme detalhado nestes documentos:
    - [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [SAP MaxDB, liveCache e implementação de Servidor de Conteúdo em VMs Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- HANA Grandes Instâncias cenários de alta disponibilidade são detalhados em:
    - [Cenários suportados para GRANDES Instâncias HANA- HSR com STONITH para alta disponibilidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Cenários suportados para grandes instâncias HANA - Falha automática do anfitrião (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Para nenhum dos cenários acima descritos, apoiamos configurações de múltiplas instâncias de DBMS num VM. Em cada um dos casos, apenas uma instância de base de dados pode ser implantada por VM e protegida com os métodos de alta disponibilidade descritos. Proteger várias instâncias de DBMS sob o mesmo cluster de falha Windows ou Pacemaker **NÃO** é suportado neste momento. Também a Oracle Data Guard é suportada apenas por exemplo por casos de implantação vm. 

Vários sistemas de base de dados permitem alojar várias bases de dados sob uma instância DBMS. Tal como no caso do SAP HANA, várias bases de dados podem ser alojadas em vários recipientes de base de dados (MDC). Nos casos em que estas configurações multi-bases de dados estão a funcionar dentro de um recurso de cluster failover, estas configurações são suportadas. Configurações que não são suportadas são casos em que seriam necessários múltiplos recursos de cluster. Quanto a configurações em que definiria vários Grupos de Disponibilidade de ServidorEs SQL, sob uma instância do Servidor SQL.


![Configuração DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Dependente santiva do DBMS an/ou sistemas operativos, componentes como o equilíbrio de carga Azure podem ou não ser necessários como parte da arquitetura de solução. 

Especificamente para maxDB, a configuração de armazenamento tem de ser diferente. No maxDB os dados e ficheiros de registo precisam de ser localizados em armazenamento partilhado para configurações de alta disponibilidade. Apenas no caso do maxDB, o armazenamento partilhado é suportado para alta disponibilidade. Para todas as outras pilhas de armazenamento separadas DBMS por nó são as únicas configurações de disco suportadas.

Outros quadros de alta disponibilidade são conhecidos por existirem e são conhecidos por funcionar em Microsoft Azure também. No entanto, a Microsoft não testou esses quadros. Se quiser construir a sua configuração de alta disponibilidade com esses quadros, terá de trabalhar com o fornecedor desse software para:

- Desenvolver uma arquitetura de implantação
- Implantação da arquitetura
- Apoio à arquitetura

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Estes aparelhos macios podem ser utilizados para criar ações nfs também que teoricamente poderiam ser utilizados nas implementações de escala SAP HANA onde é necessário um nó de espera. Devido a várias razões, nenhum destes aparelhos macios de armazenamento é suportado para qualquer uma das implementações dbmS da Microsoft e SAP no Azure. Neste momento, as implantações de DBMS em ações SMB não são suportadas. As implementações de DBMS em ações DaFS estão limitadas a ações NFS 4.1 em [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Alta Disponibilidade para o Serviço Central SAP
O SAP Central Services é um segundo ponto de falha da sua configuração SAP. Como resultado, você precisaria proteger estes processos de Serviços Centrais também. A oferta suportada e documentada para a carga de trabalho do SAP diz como:

- Windows Failover Cluster Server utilizando serviços de ficheiros windows scale-out para diretório de transporte sapmnt e global. Os detalhes são descritos no artigo:
    - [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando uma quota de ficheiro no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Prepare a infraestrutura Azure para uma alta disponibilidade do SAP utilizando um cluster de falhas do Windows e partilha de ficheiros para instâncias SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- O Windows Failover Cluster Server utiliza a partilha De SMB com base em [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) para diretório de transporte sapmnt e global. Os detalhes estão listados no artigo:
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows Failover Cluster Server baseado no SIOS `Datakeeper`. Apesar de documentado pela Microsoft, precisa de uma relação de suporte com o SIOS, para que possa envolver-se com o suporte SIOS ao utilizar esta solução. Os detalhes são descritos no artigo:
    - [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado de cluster em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Prepare a infraestrutura Azure para SAP HA utilizando um cluster de falha do Windows e um disco partilhado para SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Pacemaker no sistema operativo SUSE com a criação de uma quota NFS altamente disponível usando dois VMs SUSE e `drdb` para replicação de ficheiros. Os detalhes estão documentados no artigo
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Pacemaker Sistema operativo SUSE com alavancagem de ações NFS fornecidas por [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). Os detalhes estão documentados em
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker no sistema operativo Red Hat com quota NFS hospedada num cluster `glusterfs`. Detalhes podem ser encontrados nos artigos
    - [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS` em VMs Azure na Red Hat Enterprise Linux para sAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker no sistema operativo Red Hat com quota NFS hospedada em [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). Os detalhes são descritos no artigo
    - [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Das soluções listadas, é necessário uma relação de apoio com o SIOS para apoiar o produto `Datakeeper` e envolver-se diretamente com a SIOS em caso de problemas. Dependendo da forma como licenciou o Windows, Red Hat e/ou SUSE OS, também pode ser obrigado a ter um contrato de suporte com o seu fornecedor de SO para ter todo o suporte das configurações de alta disponibilidade listadas.

A configuração também pode ser exibida como:

![Configuração DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Do lado direito dos gráficos, os serviços centrais sap altamente disponíveis são mostrados. Além de ter os serviços SAP Central protegidos com um quadro de cluster failover que pode falhar em caso de problema, há uma necessidade para uma participação nFS ou SMB altamente disponível, ou um disco partilhado Windows para garantir que o diretório de transporte sapmnt e global são disponível independentemente da existência de um único VM. Algumas das soluções adicionais, como o Windows Failover Cluster Server e o Pacemaker vão exigir um equilíbrio de carga Azure para direcionar ou redirecionar o tráfego para um nó saudável.

Na lista mostrada, não há qualquer menção ao sistema operativo Oracle Linux. A Oracle Linux não apoia o Pacemaker como uma estrutura de cluster. Se quiser implementar o seu sistema SAP no Oracle Linux e necessitar de um quadro de alta disponibilidade para a Oracle Linux, precisa de trabalhar com fornecedores de terceiros. Um dos fornecedores é o SIOS com a sua Suite de Proteção para Linux que é apoiada pela SAP no Azure. Para mais informações leia a nota SAP #1662610 - Detalhes de suporte para a Suíte de [Proteção SIOS para Linux](https://launchpad.support.sap.com/#/notes/1662610) para mais detalhes.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Armazenamento suportado com os cenários dos Serviços Centrais da SAP listados acima
Uma vez que apenas um subconjunto de tipos de armazenamento Azure estão a fornecer ações NFS ou SMB altamente disponíveis que a qualidade para o uso nos nossos cenários de cluster SAP Central Services uma lista de tipos de armazenamento suportados

- O Windows Failover Cluster Server com o Windows Scale-out File Server pode ser implementado em todos os tipos de armazenamento nativo do Azure, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superiores em entrada e IOPS.
- O Windows Failover Cluster Server com SMB no Azure NetApp Files é suportado em Ficheiros Azure NetApp. As ações da SMB nos serviços do Ficheiro Azure **não** são suportadas neste momento.
- O Windows Failover Cluster Server com o windows shared disk com base no SIOS `Datakeeper` pode ser implementado em todos os tipos de armazenamento nativo do Azure, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superiores em entrada e IOPS.
- SUSE ou Red Hat Pacemaker utilizando ações da NFS em Ficheiros Azure NetApp é suportado em Ficheiros Azure NetApp. 
- O SUSE Pacemaker utilizando uma configuração `drdb` entre dois VMs é suportado utilizando tipos de armazenamento azure nativo, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superiores em entrada e IOPS.
- O Red Hat Pacemaker que usa `glusterfs` para fornecer a quota nFS é suportado usando tipos de armazenamento azure nativo, exceto ficheiros Azure NetApp. No entanto, a recomendação é alavancar o Armazenamento Premium devido a acordos de nível de serviço superiores em entrada e IOPS.

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Estes aparelhos macios podem ser utilizados para criar ações NFS ou SMB que teoricamente poderiam ser usadas nos serviços centrais SAP clustered failover também. Estas soluções não são suportadas diretamente para a carga de trabalho do SAP pela Microsoft. Se decidir utilizar tal solução para criar a sua quota NFS ou SMB, o suporte para a configuração do Serviço Central SAP tem de ser fornecido pelo terceiro que detém o software no aparelho macio de armazenamento.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Clusters de falhas dos serviços centrais multi-SID SAP
Para reduzir o número de VMs que são necessários em grandes paisagens SAP, o SAP permite executar instâncias de Serviços Centrais SAP de vários sistemas SAP diferentes na configuração do cluster failover. Imagine casos em que tenha 30 ou mais sistemas de produção NetWeaver ou S/4HANA. Sem o clustering multi-SID, estas configurações exigiriam 60 ou mais VMs em 30 ou mais configurações de cluster sinuosas do Windows ou Pacemaker. Além dos clusters de failover DBMS necessários. A implantação de vários serviços centrais SAP em dois nós numa configuração de cluster failover pode reduzir significativamente o número de VMs. No entanto, a implementação de vários casos de serviços SAP Central numa única configuração de cluster de dois nós também tem algumas desvantagens. Os problemas em torno de um único VM na configuração do cluster aplicam-se a vários sistemas SAP. A manutenção no sistema operativo hóspede que funciona na configuração do cluster requer mais coordenação, uma vez que vários sistemas SAP de produção são afetados. Ferramentas como a SAP LaMa não estão a apoiar o agrupamento multi-SID no seu processo de clonagem do sistema.

No Azure, é suportada uma configuração de cluster multi-SID para o sistema operativo Windows com ENSA1 e ENSA2. Recomendação não é combinar a antiga arquitetura do Serviço de Replicação enfila (ENSA1) com a nova arquitetura (ENSA2) num cluster multi-SID. Detalhes sobre tal arquitetura são documentados nos artigos

- [SAP ASCS/SCS caso multi-SID alta disponibilidade com Clustering de Falha do Servidor windows e disco partilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [SAP ASCS/SCS caso multi-SID alta disponibilidade com Clustering de Falha do Servidor windows e partilha de ficheiros no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Para a SUSE, um cluster multi-SID baseado no Pacemaker também é suportado. Até agora a configuração é suportada para:

- Um máximo de cinco instâncias SAP ASCS/SCS
- A antiga arquitetura de gelo do servidor de replicação de fila (ENSA1)
- Duas configurações de cluster pacemaker de nó

A configuração está documentada em [alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP guia multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Um cluster multi-SID com servidor de replicação Enqueue parece esquematicamente

![Configuração DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Cenários de escala SAP HANA
Os cenários de escala SAP HANA são suportados para um subconjunto dos VMs Azure certificados pela HANA, listados no diretório de [hardware SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Todos os VMs marcados com "Sim" na coluna 'Clustering' podem ser utilizados para a escala OLAP ou S/4HANA. As configurações sem espera são suportadas com os tipos de armazenamento Azure de: 

- Armazenamento Azure Premium, incluindo acelerador Azure Write para o volume /hana/log
- [Disco ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

As configurações de escala SAP HANA para OLAP ou S/4HANA com nó de standby(s) são exclusivamente suportadas com nFS partilhado hospedado em Ficheiros Azure NetApp.

Para mais informações sobre configurações exatas de armazenamento com ou sem nó de espera, consulte os artigos:

- [Configurações de armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Nota de apoio SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Para mais detalhes sobre as grandes instâncias HANA suportadas configurações de escala HANA, aplica-se a seguinte documentação:

- [Cenários suportados para HANA Grandes Instâncias escala-out com standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Cenários suportados para HANA Grandes Instâncias escala-out sem espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Cenário de recuperação de desastres
Há uma variedade de cenários de recuperação de desastres que são apoiados. Definimos arquiteturas de desastres como arquiteturas que devem compensar uma completa região azure saindo da rede. Isto significa que precisamos que o objetivo de recuperação de desastres seja uma região azure diferente como alvo para gerir a sua paisagem SAP. Separamos métodos e configurações na camada DBMS e na camada não DBMS. 

### <a name="dbms-layer"></a>Camada DBMS
Para a camada DBMS, são suportadas configurações utilizando os mecanismos de replicação nativa DBMS, como Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On ou HANA System Replication. É obrigatório que o fluxo de replicação nestes casos seja assíncrono, em vez de sincronizado como em cenários típicos de alta disponibilidade que são implantados numa única região do Azure. Um exemplo típico de tal configuração de recuperação de desastres DBMS suportado é descrito no artigo [Disponibilidade SAP HANA em todas as regiões de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions). O segundo gráfico desta secção descreve um cenário com HANA como um exemplo. As principais bases de dados suportadas para aplicações SAP podem ser implementadas neste cenário.

É apoiado a utilização de um VM menor como exemplo-alvo na região de recuperação de catástrofes, uma vez que a VM não experimenta todo o tráfego de carga de trabalho. Ao fazê-lo, é preciso ter em mente as seguintes considerações:

- Os tipos de VM mais pequenos não permitem que muitos discos ligados do que VMs menores
- VMs menores têm menos rede e suporte de armazenamento
- Redimensionar as famílias vM pode ser um problema quando os Diferentes VMs são recolhidos em um Conjunto de Disponibilidade Azure ou quando o redimensionamento deve acontecer entre a família Série M e a família Mv2 de VMs
- CPU e consumo de memória para a instância de base de dados ser capaz de receber o fluxo de alterações com atraso mínimo e cpu suficiente e recursos de memória para aplicar estas alterações com atraso mínimo aos dados  

Mais detalhes sobre limitações de diferentes tamanhos de VM podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Outro método suportado para implementar um alvo DR é ter uma segunda instância DBMS instalada num VM que executa uma instância DBMS não-produção de uma instância SAP não-produção. Isto pode ser um pouco mais desafiante, uma vez que precisa de descobrir o que na memória, recursos cpu, largura de banda da rede e largura de banda de armazenamento é necessário para as instâncias-alvo específicas que devem funcionar como exemplo principal no cenário DR. Especialmente em HANA é altamente recomendado que esteja a configurar a instância que funciona como alvo DR num hospedeiro partilhado para que os dados não sejam pré-carregados na instância-alvo DR.

Para os cenários DE HANA Large Instance DR, verifique estes documentos:

- [Nó único com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Nó único com DR (multiuso) utilizando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Nó único com DR (multiuso) utilizando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Alta disponibilidade com HSR e DR com replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Scale-out com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Nó único com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Nó único HSR para DR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Elevada disponibilidade e recuperação de desastres com HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Elevada disponibilidade e recuperação de desastres com HSR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Scale-out com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> A [utilização](https://azure.microsoft.com/services/site-recovery/) da Recuperação do Site Azure não foi testada para implementações de DBMS sob carga de trabalho SAP. Como resultado, não é suportado para a camada DBMS dos sistemas SAP neste momento. Outros métodos de replicação por parte da Microsoft e do SAP que não estão listados não são suportados. A utilização de software de terceiros para replicar a camada DBMS de sistemas SAP entre diferentes Regiões Azure, precisa de ser suportada pelo fornecedor do software e não será suportada através dos canais de suporte da Microsoft e da SAP. 
 
## <a name="non-dbms-layer"></a>Camada não DBMS
Para a camada de aplicação SAP e eventuais partilhas ou locais de armazenamento que são necessários, os dois principais cenários são alavancados pelos clientes:

- Os objetivos de recuperação de catástrofes na segunda região de Azure não estão a ser utilizados para fins de produção ou não produção. Neste cenário, os VMs que funcionam como alvo de recuperação de desastres não são idealmente implantados e a imagem e alterações nas imagens da camada de aplicação SAP de produção são replicadas para a região de recuperação de desastres. Uma funcionalidade que pode executar tal tarefa é a Recuperação do [Site Azure.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview) A Recuperação do Site Azure suporta cenários de replicação Azure-to-Azure como este. 
- Os objetivos de recuperação de desastres são os VMs que são efetivamente utilizados por sistemas não produtivos. Toda a paisagem SAP está espalhada por duas regiões azure diferentes com sistemas de produção geralmente em uma região e sistemas de não produção em outra região. Em muitas implementações de clientes, o cliente tem um sistema de não produção que é equivalente a um sistema de produção. O cliente tem instâncias de aplicação de produção pré-instaladas nos sistemas de não produção da camada de aplicação. Em caso de falha, as instâncias de não produção seriam encerradas, os nomes virtuais das VMs de produção mudaram-se para os VMs não produtivos (após a atribuição de novos endereços IP em DNS), e as instâncias de produção pré-instaladas estão a começar

### <a name="sap-central-services-clusters"></a>Aglomerados de Serviços Centrais SAP
Os clusters dos Serviços Centrais SAP que utilizam discos partilhados (Windows), ações SMB (Windows) ou NFS são um pouco mais difíceis de replicar. Do lado do Windows, a Replicação de Armazenamento do Windows é uma solução possível. No Linux rsync é uma solução viável.



## <a name="non-supported-scenario"></a>Cenário não apoiado
Há uma lista de cenários, que não são suportados para a carga de trabalho do SAP nas arquiteturas Azure. **Não suportado** significa que o SAP e a Microsoft não serão capazes de suportar estas configurações e precisam de adiar para um eventual terceiro envolvido que forneceu software para estabelecer tais arquiteturas. Duas das categorias são:

- Armazenamento de aparelhos macios: Há uma série de aparelhos macios de armazenamento oferecidos no mercado Azure. Alguns dos fornecedores oferecem documentação própria sobre como usar esses eletrodomésticos de armazenamento em Azure relacionados com software SAP. O suporte de configurações ou implantações que envolvam tais aparelhos macios de armazenamento deve ser fornecido pelo fornecedor desses aparelhos macios de armazenamento. Este facto também se manifesta na nota de [apoio da SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Quadros de alta disponibilidade: Apenas o Pacemaker e o Windows Server Failover Cluster são suportados quadros de alta disponibilidade para carga de trabalho SAP no Azure. Como mencionado anteriormente, a solução do SIOS `Datakeeper` é descrita e documentada pela Microsoft. No entanto, os componentes do SIOS `Datakeeper` precisam de ser suportados através do SIOS como o fornecedor que fornece esses componentes. A SAP também enumerou outros quadros certificados de alta disponibilidade em várias notas SAP. Alguns deles foram certificados pelo fornecedor de terceiros para o Azure também. No entanto, o fornecedor de produtos deve fornecer suporte para configurações que utilizem esses produtos. Diferentes fornecedores têm uma integração diferente nos processos de suporte sAP. Deve esclarecer qual o processo de suporte que funciona melhor para o fornecedor em particular antes de decidir utilizar o produto em configurações SAP implantadas no Azure.
- Os clusters de discos partilhados onde os ficheiros de base de dados residem nos discos partilhados não são suportados com exceção do maxDB. Para todas as outras bases de dados, a solução suportada é ter locais de armazenamento separados em vez de uma partilha SMB ou NFS ou disco partilhado para configurar cenários de alta disponibilidade

Outros cenários, que não são suportados são cenários como:

- Cenários de implantação que introduzem uma maior latência de rede entre o nível de aplicação SAP e o nível SAP DBMS na arquitetura comum da SAP, como mostrado na NetWeaver, S/4HANA e, por exemplo, `Hybris`. Isto inclui:
    - Implantação de um dos níveis no local, enquanto o outro nível é implantado em Azure
    - Implantação do nível de aplicação SAP de um sistema numa região azure diferente do nível DBMS
    - Implantação de um nível em datacenters que são co-localizados em Azure e no outro nível em Azure, exceto quando tais padrões de arquitetura são fornecidos por um serviço nativo Azure
    - Implantação de aparelhos virtuais da rede entre o nível de aplicação SAP e a camada DBMS
    - Aproveitando o armazenamento que está hospedado em datacenters co-localizados para o centro de dados Azure para o nível DbMS SAP ou diretório de transporte global SAP
    - Implantando as duas camadas com dois diferentes fornecedores de nuvem. Por exemplo, a implantação do nível DBMS na Oracle Cloud Infrastructure e o nível de aplicação em Azure
- Configurações de cluster de pacemaker sem instâncias HANA Pacemaker
- Configurações do Cluster do Windows com discos partilhados através de SOFS ou SMB em ANF para bases de dados SAP suportadas no Windows. Em vez disso, recomendamos o uso de replicação nativa de alta disponibilidade das bases de dados específicas e usamos pilhas de armazenamento separadas
- Implantação de bases de dados SAP suportadas no Linux com ficheiros de base de dados localizados em ações da NFS em cima da ANF, com exceção do SAP HANA
- Implantação do Oracle DBMS em qualquer outro osso convidado que não o Windows e o Oracle Linux. Consulte também [a nota de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Cenários que não testámos e, portanto, não temos experiência com listas como:

- Recuperação do site Azure replicando VMs de camada DBMS. Como resultado, recomendamos alavancar a funcionalidade de replicação assíncrona nativa da base de dados para a configuração potencial de recuperação de desastres
 

## <a name="next-steps"></a>Passos Seguintes
Leia os próximos passos no planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



