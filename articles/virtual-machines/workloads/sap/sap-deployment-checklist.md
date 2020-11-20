---
title: Sap workload planning and deployment checklist [ Microsoft Docs
description: Lista de verificação para planear as implementações da carga de trabalho DOM para a Azure e implantar as cargas de trabalho
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac75ab31f8c9cdd9405115db4f5c35d28707e29f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950368"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Cargas de trabalho sap em Azure: lista de verificação de planeamento e implantação

Esta lista de verificação destina-se a clientes que deslocam as aplicações SAP NetWeaver, S/4HANA e Hybris para a infraestrutura Azure como um serviço. Durante toda a duração do projeto, um cliente e/ou parceiro SAP deve rever a lista de verificação. É importante notar que muitos dos cheques são concluídos no início do projeto e durante a fase de planeamento. Após a implementação, as mudanças simples na infraestrutura Azure implementada ou nas versões de software SAP podem tornar-se complexas.

Reveja a lista de verificação em marcos-chave durante o seu projeto. Ao fazê-lo, irá detetar pequenos problemas antes que se tornem grandes problemas. Também terá tempo suficiente para re-engendrar e testar as alterações necessárias. Não considere esta lista completa. Dependendo da sua situação, poderá ter de fazer muitos mais controlos.

A lista de verificação não inclui tarefas independentes do Azure. Por exemplo, as interfaces de aplicação SAP mudam durante uma mudança para a plataforma Azure ou para um fornecedor de hospedagem.

Esta lista de verificação também pode ser utilizada para sistemas que já estão implantados. Novas funcionalidades, como As Zonas de Aceleração e Disponibilidade de Escrita, e novos tipos de VM podem ter sido adicionados desde que foi implementado. Por isso, é útil rever periodicamente a lista de verificação para garantir que está a par de novas funcionalidades na plataforma Azure.

## <a name="project-preparation-and-planning-phase"></a>Fase de preparação e planeamento do projeto
Durante esta fase, planeia a migração da sua carga de trabalho SAP para a plataforma Azure. No mínimo, durante esta fase é necessário criar os seguintes documentos e definir e discutir os seguintes elementos da migração:

1. Documento de design de alto nível. Este documento deve conter:
    - O atual inventário de componentes e aplicações SAP, e um inventário de aplicações-alvo para a Azure.
    - Uma matriz de atribuição de responsabilidade (RACI) que define as responsabilidades e atribuições das partes envolvidas. Comece a um nível elevado e trabalhe para níveis mais granulares ao longo do planeamento e das primeiras implementações.
    - Uma arquitetura de solução de alto nível.
    - Uma decisão sobre a qual as regiões de Azure se desloquem. Consulte a [lista das regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Para saber quais os serviços disponíveis em cada região, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
    - Uma arquitetura de networking para ligar do local ao Azure. Comece a familiarizar-se com a [planta virtual do Datacenter para o Azure.](/azure/architecture/vdc/)
    - Princípios de segurança para a execução de dados de negócios de alto impacto em Azure. Para conhecer a segurança dos dados, comece com a documentação de segurança do [Azure.](../../../security/index.yml)
2.  Documento de design técnico. Este documento deve conter:
    - Um diagrama de bloco para a solução.
    - O dimensionamento de componentes de computação, armazenamento e rede em Azure. Para o tamanho SAP de Azure VMs, consulte [SAP) 
    -  nota https://launchpad.support.sap.com/#/notes/1928533) #1928533].
    - Continuidade de negócios e arquitetura de recuperação de desastres.
    - Informações detalhadas sobre as versões do pacote de suporte DES, DB, Kernel e SAP. Não é necessariamente verdade que todas as versões de SO suportadas por SAP NetWeaver ou S/4HANA são suportadas em VMs Azure. O mesmo se aplica aos lançamentos do DBMS. Verifique as seguintes fontes para alinhar e, se necessário, atualizar as versões SAP, as versões DBMS e os sistemas operativos para garantir o suporte SAP e Azure. É necessário ter combinações de lançamento suportadas pela SAP e pela Azure para obter o apoio total da SAP e da Microsoft. Se necessário, tem de planear a atualização de alguns componentes do software. Mais detalhes sobre o software SUPORTADO SAP, OS e DBMS são documentados aqui:
        - [Nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Esta nota define as versões mínimas de SO suportadas nos VMs Azure. Também define as versões mínimas de base de dados necessárias para a maioria das bases de dados não-HANA. Por último, fornece o tamanho SAP para os tipos de VM Azure suportados pelo SAP.
        - [Nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Esta nota define políticas de suporte em torno do armazenamento Azure e da relação de suporte necessária com a Microsoft.
        - [Nota de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Esta nota define a matriz de suporte Oráculo para Azure. A Oracle suporta apenas o Windows e o Oracle Linux como sistemas operativos convidados no Azure para cargas de trabalho SAP. Esta declaração de apoio também se aplica à camada de aplicação SAP que executa instâncias SAP. No entanto, a Oracle não suporta alta disponibilidade para serviços centrais SAP em Oracle Linux através do Pacemaker. Se necessitar de alta disponibilidade para ASCS no Oracle Linux, precisa de utilizar a Suite de Proteção SIOS para o Linux. Para obter dados detalhados da certificação SAP, consulte a nota de suporte da SAP [#1662610 - Detalhes de suporte para a Suite de Proteção SIOS para o Linux](https://launchpad.support.sap.com/#/notes/1662610). Para o Windows, a solução de cluster de failover do servidor do Windows suportada pelo SAP para os Serviços Centrais SAP é suportada em conjunto com a Oracle como a camada DBMS.
        - [Nota de suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Esta nota fornece a matriz de suporte para SAP HANA em diferentes versões de SO.
        - Os VMs Azure apoiados pela SAP HANA e as [grandes instâncias da HANA](./hana-overview-architecture.md) estão listados no site da [SAP.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        - [Matriz de disponibilidade de produto SAP](https://support.sap.com/en/).
        - [Nota de suporte SAP #2555629 - SAP HANA 2.0 Dynamic Tiering – Hipervisor e Suporte à Nuvem](https://launchpad.support.sap.com/#/notes/2555629)
        - [Nota de suporte SAP #1662610 - Detalhes de suporte para suíte de proteção SIOS para Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Notas SAP para outros produtos específicos da SAP.     
    - A utilização de configurações de cluster multi-SID para serviços centrais SAP é suportada em sistemas operativos de hóspedes Windows, SLES e RHEL em Azure. Tenha em mente que o raio de explosão pode aumentar quanto mais ASCS/SCS colocar num cluster multi-SID. Pode encontrar documentação para o respetivo cenário de OS convidados nestes artigos:
        - [SAP ASCS/SCS exemplo multi-SID alta disponibilidade com Cluster de Failover do Servidor do Windows e disco partilhado em Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [SAP ASCS/SCS exemplo multi-SID alta disponibilidade com Cluster de Falha de Falha do Servidor do Windows e partilha de ficheiros no Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP multi-SID guide](./high-availability-guide-suse-multi-sid.md)
        - [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux para aplicações SAP multi-SID guide](./high-availability-guide-rhel-multi-sid.md)
    - Alta disponibilidade e arquitetura de recuperação de desastres.
        - Com base na RTO e na RPO, defina como a arquitetura de alta disponibilidade e recuperação de desastres precisa de ser.
        - Para obter uma elevada disponibilidade dentro de uma zona, verifique o que o DBMS desejado tem para oferecer em Azure. A maioria dos pacotes DBMS oferecem métodos sincronizados de um standby quente sincronizado, que recomendamos para sistemas de produção. Verifique também a documentação relacionada com o SAP para diferentes bases de dados, começando com [considerações para a implementação de DBMS de máquinas virtuais Azure para cargas de trabalho SAP e documentos relacionados.](./dbms_guide_general.md)
           A utilização do Cluster de Failover do Servidor do Windows com uma configuração de disco partilhada para a camada DBMS, uma vez que, por exemplo, [descrita para SQL Server,](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)não é suportada. Em vez disso, utilize soluções como:
           - [SQL Server AlwaysOn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Proteção de Dados Oracle](../oracle/configure-oracle-dataguard.md)
           - [Replicação do sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Para a recuperação de desastres em todas as regiões de Azure, reveja as soluções oferecidas pelos diferentes fornecedores de DBMS. A maioria suporta replicação assíncronea ou envio de registo.
        - Para a camada de aplicação SAP, determine se irá executar os seus sistemas de teste de regressão de negócios, que idealmente são réplicas das suas implementações de produção, na mesma região de Azure ou na sua região dr. No segundo caso, pode apontar o sistema de regressão do negócio como alvo dr para as suas implementações de produção.
        - Se decidir não colocar os sistemas de não produção no site DR, procure a Recuperação do Local de Azure como um método para replicar a camada de aplicação SAP na região Azure DR. Para obter mais informações, consulte uma recuperação de [desastres configurada para uma implementação de aplicações SAP NetWeaver de vários níveis.](../../../site-recovery/site-recovery-sap.md)
        - Se decidir utilizar uma configuração HADR combinada utilizando [zonas de disponibilidade de Azure,](../../../availability-zones/az-overview.md)familiarize-se com as regiões Azure onde estão disponíveis Zonas de Disponibilidade. Tenha também em conta as restrições que podem ser introduzidas por atrasos de rede aumentados entre duas Zonas de Disponibilidade.  
3.  Um inventário de todas as interfaces SAP (SAP e não-SAP).
4.  Design de serviços de fundação. Este desenho deve incluir os seguintes itens:
    - Diretório ativo e design DNS.
    - Topologia de rede dentro de Azure e atribuição de diferentes sistemas SAP.
    - [Estrutura de controlo de acesso baseado em funções (Azure RBAC)](../../../role-based-access-control/overview.md) para equipas que gerem infraestruturas e aplicações SAP em Azure.
    - Topologia de grupo de recursos.
    - [Estratégia de marcação](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Nomear convenções para VMs e outros componentes de infraestrutura e/ou nomes lógicos.
5.  Contrato microsoft Professional ou Premier Support. Identifique o seu Gestor de Conta Técnica da Microsoft (TAM) se tiver um contrato de suporte Premier com a Microsoft. Para os requisitos de suporte SAP, consulte [a nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  O número de assinaturas Azure e quota-base para as assinaturas. [Pedidos de apoio aberto para aumentar as quotas de assinaturas Azure,](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) se necessário.
7.  Plano de redução de dados e migração de dados para a migração de dados SAP para o Azure. Para os sistemas SAP NetWeaver, o SAP tem orientações sobre como limitar o volume de grandes quantidades de dados. Consulte [este guia SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) sobre gestão de dados em sistemas ERP SAP. Alguns dos conteúdos também se aplicam aos sistemas NetWeaver e S/4HANA em geral.
8.  Uma abordagem automatizada de implantação. O objetivo da automatização das infraestruturas em Azure é implementar de forma determinística e obter resultados determinísticos. Muitos clientes usam scripts baseados em PowerShell ou CLI. Mas existem várias tecnologias de código aberto que pode usar para implantar infraestruturas Azure para SAP e até instalar software SAP. Pode encontrar exemplos no GitHub:
    - [Implementações automatizadas do SAP na nuvem de Azure](https://github.com/Azure/sap-hana)
    - [Instalação SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Defina uma cadência regular de revisão de design e implementação entre si como cliente, o integrador do sistema, a Microsoft e outras partes envolvidas.


## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (fortemente recomendada)
 
Pode executar um piloto antes ou durante o planeamento e preparação do projeto. Também pode utilizar a fase piloto para testar abordagens e desenhos feitos durante a fase de planeamento e preparação. E pode expandir a fase piloto para torná-la uma verdadeira prova de conceito.

Recomendamos que crie e valide uma solução HADR completa e um design de segurança durante uma colocação piloto. Alguns clientes realizam testes de escalabilidade durante esta fase. Outros clientes utilizam as implementações de sistemas de caixas de areia SAP como uma fase piloto. Assumimos que já identificou um sistema que quer migrar para Azure para o piloto.

1. Otimize a transferência de dados para o Azure. A escolha ideal é altamente dependente do cenário específico. A transferência de instalações através do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) é a mais rápida se o circuito ExpressRoute tiver largura de banda suficiente. Noutros cenários, a transferência através da internet é mais rápida.
2. Para uma migração heterogénea da plataforma SAP que envolve uma exportação e importação de dados, teste e otimiza as fases de exportação e importação. Para grandes migrações em que o SQL Server é a plataforma de destino, pode encontrar [recomendações.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) Pode utilizar o Migration Monitor/SWPM se não precisar de uma atualização combinada de desbloqueio. Pode utilizar o processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando combinar a migração com uma atualização de desbloqueio SAP. Para tal, é necessário cumprir determinados requisitos para a combinação da plataforma DBMS de origem e alvo. Este processo está documentado na [Opção de Migração da Base de Dados (DMO) de SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportação para fonte, upload de ficheiros de exportação para Azure e desempenho das importações. Maximizar a sobreposição entre exportação e importação.
   2.  Avaliar o volume da base de dados nas plataformas de destino e alvo para efeitos de dimensionamento de infraestruturas.
   3.  Validar e otimizar o tempo.
1. Validação técnica.
   1. Tipos de VM.
        - Reveja os recursos nas notas de suporte SAP, no diretório de hardware SAP HANA, e no SAP PAM novamente. Certifique-se de que não existem alterações nos VMs suportados para Azure, versões de SO suportadas para esses tipos de VM e lançamentos de SAP e DBMS suportados.
        - Valide novamente o dimensionamento da sua aplicação e a infraestrutura que implementa no Azure. Se estiver a mover as aplicações existentes, pode frequentemente obter os SAPS necessários da infraestrutura que utiliza e da [página de referência sap](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-la com os números SAPS listados na nota de suporte SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Tenha também em mente [este artigo sobre as classificações DO SAPS.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - Avalie e teste o dimensionamento dos seus VMS Azure no que diz respeito ao rendimento máximo de armazenamento e produção de rede dos tipos de VM que escolheu durante a fase de planeamento. Pode encontrar os dados aqui:
           -  [Tamanhos para máquinas virtuais Windows em Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar o *limite máximo de disco não-achado* para o tamanho.
           -  [Tamanhos para máquinas virtuais Linux em Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar o *limite máximo de disco não-achado* para o tamanho.
   2. Armazenamento.
        - Consulte os [tipos de armazenamento Azure para obter carga de trabalho SAP](./planning-guide-storage.md)
        - No mínimo, utilize [o armazenamento SSD Standard Azure](../../disks-types.md#standard-ssd) para VMs que representem camadas de aplicação SAP e para a implantação de DBMSs que não sejam sensíveis ao desempenho.
        - Em geral, não recomendamos a utilização de [discos HDD Standard Azure](../../disks-types.md#standard-hdd).
        - Utilize [o Azure Premium Storage](../../disks-types.md#premium-ssd) para quaisquer VMs DBMS que sejam remotamente sensíveis ao desempenho.
        - Utilize [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/).
        - Utilize o acelerador de escrita Azure para as unidades de registo DBMS com série M. Esteja atento aos limites e utilização do Acelerador de Escrita, conforme documentado no [Write Accelerator](../../how-to-enable-write-accelerator.md).
        - Para os diferentes tipos de DBMS, verifique a [documentação genérica do DBMS relacionada com](./dbms_guide_general.md) o SAP e a documentação específica do DBMS a que o documento genérico aponta.
        - Para obter mais informações sobre o SAP HANA, consulte [as configurações e operações de infraestrutura SAP HANA em Azure.](./hana-vm-operations.md)
        - Nunca monte discos de dados Azure num VM Azure Linux utilizando o ID do dispositivo. Em vez disso, utilize o identificador universalmente único (UUID). Tenha cuidado ao utilizar ferramentas gráficas para montar discos de dados Azure, por exemplo. Verifique duas vezes as entradas em /etc/fstab para se certificar de que o UUID é utilizado para montar os discos. Pode encontrar mais detalhes [neste artigo.](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Em rede.
        - Teste e avalie a sua infraestrutura de rede virtual e a distribuição das suas aplicações SAP através ou dentro das diferentes redes virtuais Azure.
        -  Avalie a abordagem da arquitetura de rede virtual hub-and-spoke ou a abordagem de microsegmentação dentro de uma única rede virtual Azure. Basear esta avaliação em:
               1. Custos de troca de dados entre [redes virtuais Azure.](../../../virtual-network/virtual-network-peering-overview.md) Para obter informações sobre os custos, consulte [os preços da Rede Virtual.](https://azure.microsoft.com/pricing/details/virtual-network/)
               2. Vantagens de uma rápida desconexão entre as redes virtuais Azure em oposição a alterar o grupo de segurança da rede para isolar uma sub-rede dentro de uma rede virtual. Esta avaliação destina-se a casos em que as aplicações ou VMs alojados numa sub-rede da rede virtual se tornaram um risco para a segurança.
                3. Registo central e auditoria do tráfego de rede entre as instalações, o mundo exterior e o centro de dados virtual que construiu em Azure.
        - Avaliar e testar o caminho de dados entre a camada de aplicação SAP e a camada SAP DBMS.
            -  A colocação de aparelhos virtuais da [rede Azure](https://azure.microsoft.com/solutions/network-appliances/) na via de comunicação entre a aplicação SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA não é suportada.
            -  A colocação da camada de aplicação SAP e do SAP DBMS em diferentes redes virtuais Azure que não são espreitadas não é suportada.
            -  Pode utilizar [regras do grupo de segurança de aplicações e do grupo de segurança](../../../virtual-network/network-security-groups-overview.md) de rede para definir rotas entre a camada de aplicação SAP e a camada SAP DBMS.
        - Certifique-se de que [o Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado nos VMs utilizados na camada de aplicação SAP e na camada SAP DBMS. Tenha em mente que diferentes níveis de SO são necessários para suportar a rede acelerada em Azure:
            - Windows Server 2012 R2 ou posterior.
            - SUSE Linux 12 SP3 ou mais tarde.
            - RHEL 7.4 ou mais tarde.
            - Oracle Linux 7.5. Se estiver a utilizar o núcleo RHCKL, é necessário soltar 3.10.0-862.13.1.1.7. Se estiver a utilizar o kernel Oracle UEK, é necessário libertar 5.
        - Testar e avaliar a latência da rede entre os VMs da camada de aplicação SAP e os VMs DBMS de acordo com as notas de suporte [SAP #500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte da SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar ao alcance moderado ou bom. As exceções aplicam-se ao tráfego entre as unidades de VMs e HANA Large Instance, conforme documentado [neste artigo.](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)
        - Certifique-se de que as implementações do ILB estão configurada para utilizar a Return do Servidor Direto. Esta definição reduzirá a latência quando os ILBs Azure forem utilizados para configurações de alta disponibilidade na camada DBMS.
        - Se estiver a utilizar o Azure Load Balancer juntamente com os sistemas operativos de hóspedes Linux, verifique se o parâmetro da rede Linux **net.ipv4.tcp_timestamps** está definido para **0**. Esta recomendação entra em conflito com recomendações em versões mais antigas da [nota SAP #2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP é agora atualizada para indicar que este parâmetro precisa de ser definido para **0** para funcionar com os equilibradores de carga Azure.
        - Considere usar [grupos de colocação de proximidade Azure](../../linux/co-location.md) para obter a latência ideal da rede. Para obter mais informações, consulte [os grupos de colocação de proximidade do Azure para obter uma latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
   4. Alta disponibilidade e implantações de recuperação de desastres.
        - Se implementar a camada de aplicação SAP sem definir uma zona específica de disponibilidade de Azure, certifique-se de que todos os VMs que executam instâncias de diálogo SAP ou instâncias de middleware de um único sistema SAP são implantados num [conjunto de disponibilidade](../../manage-availability.md).
        - Se não necessitar de alta disponibilidade para os Serviços Centrais SAP e para o DBMS, pode implantar estes VMs no mesmo conjunto de disponibilidade que a camada de aplicação SAP.
        - Se proteger os Serviços Centrais SAP e a camada DBMS para uma elevada disponibilidade utilizando replicação passiva, coloque os dois nós para os Serviços Centrais SAP num conjunto de disponibilidades separados e os dois nós DBMS em outro conjunto de disponibilidade.
        - Se implementar em Zonas de Disponibilidade Azure, não pode utilizar conjuntos de disponibilidade. Mas você precisa ter certeza de que você implanta os nós de Serviços Centrais ativos e passivos em duas diferentes Zonas de Disponibilidade. Utilize zonas de disponibilidade que tenham a menor latência entre elas.
          Tenha em mente que precisa de utilizar o [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) para o caso de criar clusters de falha do Windows ou pacemaker para a camada de Serviços Centrais DBMS e SAP através de Zonas de Disponibilidade. Não pode utilizar [o Balancer de Carga Básica](../../../load-balancer/load-balancer-overview.md) para implementações zonais.
   5. Definições de tempo limite.
        - Verifique os vestígios do desenvolvedor DA SAP NetWeaver das instâncias SAP para se certificar de que não existem ruturas de ligação entre o servidor de enqueue e os processos de trabalho SAP. Pode evitar estas quebras de ligação definindo estes dois parâmetros de registo:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros\KeepAliveTime = 120000. Para mais informações, consulte [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros\KeepAliveInterval = 120000. Para mais informações, consulte [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Para evitar intervalos de tempo gui entre as interfaces SAP GUI no local e as camadas de aplicação SAP implementadas em Azure, verifique se estes parâmetros estão definidos no perfil padrão.pfl ou no perfil de instância:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Para evitar a rutura das ligações estabelecidas entre o processo de enquese SAP e os processos de trabalho SAP, é necessário definir o parâmetro **enque/encni/set_so_keepalive** para **ser verdadeiro**. Consulte também [a nota SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se utilizar uma configuração de cluster de falha do Windows, certifique-se de que a hora de reagir nos nós não responsivos está corretamente definida para O Azure. O artigo [Afinação dos limiares da rede de cluster](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) de falhas lista os parâmetros e como afetam as sensibilidades de failover. Assumindo que os nóns de cluster estão na mesma sub-rede, deve alterar estes parâmetros:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - EncaminhamentoSDestórias = 30
    6. Definições ou patches de OS
        - Para executar HANA no SAP, leia estas notas e documentações:
            -   [Nota de suporte SAP #2814271 - SAP HANA Backup falha em Azure com Erro de Verificação](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Nota de suporte SAP #2753418 - Potencial degradação do desempenho devido ao recuo do temporizador](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Nota de suporte SAP #2791572 - Degradação de desempenho por causa do suporte vdso em falta para hiper-V em Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Nota de suporte SAP #2382421 - Otimização da Configuração da Rede no Nível HANA e OS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Nota de suporte DA SAP #2694118 - Red Hat Enterprise Linux HA Add-On em Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Nota de suporte SAP #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Nota de suporte SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Nota de suporte SAP #2777782 - SAP HANA DB: Definições recomendadas de OS para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Nota de suporte SAP #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Nota de suporte SAP #2455582 - Linux: Aplicações SAP em execução compiladas com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Nota de suporte SAP #2729475 - HWCCT Falhado com Erro "O hipervisor não é suportado" em VMs Azure certificados para SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Teste os seus elevados procedimentos de disponibilidade e recuperação de desastres.
   1. Simular situações de failover desligando VMs (sistemas operativos para hóspedes do Windows) ou colocando os sistemas operativos em modo de pânico (sistemas operativos para hóspedes Linux). Este passo irá ajudá-lo a descobrir se as suas configurações de failover funcionam como projetado.
   1. Meça o tempo que leva para executar um fracasso. Se os tempos forem muito longos, considere:
        - Para o SUSE Linux, utilize dispositivos SBD em vez do agente Azure Fence para acelerar o failover.
        - Para o SAP HANA, se a recarga de dados demorar demasiado tempo, considere a provisionação de mais largura de banda de armazenamento.
   3. Teste a sua sequência de backup/restauro e tempo e faça correções se for necessário. Certifique-se de que os tempos de reserva são suficientes. Você também precisa testar as atividades de restauração e restauro do tempo. Certifique-se de que os tempos de restauro estão dentro dos seus RTO SLAs onde quer que o seu RTO dependa de uma base de dados ou de um processo de restauro de VM.
   4. Teste a funcionalidade e arquitetura DR transversal.
1. Verificações de segurança.
   1. Teste a validade da sua arquitetura de controlo de acesso baseado em funções Azure (Azure RBAC). O objetivo é separar e limitar o acesso e permissões de diferentes equipas. Por exemplo, os membros da equipa SAP Basis devem ser capazes de implantar VMs e atribuir discos do Azure Storage para uma determinada rede virtual Azure. Mas a equipa sap base não deve ser capaz de criar as suas próprias redes virtuais ou alterar as configurações das redes virtuais existentes. Os membros da equipa de rede não devem ser capazes de implantar VMs em redes virtuais nas quais a aplicação SAP e os VMs DBMS estão em execução. Os membros desta equipa também não devem ser capazes de alterar atributos de VMs ou mesmo apagar VMs ou discos.  
   1.  Verifique se o grupo de segurança da rede e as regras [da ASC](../../../virtual-network/network-security-groups-overview.md) funcionam como esperado e proteja os recursos protegidos.
   1.  Certifique-se de que todos os recursos que precisam de ser encriptados estão encriptados. Definir e implementar processos para fazer o back up certificados, armazenar e aceder a esses certificados, e restaurar as entidades encriptadas.
   1.  Utilize [encriptação do disco Azure](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) para discos OS sempre que possível do ponto de vista do suporte do OS.
   1.  Certifique-se de que não está a usar muitas camadas de encriptação. Em alguns casos, faz sentido utilizar a Encriptação do Disco Azure juntamente com um dos métodos de encriptação de dados transparentes DBMS para proteger diferentes discos ou componentes no mesmo servidor.  Por exemplo, num servidor SAP DBMS, a Encriptação do Disco Azure (ADE) pode ser ativada no disco de arranque do sistema operativo (se o SISTEMA suporta ADE) e os discos de dados não utilizados pelos ficheiros de persistência de dados DBMS.  Um exemplo é utilizar o ADE no disco que mantém as chaves de encriptação DBMS TDE.
1. Teste de desempenho. No SAP, com base no rastreio e medições SAP, estas comparações:
   - Quando aplicável, compare os 10 principais relatórios online com a sua implementação atual.
   - Quando aplicável, compare os 10 melhores trabalhos de lote com a sua implementação atual.
   - Compare as transferências de dados através de interfaces para o sistema SAP. Concentre-se em interfaces onde sabe que a transferência vai entre diferentes locais, como desde as instalações até Azure.


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, assumimos que depois de um piloto de sucesso ou prova de conceito (POC), você está começando a implementar sistemas SAP não produtivos para Azure. Incorpore tudo o que aprendeu e experimentou durante o POC para esta implantação. Todos os critérios e passos listados para POCs aplicam-se também a esta implantação.

Durante esta fase, normalmente implementa sistemas de desenvolvimento, sistemas de teste de unidade e sistemas de teste de regressão de negócios para a Azure. Recomendamos que pelo menos um sistema de não produção numa linha de aplicações SAP tenha a configuração de alta disponibilidade que o futuro sistema de produção terá. Aqui estão alguns passos adicionais que precisa de completar durante esta fase:  

1.  Antes de mover os sistemas da antiga plataforma para o Azure, recolha dados de consumo de recursos, como utilização de CPU, produção de armazenamento e dados do IOPS. Recolher especialmente estes dados das unidades de camada DBMS, mas também recolhê-lo das unidades de camada de aplicação. Também meça a rede e a latência de armazenamento.
2.  Grave os padrões de tempo de utilização da disponibilidade dos seus sistemas. O objetivo é descobrir se os sistemas de não produção precisam de estar disponíveis todos os dias, todos os dias ou se existem sistemas de não produção que podem ser desligados durante determinadas fases de uma semana ou mês.
3.  Teste e determine se pretende criar as suas próprias imagens de SO para os seus VMs em Azure ou se pretende utilizar uma imagem da Galeria de Imagens Partilhadas Azure. Se estiver a utilizar uma imagem da Galeria de Imagens Partilhadas, certifique-se de utilizar uma imagem que reflita o contrato de suporte com o seu fornecedor de SISTEMA. Para alguns vendedores de OS, a Shared Image Gallery permite-lhe trazer as suas próprias imagens de licença. Para outras imagens de SO, o suporte está incluído no preço citado pela Azure. Se decidir criar as suas próprias imagens de SO, pode encontrar documentação nestes artigos:
    -   [Construir uma imagem generalizada de um Windows VM implantado em Azure](../../windows/capture-image-resource.md)
    -   [Construa uma imagem generalizada de um Linux VM implantado em Azure](../../linux/capture-image.md)
3.  Se utilizar imagens SUSE e Red Hat Linux da Galeria de Imagens Partilhadas, tem de utilizar as imagens para SAP fornecidas pelos fornecedores Linux na Galeria de Imagens Partilhadas.
4.  Certifique-se de que preenche os requisitos de suporte SAP para acordos de suporte da Microsoft. Consulte [a nota de suporte da SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Para HANA Grandes Instâncias, consulte [os requisitos de embarque](./hana-onboarding-requirements.md).
4.  Certifique-se de que as pessoas certas recebem [notificações de manutenção planeadas](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) para que possa escolher os melhores tempos de inatividade.
5.  Verifique frequentemente se há apresentações do Azure em canais como [o Canal 9](https://channel9.msdn.com/) para obter novas funcionalidades que possam ser aplicadas às suas implementações.
6.  Consulte as notas SAP relacionadas com o Azure, como [a nota de suporte #1928533,](https://launchpad.support.sap.com/#/notes/1928533)para novos SKUs VM e lançamentos de OS e DBMS recentemente suportados. Compare os preços dos novos tipos de VM com os dos tipos de VM mais antigos, para que possa implementar VMs com a melhor relação preço/desempenho.
7.  Rever as notas de suporte SAP, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nos VMs suportados para a Azure, usou lançamentos de SO apoiados nesses VMs e apoiou as versões SAP e DBMS.
8.  Consulte [o site da SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para obter novos SKUs certificados pela HANA em Azure. Compare os preços dos novos SKUs com os que planeou utilizar. Eventualmente, faça as alterações necessárias para usar os que têm a melhor relação preço/desempenho.
9.  Adapte os scripts de implementação para utilizar novos tipos de VM e incorpore novas funcionalidades Azure que pretende utilizar.
10. Após a implantação da infraestrutura, teste e avalia a latência da rede entre vMs de aplicação SAP e VMs DBMS, de acordo com as notas de suporte [SAP #500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte da SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar ao alcance moderado ou bom. As exceções aplicam-se ao tráfego entre as unidades de VMs e HANA Large Instance, conforme documentado [neste artigo.](./hana-network-architecture.md#networking-architecture-for-hana-large-instance) Certifique-se de que nenhuma das restrições mencionadas em [Considerações para a implantação de DBMS de máquinas virtuais Azure para cargas de trabalho SAP](./dbms_guide_general.md#azure-network-considerations) e [configurações e operações de infraestrutura SAP HANA no Azure](./hana-vm-operations.md) se aplicam à sua implantação.
11. Certifique-se de que os seus VMs são implantados no grupo de colocação de [proximidade Azure](../../linux/co-location.md)correto, conforme descrito nos [grupos de colocação de proximidade Azure para uma latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
11. Efetue todas as outras verificações listadas para a fase de prova de conceito antes de aplicar a carga de trabalho.
12. À medida que a carga de trabalho se aplica, registe o consumo de recursos dos sistemas em Azure. Compare este consumo com os registos da sua antiga plataforma. Ajuste o tamanho de VM de futuras implementações se vir que tem grandes diferenças. Tenha em mente que quando reduzir, o armazenamento e as larguras de banda de VM também serão reduzidos.
    - [Tamanhos de máquinas virtuais do Windows no Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Sizes for Linux virtual machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimente a funcionalidade e os processos de cópia do sistema. O objetivo é facilitar a cópia de um sistema de desenvolvimento ou de um sistema de teste, para que as equipas de projeto possam obter novos sistemas rapidamente. 
14. Otimize e aprimore o acesso, permissões e processos baseados em funções da sua equipa para garantir a separação de deveres. Ao mesmo tempo, certifique-se de que todas as equipas podem desempenhar as suas tarefas na infraestrutura Azure.
15. Exercite, teste e documente procedimentos de alta disponibilidade e recuperação de desastres para permitir que o seu pessoal execute estas tarefas. Identifique as deficiências e adapte a nova funcionalidade Azure que está a integrar nas suas implementações.


## <a name="production-preparation-phase"></a>Fase de preparação da produção 
Nesta fase, colete o que experimentou e aprendeu durante as suas implementações de não produção e aplique-o em futuras implementações de produção. Também precisa de preparar o trabalho da transferência de dados entre o seu local de hospedagem atual e o Azure.

1.  Complete as atualizações necessárias de desbloqueio SAP dos seus sistemas de produção antes de se mudar para a Azure.
1.  Concorde com os proprietários de empresas em testes funcionais e empresariais que precisam de ser realizados após a migração do sistema produtivo.
1.  Certifique-se de que estes testes são concluídos com os sistemas de origem no local de hospedagem atual. Evite realizar testes pela primeira vez após a deslocação do sistema para Azure.
1.  Teste o processo de migração dos sistemas de produção para Azure. Se não estiver a mover todos os sistemas de produção para a Azure durante o mesmo período de tempo, construa grupos de sistemas de produção que precisam de estar no mesmo local de hospedagem. Teste de migração de dados. Aqui estão alguns métodos comuns:
    - Utilize métodos DBMS como backup/restauro em combinação com SQL Server Always On, HANA System Replication, ou Log shipping para semente e sincronizar o conteúdo da base de dados em Azure.
    - Utilize backup/restauro para bases de dados mais pequenas.
    - Utilize o SAP Migration Monitor, que está integrado no SAP SWPM, para realizar migrações heterogéneas.
    - Utilize o processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se precisar de combinar a sua migração com uma atualização de desbloqueio SAP. Tenha em mente que nem todas as combinações de DBMS de origem e DBMS-alvo são suportadas. Pode encontrar mais informações nas notas de suporte sap específicas para as diferentes versões de DMO. Por exemplo, [Opção de Migração de Bases de Dados (DMO) de SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Teste se a transferência de dados é melhor através da internet ou através do ExpressRoute, caso precise de mover backups ou ficheiros de exportação SAP. Se estiver a mover dados através da internet, poderá ter de alterar algumas das regras do seu grupo de segurança de rede/grupo de segurança de aplicações que terá de ter em vigor para futuros sistemas de produção.
1.  Antes de mover os sistemas da sua antiga plataforma para o Azure, recolha dados de consumo de recursos. Os dados úteis incluem o uso do CPU, a produção de armazenamento e os dados do IOPS. Recolher especialmente estes dados das unidades de camada DBMS, mas também recolhê-lo das unidades de camada de aplicação. Também meça a rede e a latência de armazenamento.
1.  Rever as notas de suporte SAP e as definições de SO necessárias, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nos VMs suportados para a Azure, as versões de SO suportadas nesses VMs e as versões SAP e DBMS suportadas.
1.  Atualizar scripts de implementação para ter em conta as decisões mais recentes que tomou sobre os tipos de VM e a funcionalidade Azure.
1.  Após a implementação de infraestruturas e aplicações, valide que:
    - Foram implantados os tipos de VM corretos, com os atributos corretos e os tamanhos de armazenamento.
    - Os VMs estão nos desbloqueios e remendos de SO corretos e desejados e são uniformes.
    - Os VMs são endurecidos conforme necessário e de forma uniforme.
    - As versões e correções corretas foram instaladas e implantadas.
    - Os VM foram implantados em conjuntos de disponibilidade de Azure como planeado.
    - O Azure Premium Storage é utilizado para discos sensíveis à latência ou quando é necessário um [SLA single-VM de 99,9%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - O acelerador Azure Write é implantado corretamente.
        - Certifique-se de que, dentro dos VMs, os espaços de armazenamento ou os conjuntos de listras foram construídos corretamente através de discos que necessitam de Write Accelerator.
        - Verifique a [configuração do software RAID no Linux](../../linux/configure-raid.md).
        - Verifique a [configuração do LVM em VMs Linux em Azure](../../linux/configure-lvm.md).
    - [Os discos geridos Azure](https://azure.microsoft.com/services/managed-disks/) são utilizados exclusivamente.
    - Os VMs foram implantados nos conjuntos de disponibilidade corretos e zonas de disponibilidade.
    - [O Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado nos VMs utilizados na camada de aplicação SAP e na camada SAP DBMS.
    - Nenhum [aparelho virtual de rede Azure](https://azure.microsoft.com/solutions/network-appliances/) está no caminho de comunicação entre a aplicação SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA.
    - As regras do grupo de segurança de aplicações e do grupo de segurança da rede permitem a comunicação conforme o desejado e planeado e bloqueiam a comunicação sempre que necessário.
    - As definições de tempo limite são definidas corretamente, como descrito anteriormente.
    - Os VMs são implantados para o grupo de colocação de [proximidade Azure](../../linux/co-location.md)correto, conforme descrito em [grupos de colocação de proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
    - A latência da rede entre vMs de aplicação SAP e VMs DBMS é testada e validada conforme descrito nas notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte da SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar ao alcance moderado ou bom. As exceções aplicam-se ao tráfego entre as unidades de VMs e HANA Large Instance, conforme documentado [neste artigo.](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)
    - A encriptação foi implementada sempre que necessário e com o método de encriptação apropriado.
    - Interfaces e outras aplicações podem ligar a infraestrutura recém-implantada.
1.  Crie um livro para reagir à manutenção planeada do Azure. Determinar a ordem em que os sistemas e VMs devem ser reiniciados para manutenção planeada.
    

## <a name="go-live-phase"></a>Fase ao vivo
Durante a fase de go-live, certifique-se de seguir os livros que desenvolveu durante as fases anteriores. Execute os passos que testou e praticou. Não aceite alterações de última hora em configurações e processos. Também complete estes passos:

1. Verifique se a monitorização do portal Azure e outras ferramentas de monitorização estão a funcionar. Recomendamos o Monitor de Desempenho do Windows (perfmon) para Windows e SAR para o Linux.
    - Balcões da CPU.
        - Tempo médio de CPU, total (todos os CPUs)
        - Tempo médio de CPU, cada processador individual (128 processadores em M128 VMs)
        - Tempo de núcleo cpu, cada processador individual
        - Tempo de utilizador do CPU, cada processador individual
    - Memória.
        - Memória livre
        - Página de memória em/segundo
        - Página de memória para fora/segundo
    - O disco.
        - Disco lido em KBps, por disco individual
        - Leituras de disco/segundo, por disco individual
        - Disco lido em microsegundos/leitura, por disco individual
        - Disco escrever em KBps, por disco individual
        - Escrita/segunda disco, por disco individual
        - Disco escrito em microsegundos/leitura, por disco individual
    - A rede.
        - Pacotes de rede em/segundo
        - Pacotes de rede para fora/segundo
        - Rede KB em/segundo
        - Rede KB fora/segundo
1.  Após a migração de dados, efetue todos os testes de validação acordados com os proprietários do negócio. Aceite os resultados dos testes de validação apenas quando tiver resultados para os sistemas de origem originais.
1.  Verifique se as interfaces estão a funcionar e se outras aplicações podem comunicar com os sistemas de produção recém-implantados.
1.  Consulte o sistema de transporte e correção através da transação SAP STMS.
1.  Execute cópias de segurança de base de dados após o lançamento do sistema para produção.
1.  Execute cópias de segurança VM para os VMs da camada de aplicação SAP após o sistema ser lançado para produção.
1.  Para os sistemas SAP que não faziam parte da atual fase de go-live mas que comunicam com os sistemas SAP que se mudou para Azure durante esta fase de live-live, precisa de redefinir o tampão de nome do anfitrião em SM51. Ao fazê-lo, removerá os antigos endereços IP em cache associados aos nomes das instâncias de aplicação que mudou para Azure.  


## <a name="post-production"></a>Pós-produção
Esta fase tem a ver com monitorização, operação e administração do sistema. Do ponto de vista SAP, aplicam-se as tarefas habituais que foi obrigada a completar no seu antigo local de hospedagem. Complete estas tarefas específicas do Azure também:

1. Reveja as faturas do Azure para sistemas de carregamento elevado.
2. Otimize a eficiência preço/desempenho do lado VM e do lado do armazenamento.
3. Otimize os tempos em que pode desligar os sistemas.  


## <a name="next-steps"></a>Passos seguintes
Veja estes artigos:

- [Azure Virtual Machines planejamento e implementação para SAP NetWeaver](./planning-guide.md)
- [Implementação de máquinas virtuais Azure para SAP NetWeaver](./deployment-guide.md)
- [Considerações para a implantação de DBMS de máquinas virtuais Azure para cargas de trabalho SAP](./dbms_guide_general.md)