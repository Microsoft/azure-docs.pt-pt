---
title: Lista de verificação de planejamento e implantação de carga de trabalho SAP | Microsoft Docs
description: lista de verificação para planejamento e implantações de carga de trabalho do SAP no Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d6170dead4e8577cea6883ffea25b90ebe39b88
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078998"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Lista de verificação de carga de trabalho do SAP no planejamento e implantação do Azure 

Esta lista de verificação foi projetada para clientes que movem seus aplicativos SAP NetWeaver, S/4HANA e Hybris para a infraestrutura do Azure como um serviço.  Esta lista de verificação deve ser revisada por um cliente e/ou parceiro SAP durante a duração do projeto. É importante que muitas das verificações sejam realizadas no início do projeto e na fase de planejamento. Depois que a implantação for concluída, as alterações elementares na infraestrutura implantada do Azure ou em versões de software SAP podem se tornar complexas. Examine esta lista de verificação em principais etapas em todo o projeto.  Pequenos problemas podem ser detectados antes que eles se tornem grandes problemas e há tempo suficiente para refazer a engenharia e testar as alterações necessárias. A lista de verificação de nenhuma maneira alega ser concluída. Dependendo de sua situação individual, pode haver muito mais verificações que precisam ser realizadas. 

A lista de verificação montada não inclui tarefas que são independentes do Azure.  Exemplo: As interfaces de aplicativo SAP mudam durante uma mudança para a nuvem pública do Azure ou para um provedor de hospedagem.    

Esta lista de verificação também pode ser usada para sistemas já implantados. Novos recursos, como Acelerador de Gravação, Zonas de Disponibilidade e novos tipos de VM, podem ter sido adicionados desde que você implantou.  Portanto, é útil revisar a lista de verificação periodicamente para garantir que você esteja ciente dos novos recursos na plataforma Azure. 

## <a name="project-preparation-and-planning-phase"></a>Fase de preparação e planejamento do projeto
Nesta fase, uma migração da carga de trabalho do SAP para a nuvem pública do Azure é planejada. O conjunto mínimo de entidades e itens discutidos e uma lista definida como:

1. Documento de design de alto nível – este documento deve conter:
    1. O inventário atual de componentes e aplicativos SAP e o inventário de aplicativos de destino no Azure
    2. Crie e trabalhe com uma matriz de atribuição de responsabilidade (RACI) que define as responsabilidades e atribuições das diferentes partes envolvidas. Comece no alto nível e trabalhe com níveis mais e mais granulares de taxa de transferência do planejamento e das primeiras implantações
    2. Uma arquitetura de solução de alto nível
    3. Decisão sobre as regiões do Azure para implantar no. Para obter uma lista de regiões do Azure, verifique as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Para serviços disponíveis em cada uma das regiões do Azure, verifique o artigo [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/)
    4. Arquitetura de rede para se conectar do local ao Azure. Comece a se familiarizar com o [Blueprint do datacenter virtual para o Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Princípios de segurança para executar dados de alto impacto nos negócios no Azure. Para ler o material de início com a [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)
2.  Documento de design técnico – que contém:
    1.  Um diagrama de bloco de solução 
    2.  Dimensionamento de componentes de computação, armazenamento e rede no Azure. Para o dimensionamento do SAP de VMs do Azure, consulte a observação de suporte do SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Continuidade dos negócios e arquitetura de recuperação de desastre
    4.  Versões detalhadas do pacote de suporte do sistema operacional, DB, kernel e SAP. Não é um dado que qualquer versão do sistema operacional compatível com SAP NetWeaver ou S/4HANA seja suportada em VMs do Azure. O mesmo vale para as versões do DBMS. É obrigatório que as seguintes fontes sejam verificadas para alinhar e, se necessário, atualizar as versões do SAP, as versões do DBMS ou as versões do sistema operacional para que estejam em uma janela do SAP e do Azure com suporte. É obrigatório que você esteja no SAP e nas combinações de versões com suporte do Azure para obter suporte completo da SAP e da Microsoft. Se necessário, você precisa planejar a atualização de alguns componentes de software. Mais detalhes sobre o software SAP, so e DBMS com suporte estão documentados nestes locais:
        1.  [#1928533](https://launchpad.support.sap.com/#/notes/1928533)de observação de suporte SAP. Esta observação define as versões mínimas do sistema operacional com suporte em VMs do Azure. Ele também define as versões mínimas do banco de dados necessárias para a maioria dos bancos de dados do HANA. A observação também apresenta o dimensionamento SAP dos diferentes tipos de VM do Azure com suporte do SAP.
        2.  [#2039619](https://launchpad.support.sap.com/#/notes/2039619)de observação de suporte SAP. A observação define a matriz de suporte Oracle no Azure. Observe que o Oracle dá suporte apenas ao Windows e Oracle Linux como SO convidado no Azure para carga de trabalho do SAP. Essa instrução de suporte também se aplica à camada de aplicativo SAP que executa instâncias SAP. No entanto, a Oracle não oferece suporte à alta disponibilidade para os serviços centrais do SAP no Oracle Linux por meio do pacemaker. Se precisar de alta disponibilidade para ASCS em Oracle Linux, você precisará aproveitar o SIOS Protection Suite para Linux. Para obter dados de certificação SAP detalhados, consulte a observação de suporte do SAP [#1662610-detalhes do suporte para o sios Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para o Windows, há suporte para a solução de failover de cluster de failover do Windows do SAP para serviços centrais do SAP em conjunto com a Oracle como camada de DBMS. 
        3.  Nota de suporte da SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) obter a matriz de suporte para SAP Hana nas diferentes versões do sistema operacional
        4.  SAP HANA VMs do Azure com suporte e [instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) estão listadas [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Matriz de disponibilidade de produto SAP](https://support.sap.com/en/)
        6.  Outras notas SAP para outros produtos SAP específicos  
    5.  Recomendamos designs de três camadas estritos para sistemas de produção SAP. A combinação de ASCS + servidores de aplicativos na mesma VM não é recomendada.  O uso de configurações de cluster de vários SID para serviços centrais do SAP é compatível com o Windows como SO convidado no Azure. Enquanto não há suporte para configurações de cluster de vários SID do SAP central Services com sistemas operacionais Linux no Azure. A documentação para o caso do sistema operacional convidado do Windows pode ser encontrada em:
        1.  [Alta disponibilidade da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Alta disponibilidade da instância do SAP ASCS/SCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Arquitetura de alta disponibilidade e recuperação de desastres
        1.  Defina com base no RTO e no RPO do que a arquitetura de alta disponibilidade e recuperação de desastre precisa ser semelhante
        2.  Para alta disponibilidade na mesma zona, verifique o que o DBMS desejado tem a oferecer no Azure. A maioria dos DBMS oferece métodos síncronos de uma espera ativa síncrona, que é recomendável para sistemas de produção. Verifique também a documentação relacionada ao SAP para os diferentes bancos de dados que começam com [considerações para implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documentos relacionados
            1.  **Não** há suporte para o uso do serviço de cluster de failover do Windows com a configuração de disco compartilhado para a camada DBMS como, por exemplo, descrito para SQL Server [aqui](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) . Em vez disso, soluções como:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Proteção de dados Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replicação do sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Para a recuperação de desastres em diferentes regiões do Azure, verifique quais possibilidades são oferecidas pelos diferentes fornecedores de DBMS. A maioria deles dá suporte à replicação assíncrona ou ao envio de logs
        4.  Para a camada de aplicativo SAP, defina se você executaria seus sistemas de teste de regressão de negócios, o que ideal é réplicas de suas implantações de produção, na mesma região do Azure ou em sua região de recuperação de desastres. No último caso, você pode direcionar esse sistema de regressão de negócios como destino de recuperação de desastre para sua produção
        5.  Se você decidir não posicionar os sistemas que não são de produção no site de recuperação de desastres, examine Azure Site Recovery como método viável de replicar a camada do aplicativo SAP na região de recuperação de desastre do Azure. Consulte também [Configurar a recuperação de desastre para uma implantação de aplicativo SAP NetWeaver de várias camadas](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Se você decidir usar uma configuração combinada de HA/DR, aproveitar [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) se familiarizar com as regiões do Azure em que zonas de disponibilidade estão disponíveis e com restrições que podem ser introduzidas por latências de rede maiores entre dois Zonas de Disponibilidade  
3.  O cliente/parceiro deve criar um inventário de todas as interfaces SAP (SAP e não SAP). 
4.  Design do design de serviços de fundação-esse design inclui itens como
    1.  Design de Active Directory e DNS
    2.  Topologia de rede no Azure e atribuição de diferentes sistemas SAP
    3.  Estrutura de [acesso baseada em função](https://docs.microsoft.com/azure/role-based-access-control/overview) para suas diferentes equipes que gerenciam aplicativos SAP e infraestrutura no Azure
    3.  Topologia do grupo de recursos 
    4.  [Estratégia de marcação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Convenção de nomenclatura para VMs e outros componentes de infraestrutura e/ou nomes lógicos
5.  Contrato do Microsoft Suporte Premier – identificar o TAM (gerente técnico de contas) da MS. Para obter requisitos de suporte pelo SAP, leia nota de suporte SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Defina o número de assinaturas do Azure e a cota de núcleo para as diferentes assinaturas. [Abrir solicitações de suporte para aumentar as cotas de assinaturas do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) conforme necessário 
7.  Data de redução de dados e plano de migração de dados para migrar dados SAP para o Azure. Para sistemas SAP NetWeaver, a SAP tem diretrizes sobre como manter o volume de um grande número de dados limitados. O SAP publicou [este guia profundo sobre o](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) gerenciamento de dados em sistemas SAP ERP. No entanto, algum conteúdo se aplica aos sistemas NetWeaver e S/4HANA em geral.
8.  Defina e decida a abordagem de implantação automatizada. O objetivo da automação por trás das implantações de infraestrutura no Azure é implantar de maneira determinística e obter resultados determinísticos. Muitos clientes usam scripts de Shell de energia ou baseados na CLI. Mas há várias tecnologias de código-fonte aberto que podem ser usadas para implantar a infraestrutura do Azure para SAP e até mesmo instalar o software SAP. Exemplos podem ser encontrados no GitHub:
    1.  [Implantações automatizadas do SAP na nuvem do Azure](https://github.com/Azure/sap-hana)
    2.  [Instalação do SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Defina uma cadência regular de design e de análise de implantação entre você como cliente, integrador de sistemas, Microsoft e outras partes envolvidas

 
## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (altamente recomendável)
 
O piloto pode ser executado antes ou em paralelo ao planejamento e à preparação do projeto. A fase também pode ser usada para testar abordagens e design feitos na fase de planejamento e preparação. A fase piloto pode ser ampliada para uma prova real de conceitos. É recomendável configurar e validar uma solução completa de HA/DR, bem como o design de segurança durante uma implantação piloto. Em alguns casos de clientes, os testes de escalabilidade também podem ser conduzidos nesta fase. Outros clientes usam a implantação de sistemas de área restrita do SAP como fase piloto. Portanto, supomos que você identificou um sistema que deseja migrar para o Azure com a finalidade de executar um piloto.

1. Otimize a transferência de dados para o Azure. A transferência de casos de clientes altamente dependente do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) do local foi mais rápida se o circuito expresso tiver largura de banda suficiente. Com outros clientes, passar pela Internet percebeu que é mais rápido
2. No caso de uma migração de plataforma heterogênea do SAP, que envolve uma exportação e uma importação dos dados do banco, teste e otimize as fases de exportação e importação. Para grandes migrações envolvendo SQL Server como a plataforma de destino, as recomendações podem ser encontradas [aqui](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Você pode usar a abordagem de monitor de migração/SWPM caso não precise de um processo de atualização de versão combinada ou do [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) ao combinar a migração com uma atualização de versão do SAP e atender a determinadas combinações de plataforma de DBMS de origem e destino como documentado, por exemplo, na [DMO (opção de migração de banco de dados) de SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportar para origem, exportar upload de arquivo para o Azure e importar o desempenho.  Maximizar sobreposição entre exportação e importação
   2.  Avaliar o volume de banco de dados entre a plataforma de destino e de destino para refletir no dimensionamento da infraestrutura    
   3.  Validar e otimizar o tempo 
3. Validação técnica 
   1. Tipos de VM
      1.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte para esses tipos de VM e as versões de SAP e DBMS com suporte
      2.  Valide novamente o dimensionamento do seu aplicativo e a infraestrutura implantada no Azure. No caso de movimentação de aplicativos existentes, muitas vezes você pode derivar os SAPS necessários da infraestrutura que você usa e a [página da Web de benchmark do SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-los com os números de SAPs listados na nota de suporte SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Mantenha [Este artigo](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) em mente também
      3.  Avalie e teste o dimensionamento de suas VMs do Azure com relação à taxa de transferência máxima de armazenamento e à taxa de transferência de rede dos diferentes tipos de VM que você escolheu na fase de planejamento. Os dados podem ser encontrados em:
          1.  [Tamanhos das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar a taxa de **transferência máxima do disco não armazenado em cache** para o dimensionamento
          2.  [Tamanhos das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) É importante considerar a taxa de **transferência máxima do disco não armazenado em cache** para o dimensionamento
   2. Armazenamento
      1.  Usar o [armazenamento de SSD standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) como mínimo para VMs que representam as camadas de aplicativos SAP e para a implantação de DBMS não sensível ao desempenho
      2.  Recomendamos não usar os [discos do Azure HDD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) em geral
      2.  Usar o [armazenamento Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) para qualquer VM do DBMS que seja sensível ao desempenho remotamente
      2.  Usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Use o Azure Acelerador de Gravação para unidades de log DBMS com a série M. Lembre-se dos limites e do uso do acelerador de gravação, conforme documentado em [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Para os diferentes tipos de DBMS, verifique a [documentação genérica do DBMS relacionado ao SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e as documentações específicas do DBMS que o documento genérico aponta para
      5.  Por SAP HANA, mais detalhes estão documentados em [configurações de infraestrutura SAP Hana e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Nunca monte os discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o UUID (identificador universal exclusivo). Tenha cuidado ao usar ferramentas gráficas para montar discos de dados do Azure, por exemplo. Verifique as entradas em/etc/fstab para garantir que os discos sejam montados usando o UUID
          1.  Podem encontrar mais detalhes [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Redes
      1.  Testar e avaliar sua infraestrutura de VNet e a distribuição de seus aplicativos SAP entre as diferentes redes virtuais do Azure ou dentro delas
          1.  Avaliar a abordagem de arquitetura de rede virtual Hub e spoke ou microsegmentação em uma única rede virtual do Azure com base em
              1.  Custos devido à troca de dados entre o [Azure VNets emparelhado](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Para custos, verifique os [preços da rede virtual](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Vantagem de desconectar rapidamente do emparelhamento entre redes virtuais do Azure em comparação a alterar NSG para isolar uma sub-rede em uma rede virtual para casos em que os aplicativos ou as VMs hospedadas em uma sub-rede da rede virtual se tornaram um risco de segurança
              3.  Registro em log central e auditoria de tráfego de rede entre o local, o mundo exterior e o datacenter virtual que você criou no Azure
          2.  Avalie e teste o caminho de dados entre a camada de aplicativo SAP e a camada do SAP DBMS. 
              1.  Qualquer posicionamento de [dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S/4HANA baseados em sistemas SAP não tem suporte
              2.  Não há suporte para a colocação da camada de aplicativo SAP e do DBMS do SAP em diferentes redes virtuais do Azure que não são emparelhadas
              3.  [As regras ASG e NSG do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) têm suporte para definir rotas entre a camada de aplicativo SAP e a camada de DBMS SAP
          3.  Verifique se a [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs usadas na camada de aplicativo SAP e na camada do DBMS SAP. Tenha em mente que diferentes níveis de sistema operacional são necessários para dar suporte à rede acelerada no Azure:
              1.  Windows Server 2012 R2 ou versões mais recentes
              2.  SUSE Linux 12 SP3 ou versões mais recentes
              3.  RHEL 7,4 ou versões mais recentes
              4.  Oracle Linux 7,5. Usando o kernel RHCKL, a versão precisa ser 3.10.0-862.13.1. EL7. O uso do Oracle UEK kernel versão 5 é necessário
          4.   Teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados contra as diretrizes de latência de rede da observação de suporte do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Verifique se as implantações ILB estão configuradas para usar o retorno de servidor direto. Essa configuração reduzirá a latência nos casos em que os ILBs do Azure são usados para configurações de alta disponibilidade na camada do DBMS
          6.   Se você estiver usando Azure Load Balancer em conjunto com os sistemas operacionais convidados do Linux, verifique se o parâmetro de rede do Linux **net. IPv4. TCP _timestamps** está definido como **0**. Contra as recomendações em versões mais antigas do SAP Note [#2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP, enquanto isso, é atualizada para refletir o fato de que o parâmetro precisa ser definido como 0 para trabalhar em conjunto com os balanceadores de carga do Azure.
          7.   Considere usar o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md) para obter a latência de rede ideal.
   4. Implantações de alta disponibilidade e recuperação de desastres. 
      1. Se você implantar a camada de aplicativo do SAP sem definir uma zona de disponibilidade do Azure específica, certifique-se de que todas as VMs que executam instâncias de caixa de diálogo ou de middleware do SAP de um único sistema SAP sejam implantadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Caso você não precise de alta disponibilidade para os serviços SAP central e o DBMS, essas VMs podem ser implantadas no mesmo conjunto de disponibilidade que a camada de aplicativo SAP
      2. Se você proteger os serviços SAP central e a camada de DBMS para alta disponibilidade com réplicas passivas, tenha os dois nós para os serviços centrais do SAP em um conjunto de disponibilidade separado e os dois nós DBMS em outro conjunto de disponibilidade
      3. Se você implantar o no Zonas de Disponibilidade do Azure, não poderá aproveitar os conjuntos de disponibilidade. No entanto, você precisaria se certificar de implantar os nós de serviços centrais ativos e passivos em duas Zonas de Disponibilidade diferentes, o que mostra a menor latência entre as zonas.
         1.   Tenha em mente que você precisa usar o [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) para o caso de estabelecer clusters de failover do Windows ou pacemaker para o DBMS e a camada de serviços centrais do SAP em zonas de disponibilidade. O [Load Balancer básico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) não pode ser usado para implantações zonais 
   5. Configurações de tempo limite
      1. Verifique os rastreamentos de desenvolvedor do SAP NetWeaver das diferentes instâncias do SAP e certifique-se de que não haja quebras de conexão entre o servidor de enfileiramento e os processos de trabalho do SAP. Essas quebras de conexão podem ser evitadas definindo esses dois parâmetros de registro:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000-consulte também [Este artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000-consulte também [Este artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Para evitar o tempo limite da GUI entre as interfaces de GUI do SAP implantadas por um local e as camadas de aplicativo SAP implantadas no Azure, verifique se os seguintes parâmetros estão definidos no default. PFL ou no perfil de instância:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/KeepAlive = 20
      3. Para evitar a interrupção de conexões estabelecidas entre o processo de enfileiramento do SAP e o SAP workprocesse, o parâmetro enfileirar/encni/set_so_keepalive precisa ser definido como "true". Consulte também o [SAP Note #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Se você usar uma configuração de cluster de failover do Windows, certifique-se de que o tempo para reagir em nós não responsivos esteja definido corretamente para o Azure. O artigo da Microsoft [ajustando limites de rede de cluster de failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) lista parâmetros e como eles afetam o failover diferenciação. Supondo que os nós de cluster estejam na mesma sub-rede, os seguintes parâmetros devem ser alterados:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Teste seus procedimentos de alta disponibilidade e recuperação de desastres
   1. Simule situações de failover desligando VMs (SO convidado do Windows) ou colocando sistemas operacionais em modo de pane (SO convidado do Linux) para descobrir se as configurações de failover funcionam conforme projetado. 
   2. Meça seu tempo necessário para executar um failover. Se os tempos demorarem muito, considere:
      1.   Para o SUSE Linux, use dispositivos SBD em vez do agente de isolamento do Azure para acelerar o failover
      2.   Por SAP HANA, se o recarregamento de dados levar muito tempo, considere o provisionamento de mais largura de banda de armazenamento
   3. Teste a sequência de backup/restauração e o tempo e ajuste, se necessário. Certifique-se de que não apenas os tempos de backup sejam suficientes. Além disso, teste a restauração e assuma o prazo das atividades de restauração. Verifique se os tempos de restauração estão dentro de seus SLAs de RTO onde o RTO depende de um banco de dados ou processo de restauração de VM
   4. Teste entre a arquitetura e a funcionalidade de DR de região
5. Verificações de segurança
   1.  Teste a validade da arquitetura RBAC (acesso baseado em função) do Azure que você implementou. O objetivo é separar e limitar o acesso e as permissões de equipes diferentes. Por exemplo, os integrantes da equipe SAP devem ser capazes de implantar VMs e atribuir discos do armazenamento do Azure em uma determinada rede virtual do Azure. No entanto, a equipe SAP não deve ser capaz de criar redes virtuais próprias nem alterar as configurações de redes virtuais existentes. Por outro lado, os membros da equipe de rede não devem ser capazes de implantar VMs em redes virtuais nas quais o aplicativo SAP e as VMs do DBMS estão em execução. Nem os membros da equipe de rede podem alterar atributos de VMs ou até mesmo excluir VMs ou discos.  
   2.  Verifique se as regras [NSG e ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) estão funcionando conforme o esperado e proteja os recursos protegidos
   3.  Verifique se todos os recursos que precisam ser criptografados estão criptografados. Defina e execute processos para fazer backup de certificados, armazenar e acessar esses certificados e restaurar as entidades criptografadas. 
   4.  Use [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) e/ou para discos do sistema operacional onde possível de um ponto de vista de suporte do so
   5.  Verifique se não há muitas camadas de criptografia usadas. Ele faz sentido limitado para usar o Azure Disk Encryption e, em seguida, um dos métodos de criptografia de banco de dados transparente do DBMS
6. Testes de Desempenho
   1.  No SAP com base no rastreamento e nas medições do SAP, compare os 10 principais relatórios online com a implementação atual, quando aplicável 
   2.  No SAP com base no rastreamento e nas medições do SAP, compare os 10 principais trabalhos do lote com a implementação atual, quando aplicável 
   3.  No SAP com base no rastreamento e nas medições do SAP, compare as transferências de dados por meio de interfaces no sistema SAP. Concentre-se nas interfaces em que você sabe que agora a transferência está indo entre locais diferentes, como ir do local para o Azure 


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, presumimos que depois de um piloto ou PoC bem-sucedido, você está começando a implantar sistemas SAP de não produção no Azure. Todos os aprendizados e experiências fora da prova de conceitos devem ser adaptados para essa implantação. Todos os critérios e etapas listados na PoC também se aplicam nesse tipo de implantação. Nesta fase, você geralmente implanta sistemas de desenvolvimento, sistemas de testes de unidade e sistema de testes de regressão de negócios no Azure. É recomendável que pelo menos um sistema de não produção em uma linha de aplicativo SAP tenha a configuração de alta disponibilidade completa que o sistema de produção futuro terá. As etapas adicionais que você precisa considerar durante essa fase são:  

1.  Antes de mover os sistemas da plataforma antiga para o Azure, colete dados de consumo de recursos, como uso da CPU, taxa de transferência de armazenamento e dados de IOPS. Especialmente das unidades de camada DBMS, mas também das unidades de camada de aplicativo. Além disso, meça a latência de rede e de armazenamento.
2.  Registre os padrões de tempo de uso de disponibilidade dos sistemas. Meta é descobrir se os sistemas que não são de produção precisam estar disponíveis 24 horas ou se há sistemas de não produção que podem ser desligados durante determinadas fases de uma semana ou mês
3.  Teste e defina se você deseja criar suas próprias imagens de sistema operacional para suas VMs no Azure ou se deseja usar uma imagem fora da Galeria de imagens do Azure. Se você estiver usando uma imagem fora da galeria do Azure, certifique-se de usar a imagem correta que reflete o contrato de suporte com o fornecedor do sistema operacional. Para alguns fornecedores de sistema operacional, as galerias do Azure oferecem para trazer suas próprias imagens de licença. Para outras imagens do so, o suporte está incluído no preço citado pelo Azure. Se você decidir criar suas próprias imagens de sistema operacional, poderá encontrar a documentação nestes artigos:
    1.  Você pode criar uma imagem generalizada de uma VM do Windows implantada no Azure com base nesta [documentação](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Você pode criar uma imagem generalizada de uma VM Linux implantada no Azure com base nesta [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se você usar imagens SUSE e Red Hat Linux da Galeria de VMs do Azure, será necessário usar as imagens do SAP fornecidas pelos fornecedores do Linux na Galeria de VMs do Azure
4.  Certifique-se de atender aos requisitos de suporte que o SAP tem em relação aos contratos de suporte da Microsoft. As informações podem ser encontradas no [#2015553](https://launchpad.support.sap.com/#/notes/2015553)de observação de suporte SAP. Para instâncias grandes do HANA, consulte os [requisitos de integração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements) do documento
4.  Verifique se as pessoas certas recebem [notificações de manutenção planejada](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), portanto, você pode escolher o tempo de inatividade e uma reinicialização das VMs no prazo
5.  Verificar constantemente a documentação do Azure de apresentações da Microsoft em canais como [channel9](https://channel9.msdn.com/) para novas funcionalidades que podem se aplicar às suas implantações
6.  Verifique as notas SAP relacionadas ao Azure, como nota de suporte [#1928533](https://launchpad.support.sap.com/#/notes/1928533) para novos SKUs de VM ou versão de sistema operacional e DBMS recentemente suportados. Compare novos tipos de VM com os tipos de VM mais antigos em preços, portanto, você pode implantar VMs com a melhor relação preço/desempenho
7.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte
8.  Verifique [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) se há novos SKUs certificados do Hana no Azure e compare os preços com aqueles que você planejou e, eventualmente, altere para obter as unidades com melhor desempenho de preço ração 
9.  Adapte seus scripts de implantação para aproveitar os novos tipos de VM e incorpore os novos recursos do Azure que você deseja usar
10. Após a implantação da infraestrutura, teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados contra as diretrizes de latência de rede da observação de suporte do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Certifique-se de que nenhuma das restrições mencionadas em [considerações para implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e [configurações de infraestrutura SAP Hana e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se aplicam à sua implantação
11. Certifique-se de que as VMs sejam implantadas usando o grupo de posicionamento correto de [proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md)
11. Execute todas as outras verificações, conforme listado na fase de prova de conceitos antes de aplicar a carga de trabalho
12. À medida que a carga de trabalho se aplica, registre o consumo de recursos desses sistemas no Azure e compare com os registros obtidos de sua plataforma antiga. Ajuste o dimensionamento de VM de implantações futuras se você perceber que tem diferenças maiores. Tenha em mente que, no caso de downsizing, armazenamento e largura de banda de rede de uma VM também serão reduzidas:
    1.  [Tamanhos das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Trabalhe em processos e funcionalidade de cópia do sistema. O objetivo é facilitar a cópia de um sistema de desenvolvimento ou de um sistema de teste, portanto, as equipes de projeto podem obter novos sistemas rapidamente. Considere o [SAP lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) como uma ferramenta que executa essas tarefas.
14. Otimize e aprimore os acessos, as permissões e os processos baseados na função do Azure da sua equipe para ter certeza de que você tem uma separação de imposto em um lado. No outro lado, você deseja ter todas as equipes habilitadas para executar suas tarefas na infraestrutura do Azure.
15. Execute, teste e documente os procedimentos de alta disponibilidade e recuperação de desastres para permitir que sua equipe executar tarefas. Identifique deficiências e adapte novas funcionalidades do Azure que você está integrando às suas implantações

 
## <a name="production-preparation-phase"></a>Fase de preparação de produção 
Nesta fase, você deseja coletar todas as experiências e aprender sobre suas implantações de não produção e aplicá-las nas implantações futuras de produção. Além disso, para as fases anteriores, você também precisa preparar o trabalho da transferência de dados entre o local de hospedagem atual e o Azure. 

1.  Trabalhe com as atualizações de versão do SAP necessárias de seus sistemas de produção antes de passar para o Azure
2.  Concorde com os proprietários de negócios nos testes funcionais e de negócios que precisam ser conduzidos após a migração do sistema de produção
    1.  Certifique-se de que todos esses testes sejam executados com os sistemas de origem no local de hospedagem atual. Você deseja evitar que testes sejam realizados pela primeira vez quando o sistema for movido para o Azure
2.  Teste o processo de migração de produção no Azure. No caso de não mover todos os sistemas de produção para o Azure no mesmo período de tempo, crie grupos de sistemas de produção que precisam estar no mesmo local de hospedagem. Migração de dados de teste e exercício. Lista de métodos comuns como:
    1.  Usando métodos do DBMS como backup/restauração em combinação com SQL Server AlwaysOn, replicação de sistema do HANA ou envio de logs para propagar e sincronizar o conteúdo do banco de dados no Azure
    2.  Usar backup/restauração para bancos de dados menores
    3.  Use o monitor de migração do SAP implementado na ferramenta SAP SWPM para executar migrações heterogêneas
    4.  Use o processo do [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se precisar combinar com uma atualização de versão do SAP. Tenha em mente que nem todas as combinações entre o DBMS de origem e de destino têm suporte. Mais informações podem ser encontradas nas notas de suporte específicas do SAP para as diferentes versões do DMO. Por exemplo, [opção de migração de banco de dados (DMO) de SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Teste se a transferência de dados pela Internet ou por meio do ExpressRoute é melhor na taxa de transferência, caso você precise mover backups ou arquivos de exportação do SAP. Para o caso de mover dados pela Internet, talvez seja necessário alterar algumas das regras de segurança do NSG/ASG que você precisa ter em vigor para sistemas de produção futuros
3.  Antes de mover os sistemas da plataforma antiga para o Azure, colete dados de consumo de recursos, como uso da CPU, taxa de transferência de armazenamento e dados de IOPS. Especialmente das unidades de camada DBMS, mas também das unidades de camada de aplicativo. Além disso, meça a latência de rede e de armazenamento.
4.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte 
4.  Adapte os scripts de implantação às alterações mais recentes que você decidiu sobre os tipos de VM e a funcionalidade do Azure
5.  Após a implantação da infraestrutura e do aplicativo, passe por uma série de verificações para validar:
    1.  Os tipos corretos de VM foram implantados com os atributos corretos e os tamanhos de armazenamento
    2.  Verifique se as VMs estão nas versões e nos patches corretos e desejados do sistema operacional e são uniformes
    3.  Verifique se a proteção das VMs é tão necessária e uniforme
    4.  Verificar se as versões e os patches corretos do aplicativo foram instalados e implantados
    5.  As VMs foram implantadas nos conjuntos de disponibilidade do Azure como planejadas
    6.  O armazenamento Premium do Azure foi usado para os discos sensíveis à latência ou onde o [SLA de VM única de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) é necessário
    7.  Verificar a implantação correta do Azure Acelerador de Gravação
        1.  Certifique-se de que na VM, espaços de armazenamento ou conjuntos de distribuição tenham sido criados corretamente em discos que precisam de suporte de Acelerador de Gravação do Azure
            1.  Marque [Configurar RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Marque [Configurar LVM em uma VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  O [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) foi usado exclusivamente
    9.  As VMs foram implantadas nos conjuntos de disponibilidade corretos e Zonas de Disponibilidade
    10. Verifique se a [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs usadas na camada de aplicativo SAP e na camada do DBMS SAP
    11. Nenhum posicionamento de [dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada de DBMS de um SAP NetWeaver, Hybris ou S/4HANA baseados em sistemas SAP
    12. As regras ASG e NSG permitem a comunicação conforme desejado e planejada e bloqueia a comunicação quando necessário
    13. As configurações de tempo limite conforme descrito anteriormente foram definidas corretamente
    14. Certifique-se de que as VMs sejam implantadas usando o grupo de posicionamento correto de [proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md)
    15. Teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados contra as diretrizes de latência de rede da observação de suporte do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Verifique se a criptografia foi implantada quando necessário e com o método de criptografia necessário
    16. Verificar se as interfaces e outros aplicativos podem se conectar à infraestrutura implantada recentemente
6.  Crie um guia estratégico para reagir à manutenção planejada do Azure. Definir a ordem dos sistemas e das VMs a serem reinicializados no caso de manutenção planejada
    

## <a name="go-live-phase"></a>Fase de ativação
Para a fase de ativação, você precisa se certificar de seguir os guias estratégicos desenvolvidos em fases anteriores. Execute as etapas testadas e treinadas. Não aceite alterações de último minuto em configurações e processo. Além disso, aplique as seguintes medidas:

1. Verifique se portal do Azure monitoramento e outras ferramentas de monitoramento estão funcionando.  As ferramentas recomendadas são PerfMon (Windows) ou SAR (Linux): 
    1.  Contadores de CPU 
        1.  Tempo médio de CPU – total (toda a CPU)
        2.  Tempo médio de CPU – cada processador individual (so 128 processadores na VM m128)
        3.  Kernel de tempo de CPU – cada processador individual
        4.  Usuário de tempo de CPU – cada processador individual
    5.  Memória 
        1.  Memória livre
        2.  Página de memória em/s
        3.  Página de memória de saída/s
    4.  Disco 
        1.  KB de leitura de disco/s – por disco individual 
        2.  Leituras de disco/s – por disco individual
        3.  Leitura de disco MS/leitura – por disco individual
        4.  Gravação de disco em KB/s – por disco individual 
        5.  Gravação de disco/s – por disco individual
        6.  Gravação de disco MS/leitura – por disco individual
    5.  Rede 
        1.  Pacotes de rede em/s
        2.  Pacotes de rede enviados/s
        3.  KB de rede em/s
        4.  KB de rede de saída/s 
2.  Após a migração dos dados, execute todos os testes de validação acordados com os proprietários da empresa. Aceitar somente os resultados de teste de validação nos quais você tem resultados para os sistemas de origem originais
3.  Verificar se as interfaces estão funcionando e se outros aplicativos podem se comunicar com os sistemas de produção implantados recentemente
4.  Verificar o sistema de transporte e de correção por meio da transação SAP STMS
5.  Execute backups de banco de dados depois que o sistema for liberado para produção
6.  Execute backups de VM para as VMs da camada de aplicativo SAP depois que o sistema for liberado para produção
7.  Para sistemas SAP que não fazem parte da fase atual de ativação, mas se comunicam com os sistemas SAP movidos para o Azure nesta fase de ativação, você precisa redefinir o buffer de nome de host em SM51. Esta etapa eliminará os endereços IP antigos armazenados em cache associados aos nomes das instâncias do aplicativo que você moveu para o Azure  


## <a name="post-production"></a>Pós-produção
Nesta fase, trata-se de monitorar, operar e administrar o sistema. De um ponto de vista do SAP, as tarefas usuais que você precisa executar com seu local de hospedagem antigo se aplicam. As tarefas específicas do Azure que você deseja fazer são:

1. Analisar faturas do Azure para sistemas de alta carga
2. Otimize a eficiência de preço/desempenho no lado da VM e no lado do armazenamento
3. Otimizar o tempo que os sistemas podem ser desligados  


## <a name="next-steps"></a>Próximos Passos
Consulte a documentação:

- [Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

