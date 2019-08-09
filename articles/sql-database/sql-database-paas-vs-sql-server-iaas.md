---
title: Escolhendo entre as opções SQL no Azure | Microsoft Docs
description: Saiba como escolher entre as opções de implantação no banco de dados SQL do Azure e entre o banco de dados SQL do Azure e o SQL Server em máquinas virtuais do Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Nuvem SQL Server, SQL Server na nuvem, base de dados PaaS, SQL Server na nuvem, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/11/2019
ms.openlocfilehash: fc0204e774e66e9304887f47377a102df1b828cb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884335"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Escolha a opção de implantação correta no SQL do Azure

No Azure, você pode ter suas cargas de trabalho de SQL Server em execução em uma IaaS (infraestrutura hospedada) ou em execução como um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). No PaaS, você tem várias opções de implantação e camadas de serviço dentro de cada opção de implantação. A principal pergunta que você precisa fazer ao decidir entre PaaS ou IaaS é deseja gerenciar seu banco de dados, aplicar patches, fazer backups ou deseja delegar essas operações ao Azure?
Dependendo da resposta, você tem as seguintes opções:

- [Banco de dados SQL do Azure](sql-database-technical-overview.md): Um mecanismo de banco de dados SQL totalmente gerenciado, com base na mais recente edição de empresa estável do SQL Server. Esse é um DBaaS (banco de dados como serviço) relacional hospedado na nuvem do Azure que se enquadra na categoria do setor de *PaaS (plataforma como serviço)* . O banco de dados SQL tem várias opções de implantação, cada uma criada em hardware e software padronizados que são de propriedade, hospedados e mantidos pela Microsoft. Com o banco de dados SQL, você pode usar recursos internos e funcionalidades que exigem ampla configuração quando usados em SQL Server (local ou em uma máquina virtual do Azure). Ao utilizar a Base de Dados SQL, paga apenas o que utilizar, dispondo de opções para aumentar verticalmente ou horizontalmente para obter um maior desempenho sem interrupções. O banco de dados SQL tem recursos adicionais que não estão disponíveis no SQL Server, como alta disponibilidade, inteligência e gerenciamento internos. O banco de dados SQL do Azure oferece as seguintes opções de implantação:
  
  - Como um [banco de dados individual](sql-database-single-database.md) com seu próprio conjunto de recursos gerenciados por meio de um servidor de banco de dados SQL. Um banco de dados individual é semelhante a bancos de dados [independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server. Essa opção é otimizada para o desenvolvimento de aplicativos modernos de novos aplicativos gerados na nuvem.
  - Um [pool elástico](sql-database-elastic-pool.md), que é uma coleção de bancos de dados com um conjunto compartilhado de recursos gerenciados por meio de um servidor de banco de dados SQL. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico. Essa opção é otimizada para o desenvolvimento de aplicativos modernos de novos aplicativos gerados pela nuvem usando o aplicativo SaaS multilocatário.
  - [Instância gerenciada](sql-database-managed-instance.md), que é uma coleção de bancos de dados de sistema e de usuário com um conjunto compartilhado de recursos. Uma instância gerenciada é semelhante a uma instância do [Microsoft SQL Server mecanismo de banco de dados] que oferece recursos compartilhados para bancos e recursos de escopo de instância adicionais. A instância gerenciada dá suporte à migração de banco de dados local com mínimo de sem alteração de banco de dados. Essa opção fornece todos os benefícios de PaaS do banco de dados SQL do Azure, mas adiciona recursos que anteriormente só estavam disponíveis em VMs do SQL. Isso inclui uma rede virtual nativa (VNet) e quase 100% de compatibilidade com a SQL Server local.
- [SQL Server nas máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) se enquadra na *Infraestrutura como serviço (IaaS)* da categoria do setor e permite que você execute SQL Server dentro de uma máquina virtual totalmente gerenciada na nuvem do Azure. [SQL Server máquinas virtuais](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) também são executadas em hardware padronizado que é propriedade, hospedada e mantida pela Microsoft. Ao usar SQL Server em uma VM, você pode pagar conforme o uso para uma licença de SQL Server já incluída em uma imagem de SQL Server ou usar facilmente uma licença existente. Você também pode parar ou retomar a VM conforme necessário. SQL Server instalado e hospedado na nuvem no Windows Server ou em VMs (máquinas virtuais) Linux em execução no Azure, também conhecido como IaaS (infraestrutura como serviço). O SQL Server em máquinas virtuais do Azure é uma boa opção para migrar bancos de dados e aplicativos locais SQL Server sem qualquer alteração no banco. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação em uma máquina virtual IaaS. A diferença mais significativa do banco de dados SQL é que SQL Server VMs permitem controle total sobre o mecanismo de banco de dados. Você pode escolher quando iniciar a manutenção/aplicação de patches, alterar o modelo de recuperação para simples ou bulk-logged, pausar ou iniciar o serviço quando necessário, e você pode personalizar totalmente o mecanismo de banco de dados SQL Server. Com esse controle adicional, a responsabilidade adicional é o gerenciamento da máquina virtual.

As principais diferenças entre essas opções são listadas na tabela a seguir:

| SQL Server em VM | Instância gerenciada no banco de dados SQL | Banco de dados individual/pool elástico no banco de dados SQL |
| --- | --- | --- |
|Você tem controle total sobre o mecanismo de SQL Server.<br/>Até 99,99% de disponibilidade.<br/>Paridade total com a versão correspondente do SQL Server local.<br/>Versão do mecanismo de banco de dados fixa e conhecida.<br/>Migração fácil de SQL Server local.<br/>Endereço IP privado na VNet do Azure.<br/>Você tem a capacidade de implantar aplicativos ou serviços no host onde SQL Server é colocado.| Alta compatibilidade com o SQL Server local.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patches, recuperação.<br/>Versão mais recente do Mecanismo de Banco de Dados estável.<br/>Migração fácil de SQL Server.<br/>Endereço IP privado na VNet do Azure.<br/>Segurança e inteligência avançada integradas.<br/>Alteração online de recursos (CPU/armazenamento).|Os recursos de SQL Server usados com mais frequência estão disponíveis.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patches, recuperação.<br/>Versão mais recente do Mecanismo de Banco de Dados estável.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) a bancos de dados individuais.<br/>Segurança e inteligência avançada integradas.<br/>Alteração online de recursos (CPU/armazenamento).|
|Você precisa gerenciar seus backups e patches.<br>Você precisa implementar sua própria solução de alta disponibilidade.<br/>Há um tempo de inatividade ao alterar os recursos (CPU/armazenamento)|Ainda há algum número mínimo de recursos de SQL Server que não estão disponíveis.<br/>Nenhum tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser obtida usando níveis de compatibilidade de banco de dados.|A migração do SQL Server pode ser difícil.<br/>Alguns recursos SQL Server não estão disponíveis.<br/>Nenhum tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser obtida usando níveis de compatibilidade de banco de dados.<br/>O endereço IP privado não pode ser atribuído (você pode limitar o acesso usando regras de firewall).|

