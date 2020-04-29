---
title: Lista de verificação de planeamento e implantação de carga sap / Microsoft Docs
description: Lista de verificação para planear implantações de carga de trabalho SAP para o Azure e implementar as cargas de trabalho
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060068"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Cargas de trabalho SAP em Azure: lista de verificação de planeamento e implementação

Esta lista de verificação foi concebida para clientes que deslocam aplicações SAP NetWeaver, S/4HANA e Hybris para a infraestrutura Azure como um serviço. Ao longo da duração do projeto, um cliente e/ou parceiro SAP devem rever a lista de verificação. É importante notar que muitos dos cheques estão concluídos no início do projeto e durante a fase de planeamento. Após a implementação, mudanças simples na infraestrutura azure implantada ou lançamentos de software SAP podem tornar-se complexas.

Reveja a lista de verificação em marcos fundamentais durante o seu projeto. Ao fazê-lo, permitir-lhe-á detetar pequenos problemas antes que se tornem grandes problemas. Também terá tempo suficiente para re-engenhariar e testar quaisquer alterações necessárias. Não considere esta lista completa. Dependendo da sua situação, talvez precise de realizar muitos mais cheques.

A lista de verificação não inclui tarefas independentes do Azure. Por exemplo, as interfaces de aplicação SAP mudam durante uma mudança para a plataforma Azure ou para um fornecedor de hospedagem.

Esta lista de verificação também pode ser utilizada para sistemas já implantados. Novas funcionalidades, como o Write Accelerator and Availability Zones, e novos tipos de VM podem ter sido adicionados desde que foi implementado. Por isso, é útil rever periodicamente a lista de verificação para garantir que está ciente de novas funcionalidades na plataforma Azure.

## <a name="project-preparation-and-planning-phase"></a>Fase de preparação e planeamento do projeto
Durante esta fase, planeia a migração da sua carga de trabalho SAP para a plataforma Azure. No mínimo, durante esta fase é necessário criar os seguintes documentos e definir e discutir os seguintes elementos da migração:

