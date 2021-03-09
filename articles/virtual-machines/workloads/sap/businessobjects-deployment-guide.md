---
title: SAP BusinessObjects Implantação de plataforma bi em | de Azure Microsoft Docs
description: Planear, implementar e configurar a PLATAFORMA BI DO SAP BusinessObjects em Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505907"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Guia de planeamento e implementação da plataforma de BI SAP BusinessObjects no Azure

## <a name="overview"></a>Descrição Geral

O objetivo deste guia é fornecer diretrizes para o planeamento, implementação e configuração da Plataforma BI SAP BusinessObjects, também conhecida como Plataforma SAP BOBI em Azure. Este guia destina-se a abranger serviços e funcionalidades comuns da Azure que sejam relevantes para a Plataforma SAP BOBI. Este guia não é uma lista exaustiva de todas as opções de configuração possíveis. Abrange soluções comuns a cenários típicos de implantação.

Este guia não se destina a substituir os guias padrão de instalação e administração da Plataforma SAP BOBI, o sistema operativo ou qualquer documentação da base de dados.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planear e implementar a plataforma BI SAP BusinessObjects no Azure

O Microsoft Azure oferece uma vasta gama de serviços, incluindo computação, armazenamento, networking, e muitos outros para as empresas construirem as suas aplicações sem longos ciclos de compras. As máquinas virtuais Azure (VM) ajudam as empresas a implementar recursos de computação a pedido e escaláveis para diferentes aplicações SAP NetWeaver baseadas em SAP NetWeaver, SAP Hybris, SAP BusinessObjects BI Platform, com base nas suas necessidades de negócio. O Azure também apoia a conectividade transversal, que permite às empresas integrarem as máquinas virtuais Azure nos seus domínios no local, nas suas nuvens privadas e na sua paisagem do sistema SAP.

Este documento fornece orientações sobre o planeamento e a consideração de implementação para a PLATAFORMA BI SAP BusinessObjects em Azure. Complementa a documentação de instalação SAP e as Notas SAP, que representam os recursos primários para instalações e implantações do SAP BOBI.

### <a name="architecture-overview"></a>Descrição geral da arquitetura

