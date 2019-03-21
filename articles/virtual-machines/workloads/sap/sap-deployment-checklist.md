---
title: SAP carga de trabalho de planejamento e implantação lista de verificação | Documentos da Microsoft
description: lista de verificação para o planejamento e implementações da carga de trabalho SAP no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 520d417abe27887fad03257c52521c25602009eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096015"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Carga de trabalho SAP na lista de verificação de planejamento e implantação do Azure 

Esta lista de verificação foi concebida para clientes mover seus SAP NetWeaver, S/4HANA e aplicativos de Hybris para infraestrutura como um serviço do Azure.  Esta lista de verificação deve ser revista por um cliente e/ou o parceiro SAP durante a duração do projeto. É importante que muitas das verificações realizadas no início do projeto e na fase de planejamento. Depois de concluída a implementação, elementares alterações na infraestrutura do Azure implementada ou versões de software SAP podem tornar-se complexo. Reveja esta lista de verificação principais etapas em todo o projeto.  Podem ser detetados pequenos problemas antes que se tornem problemas grandes e não existe tempo suficiente para fazer a reengenharia e testar as alterações necessárias. A lista de verificação de nenhuma forma de afirmações ser concluída. Depende da situação individual, pode haver muitos mais verificações que têm de ser realizada. 

A lista de verificação montada não inclui tarefas que são independentes do Azure.  Exemplo: SAP aplicação se alteram Interfaces durante uma mudança para a Cloud pública do Azure ou para um fornecedor de alojamento.    

Esta lista também pode ser usada para sistemas já implementados. Novos recursos, como o acelerador de escrita, zonas de disponibilidade e novos tipos VM foram adicionados, uma vez que implementou.  Portanto, é útil rever a lista de verificação periodicamente para garantir que está atento a novas funcionalidades na plataforma do Azure. 

## <a name="project-preparation-and-planning-phase"></a>Fase de planeamento e preparação de projeto
Nesta fase, estiver planeada uma migração da carga de trabalho SAP na cloud pública do Azure. O conjunto mínimo de entidades e os itens abordados e definidos lista, como:

1. Documento de Design de alto nível – esse documento deve conter:
    1. O inventário atual de aplicações e componentes SAP e de inventário de aplicativos de destino no Azure
    2. Criar e trabalhar com uma matriz de atribuição de responsabilidade (RACI) que define as responsabilidades e atribuições das diferentes partes envolvidas. Começar em alto nível e for para o débito de níveis mais granular, as implementações de planejamento e a primeira
    2. Uma arquitetura de solução de alto nível
    3. Decisão sobre regiões do Azure para implementar numa. Para obter uma lista de regiões do Azure, consulte a [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Para os serviços disponíveis em cada uma das regiões do Azure, consulte o artigo [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/)
    4. Arquitetura para ligar a partir do local para o Azure de rede. Começar a se tornar familiarizado com o [esquema do Virtual Datacenter do Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Princípios de segurança para a execução de dados de impacto comercial de elevada no Azure. Para a leitura de início de material com [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)
2.  Documento técnico de Design – que contém:
    1.  Um diagrama de bloco de solução 
    2.  Dimensionamento de computação, armazenamento e componentes de rede no Azure. Para o dimensionamento de SAP de VMs do Azure, consulte a nota SAP suporte [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Arquitetura de continuidade do negócio e recuperação após desastre
    4.  Detalhadas SO, DB, Kernel e SAP suportam versões de pacote. Não é uma vez que qualquer versão de SO suportada pelo SAP NetWeaver ou S/4HANA é suportada em VMs do Azure. Mesmo se aplica para versões do DBMS. É obrigatório que as seguintes origens são verificadas para alinhar e se for necessário atualizar versões SAP, DBMS versões ou versões do SO para estar num SAP e janela suportados pelo Azure. É obrigatório que estão dentro do SAP e combinações de versão para obter suporte completo ao SAP e a Microsoft suportados pelo Azure. Se necessário, terá de planear a atualização alguns dos componentes de software. Obter mais detalhes sobre software SAP, o sistema operacional e o DBMS suportado está documentado nestas localizações:
        1.  Suporte a nota SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Esta nota define as versões de SO mínimas suportadas em VMs do Azure. Também define versões de base de dados mínimo necessários para a maioria das bases de dados não HANA. A nota também apresenta o dimensionamento de SAP de diferentes tipos VM do Azure do SAP suportado.
        2.  Suporte a nota SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619). A nota define a matriz de suporte da Oracle no Azure. Tenha em atenção que Oracle suporta apenas Windows e o Oracle Linux como o SO de convidado no Azure para a carga de trabalho do SAP. Esta declaração de suporte aplica-se para a camada de aplicação SAP também instâncias SAP em execução. No entanto, o Oracle suporta elevada disponibilidade para SAP Central Services no Oracle Linux através de Pacemaker. Se necessitar de elevada disponibilidade para ASCS no Oracle Linux, terá de tirar partido do SIOS Protection Suite para Linux. Para dados detalhados de certificação SAP, consulte a nota de suporte do SAP [#1662610 - detalhes de suporte do SIOS Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para Windows, o SAP suportadas solução de ativação pós-falha de Cluster de ativação pós-falha do Windows para o SAP Central Services é suportada em conjunto com a Oracle como camada do DBMS. 
        3.  Suporte a nota SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) para obter a matriz de suporte para versões do SAP HANA no sistema operacional diferente
        4.  SAP HANA suportadas VMs do Azure e [instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) estão listados [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Matriz de disponibilidade do produto SAP](https://support.sap.com/en/)
        6.  Outras notas SAP para outros produtos específicos do SAP  
    5.  Recomendamos que strict designs de 3 camadas para sistemas de produção do SAP. A combinação do ASCS + aplicação servidores na mesma VM não é recomendada.  A utilização de configurações de cluster de múltiplos SID para SAP Central Services é suportada com o Windows como sistema operacional convidado no Azure. Ao passo que as configurações de cluster de múltiplos SID SAP Central Services não são suportadas com sistemas de operativos Linux no Azure. Documentação para o caso de SO Windows convidado pode ser encontrada no:
        1.  [Elevada disponibilidade de múltiplos SID com disco partilhado no Azure e o Clustering de ativação pós-falha do Windows Server de instância do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Elevada disponibilidade de múltiplos SID com partilha de ficheiros e de Clustering de ativação pós-falha do Windows Server no Azure de instância do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Arquitetura de recuperação após desastre e de disponibilidade elevada
        1.  Definir com base no RTO e RPO, o que a arquitetura de recuperação após desastre e de disponibilidade elevada tem de ter o seguinte aspeto
        2.  Para elevada disponibilidade na mesma zona, verifique que o DBMS pretendido tem a oferecer no Azure. A maioria dos DBMS oferecer métodos síncronos, de um síncrono modo de espera ativo, que recomendamos para sistemas de produção. Também verificação do SAP relacionados com a documentação para as diferentes bases de dados a partir [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e relacionados a documentos
            1.  Utilizar o serviço de Cluster de ativação pós-falha do Windows com a configuração de disco partilhado para a camada do DBMS como, por exemplo, descrito para o SQL Server [aqui](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) é **não** suportado. Em vez disso, soluções como:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Proteção de dados Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replicação do sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Recuperação de desastres em diferentes regiões do Azure, verifique as possibilidades são oferecidas por fornecedores diferentes DBMS. A maioria dos editores suporta replicação assíncrona ou envio de log
        4.  Para a camada de aplicação SAP, defina se, deverá executar seu regressão comerciais a sistemas de teste, que são o ideal é que as réplicas das suas implementações de produção, na mesma região do Azure ou sua região de DR. No último caso, pode direcionar desse sistema de regressão de negócio como destino de DR para a produção
        5.  Se decidir não colocar os sistemas de não produção no site de DR, examine o Azure Site Recovery como método viável para replicar a camada de aplicação SAP para a região de DR do Azure. Consulte também [configurar a recuperação após desastre para uma implementação de aplicação SAP NetWeaver com várias camada](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Se optar por utilizar uma configuração de HA/DR combinada, tirando partido [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) tornar-se familiarizado com as regiões do Azure onde as zonas de disponibilidade estão disponíveis e com restrições que podem ser introduzidas pelo latências de rede maior entre duas zonas de disponibilidade  
3.  Cliente/parceiro deve criar um inventário de todas as interfaces SAP (SAP como não SAP). 
4.  Design da estrutura de serviços de Foundation - esse design incluem itens como
    1.  Design do Active Directory e DNS
    2.  Topologia de rede no Azure e a atribuição de diferentes sistemas SAP
    3.  [Acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview) estrutura para suas equipes diferentes que gerir infraestruturas e aplicações SAP no Azure
    3.  Topologia de grupo de recursos 
    4.  [Estratégia de marcação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Nomenclatura Convenção para VMs e os outros componentes da infraestrutura e/ou nomes lógicos
5.  Contrato de suporte Premier da Microsoft – identifique o Gestor de conta técnico da MS (TAM). Para obter suporte os requisitos da SAP ler suporte a nota SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Defina o número de subscrições do Azure e a quota de núcleos para as subscrições diferentes. [Abrir pedidos de suporte para aumentar as quotas de subscrições do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) conforme necessário 
7.  Redução de dados e a migração de dados planearem a migração de dados SAP no Azure. Para os sistemas SAP NetWeaver, o SAP tem diretrizes sobre como manter o volume de um grande número de dados limitados. SAP publicado [este guia profundo](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) sobre a gestão de dados em sistemas de SAP ERP. No entanto, alguns conteúdos se aplica aos sistemas NetWeaver e S/4HANA em geral.
8.  Definir e decidir a abordagem de implantação automatizado. Objetivo da automação por trás de implementações de infraestrutura no Azure é a implementação de uma forma determinista e obter resultados determinísticos. Muitos clientes utilizam o Power Shell ou scripts da CLI com base. No entanto, existem várias tecnologias de código-fonte aberto que podem ser utilizadas para implementar a infraestrutura do Azure para SAP e até mesmo instalar SAP software. Podem encontrar exemplos no github:
    1.  [Implementações automatizadas SAP na Cloud do Azure](https://github.com/Azure/sap-hana)
    2.  [Instalação do SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definir um regular design e a cadência de revisão de implantação entre, como cliente, integrador de sistemas, Microsoft e outras partes de envolvidos

 
## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (muito recomendado)
 
O piloto pode ser executado antes ou em paralelo para planejamento de projetos e preparação. A fase também pode ser utilizada para testar abordagens e design feitas na fase de planejamento e preparação. A fase piloto pode ser transferida para uma verdadeira prova de conceitos. Recomenda-se para configurar e validar uma solução completa de HA/DR, bem como o design de segurança durante uma implantação piloto. Em alguns casos de cliente, testes de escalabilidade também podem ser conduzidos de nesta fase. Outros clientes utilizam a implementação de sistemas de proteção de segurança do SAP como fase piloto. Portanto, partimos do princípio de que identificou um sistema que pretende migrar para o Azure com o objetivo de executar um piloto.

1. Otimize a transferência de dados para o Azure. Altamente dependente de transferência de casos de clientes por meio [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) do local foi mais rápida, se tivesse do circuito de largura de banda suficiente. Com outros clientes, vai através da internet percebeu para ser mais rápida
2. Em caso de uma migração de plataforma heterogêneos do SAP, que envolve uma exportação e importação de banco de dados, testar e otimizar a exportar e importar fases. Para grandes migrações entre o SQL Server como a plataforma de destino, recomendações podem ser encontradas [aqui](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/08/sap-osdb-migration-to-sql-server-faq-v6-2-april-2017/). Pode demorar a abordagem de migração de Monitor/SWPM no caso de não precisar de uma atualização de versão combinada ou [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) processar ao combinar a migração com uma atualização de versão do SAP e satisfazer determinada plataforma DBMS de origem e destino combinações como documentado, por exemplo, no [opção de migração da base de dados (DMO) de soma 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportar a origem, de carregamento de ficheiros de exportação para o desempenho do Azure e importar.  Maximizar a sobreposição entre exportação e importação
   2.  Avalie o volume da base de dados entre a plataforma de destino e de destino de modo a refletirem ao dimensionamento de infraestrutura    
   3.  Validar e otimizar o tempo 
3. Validação técnica 
   1. Tipos de VM
      1.  Validar os recursos em notas de suporte do SAP, diretório de hardware do SAP HANA e SAP PAM novamente para certificar-se de que havia sem alterações de VMs suportadas para o Azure, suportado versões do SO para esses tipos VM suportados e as versões e do SAP DBMS
      2.  Valide novamente o dimensionamento da sua aplicação e a infraestrutura que implementa no Azure. Em caso de mover aplicativos existentes, geralmente pode derivar as SAPS necessários da infraestrutura que utiliza e o [página Web de benchmark do SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-lo com os números de SAPS listados no suporte a nota SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Tenha também [este artigo](https://blogs.msdn.microsoft.com/saponsqlserver/2018/11/04/saps-ratings-on-azure-vms-where-to-look-and-where-you-can-get-confused/) em mente
      3.  Avaliar e testar o dimensionamento de VMs do Azure relativamente a débito máximo de armazenamento e débito de rede de diferentes tipos VM que selecionou na fase de planejamento. Os dados podem ser encontrados no:
          1.  [Tamanhos de máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar o **débito máximo do disco eliminadas do cache** para o dimensionamento
          2.  [Tamanhos de máquinas de virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) é importante considerar o **débito máximo do disco eliminadas do cache** para o dimensionamento
   2. Armazenamento
      1.  Utilizar o armazenamento Premium do Azure para VMs de base de dados
      2.  Utilize [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/)
      3.  Utilize o acelerador de escrita do Azure para discos de registo do DBMS com série M. Lembre-se dos limites de acelerador de escrita e de utilização conforme documentado no [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Para os diferentes tipos DBMS, consulte a [SAP genérico relacionados com a documentação do DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documentação atualizados específicos do DBMS de documento genérico pontos a
      5.  Para o SAP HANA, obter mais detalhes estão documentados em [configurações de infraestrutura de SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Nunca discos de dados do Azure de montagem para uma VM do Linux do Azure com o ID de dispositivo. Em alternativa, utilize o identificador exclusivo universalmente (UUID). Tenha cuidado ao utilizar ferramentas gráficas para discos de dados do Azure de montagem, por exemplo. Verifique as entradas no/etc/fstab para se certificar de que os discos estão montados usando o UUID
          1.  Podem encontrar mais detalhes [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Redes
      1.  Testar e avaliar a sua infraestrutura de VNet e a distribuição das suas aplicações SAP em ou em diferentes redes virtuais do Azure
          1.  Avaliar a abordagem de hub e arquitetura de rede virtual do spoke ou microsegmentation dentro de uma única rede virtual do Azure com base em
              1.  Os custos devido a troca de dados entre [VNets do Azure em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Os custos de verificar [preços da rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Vantagem do fast desligar do peering entre redes virtuais do Azure em comparação com a alteração de NSG para isolar uma sub-rede numa rede virtual para casos em que aplicações ou VMs alojadas numa sub-rede da rede virtual se tornou um risco de segurança
              3.  Registo central e a auditoria de tráfego de rede entre locais, o mundo exterior e o datacenter virtual que criou a cópia de segurança no Azure
          2.  Avaliar e testar o caminho de dados entre a camada de aplicação SAP e a camada do SAP DBMS. 
              1.  A colocação das [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S/4HANA baseada em sistemas SAP não é suportada em todos os
              2.  Não é suportada a colocar a camada de aplicação SAP e do SAP DBMS em diferentes redes virtuais do Azure que não estão emparelhadas
              3.  [Regras ASG e NSG do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) são suportadas para definir rotas entre a camada de aplicação SAP e a camada do SAP DBMS
          3.  Certifique-se de que [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativada nas VMs utilizadas na camada de aplicação SAP e a camada do SAP DBMS. Tenha em atenção que os diferentes níveis de sistema operacional são necessárias para oferecer suporte a redes aceleradas de mensagens em fila no Azure:
              1.  Windows Server 2012 R2 ou versões mais recentes
              2.  SUSE Linux 12 SP3 ou versões mais recentes
              3.  RHEL 7.4 ou versões mais recentes
              4.  Oracle Linux 7.5. Utilizar o kernel RHCKL, a versão tem de ser 3.10.0-862.13.1.el7. Usando o UEK Oracle a versão de kernel 5 é necessário
          4.   Testar e avaliar a latência de rede entre a camada de aplicação SAP VM e DBMS VM, de acordo com a nota de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e o suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avaliar os resultados em relação a documentação de orientação de latência de rede de suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e boa. Exceções aplicam-se para o tráfego entre VMs e instâncias grandes do HANA unidades conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Certifique-se de que as implementações do ILB, estão configuradas para utilizar a devolução direta do servidor. Esta definição irá reduzir a latência em casos em que o Azure ILBs são utilizadas para configurações de elevada disponibilidade na camada de DBMS
   4. Implementações de recuperação do desastre e de disponibilidade elevadas. 
      1. Se implementar a camada de aplicação SAP sem definir uma zona de disponibilidade específica do Azure, certifique-se de que todas as VMs a executar a instância de caixa de diálogo do SAP ou instâncias de middleware de um único sistema SAP são implementadas num [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   No caso de não necessitar de elevada disponibilidade para o SAP Central Services e o DBMS, estas VMs podem ser implementadas no mesmo conjunto de disponibilidade como a camada de aplicação SAP
      2. Se proteger o SAP Central Services e a camada do DBMS para elevada disponibilidade com réplicas passivas, ter os dois nós para SAP Central Services num conjunto de disponibilidade de separado e o nó dois do DBMS no outro conjunto de disponibilidade
      3. Se implementar em zonas de disponibilidade do Azure, não é possível tirar partido dos conjuntos de disponibilidade. No entanto seria necessário para se certificar de que implantar os nós de serviços Central ativos e passivos em duas diferentes zonas de disponibilidade, que mostram a menor latência entre zonas.
         1.   Lembre-se de que precisa usar [Balanceador de carga Standard do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) para o caso de estabelecimento de Windows ou Pacemaker Clusters de ativação pós-falha para a camada do DBMS e SAP Central Services em zonas de disponibilidade. [Balanceador de carga básico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) não pode ser utilizado para implementações zonais 
   5. Definições de tempo limite
      1. Verifique os rastreios de desenvolvedor do SAP NetWeaver das diferentes instâncias do SAP e certifique-se de que nenhum quebras de ligação entre o servidor de colocar em fila e os processos de trabalho SAP são indicadas. Essas quebras de ligação podem ser evitadas ao definir estes parâmetros de registo de dois:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - see also [this article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - see also [this article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Para evitar a GUI detalhes sobre o tempo entre um local implementado interfaces de GUI do SAP e camadas de aplicação SAP implementadas no Azure, verifique se os seguintes parâmetros estão definidos no default.pfl ou no perfil de instância:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Se utilizar uma configuração de Cluster de ativação pós-falha do Windows, certifique-se de que o tempo para reagir em nós não responsivo está corretamente definido para o Azure. O artigo da Microsoft [otimização limiares de rede de Cluster de ativação pós-falha](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) apresenta uma lista de parâmetros e como afetam aqueles sensitivities de ativação pós-falha. Os parâmetros listados esses dois parâmetros devem ser definidos com os valores:
         1.   SameSubNetDelay = 2
         2.   SameSubNetThreshold = 15
4. Testar seus procedimentos de recuperação após desastre e de disponibilidade elevados
   1. Simule situações de ativação pós-falha ao encerrar VMs (Windows SO convidado) ou colocando os sistemas operativos no modo entre em pânico (no SO do Linux convidado) para descobrir se as configurações de ativação pós-falha funcionem conforme projetado. 
   2. Medir os tempos que demora a executar uma ativação pós-falha. Se os tempos de demorarem demasiado tempo, considere:
      1.   Para o SUSE Linux, utilizar dispositivos SBD em vez do agente de barreiras do Azure para acelerar a ativação pós-falha
      2.   Para o SAP HANA, se o recarregamento de dados demora muito considerar para aprovisionar mais largura de banda de armazenamento
   3. Testar a sequência de cópia de segurança/restauro e temporização das atualizações e otimizar se necessário. Certifique-se de que os tempos de backup não só são suficientes. Também testar a restauração e assumir o período de tempo de atividades de restauro. Certifique-se de que os tempos de restauro são dentro de seus SLAs RTO em que o RTO se baseia numa base de dados ou o processo de restauração de VM
   4. Teste em arquitetura e a funcionalidade de região DR
5. Verificações de segurança
   1.  Teste a validade da função do Azure com a base de arquitetura de acesso (RBAC) que implementou. Objetivo é limitar o acesso e permissões das equipes diferentes e separados. Por exemplo, os membros da equipe de SAP base devem ser capazes de implementar VMs e atribuir os discos de armazenamento do Azure numa determinada rede virtual do Azure. No entanto a equipe de base de SAP não deve ser capaz de criar redes virtuais próprias ou alterar as definições de redes virtuais existentes. No outro lado, os membros da equipe de rede não devem ser capazes de implantar VMs em redes virtuais em que aplicação SAP e DBMS VMs estão em execução. Nem membros da equipe de rede será capazes de alterar os atributos de VMs ou até mesmo eliminar as VMs ou discos.  
   2.  Certifique-se [NSG e ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) regras estão a funcionar conforme esperado e protegem os recursos protegidos
   3.  Certifique-se de que todos os recursos que precisam de ser encriptados são encriptados. Definir e executar processos de cópia de segurança de certificados, armazenar e aceder a esses certificados e restaurar as entidades encriptadas. 
   4.  Uso [do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) e/ou para discos de SO, onde possível de um sistema operacional suporte o ponto de vista
   5.  Verifique que não muitas camadas de encriptação terem sido utilizadas. Faz sentido limitado para utilizar a encriptação de disco do Azure e, em seguida, no principais um dos métodos de encriptação da base de dados transparente do DBMS
6. Testes de Desempenho
   1.  No SAP com base nas medições e de rastreio de SAP, comparar relatórios online de 10 principais para a implementação atual, quando aplicável 
   2.  No SAP com base nas medições e de rastreio de SAP, comparar tarefas de lote de 10 principais para a implementação atual, quando aplicável 
   3.  No SAP com base nas medições e de rastreio de SAP, compare as transferências de dados por meio de interfaces no sistema SAP. Concentre-se nas interfaces em que sabe que a transferência será agora entre diferentes localizações, como a mudança a partir do local para o Azure 


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, partimos do princípio que após um piloto com êxito ou a prova de conceito, está começando a implantar os sistemas SAP de não produção para o Azure. Todas as lições aprendidas e experiências de prova de conceitos devem ser adaptadas para o tipo de implementação. Todos os critérios e os passos listados na prova de conceito da aplicam-se neste tipo de Implantações. Nesta fase, normalmente, implementa sistemas de desenvolvimento, sistemas de testes de unidade e sistemas de testes de regressão de negócio no Azure. É recomendável que pelo menos um sistema de não produção em uma linha de aplicação SAP tem a configuração completa de elevada disponibilidade, como o sistema de produção futuras vai ter. Passos adicionais que precisa considerar durante a fase são:  

1.  Antes de passar sistemas da plataforma antigo para o Azure recolhem dados de consumo de recursos, como dados de IOPS, débito de armazenamento e utilização de CPU. Especialmente das unidades de camada DBMS, mas também a partir de unidades de camada de aplicativo. Também medir a latência de rede e armazenamento.
2.  Registe os padrões de tempo de utilização de disponibilidade dos sistemas. Objetivo é descobrir se os sistemas de não produção precisam ser 7 x 24 disponíveis ou se existem sistemas de não produção que podem ser encerrados durante determinadas fases de uma semana ou mês
3.  Teste e definir se pretende criar o próprias imagens do sistema operacional para as VMs no Azure ou se pretende utilizar uma imagem fora da Galeria de imagens do Azure. Se estiver a utilizar uma imagem fora da galeria do Azure, certifique-se de que obter a imagem correta que reflete o contrato de suporte junto do fornecedor do sistema operacional. Para alguns fornecedores de SO, galerias do Azure oferecem para usar as suas próprias imagens de licença. Para outras imagens do sistema operacional, suporte está incluído no preço indicado pelo Azure. Se optar por criar suas próprias imagens de sistema operacional, pode encontrar documentação nestes artigos:
    1.  Pode criar uma imagem generalizada de uma VM do Windows implementadas no Azure com base em [esta documentação](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Pode criar uma imagem generalizada de um Linux VM implementada no Azure com base em [esta documentação](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se utilizar imagens do SUSE e do Red Hat Linux a partir da Galeria VM do Azure, tem de utilizar as imagens para SAP fornecida pelos fornecedores do Linux na Galeria VM do Azure
4.  Certifique-se de que satisfazem os requisitos de suporte que SAP tem sobre contratos de suporte da Microsoft. Pode encontrar informações no suporte a nota SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Para as instâncias grandes do HANA, consulte o documento [requisitos de integração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Certifique-se de que as pessoas certas obtenham [planeada notificações de manutenção](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), por isso, que pode escolher o tempo de inatividade e um reinício de VMs no tempo
5.  Modo constante Verifique a documentação do Azure de apresentações de Microsoft sobre canais, como [Channel9](https://channel9.msdn.com/) a nova funcionalidade que podem ser aplicadas às suas implementações
6.  Verificação de SAP notas relacionadas com o Azure, como suporte Nota [#1928533](https://launchpad.support.sap.com/#/notes/1928533) para novos SKUs de VM ou a versão de SO e o DBMS recentemente suportado. Compare novo tipos de tipos de VM em relação a VM mais antigas dos preços, então, que é possível implementar VMs com a melhor taxa de relação preço/desempenho
7.  Validar os recursos em notas de suporte do SAP, diretório de hardware do SAP HANA e novamente a PAM SAP para se certificar de que não havia alterações nas VMs suportadas para o Azure, versões do SO suportado nesses VMs e SAP e DBMS suportadas versões
8.  Verifique [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para SKUs no Azure e comparação de preços por aqueles que o planejado e, eventualmente, alterar para obter as unidades com melhor ration de desempenho de preço com certificação HANA novo 
9.  Adaptar seus scripts de implementação para tirar partido dos novos tipos VM e incorporar os novos recursos do Azure que pretende utilizar
10. Após a implementação da infraestrutura, testar e avaliar a latência de rede entre a camada de aplicação SAP VM e DBMS VM, de acordo com a nota de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e o suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avaliar os resultados em relação a documentação de orientação de latência de rede de suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e boa. Exceções aplicam-se para o tráfego entre VMs e instâncias grandes do HANA unidades conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Certifique-se de que nenhuma das restrições mencionadas [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e [configurações de infraestrutura de SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) aplicam-se a sua implementação
11. Executar todas as outras verificações, conforme listado na fase de prova de conceitos, antes de aplicar a carga de trabalho
12. Como se aplica a carga de trabalho, registe o consumo de recursos desses sistemas no Azure e comparar com os registos de que obteve da sua plataforma antiga. Ajuste o dimensionamento de VM de Implantações futuras se vir que tem diferenças grandes. Tenha em atenção que, em caso de downsizing, armazenamento e larguras de banda de rede de uma VM, terá uma redução também:
    1.  [Tamanhos de máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Tamanhos de máquinas de virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Trabalhar em processos e funcionalidade de cópia do sistema. Objetivo é facilitar para que possa copiar um sistema de desenvolvimento ou um sistema de teste, por isso, em que as equipes de projeto pode obter novos sistemas rapidamente. Considere [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) como uma ferramenta efetuando tarefas.
14. Otimizar e aprimorar sua equipe acessos de função do Azure com base, permissões e processos para certificar-se de que tem uma separação de imposto do lado. No outro lado, deseja ter todas as equipes de ativado para realizar suas tarefas na infraestrutura do Azure.
15. Exercício, teste e procedimentos de recuperação de desastre e elevada disponibilidade documento para ativar a sua equipe para executar essas tarefas. Identificar deficiências e adaptar-se a nova funcionalidade do Azure que estiver a integrar nas suas implementações

 
## <a name="production-preparation-phase"></a>Fase de preparação de produção 
Nesta fase, que pretende recolher todas as experiências e lições aprendidas das suas implementações de não produção e aplicá-las no futuro implementações de produção. Além para as fases de antes, também tem de preparar o trabalho de transferência de dados entre a localização de alojamento atual e o Azure. 

1.  Trabalhar com atualizações de versão do SAP necessárias dos seus sistemas de produção antes de passar para o Azure
2.  Concordo com os proprietários de empresas sobre o funcional e testes de empresas que precisam ser conduzido após a migração do sistema de produção
    1.  Certifique-se de que todos esses testes são executados com os sistemas de origem na localização de alojamento atual. Deseja evitar testes que está a ser realizadas pela primeira vez depois do sistema é movido para o Azure
2.  Teste o processo de migração de produção no Azure. Em caso de não passar todos os sistemas de produção para o Azure no mesmo intervalo de tempo, crie grupos de sistemas de produção que precisam estar na mesma localização de alojamento. Migração de dados de exercício e de teste. Lista de métodos comuns, como:
    1.  Usando métodos DBMS, como backup/restauração em combinação com o AlwaysOn do SQL Server, o HANA System Replication ou o registo de envio para efetuar o seeding e sincronizar o conteúdo de base de dados do Azure
    2.  Utilize a cópia de segurança/restauro para bases de dados mais pequenos
    3.  Utilização SAP migração Monitor implementadas em ferramenta SAP SWPM para executar migrações heterogêneas
    4.  Utilize o [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) processar se tiver de combinar com uma atualização de versão do SAP. Tenha em mente que nem todas as combinações entre a origem e destino DBMS são suportadas. Podem encontrar mais informações nas notas de suporte SAP específicas para as várias versões do DMO. Por exemplo, [opção de migração da base de dados (DMO) de soma 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Se a transferência de dados através da internet ou através do ExpressRoute é melhor na taxa de transferência no caso de precisa de mover as cópias de segurança ou SAP exportar ficheiros de teste. Para o caso de mover dados através da internet, poderá ter de alterar algumas das suas regras de segurança NSG/ASG que tem de ter no local para sistemas de produção futuras
3.  Antes de passar sistemas da plataforma antigo para o Azure recolhem dados de consumo de recursos, como dados de IOPS, débito de armazenamento e utilização de CPU. Especialmente das unidades de camada DBMS, mas também a partir de unidades de camada de aplicativo. Também medir a latência de rede e armazenamento.
4.  Validar os recursos em notas de suporte do SAP, diretório de hardware do SAP HANA e novamente a PAM SAP para se certificar de que não havia alterações nas VMs suportadas para o Azure, versões do SO suportado nesses VMs e SAP e DBMS suportadas versões 
4.  Adaptar scripts de implantação para as alterações mais recentes que decidiu sobre tipos de VM e a funcionalidade do Azure
5.  Após a implementação da infraestrutura e aplicação passar por uma série de verificações em ordem para validar:
    1.  Os tipos VM corretos foram implementados com os atributos corretos e os tamanhos de armazenamento
    2.  Verifique se as VMs estão no correto e pretendido versões do SO e os patches e são uniformes
    3.  Verifique se o sistema de proteção das VMs estão tão necessária e uniforme
    4.  Verifique se a aplicação correta versões e patches de obteve instalados e implementados
    5.  As VMs foram implementadas em conjuntos de disponibilidade do Azure conforme planejado
    6.  Armazenamento Premium do Azure foi utilizado para os discos de confidenciais de latência ou onde o [única VM SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) é necessário
    7.  Verifique a existência de implementação correta de acelerador de escrita do Azure
        1.  Certifique-se de que dentro da VM, espaços de armazenamento ou stripe conjuntos de obteve criados corretamente em discos que precisam de suporte do acelerador de escrita do Azure
            1.  Verificar [configurar o RAID de Software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Verificar [configurar LVM numa VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/) foram utilizados exclusivamente
    9.  Tem de implementar VMs nos conjuntos de disponibilidade correto e as zonas de disponibilidade
    10. Certifique-se de que [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativada nas VMs utilizadas na camada de aplicação SAP e a camada do SAP DBMS
    11. Sem colocação dos [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S/4HANA com base em sistemas SAP
    12. Regras ASG e NSG permitir a comunicação como desejado e será planejada e bloqueia a comunicação onde for necessário
    13. Definições de tempo limite conforme descrito anteriormente foram definidas corretamente
    14. Testar e avaliar a latência de rede entre a camada de aplicação SAP VM e DBMS VM, de acordo com a nota de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e o suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avaliar os resultados em relação a documentação de orientação de latência de rede de suporte a nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e boa. Exceções aplicam-se para o tráfego entre VMs e instâncias grandes do HANA unidades conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Verifique se a encriptação foram implementada quando necessário e com o método de encriptação necessário
    16. Verifique se interfaces e outras aplicações podem ligar-se a infraestrutura recentemente implementada
6.  Crie um playbook para reagir a manutenção planeada do Azure. Definir a ordem dos sistemas e VMs de ser reiniciado em caso de manutenção planeada
    

## <a name="go-live-phase"></a>Aceda a fase em direto
Para a fase de Go-Live, terá de certificar-se de que siga os playbooks desenvolvido em fases anteriores. Execute os passos que testadas e treinado. Não aceite as alterações de última hora no processo e configurações. Além disso, aplique as seguintes medidas:

1. Certifique-se de que a monitorização de portal do Azure e outras ferramentas de monitorização estão a funcionar.  Ferramentas recomendadas são Perfmon (Windows) ou Kong (Linux): 
    1.  Contadores de CPU 
        1.  Tempo médio de CPU – Total (CPU de todos os)
        2.  Tempo médio de CPU – cada processador individual (até 128 processadores m128 VM)
        3.  Kernel de tempo de CPU – cada processador individual
        4.  Utilizador de tempo de CPU – cada processador individual
    5.  Memória 
        1.  Memória livre
        2.  Página de memória/seg
        3.  Página de memória out/seg
    4.  Disco 
        1.  Kb/seg – por disco individual de leitura do disco 
        2.  Leituras de disco/seg – por disco individual
        3.  Ms/leitura – por disco individual de leitura do disco
        4.  Escrita de disco kb/seg – por disco individual 
        5.  Escrita de disco/seg – por disco individual
        6.  Ms de escrita de disco/leitura – por disco individual
    5.  Rede 
        1.  Pacotes de rede/seg
        2.  Pacotes de rede de saída/seg
        3.  Rede kb/seg
        4.  Rede kb out/seg 
2.  Após a migração dos dados, execute todos os testes de validação que faz um acordo com os proprietários do negócio. Aceitar apenas os resultados do teste de validação, onde tem resultados para os sistemas de origem original
3.  Verifique se interfaces estão a funcionar e se outros aplicativos podem se comunicar com os sistemas de produção recentemente implementado
4.  Verificar a correção e transporte do sistema por meio de transação de SAP STMS
5.  Executar cópias de segurança da base de dados depois do sistema é lançado para produção
6.  Executar cópias de segurança VM para a camada de aplicação SAP VMs depois do sistema é lançado para produção
7.  Para SAP sistemas que não fazem parte do atual go-live fase, mas se comunicar com os sistemas SAP que movido para o Azure nesta fase de go-live, terá de repor o buffer de nome de anfitrião no SM51. Este passo irá eliminar os endereços IP em cache antigos associados com os nomes das instâncias de aplicações, movida para o Azure  


## <a name="post-production"></a>Produção de postagem
Nesta fase, é tudo uma questão de monitorização, operação e administração do sistema. Do SAP ponto de vista, aplicam-se as tarefas normais que eram necessárias para realizar com sua localização de alojamento antiga. As tarefas específicas do Azure que pretende fazer são:

1. Analisar faturas do Azure para sistemas de carregamento elevados
2. Otimizar a eficiência de relação preço/desempenho no lado VM e do lado do armazenamento
3. Otimizar o tempo que podem ser encerrar os sistemas  


## <a name="next-steps"></a>Próximos Passos
Consulte a documentação:

- [Máquinas de virtuais de planeamento e implementação para o SAP NetWeaver do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