1. Documento de design de alto nível. Este documento deve conter:
    - O atual inventário de componentes e aplicações SAP e um inventário de aplicações-alvo para o Azure.
    - Uma matriz de atribuição de responsabilidades (RACI) que define as responsabilidades e atribuições das partes envolvidas. Comece a um nível elevado, e trabalhe para níveis mais granulares ao longo do planeamento e das primeiras implementações.
    - Uma arquitetura de solução de alto nível.
    - Uma decisão sobre a qual as regiões de Azure se deslocar. Consulte a [lista das regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Para saber quais os serviços disponíveis em cada região, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
    - Uma arquitetura de networking para ligar a partir do local ao Azure. Comece a familiarizar-se com a planta do [Datacenter Virtual para o Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    - Princípios de segurança para a execução de dados empresariais de alto impacto no Azure. Para conhecer a segurança dos dados, comece com a documentação de [segurança do Azure.](https://docs.microsoft.com/azure/security/)
2.  Documento de design técnico. Este documento deve conter:
    - Um diagrama de bloco para a solução.
    - O dimensionamento de componentes computacionais, de armazenamento e de rede em Azure. Para o dimensionamento de SAP de VMs Azure, consulte a nota de [suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Continuidade de negócios e arquitetura de recuperação de desastres.
    - Informações detalhadas sobre as versões de pacote de suporte S, DB, kernel e SAP. Não é necessariamente verdade que cada lançamento de SO suportado por SAP NetWeaver ou S/4HANA é suportado em VMs Azure. O mesmo acontece com os lançamentos de DBMS. Verifique as seguintes fontes para alinhar e, se necessário, atualizar as versões SAP, lançamentos DBMS e lançamentos de SO para garantir suporte sAP e Azure. É necessário ter combinações de lançamento suportadas pela SAP e pelo Azure para obter o suporte total da SAP e microsoft. Se necessário, tem de planear a atualização de alguns componentes do software. Mais detalhes sobre software SAP, OS e DBMS suportados estão documentados aqui:
        - [Nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Esta nota define as libertações mínimas de SO suportadas em VMs Azure. Também define as versões mínimas de base de dados necessárias para a maioria das bases de dados não HANA. Por último, fornece o dimensionamento SAP para tipos de VM Azure suportados por SAP.
        - [Nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Esta nota define políticas de suporte em torno do armazenamento e da relação de suporte do Azure necessárias com a Microsoft.
        - [Nota de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Esta nota define a matriz de suporte do Oráculo para Omino para Azure. A Oracle suporta apenas o Windows e o Oracle Linux como sistemas operativos convidados no Azure para cargas de trabalho SAP. Esta declaração de suporte também se aplica à camada de aplicação SAP que executa instâncias SAP. No entanto, a Oracle não suporta alta disponibilidade para os Serviços Centrais SAP em Oracle Linux através do Pacemaker. Se necessitar de alta disponibilidade para ASCS no Oracle Linux, precisa de utilizar a Suíte de Proteção SIOS para o Linux. Para obter dados detalhados de certificação SAP, consulte a nota de suporte SAP #1662610 - Detalhes de suporte para a Suíte de [Proteção SIOS para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para windows, a solução de clustering de falha do servidor do Windows suportada pelo SAP para serviços centrais SAP é suportada em conjunto com a Oracle como camada DBMS.
        - [Nota de suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Esta nota fornece a matriz de suporte para SAP HANA em diferentes lançamentos de SO.
        - Os VMs Azure apoiados pela SAP HANA e as [grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) estão listados no site da [SAP.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        - [Matriz](https://support.sap.com/en/)de disponibilidade de produto SAP .
        - [Nota de suporte SAP #2555629 - SAP HANA 2.0 Tiering Dinâmico – Hypervisor e Suporte à Nuvem](https://launchpad.support.sap.com/#/notes/2555629)
        - [Nota de apoio SAP #1662610 - Detalhes de suporte para A Suíte de Proteção SIOS para Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Notas SAP para outros produtos específicos do SAP.     
    - Recomendamos designs rigorosos de três níveis para sistemas de produção SAP. Não recomendamos combinar servidores ASCS e/ou DBMS e/ou app num VM. A utilização de configurações de cluster multi-SID para serviços centrais SAP é suportada em sistemas operativos windows convidados no Azure. Mas esta configuração não é suportada para os Serviços Centrais SAP em sistemas operativos Linux no Azure. Pode encontrar documentação para o cenário de Os do Windows nestes artigos:
        - [SAP ASCS/SCS caso multi-SID alta disponibilidade com Clustering de Falha do Servidor windows e disco partilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [SAP ASCS/SCS caso multi-SID alta disponibilidade com Clustering de Falha do Servidor windows e partilha de ficheiros no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Arquitetura de alta disponibilidade e recuperação de desastres.
        - Com base na RTO e rpo, defina como a arquitetura de alta disponibilidade e recuperação de desastres precisa de ser.
        - Para uma elevada disponibilidade dentro de uma zona, verifique o que o DBMS desejado tem para oferecer em Azure. A maioria dos pacotes DBMS oferecem métodos sincronizados de um standby quente sincronizado, que recomendamos para sistemas de produção. Verifique também a documentação relacionada com o SAP para diferentes bases de dados, começando com [considerações para a implantação de DBMS de Máquinas Virtuais Azure para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documentos relacionados.
           Utilizar o Clusterde failover do Servidor windows com uma configuração de disco partilhado para a camada DBMS como, por exemplo, [descrito para o Servidor SQL,](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)não é suportado. Em vez disso, utilize soluções como:
           - [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Proteção de Dados Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replicação do sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Para a recuperação de desastres nas regiões de Azure, reveja as soluções oferecidas por diferentes fornecedores de DBMS. A maioria suporta replicação assíncrona ou envio de registos.
        - Para a camada de aplicação SAP, determine se irá executar os seus sistemas de teste de regressão de negócios, que idealmente são réplicas das suas implementações de produção, na mesma região de Azure ou na sua região DE. No segundo caso, pode visar o sistema de regressão do negócio como alvo DE DR para as suas implementações de produção.
        - Se decidir não colocar os sistemas de não produção no local dr, procure a Recuperação do Sítio Azure como um método para replicar a camada de aplicação SAP na região azure DR. Para mais informações, consulte uma recuperação de [desastres configurada para uma implementação de aplicações SAP NetWeaver de vários níveis.](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
        - Se decidir utilizar uma configuração HADR combinada utilizando zonas de [disponibilidade Azure,](https://docs.microsoft.com/azure/availability-zones/az-overview)familiarize-se com as regiões azure onde estão disponíveis zonas de disponibilidade. Tenha também em conta as restrições que podem ser introduzidas pelo aumento das tardios da rede entre duas Zonas de Disponibilidade.  
3.  Um inventário de todas as interfaces SAP (SAP e não-SAP).
4.  Design de serviços de fundação. Este desenho deve incluir os seguintes itens:
    - Design de Diretório Ativo e DNS.
    - Topologia de rede dentro do Azure e atribuição de diferentes sistemas SAP.
    - Estrutura [de acesso baseada em papéis](https://docs.microsoft.com/azure/role-based-access-control/overview) para equipas que gerem infraestruturas e aplicações SAP em Azure.
    - Topologia do grupo de recursos.
    - [Estratégia de marcação.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    - Nomeação de convenções para VMs e outros componentes de infraestrutura e/ou nomes lógicos.
5.  Contrato de Suporte Premier da Microsoft. Identifique o seu Gestor de Conta Técnica da Microsoft (TAM). Para obter requisitos de suporte SAP, consulte a nota de [suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  O número de assinaturas Azure e quota central para as subscrições. [Aberto pedidos de apoio para aumentar as quotas de subscrições do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) conforme necessário.
7.  Plano de redução de dados e migração de dados para a migração de dados do SAP para o Azure. Para os sistemas SAP NetWeaver, o SAP tem orientações sobre como limitar o volume de grandes quantidades de dados. Consulte [este guia SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) sobre gestão de dados nos sistemas ERP SAP. Alguns dos conteúdos aplicam-se também aos sistemas NetWeaver e S/4HANA em geral.
8.  Uma abordagem de implantação automatizada. O objetivo da automatização das implantações de infraestruturas no Azure é implantar de forma determinística e obter resultados determinísticos. Muitos clientes usam scripts baseados no PowerShell ou cli. Mas existem várias tecnologias de código aberto que pode usar para implementar a infraestrutura Azure para SAP e até instalar software SAP. Pode encontrar exemplos no GitHub:
    - [Implantações Automáticas de SAP em Nuvem Azure](https://github.com/Azure/sap-hana)
    - [Instalação SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Defina uma cadência regular de revisão de design e implementação entre si como cliente, integrador do sistema, Microsoft e outras partes envolvidas.

 
## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (fortemente recomendada)
 
Pode executar um piloto antes ou durante o planeamento e preparação do projeto. Também pode utilizar a fase piloto para testar abordagens e desenhos feitos durante a fase de planeamento e preparação. E pode expandir a fase piloto para torná-la uma verdadeira prova de conceito.

Recomendamos que instale e valide uma solução HADR completa e design de segurança durante uma implantação piloto. Alguns clientes realizam testes de escalabilidade durante esta fase. Outros clientes utilizam as implementações de sistemas de sandbox SAP como fase piloto. Assumimos que já identificou um sistema que quer migrar para Azure para o piloto.

1. Otimizar a transferência de dados para o Azure. A escolha ideal é altamente dependente do cenário específico. A transferência do local através do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) é a mais rápida se o circuito ExpressRoute tiver largura de banda suficiente. Noutros cenários, a transferência através da internet é mais rápida.
2. Para uma migração heterogénea da plataforma SAP que envolva a exportação e importação de dados, teste e otimize as fases de exportação e importação. Para grandes migrações em que o SQL Server é a plataforma de destino, pode encontrar [recomendações.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) Pode utilizar o Migration Monitor/SWPM se não precisar de uma atualização combinada de libertação. Pode utilizar o processo [DeMO SAP](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando combinar a migração com uma atualização de lançamento SAP. Para isso, precisa de satisfazer certos requisitos para a combinação de plataformas DBMS de origem e alvo. Este processo está documentado na Opção de [Migração de Bases de Dados (DMO) do SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportação para fonte, envio de ficheiros de exportação para Azure e desempenho de importação. Maximizar a sobreposição entre exportação e importação.
   2.  Avaliar o volume da base de dados sobre as plataformas alvo e de destino para efeitos de dimensionamento de infraestruturas.
   3.  Validar e otimizar o tempo.
1. Validação técnica.
   1. Tipos vm.
        - Reveja os recursos em notas de suporte SAP, no diretório de hardware SAP HANA, e no SAP PAM novamente. Certifique-se de que não existem alterações nos VMs suportados para o Azure, lançamentos de OS suportados para esses tipos de VM e lançamentos sap e DBMS suportados.
        - Valide novamente o tamanho da sua aplicação e a infraestrutura que implementa no Azure. Se estiver a mover as aplicações existentes, pode muitas vezes obter os SAPS necessários da infraestrutura que utiliza e da página web de [referência SAP e](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) compará-la com os números SAPS listados na nota de [suporte SAPS #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tenha também em mente [este artigo sobre as classificações do SAPS.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - Avalie e teste o dimensionamento dos seus VMs Azure no que diz respeito à máxima entrada de armazenamento e à entrada de rede dos tipos VM que escolheu durante a fase de planeamento. Pode encontrar os dados aqui:
           -  [Tamanhos para máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar a entrada de *disco max uncached* para dimensionamento.
           -  [Tamanhos para máquinas virtuais Linux em Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) É importante considerar a entrada de *disco max uncached* para dimensionamento.
   2. Armazenamento.
        - No mínimo, utilize [o armazenamento Azure Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) para VMs que representem camadas de aplicação SAP e para implantação de DBMSs que não sejam sensíveis ao desempenho.
        - Em geral, não recomendamos a utilização de [discos HDD Padrão Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Utilize [o Armazenamento Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) para quaisquer VMs DBMS que sejam remotamente sensíveis ao desempenho.
        - Utilize [discos geridos pelo Azure.](https://azure.microsoft.com/services/managed-disks/)
        - Utilize o acelerador de escrita Azure para unidades de log DBMS com série M. Esteja atento aos limites e utilização do Acelerador de Escrita, conforme documentado no Acelerador de [Escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Para os diferentes tipos de DBMS, verifique a [documentação genérica de DBMS relacionada com](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) o SAP e a documentação específica do DBMS a que o documento genérico aponta.
        - Para mais informações sobre o SAP HANA, consulte [as configurações e operações da infraestrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Nunca monte discos de dados Azure num VM Azure Linux utilizando o ID do dispositivo. Em vez disso, utilize o identificador universalmente único (UUID). Tenha cuidado ao utilizar ferramentas gráficas para montar discos de dados Azure, por exemplo. Verifique duas vezes as entradas em /etc/fstab para se certificar de que o UUID é utilizado para montar os discos. Pode encontrar mais detalhes [neste artigo.](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Em rede.
        - Teste e avalie a sua infraestrutura de rede virtual e a distribuição das suas aplicações SAP através ou dentro das diferentes redes virtuais do Azure.
        -  Avalie a abordagem de arquitetura de rede virtual hub-and-spoke ou a abordagem de microsegmentação dentro de uma única rede virtual Azure. Baseie esta avaliação em:
               1. Custos de troca de dados entre [redes virtuais azure peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Para obter informações sobre os custos, consulte [os preços da Rede Virtual.](https://azure.microsoft.com/pricing/details/virtual-network/)
               2. Vantagens de uma rápida desconexão entre redes virtuais Azure em oposição a mudar o grupo de segurança da rede para isolar uma subrede dentro de uma rede virtual. Esta avaliação é para casos em que aplicações ou VMs alojados numa subnet da rede virtual se tornaram um risco de segurança.
                3. Registo central e auditoria do tráfego de rede entre as instalações, o mundo exterior e o centro de dados virtual que construiu em Azure.
        - Avaliar e testar o caminho de dados entre a camada de aplicação SAP e a camada DBMS SAP.
            -  A colocação de aparelhos virtuais da [rede Azure](https://azure.microsoft.com/solutions/network-appliances/) na via de comunicação entre a aplicação SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA não é suportada.
            -  A colocação da camada de aplicação SAP e do SAP DBMS em diferentes redes virtuais Azure que não são espreitadas não é suportada.
            -  Pode utilizar as regras do grupo de segurança de [aplicações e da rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para definir rotas entre a camada de aplicação SAP e a camada DBMS SAP.
        - Certifique-se de que o [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado nos VMs utilizados na camada de aplicação SAP e na camada DBMS SAP. Tenha em mente que são necessários diferentes níveis de Os para apoiar o Networking Acelerado em Azure:
            - Windows Server 2012 R2 ou posterior.
            - SUSE Linux 12 SP3 ou mais tarde.
            - RHEL 7.4 ou mais tarde.
            - Oracle Linux 7.5. Se estiver a utilizar o núcleo RHCKL, é necessário libertar 3.10.0-862.13.1.el7. Se estiver a utilizar o núcleo Oráculo UEK, é necessário libertar 5.
        - Teste e avalie a latência da rede entre os VMs da camada de aplicação SAP e os VMs DBMS de acordo com as notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na gama moderada ou boa. As exceções aplicam-se ao tráfego entre as Unidades VMs e HANA Large Instance, tal como documentado [no presente artigo.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
        - Certifique-se de que as implementações ILB estão configuradas para utilizar a Devolução do Servidor Direto. Esta definição reduzirá a latência quando os IlBs Azure forem utilizados para configurações de alta disponibilidade na camada DBMS.
        - Se estiver a utilizar o Azure Load Balancer juntamente com os sistemas operativos de hóspedes Linux, verifique se o parâmetro de rede Linux **net.ipv4.tcp_timestamps** está definido para **0**. Esta recomendação entra em conflito com recomendações em versões mais antigas da [nota sap #2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP é agora atualizada para indicar que este parâmetro precisa de ser definido para **0** para trabalhar com os equilibradores de carga Azure.
        - Considere utilizar grupos de colocação de [proximidade Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) para obter a latência ideal da rede. Para mais informações, consulte os grupos de [colocação de proximidade do Azure para obter uma ótima latência da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
   4. Alta disponibilidade e destacamentos de recuperação de desastres.
        - Se implementar a camada de aplicação SAP sem definir uma zona de disponibilidade azure específica, certifique-se de que todos os VMs que executam instâncias de diálogo SAP ou instâncias de middleware de um único sistema SAP são implantados num conjunto de [disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Se não necessitar de alta disponibilidade para os Serviços Centrais sAP e o DBMS, pode implementar estes VMs na mesma disponibilidade definida como a camada de aplicação SAP.
        - Se proteger os Serviços Centrais sAP e a camada DBMS para uma elevada disponibilidade utilizando replicação passiva, coloque os dois nós para os Serviços Centrais SAP num conjunto de disponibilidade separado e os dois nós DBMS em outro conjunto de disponibilidade.
        - Se se instalar em Zonas de Disponibilidade Azure, não pode utilizar conjuntos de disponibilidade. Mas você precisa garantir que você implementa os nódosos centrais ativos e passivos em duas diferentes Zonas de Disponibilidade. Utilize Zonas de Disponibilidade que tenham a menor latência entre elas.
          Tenha em mente que necessita de utilizar o Equilíbrio de [Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) para a utilização de clusters de falha windows ou pacemaker para a camada dbms e SAP Central Services em todas as Zonas de Disponibilidade. Não pode utilizar o [Equilíbrio básico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) de carga para implantações zonais.
   5. Definições de tempo para fora.
        - Verifique os vestígios do desenvolvedor SAP NetWeaver das instâncias SAP para se certificar de que não existem ruturas de ligação entre o servidor de fila e os processos de trabalho do SAP. Pode evitar estas quebras de ligação definindo estes dois parâmetros de registo:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Para mais informações, consulte [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros\KeepAliveInterval = 120000. Para mais informações, consulte [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Para evitar intervalos de GUI entre as interfaces SAP GUI no local e as camadas de aplicação SAP implantadas no Azure, verifique se estes parâmetros estão definidos no padrão.pfl ou no perfil de instância:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Para evitar perturbações das ligações estabelecidas entre o processo de enfila SAP e os processos de trabalho SAP, é necessário definir o parâmetro **enque/ncni/set_so_keepalive** **verdadeiro**. Consulte também [a nota SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se utilizar uma configuração de cluster de failover do Windows, certifique-se de que o tempo para reagir em nós não responsivos está corretamente definido para o Azure. O artigo [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) lista parâmetros e como afetam sensibilidades de failover. Assumindo que os nós do cluster estão na mesma sub-rede, deve alterar estes parâmetros:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutHistorylength = 30
    6. Definições ou Patches do OS
        - Para executar hana no SAP, leia estas notas e documentação:
            -   [Nota de suporte SAP #2814271 - SAP HANA Backup falha em Azure com Erro de Verificação](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Nota de suporte SAP #2753418 - Potencial degradação do desempenho devido ao recuo do timer](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Nota de suporte SAP #2791572 - Degradação do desempenho devido ao apoio vDSO desaparecido para hiper-V em Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Nota de suporte SAP #2382421 - Otimização da Configuração da Rede em HANA- e Nível OS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Nota de apoio SAP #2694118 - Red Hat Enterprise Linux HA Add-On on Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Nota de suporte SAP #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e Upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Nota de suporte SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Nota de suporte SAP #2777782 - SAP HANA DB: Definições recomendadas de OS para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Nota de suporte SAP #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Nota de suporte SAP #2455582 - Linux: Executar aplicações SAP compiladas com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Nota de suporte SAP #2729475 - HWCCT Falhou com erro "Hypervisor não é suportado" em VMs Azure certificados para SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Teste os seus procedimentos de alta disponibilidade e recuperação de desastres.
   1. Simular situações de falha desligando VMs (sistemas operativos Windows) ou colocando sistemas operativos em modo de pânico (sistemas operativos de hóspedes Linux). Este passo irá ajudá-lo a descobrir se as suas configurações de failover funcionam como projetadas.
   1. Meça o tempo que leva para executar uma falha. Se os tempos forem muito longos, considere:
        - Para o SUSE Linux, utilize dispositivos SBD em vez do agente Da Vedação Azure para acelerar a falha.
        - Para o SAP HANA, se a recarga de dados demorar demasiado tempo, considere fornecer mais largura de banda de armazenamento.
   3. Teste a sua sequência de backup/restauro e o seu tempo e faça correções se necessário. Certifique-se de que os tempos de reserva são suficientes. Você também precisa testar as atividades de restauro e restauro do tempo. Certifique-se de que os tempos de restauro estão dentro dos seus RTO SLAs onde quer que o seu RTO dependa de uma base de dados ou do processo de restauração vm.
   4. Teste funcionalidade dr inter-região e arquitetura.
1. Verificações de segurança.
   1. Teste a validade da sua arquitetura de controlo de acesso baseado em funções Azure (RBAC). O objetivo é separar e limitar o acesso e as permissões de diferentes equipas. Por exemplo, os membros da equipa SAP Basis devem ser capazes de implementar VMs e atribuir discos do Azure Storage numa determinada rede virtual Azure. Mas a equipa da SAP Basis não deve ser capaz de criar as suas próprias redes virtuais ou alterar as definições das redes virtuais existentes. Os membros da equipa de rede não devem ser capazes de implantar VMs em redes virtuais nas quais a aplicação SAP e os VMs DBMS estão a funcionar. Os membros desta equipa também não devem ser capazes de alterar atributos de VMs ou mesmo apagar VMs ou discos.  
   1.  Verifique se o grupo de segurança da rede e as regras [asc](https://docs.microsoft.com/azure/virtual-network/security-overview) funcionam como esperado e proteja os recursos protegidos.
   1.  Certifique-se de que todos os recursos que precisam de ser encriptados estão encriptados. Defina e implemente processos para fazer cópias de serviço, armazenar e aceder a esses certificados e restaurar as entidades encriptadas.
   1.  Utilize encriptação de [disco Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) para discos OS sempre que possível do ponto de vista do sistema de suporte ao OS.
   1.  Certifique-se de que não está a usar muitas camadas de encriptação. Em alguns casos, faz sentido usar a Encriptação do Disco Azure juntamente com um dos métodos de encriptação transparente de dados do DBMS para proteger diferentes discos ou componentes no mesmo servidor.  Por exemplo, num servidor DBMS SAP, a Encriptação do Disco Azure (ADE) pode ser ativada no disco de arranque do sistema operativo (se o SISTEMA suporta a ADE) e esses discos de dados não utilizados pelos ficheiros de persistência de dados DBMS.  Um exemplo é usar ADE no disco segurando as teclas de encriptação DBMS TDE.
1. Testes de desempenho. No SAP, com base no rastreio e medições do SAP, faça estas comparações:
   - Quando aplicável, compare os 10 melhores relatórios online com a sua implementação atual.
   - Quando aplicável, compare os 10 melhores trabalhos de lote com a sua implementação atual.
   - Compare as transferências de dados através de interfaces no sistema SAP. Concentre-se em interfaces onde sabe que a transferência vai entre diferentes locais, como de instalações para Azure.


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, assumimos que depois de um piloto de sucesso ou prova de conceito (POC), você está começando a implantar sistemas SAP não-produção para Azure. Incorpore tudo o que aprendeu e experimentou durante o POC para esta implantação. Todos os critérios e passos listados para os POCs aplicam-se também a esta implantação.

Durante esta fase, normalmente implementa sistemas de desenvolvimento, sistemas de teste de unidades e sistemas de testes de regressão empresarial para o Azure. Recomendamos que pelo menos um sistema de não produção numa linha de aplicação SAP tenha a configuração de alta disponibilidade que o futuro sistema de produção terá. Aqui estão alguns passos adicionais que precisa de completar durante esta fase:  

1.  Antes de mover sistemas da antiga plataforma para o Azure, recolha dados de consumo de recursos, como utilização de CPU, entrada de armazenamento e dados iops. Recolha especialmente estes dados das unidades de camadaDBMS, mas também recolha-os das unidades de camada de aplicação. Também meça a rede e a latência de armazenamento.
2.  Registem os padrões de tempo de utilização da disponibilidade dos seus sistemas. O objetivo é descobrir se os sistemas não produtivos precisam de estar disponíveis todos os dias, todos os dias ou se existem sistemas de não produção que podem ser encerrados durante determinadas fases de uma semana ou mês.
3.  Teste e determine se pretende criar as suas próprias imagens de SO para os seus VMs em Azure ou se pretende utilizar uma imagem da Galeria de Imagem Partilhada Azure. Se estiver a usar uma imagem da Galeria de Imagem Partilhada, certifique-se de usar uma imagem que reflita o contrato de suporte com o seu fornecedor de SO. Para alguns vendedores de SO, a Shared Image Gallery permite-lhe trazer as suas próprias imagens de licença. Para outras imagens do SO, o suporte está incluído no preço indicado pelo Azure. Se decidir criar as suas próprias imagens de SO, pode encontrar documentação nestes artigos:
    -   [Construir uma imagem generalizada de um VM Windows implantado no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Construa uma imagem generalizada de um VM Linux implantado em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se utilizar imagens SUSE e Red Hat Linux da Galeria de Imagem Partilhada, precisa de utilizar as imagens para sap fornecidas pelos vendedores Linux na Galeria de Imagem Partilhada.
4.  Certifique-se de que preenche os requisitos de suporte sap para os acordos de suporte da Microsoft. Consulte a nota de [suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Para as grandes instâncias HANA, consulte [os requisitos de embarque](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Certifique-se de que as pessoas certas recebem notificações de [manutenção planeadas](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) para que possa escolher os melhores tempos de paragem.
5.  Verifique frequentemente as apresentações do Azure em canais como o [Canal 9](https://channel9.msdn.com/) para obter novas funcionalidades que possam aplicar-se às suas implementações.
6.  Consulte as notas SAP relacionadas com o Azure, como [nota de suporte #1928533,](https://launchpad.support.sap.com/#/notes/1928533)para novas VM SKUs e lançamentos de OS e DBMS recentemente suportados. Compare o preço de novos tipos de VM com o dos tipos de VM mais antigos, para que possa implementar VMs com a melhor relação preço/desempenho.
7.  Reverifique as notas de suporte sap, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nos VMs suportados para o Azure, suportados lançamentos de OS nesses VMs, e lançamentos de SAP e DBMS suportados.
8.  Consulte [o site da SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para novas SKUs certificadas pela HANA em Azure. Compare o preço de novas SKUs com os que planeou usar. Eventualmente, faça alterações necessárias para utilizar as que têm a melhor relação preço/desempenho.
9.  Adapte os seus scripts de implementação para utilizar novos tipos de VM e incorpore novas funcionalidades Azure que pretende utilizar.
10. Após a implantação da infraestrutura, teste e avalie a latência da rede entre vMs de camada de aplicação SAP e VMs DBMS, de acordo com as notas de suporte da SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na gama moderada ou boa. As exceções aplicam-se ao tráfego entre as Unidades VMs e HANA Large Instance, tal como documentado [no presente artigo.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Certifique-se de que nenhuma das restrições mencionadas em [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para cargas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) de trabalho SAP e [configurações e operações de infraestrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se aplicam à sua implantação.
11. Certifique-se de que os seus VMs estão implantados para o grupo de colocação de [proximidade azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)correto, conforme descrito em grupos de colocação de [proximidade azure para uma latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md).
11. Efetuar todas as outras verificações listadas para a prova da fase de conceito antes de aplicar a carga de trabalho.
12. À medida que a carga de trabalho se aplica, registe o consumo de recursos dos sistemas em Azure. Compare este consumo com registos da sua antiga plataforma. Ajuste o tamanho vm de futuras implementações se vir que tem grandes diferenças. Tenha em mente que quando reduzir o tamanho, armazenamento e larguras de banda da rede de VMs também serão reduzidas.
    - [Tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimente a funcionalidade e os processos de cópia do sistema. O objetivo é facilitar a cópia de um sistema de desenvolvimento ou de um sistema de teste, para que as equipas de projeto possam obter novos sistemas rapidamente. Considere usar [o SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) para estas tarefas.
14. Otimize e aprimoreo o acesso, permissões e processos baseados no papel da sua equipa, para garantir que tem separação de deveres. Ao mesmo tempo, certifique-se de que todas as equipas podem executar as suas tarefas na infraestrutura Azure.
15. Exercício, teste e documentar procedimentos de alta disponibilidade e recuperação de desastres para permitir que o seu pessoal execute estas tarefas. Identifique as deficiências e adapte a nova funcionalidade Azure que está a integrar nas suas implementações.

 
## <a name="production-preparation-phase"></a>Fase de preparação da produção 
Nesta fase, colete o que experimentou e aprendeu durante as suas implementações não produtivas e aplique-o em futuras implementações de produção. Também precisa de preparar o trabalho da transferência de dados entre a sua localização atual de hospedagem e o Azure.

1.  Complete as atualizações necessárias de lançamento do SAP dos seus sistemas de produção antes de se mudar para o Azure.
1.  Concordo com os empresários em testes funcionais e empresariais que precisam de ser realizados após a migração do sistema produtivo.
1.  Certifique-se de que estes testes estão concluídos com os sistemas de origem no local de acolhimento atual. Evite realizar testes pela primeira vez após a deslocação do sistema para Azure.
1.  Teste o processo de migração dos sistemas de produção para O Azure. Se não estiver a mover todos os sistemas de produção para OT durante o mesmo período de tempo, construa grupos de sistemas de produção que precisam de estar no mesmo local de acolhimento. Teste migração de dados. Aqui estão alguns métodos comuns:
    - Utilize métodos DBMS como backup/restauro em combinação com o SQL Server Always On, hana System Replication ou Log shipping para semente e sincronizar conteúdo de base de dados em Azure.
    - Utilize cópias de segurança/restauro para bases de dados mais pequenas.
    - Utilize o SAP Migration Monitor, integrado no SAP SWPM, para realizar migrações heterogéneas.
    - Utilize o processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se precisar de combinar a sua migração com uma atualização de libertação SAP. Tenha em mente que nem todas as combinações de DBMS de origem e DBMS alvo são suportadas. Pode encontrar mais informações nas notas específicas de suporte do SAP para as diferentes versões de DMO. Por exemplo, Opção de [Migração de Bases de Dados (DMO) de SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Teste se a entrada de transferência de dados é melhor através da internet ou através do ExpressRoute, caso precise mover cópias de segurança ou ficheiros de exportação SAP. Se estiver a mover dados através da internet, poderá ter de alterar algumas das regras do grupo de segurança/grupo de segurança de aplicações que terá de ter em vigor para futuros sistemas de produção.
1.  Antes de mover sistemas da sua antiga plataforma para o Azure, recolha dados de consumo de recursos. Os dados úteis incluem a utilização do CPU, a entrada de armazenamento e os dados do IOPS. Recolha especialmente estes dados das unidades de camadaDBMS, mas também recolha-os das unidades de camada de aplicação. Também meça a rede e a latência de armazenamento.
1.  Reverifique as notas de suporte SAP e as definições de SO necessárias, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nos VMs suportados para o Azure, suportados lançamentos de OS nesses VMs, e lançamentos de SAP e DBMS suportados.
1.  Atualize scripts de implementação para ter em conta as mais recentes decisões que tomou sobre os tipos vM e funcionalidade Azure.
1.  Depois de implantar infraestruturas e aplicações, valide-a:
    - Os tipos de VM corretos foram implantados, com os atributos corretos e os tamanhos de armazenamento corretos.
    - Os VMs estão nos lançamentos e patches corretos e desejados e são uniformes.
    - Os VMs são endurecidos conforme necessário e de forma uniforme.
    - As versões e patches corretos da aplicação foram instalados e implantados.
    - Os VMs foram implantados em conjuntos de disponibilidade azure como planeado.
    - O Armazenamento Azure Premium é utilizado para discos sensíveis à latência ou onde é necessário o [SLA de 99,9% de VM único.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - O acelerador Azure Write está corretamente implantado.
        - Certifique-se de que, dentro dos VMs, espaços de armazenamento ou conjuntos de riscas foram construídos corretamente através de discos que precisam de Write Accelerator.
        - Verifique a [configuração do software RAID no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Verifique a [configuração do LVM nos VMs Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - Os [discos geridos pelo Azure](https://azure.microsoft.com/services/managed-disks/) são utilizados exclusivamente.
    - Os VMs foram implantados nos conjuntos de disponibilidade e zonas de disponibilidade corretos.
    - [A rede acelerada Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativada nos VMs utilizados na camada de aplicação SAP e na camada DBMS SAP.
    - Nenhum aparelho virtual da [rede Azure](https://azure.microsoft.com/solutions/network-appliances/) está na via de comunicação entre a aplicação SAP e a camada DBMS dos sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA.
    - As regras do grupo de segurança de aplicações e da rede permitem a comunicação conforme desejado e planeado e bloqueia a comunicação sempre que necessário.
    - As definições de tempo são definidas corretamente, como descrito anteriormente.
    - Os VMs são implantados para o grupo de colocação de [proximidade azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)correto, como descrito em grupos de colocação de [proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
    - A latência da rede entre vMs e VMs de aplicação SAP é testada e validada conforme descrito nas notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na gama moderada ou boa. As exceções aplicam-se ao tráfego entre as Unidades VMs e HANA Large Instance, tal como documentado [no presente artigo.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    - A encriptação foi implementada sempre que necessário e com o método de encriptação apropriado.
    - Interfaces e outras aplicações podem ligar a infraestrutura recém-implantada.
1.  Crie um livro de jogadas para reagir à manutenção planeada do Azure. Determine a ordem em que os sistemas e os VMs devem ser reiniciados para manutenção planeada.
    

## <a name="go-live-phase"></a>Fase go-live
Durante a fase de go-live, não se esqueça de seguir os livros que desenvolveu durante fases anteriores. Execute os passos que testou e praticou. Não aceite alterações de última hora em configurações e processos. Complete também estes passos:

1. Verifique se a monitorização do portal Azure e outras ferramentas de monitorização estão a funcionar. Recomendamos o Windows Performance Monitor (perfmon) para Windows e SAR para Linux.
    - Contadores da CPU.
        - Tempo médio de CPU, total (todos os CPUs)
        - Tempo médio de CPU, cada processador individual (128 processadores em VMs M128)
        - Tempo de kernel CPU, cada processador individual
        - Tempo de utilizador do CPU, cada processador individual
    - Memória.
        - Memória livre
        - Página de memória em/segundo
        - Página de memória para fora/segundo
    - O disco.
        - Disco lido em KBps, por disco individual
        - Leituras de disco/segundo, por disco individual
        - Disco lido em microsegundos/leitura, por disco individual
        - Escrita em disco em KBps, por disco individual
        - Escrita/segundo disco, por disco individual
        - Escrever em microsegundos/leitura, por disco individual
    - A rede.
        - Pacotes de rede em/segundo
        - Pacotes de rede fora/segundo
        - Rede KB em/segundo
        - Rede KB fora/segundo
1.  Após a migração de dados, execute todos os testes de validação acordados com os proprietários do negócio. Aceite os resultados dos testes de validação apenas quando tiver resultados para os sistemas de origem originais.
1.  Verifique se as interfaces estão a funcionar e se outras aplicações podem comunicar com os sistemas de produção recém-implantados.
1.  Verifique o sistema de transporte e correção através da transação STMS.
1.  Execute cópias de dados após a libertação do sistema para produção.
1.  Execute cópias de segurança VM para as VMs da camada de aplicação SAP após a libertação do sistema para produção.
1.  Para sistemas SAP que não faziam parte da fase atual de go-live mas que comunicam com os sistemas SAP que mudou para O Azure durante esta fase de go-live, você precisa redefinir o amortecedor de nome anfitrião em SM51. Ao fazê-lo, removerá os antigos endereços IP em cache associados aos nomes das instâncias de aplicação que mudou para O Azure.  


## <a name="post-production"></a>Pós-produção
Esta fase tem a ver com monitorização, operação e administração do sistema. Do ponto de vista da SAP, aplicam-se as tarefas habituais que foi necessário para completar no seu antigo local de acolhimento. Complete também estas tarefas específicas do Azure:

1. Reveja as faturas azure para sistemas de carregamento elevado.
2. Otimize a eficiência de preço/desempenho do lado vM e do lado do armazenamento.
3. Otimize os tempos em que pode desligar os sistemas.  


## <a name="next-steps"></a>Passos seguintes
Veja estes artigos:

- [Planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