O SAP BusinessObjects BI Platform é um sistema autossuficiente que pode existir numa única máquina virtual Azure ou pode ser dimensionado para um conjunto de muitas Máquinas Virtuais Azure que executam diferentes componentes. A Plataforma SAP BOBI é composta por seis níveis conceptuais: Nível de Cliente, Web Tier, Nível de Gestão, Nível de Armazenamento, Nível de Processamento e Tier de Dados. (Para mais detalhes sobre cada nível, consulte o Guia de Administradores no portal de ajuda da Plataforma de [Inteligência Empresarial SAP BusinessObjects).](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Seguem-se os detalhes de alto nível em cada nível:

- **Nível de Cliente:** Contém todas as aplicações de clientes de desktop que interagem com a plataforma bi para fornecer diferentes tipos de capacidades de reporte, analítica e administrativa.
- **Nível web:** Contém aplicações web implantadas em servidores de aplicações web JAVA. As aplicações web fornecem funcionalidade de plataforma BI para utilizadores finais através de um navegador web.
- **Nível de Gestão:** Coordena e controla todos os componentes que fazem a Plataforma BI. Inclui o Central Management Server (CMS) e o Servidor de Eventos e serviços associados
- **Nível de armazenamento:** É responsável pelo manuseamento de ficheiros, tais como documentos e relatórios. Também trata de relatórios para economizar recursos do sistema quando os relatórios de acesso ao utilizador.
- **Nível de processamento:** Analisa dados e produz relatórios e outros tipos de saída. É o único nível que acede às bases de dados que contêm dados de relatórios.
- **Nível de dados:** Consiste nos servidores de base de dados que hospedam as bases de dados do sistema CMS e a Loja de Dados de Auditoria.

A Plataforma SAP BI consiste numa coleção de servidores em execução em um ou mais anfitriões. É essencial que escolha a estratégia de implementação correta com base no tamanho, necessidade de negócio e tipo de ambiente. Para uma pequena instalação como desenvolvimento ou teste, pode utilizar uma única Máquina Virtual Azure para servidor de aplicações web, servidor de base de dados e todos os servidores da Plataforma BI. Caso esteja a utilizar a oferta de Database-as-a-Service (DBaaS) do Azure, o servidor de base de dados será executado separadamente de outros componentes. Para uma instalação média e grande, pode ter servidores em funcionamento em várias máquinas virtuais Azure.

Em figura abaixo, é mostrada a arquitetura de implantação em larga escala da Plataforma SAP BOBI em máquinas virtuais Azure, onde cada componente é distribuído e colocado em conjuntos de disponibilidade que podem sustentar falhas se houver perturbação de serviço.

![SAP BusinessObjects BI Platform Architecture on Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Detalhes da arquitetura

- Balanceador de carga

  Na implementação de vários instâncias SAP BOBI, os servidores de aplicações Web (ou web tier) estão a funcionar em dois ou mais anfitriões. Para distribuir a carga do utilizador uniformemente através de servidores web, pode utilizar um balanceador de carga entre utilizadores finais e servidores web. No Azure, pode utilizar [o Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ou [o Azure Application Gateway](../../../application-gateway/overview.md) para gerir o tráfego nos seus servidores web.

- Servidores de aplicações web

  O servidor web acolhe as aplicações web da Plataforma SAP BOBI como CMC e BI Launch Pad. Para obter uma elevada disponibilidade para o servidor web, tem de implementar pelo menos dois servidores de aplicações web para gerir o equilíbrio de redundância e carga. No Azure, estes servidores de aplicações web podem ser colocados em conjuntos de disponibilidade ou zonas de disponibilidade para uma melhor disponibilidade.

  Tomcat é a aplicação web padrão para a Plataforma SAP BI. Para obter uma elevada disponibilidade para o tomcat, ative a replicação da sessão utilizando [o Intercetor de Membro Estático](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) em Azure. Garante que o utilizador pode aceder à aplicação web SAP BI mesmo quando o serviço tomcat é interrompido.

  > [!Important]
  > Por predefinição, o Tomcat utiliza IP e Porta multicast para agrupamentos que não são suportados no Azure (NOTA SAP [2764907).](https://launchpad.support.sap.com/#/notes/2764907)

- Servidores de plataformas BI

  Os servidores da Plataforma BI incluem todos os serviços que fazem parte da aplicação SAP BOBI (nível de gestão, nível de processamento e nível de armazenamento). Quando um servidor web recebe um pedido, deteta cada servidor de plataforma BI (especificamente, todos os servidores CMS num cluster) e carrega automaticamente os seus pedidos. Caso um dos anfitriões da Plataforma BI falhe, o servidor web envia automaticamente pedidos para outro anfitrião.

  Para obter uma elevada disponibilidade ou redundância para a Plataforma BI, deve implementar a aplicação em pelo menos duas máquinas virtuais Azure. Com base no dimensionamento, pode escalar a sua Plataforma BI para funcionar em mais máquinas virtuais Azure.

- Servidor de repositório de ficheiros (FRS)

  O Servidor de Repositório de Ficheiros contém todos os relatórios e outros documentos bi que foram criados. Na implementação em várias instâncias, os servidores da Plataforma BI estão a funcionar em várias máquinas virtuais e cada VM deve ter acesso a estes relatórios e outros documentos bi. Assim, um sistema de ficheiros precisa de ser partilhado em todos os servidores da plataforma BI.

  No Azure, pode utilizar [ficheiros Azure Premium](../../../storage/files/storage-files-introduction.md) ou [ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md) para o Servidor de Repositório de Ficheiros. Ambos os serviços da Azure têm despedimentos embutidos.

  > [!Important]
  > O Protocolo SMB para Ficheiros Azure está geralmente disponível, mas o suporte do Protocolo NFS para Ficheiros Azure está atualmente em pré-visualização. Para mais informações, consulte [o suporte NFS 4.1 para Ficheiros Azure está agora em pré-visualização](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- Base de dados de auditoria & CMS
  
  A Plataforma SAP BOBI requer uma base de dados para armazenar os seus dados do sistema, que é referido como base de dados CMS. É utilizado para armazenar informações da plataforma BI, tais como utilizador, servidor, pasta, documento, configuração e dados de autenticação.

  A Azure oferece a [base de dados MySQL](https://azure.microsoft.com/en-us/services/mysql/) e a base de [dados Azure SQL](https://azure.microsoft.com/en-us/services/sql-database/) Database-as-a-Service (DBaaS) que pode ser usada para base de dados CMS e base de dados de auditoria. Sendo esta uma oferta paaS, os clientes não têm que se preocupar com operação, disponibilidade e manutenção das bases de dados. O cliente também pode escolher a sua própria base de dados para o repositório de CMS e Auditoria com base nas suas necessidades comerciais.

## <a name="support-matrix"></a>Matriz de suporte

Esta secção descreve a capacidade de suporte de diferentes componentes SAP BOBI como a versão DA PLATAFORMA BI SAP BusinessObjects, Sistema Operativo e, Bases de Dados em Azure.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects plataforma BI

A Azure Infrastructure as a Service (IaaS) permite-lhe implantar e configurar a plataforma BI SAP BusinessObjects no Azure Compute. Suporta a seguinte versão da Plataforma SAP BOBI -

- SAP BusinessObjects BI Plataforma 4.3
- SAP BusinessObjects BI Plataforma 4.2 SP04+
- SAP BusinessObjects BI Plataforma 4.1 SP05+

A Plataforma SAP BI funciona em diferentes sistemas operativos e bases de dados. A capacidade de suporte da plataforma SAP BOBI entre o Sistema Operativo e a versão Base de Dados pode ser encontrada na [Matriz de Disponibilidade de Produto](https://apps.support.sap.com/sap/support/pam) para SAP BOBI.

### <a name="operating-system"></a>Sistema operativo

O Azure suporta os sistemas operativos seguintes para a implementação da plataforma BI SAP BusinessObjects.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

A versão do sistema operativo que está listada na [Matriz de Disponibilidade de Produto (PAM) para a Plataforma BI SAP BusinessObjects](https://support.sap.com/pam) são suportadas desde que sejam compatíveis a funcionar na Infraestrutura Azure.

### <a name="databases"></a>Bases de Dados

A Plataforma BI necessita de uma base de dados para a loja de dados CMS e auditamento, que pode ser instalada em quaisquer bases de dados suportadas que estejam listadas na [Matriz de Disponibilidade de Produto SAP](https://support.sap.com/pam) que inclua o seguinte -

- Microsoft SQL Server

- [Base de Dados Azure SQL (base](https://azure.microsoft.com/en-us/services/sql-database/) de dados suportada apenas para plataforma SAP BOBI no Windows)

  É um motor de base de dados sql server totalmente gerido, baseado na mais recente edição estável da Enterprise Edition do SQL Server. A base de dados Azure SQL lida com a maioria das funções de gestão da base de dados, tais como upgrade, remendamento e monitorização sem o envolvimento do utilizador. Com a Base de Dados SQL do Azure, pode criar uma camada de armazenamento altamente disponível e de elevado desempenho para as aplicações e soluções no Azure. Para mais detalhes, consulte a documentação [da Base de Dados Azure SQL.](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (Siga as mesmas diretrizes de compatibilidade mencionadas para mySQL AB em SAP PAM)

  É um serviço de base de dados relacional alimentado pela edição comunitária mySQL. Sendo uma oferta totalmente gerida de Base de Dados como um Serviço (DBaaS), pode lidar com cargas de trabalho críticas de missão com desempenho previsível e escalabilidade dinâmica. Tem alta disponibilidade incorporada, backups automáticos, patching de software, deteção automática de falhas e restauro pontual por até 35 dias, o que reduz substancialmente as tarefas de funcionamento. Para mais detalhes, consulte [a Base de Dados Azure para obter documentação mySQL.](../../../mysql/overview.md)

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (Para ver versão e restrição, verifique SAP Note [2039619)](https://launchpad.support.sap.com/#/notes/2039619)

- MaxDB

Este documento ilustra as diretrizes para a implantação da **Plataforma SAP BOBI no Windows com base de dados Azure SQL** e **plataforma SAP BOBI no Linux com Base de Dados Azure para o MySQL**. É também a nossa abordagem recomendada para executar a PLATAFORMA BI SAP BusinessObjects em Azure.

## <a name="sizing"></a>Dimensionamento

O dimensionamento é um processo de determinação do requisito de hardware para executar a aplicação de forma eficiente. Para a Plataforma SAP BOBI, o dimensionamento precisa de ser feito utilizando uma ferramenta de dimensionamento SAP chamada [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). A ferramenta fornece o SAPS com base na entrada, que depois precisa de ser mapeada para tipos de máquinas virtuais Azure certificadas para SAP. A SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) fornece a lista de produtos SAP suportados e tipos de VM Azure juntamente com SAPS. Para obter mais informações sobre o tamanho, consulte [o Guia de Dimensionamento SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Para necessidade de armazenamento para a Plataforma SAP BOBI, o Azure oferece diferentes tipos de [Discos Geridos.](../../managed-disks-overview.md) Para o diretório de instalação SAP BOBI, é aconselhável utilizar o disco gerido premium e para a base de dados que funciona em máquinas virtuais, siga as orientações fornecidas na [implementação de DBMS para a carga de trabalho SAP](dbms_guide_general.md).

O Azure suporta duas ofertas de DBaaS para o nível de dados da Plataforma SAP BOBI - [Base de Dados Azure SQL](https://azure.microsoft.com/en-us/services/sql-database) (aplicação BI em execução no Windows) e [Base de Dados Azure para o MySQL](https://azure.microsoft.com/en-us/services/mysql) (aplicação BI em execução no Linux e Windows). Assim, com base no resultado do tamanho, pode escolher um modelo de compra que melhor se adapte à sua necessidade.

> [!Tip]
> Para uma referência de dimensionamento rápido, considere 800 SAPS = 1 vCPU enquanto mapeia o resultado DO SAPS do nível de base de dados da Plataforma SAP BOBI para Azure Database-as-a-Service (Base de Dados Azure SQL ou Base de Dados Azure para o MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Modelos de dimensionamento para base de dados Azure SQL

A Azure SQL Database oferece os seguintes três modelos de compra:

- vCore

  Permite-lhe escolher o número de vCores, quantidade de memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use [Azure Hybrid Benefit para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter economia de custos. Este modelo é adequado para o cliente que valoriza a flexibilidade, o controlo e a transparência.

  Existem três [opções de nível de serviço](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) que estão sendo oferecidas no modelo vCore que incluem - Final Geral, Critical de Negócios e Hiperescala. O nível de serviço define a arquitetura de armazenamento, o espaço, os limites de E/S e as opções de continuidade do negócio relacionadas com a disponibilidade e recuperação de desastres. Seguem-se detalhes de alto nível em cada opção de nível de serviço -

  1. O nível de serviço **de Finalidade Geral** é o mais adequado para cargas de trabalho de negócios. Oferece opções de cálculo e armazenamento orientadas para o orçamento, equilibradas e escaláveis. Para obter mais informações, consulte [as opções e limites de Recursos.](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)
  2. **Business Critical** o serviço tier oferece aplicações de negócios a maior resiliência às falhas usando várias réplicas isoladas, e fornece o maior desempenho de I/O por réplica de base de dados. Para obter mais informações, consulte [as opções e limites de Recursos.](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)
  3. O nível de serviço **de hiperescala** é o melhor para cargas de trabalho de negócios com requisitos de armazenamento altamente escaláveis e escala de leitura. Oferece maior resiliência às falhas, permitindo a configuração de mais de uma réplica isolada da base de dados. Para obter mais informações, consulte [as opções e limites de Recursos.](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)

- Baseado em DTU

  O modelo de compra baseado em DTU oferece uma mistura de recursos de computação, memória e E/S em três níveis de serviço, para suportar cargas de dados leves e pesadas. Os tamanhos de cálculo dentro de cada nível fornecem uma mistura diferente destes recursos, aos quais pode adicionar recursos de armazenamento adicionais. É o mais adequado para clientes que querem opções de recursos simples e pré-configuração.

  [Os Níveis](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) de Serviço no modelo de compra baseado em DTU são diferenciados por uma gama de tamanhos de cálculo com uma quantidade fixa de armazenamento incluído, período de retenção fixo de backups e preço fixo.

- Sem servidor

  O modelo sem servidor escala automaticamente o cálculo com base na procura de carga de trabalho e as contas da quantidade de cálculo utilizada por segundo. O nível de computação sem servidor interrompe automaticamente as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade retorna. Para obter mais informações, consulte [as opções e limites de Recursos.](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)
  
  É mais adequado para uma utilização intermitente e imprevisível com baixa utilização média do cálculo ao longo do tempo. Assim, este modelo pode ser utilizado para a implantação sap bobi não-produção.

> [!Note]
> Para o SAP BOBI, é conveniente usar o modelo baseado em vCore e escolher o nível de serviço General Purpose ou Business Critical com base na necessidade do negócio.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Modelos de dimensionamento para base de dados Azure para MySQL

A Azure Database for MySQL vem com três níveis de preços diferentes. São diferenciados pela quantidade de cálculo em vCores, memória por vCore e a tecnologia de armazenamento usada para armazenar a data. Seguem-se os detalhes de alto nível sobre as opções e para mais detalhes sobre diferentes atributos, consulte [o Priceing Tier](../../../mysql/concepts-pricing-tiers.md) para Azure Database for MySQL.

- Básico

  É usado para as cargas de trabalho-alvo que requerem computação leve e desempenho de I/S.

- Fins Gerais

  É adequado para a maioria das cargas de trabalho que requerem computação equilibrada e memória com rendimento escalável de I/O.

- Otimizada para Memória

  Para cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento de transação mais rápido e maior concordância.

> [!Note]
> Para o SAP BOBI, é conveniente utilizar o nível de preços otimizado para fins gerais ou memória com base na carga de trabalho do negócio.

## <a name="azure-resources"></a>Recursos do Azure

### <a name="choosing-regions"></a>Escolha de regiões

A região de Azure é um ou uma coleção de data-centers que contém a infraestrutura para executar e hospeda diferentes Serviços Azure. Esta infraestrutura inclui um grande número de nós que funcionam como nós de computação ou nós de armazenamento, ou executar a funcionalidade de rede. Nem toda a região oferece os mesmos serviços.

A Plataforma SAP BI contém diferentes componentes que podem necessitar de tipos de VM específicos, armazenamento como ficheiros Azure ou Ficheiros NetApp Azure ou Base de Dados como um Serviço (DBaaS) para o seu nível de dados que pode não estar disponível em certas regiões. Pode descobrir as informações exatas sobre os tipos de VM, tipos de Armazenamento Azure ou outros Serviços Azure em Produtos disponíveis por site [da região.](https://azure.microsoft.com/en-us/global-infrastructure/services/) Se já está a executar os seus sistemas SAP no Azure, provavelmente tem a sua região identificada. Nesse caso, é necessário primeiro investigar que os serviços necessários estão disponíveis nessas regiões para decidir a arquitetura da Plataforma SAP BI.

### <a name="availability-zones"></a>Zonas de disponibilidade

As Zonas de Disponibilidade são locais fisicamente separados dentro de uma região de Azure. Cada Zona de Disponibilidade é feita de um ou mais datacenters equipados com potência, arrefecimento e networking independentes.

Para obter uma elevada disponibilidade em cada nível para a Plataforma SAP BI, pode distribuir VMs pela Zona de Disponibilidade implementando um quadro de alta disponibilidade, que pode fornecer o melhor SLA em Azure. Para O SLA de Máquina Virtual em Azure, consulte a versão mais recente do [Virtual Machine SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Para o nível de dados, o serviço Azure Database as a Service (DBaaS) fornece um quadro de alta disponibilidade por padrão. Basta selecionar a região e o serviço inerente a alta disponibilidade, redundância e capacidades de resiliência para mitigar o tempo de inatividade da base de dados a partir de paragens planeadas e não planeadas, sem que você configue quaisquer componentes adicionais. Para obter mais detalhes sobre o SLA para a oferta de DBaaS suportada no Azure, consulte [alta disponibilidade na Base de Dados Azure para o MySQL](../../../mysql/concepts-high-availability.md) e alta disponibilidade para a Base de [Dados Azure SQL](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Conjuntos de disponibilidade

O conjunto de disponibilidade é uma capacidade lógica de agrupamento para isolar os recursos da Máquina Virtual (VM) uns dos outros ao serem implantados. O Azure garante que os VMs que coloca dentro de um Conjunto de Disponibilidades passam por vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se uma falha de hardware ou software acontecer, apenas um subconjunto dos seus VMs é afetado e a sua solução global permanece operacional. Assim, quando as máquinas virtuais são colocadas em conjuntos de disponibilidade, o Controlador de Tecido Azure distribui os VMs em diferentes domínios [de Falha](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) e [Atualização](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) para evitar que todos os VMs sejam inacessíveis devido à manutenção ou falha da infraestrutura dentro de um domínio de falha.

A Plataforma SAP BI contém muitos componentes diferentes e ao desenhar a arquitetura tem de se certificar de que cada um deste componente é resistente a qualquer perturbação. Pode ser conseguido colocando máquinas virtuais Azure de cada componente dentro dos conjuntos de disponibilidade. Tenha em mente que, ao misturar VMs de diferentes famílias VM dentro de um conjunto de disponibilidade, poderá encontrar problemas que o impeçam de incluir um determinado tipo VM nesse conjunto de disponibilidade. Assim, tenha um conjunto de disponibilidade separado para aplicação web, aplicação de BI para plataforma SAP BI como destacado na Visão Geral da Arquitetura.

Também o número de domínios de atualização e avaria que podem ser utilizados por um Conjunto de Disponibilidade de Azure dentro de uma unidade de Escala Azure é finito. Assim, se continuar a adicionar VMs a um único conjunto de disponibilidade, dois ou mais VMs acabarão eventualmente no mesmo domínio de falha ou atualização. Para obter mais informações, consulte a secção [Azure Availability Sets](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) do planeamento e implementação de máquinas virtuais Azure para o documento SAP.

Para compreender o conceito de conjuntos de disponibilidade de Azure e a forma como os conjuntos de disponibilidade se relacionam com Os Domínios de Falha e Atualização, leia gerir o artigo [de disponibilidade.](../../availability.md)

> [!Important]
> Os conceitos de Zonas de Disponibilidade Azure e conjuntos de disponibilidade azure são mutuamente exclusivos. Isto significa que pode colocar um par ou vários VMs numa zona de disponibilidade específica ou um conjunto de disponibilidades Azure. Mas não os dois.

### <a name="virtual-machines"></a>Máquinas virtuais

A Azure Virtual Machine é uma oferta de serviço que lhe permite implementar imagens personalizadas para Azure como instâncias infra-as-a-service (IaaS). Simplifica a manutenção e a operação de aplicações, fornecendo computação e armazenamento a pedido para hospedar, escalar e gerir aplicações web e aplicações conectadas.

A Azure oferece variedades de máquinas virtuais para todas as necessidades da sua aplicação. Mas para a carga de trabalho sap, a Azure reduziu a seleção para diferentes famílias VM que são adequadas para a carga de trabalho SAP e a carga de trabalho SAP HANA mais especificamente. Para obter mais informações, verifique [o que o software SAP é suportado para implementações Azure](sap-supported-product-on-azure.md).

Com base no dimensionamento da Plataforma SAP BI, é necessário mapear a sua exigência para a Azure Virtual Machine, que é suportada em Azure para o produto SAP. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) é um bom ponto de partida que lista os tipos de VM suportados para produtos SAP em Windows e Linux. Também um ponto a ter em mente que, para além da seleção de tipos de VM puramente suportados, também é necessário verificar se esses tipos de VM estão disponíveis em regiões específicas. Pode verificar a disponibilidade do tipo VM em [Produtos disponíveis por página da região.](https://azure.microsoft.com/global-infrastructure/services/) Para escolher o modelo de preços, pode consultar [máquinas virtuais Azure para a carga de trabalho SAP](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Armazenamento

O Azure Storage é um serviço de nuvem gerido pelo Azure que fornece armazenamento altamente disponível, seguro, durável, escalável e redundante. Alguns dos tipos de armazenamento têm uso limitado para cenários SAP. Mas vários tipos de Armazenamento Azure são bem adaptados ou otimizados para cenários específicos de carga de trabalho SAP. Para obter mais informações, consulte [os tipos de armazenamento Azure para](planning-guide-storage.md) guia de carga de trabalho SAP, uma vez que destaca diferentes opções de armazenamento adequadas para SAP.

O Azure Storage tem diferentes tipos de Armazenamento disponíveis para clientes e detalhes para o mesmo podem ser lidos no artigo [Que tipos de disco estão disponíveis no Azure?](../../disks-types.md) A Plataforma SAP BOBI utiliza o Azure Storage para construir a aplicação -

- Discos geridos pelo Azure

  É um volume de armazenamento de nível de bloco que é gerido pelo Azure. Pode utilizar os discos para servidores e bases de dados da Plataforma SAP BOBI, quando instalados em máquinas virtuais Azure. Existem diferentes tipos de [Discos Geridos Azure](../../managed-disks-overview.md) disponíveis, mas é recomendado usar [SSDs Premium](../../disks-types.md#premium-ssd) para aplicação e base de dados da Plataforma SAP BOBI.

  Por exemplo, os SSDs Premium são utilizados para o diretório de instalação da Plataforma BOBI. Para a base de dados instalada na máquina virtual, pode utilizar discos geridos para dados e volume de registo de acordo com as diretrizes. As bases de dados cms e de auditoria são tipicamente pequenas e não têm os mesmos requisitos de desempenho de armazenamento que as de outras bases de dados SAP OLTP/OLAP.

- Ficheiros Azure Premium ou Ficheiros Azure NetApp

  Na Plataforma SAP BOBI, o Servidor de Repositório de Ficheiros (FRS) refere-se aos diretórios de discos onde são armazenados conteúdos como relatórios, universos e ligações que são utilizados por todos os servidores de aplicações desse sistema. [Os ficheiros Azure Premium](../../../storage/files/storage-files-introduction.md) ou o armazenamento [de Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md) podem ser utilizados como um sistema de ficheiros partilhado para aplicações SAP BOBI FRS. Como esta oferta de armazenamento não está disponível em todas as regiões, consulte [os Produtos disponíveis por região](https://azure.microsoft.com/en-us/global-infrastructure/services/) para obter informações atualizadas.

  Se o serviço não estiver disponível na sua região, pode criar o servidor NFS a partir do qual pode partilhar o sistema de ficheiros com a aplicação SAP BOBI. Mas também terá de considerar a sua elevada disponibilidade.

![SAP BusinessObjects BI Plataforma De Armazenamento layout em Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Rede

O SAP BOBI é uma plataforma bi de reporte e análise que não detém quaisquer dados de negócio. Assim, o sistema está ligado a outros servidores de base de dados de onde recolhe todos os dados e fornece informações aos utilizadores. O Azure disponibiliza uma infraestrutura de rede, que permite o mapeamento de todos os cenários que podem ser realizados com a Plataforma SAP BI, como a ligação ao sistema no local, sistemas em diferentes redes virtuais e outros. Para obter mais informações, consulte [a Rede Microsoft Azure para a carga de trabalho SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Para a oferta de Base de Dados como serviço, qualquer base de dados recém-criada (Base de Dados Azure SQL ou Base de Dados Azure para o MySQL) tem uma firewall que bloqueia todas as ligações externas. Para permitir o acesso ao serviço DBaaS a partir de máquinas virtuais bi plataforma, é necessário especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao servidor DBaaS. Para obter mais informações, consulte [as regras de Firewall](../../../mysql/concepts-firewall-rules.md) para a base de dados Azure para a secção MySQL e [Controlos de Acesso à Rede](../../../azure-sql/database/network-access-controls-overview.md) para base de dados Azure SQL.

## <a name="next-steps"></a>Passos seguintes

- [SAP BusinessObjects implantação de plataforma bi no Linux](businessobjects-deployment-guide-linux.md)
- [Azure Virtual Machines planejamento e implementação para SAP](planning-guide.md)
- [Implantação de máquinas virtuais Azure para SAP](deployment-guide.md)
- [Implantação DBMS de máquinas virtuais Azure para SAP](./dbms_guide_general.md)