Saiba como cada opção de implantação se encaixa na plataforma de dados da Microsoft e Obtenha ajuda para corresponder à opção certa para seus requisitos de negócios. Quer dê prioridade à redução de custos ou a uma administração mínima acima de tudo o resto, este artigo pode ajudá-lo a decidir qual é a abordagem que melhor responde aos requisitos comerciais a que dá mais importância.

## <a name="microsofts-sql-data-platform"></a>Plataforma de dados SQL da Microsoft

Um dos primeiros aspetos a compreender em qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar todas. A plataforma de dados da Microsoft tira partido da tecnologia do SQL Server e disponibiliza-a em máquinas físicas no local, ambientes de nuvem privada, ambientes de nuvem privada alojada de terceiros e nuvem pública. O SQL Server nas máquinas virtuais do Azure permite-lhe responder a necessidades comerciais únicas e diversas através de uma combinação de implementações alojadas na cloud e no local, utilizando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e conhecimentos em todos estes ambientes.

   ![Opções de SQL Server de nuvem: SQL Server em IaaS ou banco de dados SQL SaaS na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Tal como ilustrado no diagrama, cada oferta pode ser caracterizada pelo nível de administração que têm sobre a infraestrutura (no eixo X) e pelo grau de eficiência de custos alcançada através da automatização e consolidação de nível de base de dados (no eixo Y).

Ao conceber uma aplicação, estão disponíveis quatro opções básicas para alojar a parte do SQL Server da aplicação:

- SQL Server em máquinas físicas não virtualizadas
- SQL Server em máquinas virtualizadas no local (nuvem privada)
- SQL Server numa Máquina Virtual do Azure (nuvem pública da Microsoft)
- Base de Dados SQL do Azure (nuvem pública da Microsoft)

Nas seções a seguir, você aprenderá sobre SQL Server na nuvem pública da Microsoft: Banco de dados SQL do Azure e SQL Server em VMs do Azure. Além disso, explorará motivadores de negócio comuns para determinar qual é a opção que funciona melhor para a sua aplicação.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Um olhar mais atento sobre a Base de Dados SQL do Azure e o SQL Server em VMs do Azure

Em geral, estas duas opções de SQL estão otimizadas para diferentes fins:

- **Base de Dados SQL do Azure**

Otimizado para reduzir os custos gerais de gerenciamento no mínimo para provisionamento e gerenciamento de vários bancos de dados. Reduz os custos correntes de administração, uma vez que não é necessário gerir as máquinas virtuais, o sistema operativo ou o software de base de dados. Não é necessário gerir as atualizações, a elevada disponibilidade ou as [cópias de segurança](sql-database-automated-backups.md). Em geral, a Base de Dados SQL do Azure permite aumentar substancialmente o número de bases de dados geridas por um único recurso de TI ou de desenvolvimento. [](sql-database-elastic-pool.md) Os pools elásticos também dão suporte a arquiteturas de aplicativos de multilocatários SaaS com recursos, incluindo o isolamento de locatários e a capacidade de dimensionar para reduzir custos compartilhando recursos entre bancos de dados. A [instância gerenciada](sql-database-managed-instance.md) fornece suporte para recursos com escopo de instância, permitindo a fácil migração de aplicativos existentes, bem como o compartilhamento de recursos entre bancos de dados.

- **SQL Server em execução em VMs do Azure**

Otimizado para migrar aplicativos existentes para o Azure ou estender aplicativos locais existentes para a nuvem em implantações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais . Com o SQL Server em VMs do Azure, tem direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada na nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Estas capacidades permite-lhe criar um sistema altamente personalizado para fazer face aos requisitos de disponibilidade e desempenho específicos da sua aplicação.

A tabela seguinte resume as principais caraterísticas da Base de Dados SQL e do SQL Server em VMs do Azure:

| | Bancos de dados individuais e pools elásticos do banco de dados SQL | Instâncias gerenciadas do banco de dados SQL |Máquinas virtuais do Azure com SQL Server |
| --- | --- | --- |---|
| **Melhor para:** |Novos aplicativos projetados para a nuvem que desejam usar os recursos de SQL Server estáveis mais recentes e têm restrições de tempo no desenvolvimento e no marketing. | Novos aplicativos ou aplicativos locais existentes que desejam usar os mais recentes recursos de SQL Server estáveis e que são migrados para a nuvem com alterações mínimas.  | Aplicativos existentes que exigem migração rápida para a nuvem com alterações mínimas ou nenhuma alteração. Cenários de desenvolvimento e teste rápidos quando não pretende comprar hardware de SQL Server de não produção no local. |
|  | Equipas que necessitam de atualizações, recuperação após desastre e elevada disponibilidade para a base de dados. | Mesmo que bancos de dados SQL individuais e em pool. | Equipes que podem configurar, ajustar, personalizar e gerenciar a alta disponibilidade, a recuperação de desastre e a aplicação de patches para SQL Server. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. |
|  | Equipas que não pretendem gerir o sistema operativo subjacente e definições de configuração. | Mesmo que bancos de dados SQL individuais e em pool. | Você precisa de um ambiente personalizado com direitos administrativos completos. |
|  | Bancos de dados de até 100 TB. | Até 8 TB. | SQL Server instâncias com até 256 TB de armazenamento. A instância pode suportar tantas bases de dados quanto necessário. |
| **Compatibilidade** | Oferece suporte à maioria dos recursos de nível de banco de dados local. | Dá suporte a quase todos os recursos no nível de instância e de banco de dados locais. | Dá suporte a todos os recursos locais. |
| **Recursos:** | Você não deseja empregar recursos de ti para configuração e gerenciamento da infraestrutura subjacente, mas deseja se concentrar na camada de aplicativo. | Mesmo que bancos de dados SQL individuais e em pool. | Tem alguns recursos de TI para a configuração e gestão. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. |
| **Custo total de propriedade:** | Elimina os custos de hardware e reduz os custos administrativos. | Mesmo que bancos de dados SQL individuais e em pool. | Elimina os custos com hardware. |
| **Continuidade do negócio:** |Além dos [recursos internos de infraestrutura de tolerância a falhas](sql-database-high-availability.md), o banco de dados SQL do Azure fornece recursos, como [backups automatizados](sql-database-automated-backups.md), [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore), [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore), [replicação geográfica ativa](sql-database-active-geo-replication.md)e [ Grupos de failover automático](sql-database-auto-failover-group.md) para aumentar a continuidade dos negócios. Para obter mais informações, consulte [Descrição geral da continuidade de negócio da Base de Dados SQL](sql-database-business-continuity.md). | O mesmo que os bancos de dados SQL único e em pool, além de backups somente cópia iniciados pelo usuário, estão disponíveis. | O SQL Server em VMs do Azure permite configurar uma solução de elevada disponibilidade e recuperação após desastre para as necessidades específicas da sua base de dados. Assim, poderá ter um sistema que está altamente otimizado para a sua aplicação. Pode testar e executar ativações pós-falha sempre que necessário. Para obter mais informações, consulte [Elevada Disponibilidade e Recuperação Após Desastre do SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Nuvem híbrida:** |A sua aplicação no local pode aceder a dados na Base de Dados SQL do Azure. | [Implementação de rede virtual nativa](sql-database-managed-instance-vnet-configuration.md) e conectividade com seu ambiente local usando o gateway de VPN ou a rota expressa do Azure. | Com o SQL Server em VMs do Azure, pode ter aplicações que são executadas parcialmente na nuvem e parcialmente no local. Por exemplo, pode expandir a sua rede no local e o Domínio do Active Directory para a nuvem através da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções de nuvem híbrida, consulte Estendendo [soluções de dados locais para a nuvem](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Suporta a [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um subscritor para replicar os dados. | A replicação tem suporte para a instância gerenciada como um recurso de visualização. | Dá suporte total à [replicação transacional SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Always on grupos de disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services e envio de logs para replicar dados. Além disso, as cópias de segurança do SQL Server tradicionais são totalmente suportadas |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações comerciais para escolher a Base de Dados SQL do Azure ou o SQL Server em VMs do Azure

Há vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados SQL:

- O [custo](#cost) – a opção de PaaS e IaaS inclui o preço base que abrange a infraestrutura e o licenciamento subjacentes. No entanto, com a opção IaaS, você precisa investir tempo e recursos adicionais para gerenciar seu banco de dados, enquanto em PaaS você está obtendo esses recursos de administração incluídos no preço. A opção IaaS permite que você desligue seus recursos enquanto não os estiver usando para diminuir o custo, enquanto a versão PaaS está sempre em execução, a menos que se você descartar e recriar seus recursos quando forem necessários.
- [Administração](#administration) – as opções de PaaS reduzem a quantidade de tempo que você precisa investir para administrar o banco de dados. No entanto, ele também limita o intervalo de tarefas e scripts de administração personalizados que você pode executar ou executar. Por exemplo, o CLR não tem suporte com bancos de dados únicos ou em pool, mas tem suporte para uma instância gerenciada. Além disso, nenhuma opção de implantação no PaaS dá suporte ao uso de sinalizadores de rastreamento.
- [Contrato de nível de serviço](#service-level-agreement-sla) -tanto IaaS quanto PaaS fornecem SLA de alto, padrão do setor. A opção PaaS garante 99,99% de SLA, enquanto a IaaS garante 99,95% de SLA para infraestrutura, o que significa que você precisa implementar mecanismos adicionais para garantir a disponibilidade de seus bancos de dados. Você pode implementar a solução de alta disponibilidade em 99,99% criando um SQL Server adicional na VM e configurar grupos de disponibilidade AlwaysOn.
- O [tempo de mudança para o Azure](#market) -SQL Server na VM do Azure é a correspondência exata do seu ambiente, portanto, a migração do local para a VM do SQL do Azure não é diferente de mover os bancos de dados de um servidor local para outro. A instância gerenciada também permite uma migração extremamente fácil; no entanto, pode haver algumas alterações que você precisa aplicar antes de migrar para uma instância gerenciada.

Esses fatores serão discutidos em mais detalhes nas seções a seguir.

### <a name="cost"></a>Custo

Quer seja uma start-up com problemas de liquidez ou uma equipa numa empresa estabelecida que opera com grandes restrições orçamentais, o financiamento limitado é, muitas vezes, o principal fator quando tem de decidir como alojar as suas bases de dados. Nesta seção, você aprenderá sobre as noções básicas de cobrança e licenciamento no Azure com relação a essas duas opções de banco de dados relacional: Banco de dados SQL e SQL Server em VMs do Azure. Também podem aprender a calcular o custo total da aplicação.

#### <a name="billing-and-licensing-basics"></a>Noções básicas de faturação e licenciamento

Atualmente, o **banco de dados SQL** é vendido como um serviço e está disponível com várias opções de implantação e em várias camadas de serviço com preços diferentes para recursos, todos os quais são cobrados por hora a uma taxa fixa com base na camada de serviço e no tamanho de computação que você escolher . Para obter as informações mais recentes sobre as atuais camadas de serviço com suporte, tamanhos de computação e valores de armazenamento, consulte modelo [de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

- Com o banco de dados SQL, você pode escolher uma camada de serviço que atenda às suas necessidades de uma ampla gama de preços a partir de 5 $/mês para a camada básica.
- Você pode criar [pools elásticos](sql-database-elastic-pool.md) para compartilhar recursos entre instâncias de banco de dados para reduzir custos e acomodar picos de uso.
- Com a instância gerenciada do banco de dados SQL, você também pode trazer sua própria licença. Para obter mais informações sobre o seu próprio licenciamento, consulte [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/) ou use a [calculadora de benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **economizar até 40%** .

Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares. Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às necessidades de taxa de transferência variadas do seu aplicativo.

Com a **Base de Dados SQL**, o software de base de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](sql-database-automated-backups.md) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados.

Com o **SQL Server em VMs do Azure**, pode utilizar qualquer uma das imagens do SQL Server fornecidas pela plataforma (que inclui uma licença) ou utilizar a sua licença do SQL Server. Todas as versões (2008R2, 2012, 2014, 2016) e edições (programador, rápida, Web, Standard, Enterprise) do SQL Server suportadas estão disponíveis. Além disso, as versões Traga a sua própria licença (BYOL) das imagens estão disponíveis. Quando utiliza as imagens fornecidas pelo Azure, o custo operacional depende do tamanho da VM e a edição do SQL Server que escolher. Independentemente do tamanho da VM ou da edição de SQL Server, você paga o custo de licenciamento por minuto de SQL Server e o servidor Windows ou Linux, juntamente com o custo de armazenamento do Azure para os discos de VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server durante o tempo que precisar, sem comprar licenças adicionais do SQL Server. Se você trazer sua própria licença de SQL Server para o Azure, será cobrado apenas pelos custos de servidor e armazenamento. Para obter mais informações sobre o modelo «traga a sua própria licença», consulte o artigo [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total da aplicação

Quando você começa a usar uma plataforma de nuvem, o custo da execução do seu aplicativo inclui o custo para novos custos de desenvolvimento e administração contínua, além dos custos de serviço da plataforma de nuvem pública.

**Ao utilizar a Base de Dados SQL do Azure:**

- Custos de administração altamente minimizados
- Custos de desenvolvimento limitados para aplicativos migrados (instâncias gerenciadas)
- Custos do serviço do banco de dados SQL
- Nenhum custo de compra de hardware

**Ao utilizar o SQL Server em VMs do Azure:**

- Custos de administração mais altos
- Limitado a nenhum custo de desenvolvimento para aplicativos migrados
- Custos do serviço de máquina virtual
- Nenhum custo de compra de hardware

Para obter mais informações sobre preços, consulte os seguintes recursos:

- [Preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preço da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem passa tanto por descartar a complexidade de administração como pelo custo. Com IaaS e PaaS, a Microsoft administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software de banco de dado, gerencia o balanceamento de carga e faz failover transparente se houver um falha de servidor em um data center.

- Com o **banco de dados SQL do Azure**, você pode continuar administrando seu banco de dados, mas não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança. Além disso, a configuração de alta disponibilidade para outra data center requer configuração e administração mínimas.
- Com o **SQL Server em VMs do Azure**, tem controlo total sobre o sistema operativo e a configuração da instância do SQL Server. Com uma VM, cabe-lhe a si decidir quando atualizar o sistema operativo e o software de base de dados e quando instalar software adicional, tal como o antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. O Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)

Para muitos departamentos de TI, cumprir as obrigações de tempo de atividade de um Contrato de Nível de Serviço (SLA) é uma prioridade máxima. Nesta secção, vamos ver que SLA se aplica a cada opção de alojamento de base de dados.

Para o **banco de dados SQL**, a Microsoft fornece um SLA de disponibilidade de 99,99%. Para obter as informações mais recentes, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para o **SQL Server em execução em VMs do Azure**, a Microsoft fornece um SLA de 99,95% de disponibilidade, que abrange apenas a Máquina Virtual. Este SLA não abrange os processos (como o SQL Server) em execução na VM e requer que aloje, pelo menos, duas instâncias de VM num conjunto de disponibilidade. Para obter as informações mais recentes, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para HA (alta disponibilidade) de banco de dados em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Utilizar uma opção de elevada disponibilidade suportadas não fornece um SLA adicional, mas permite-lhe alcançar uma disponibilidade de base de dados > 99,99%.

### <a name="market"></a>Tempo para mover para o Azure

Os **bancos de dados individuais ou pools elásticos** são a solução certa para aplicativos projetados na nuvem quando a produtividade do desenvolvedor e o rápido tempo de colocação no mercado para novas soluções são essenciais. Com funcionalidade semelhante a DBA programática, é perfeita para programadores e arquitetos de nuvem, uma vez que reduz a necessidade de gerir a base de dados e o sistema operativo subjacentes.

A **instância gerenciada do banco de dados SQL** simplifica muito a migração de aplicativos existentes para o banco de dados SQL do Azure, permitindo que você traga rapidamente os aplicativos de banco de dados migrados para o mercado no Azure.

**SQL Server em execução em VMs do Azure** serão perfeitos se seus aplicativos novos ou existentes exigirem grandes bancos de dados ou acesso a todos os recursos no SQL Server ou no Windows/Linux, e você quiser evitar o tempo e as despesas de adquirir um novo hardware local. Também é uma boa opção quando você deseja migrar aplicativos locais e bancos de dados existentes para o Azure no estado em que se encontra, em casos em que a instância gerenciada do banco de dados SQL do Azure não é uma boa opção. Como não é necessário alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento na nova arquitetura da solução existente. Em vez disso, pode concentrar-se na migração de todas as suas soluções para o Azure e na execução de algumas otimizações de desempenho que possam ser exigidas pela plataforma do Azure. Para obter mais informações, consulte o artigo [Melhores Práticas de Desempenho para o SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Passos Seguintes

- Consulte [A sua primeira Base de Dados SQL do Azure](sql-database-single-database-get-started.md) para começar a utilizar a Base de Dados SQL.
- Consulte [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte [Aprovisionar uma máquina virtual do SQL Server no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) para começar a utilizar o SQL Server em VMs do Azure.
- [Identifique o banco de dados SQL do Azure/instância gerenciada SKU correto para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).
