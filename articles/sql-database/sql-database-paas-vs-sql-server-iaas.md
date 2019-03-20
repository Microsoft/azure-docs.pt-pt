---
title: Escolher entre opções SQL no Azure | Documentos da Microsoft
description: Saiba como escolher entre as opções de implementação na base de dados do Azure SQL e entre a base de dados do Azure SQL e SQL Server em máquinas virtuais do Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Nuvem SQL Server, SQL Server na nuvem, base de dados PaaS, SQL Server na nuvem, DBaaS
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: e5f400bb25fce58a4fb63ca1ad76ddf2b71fbd9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835423"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Escolher o melhor opção de SQL Server no Azure

No Azure, pode ter suas cargas de trabalho do SQL Server em execução numa infraestrutura alojada (IaaS) ou em execução como um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). No PaaS, tem várias opções de implementação e camadas de serviço dentro de cada opção de implementação. A questão principal que precisa de pedir ao decidir entre PaaS ou IaaS é que pretende gerir a sua base de dados, aplicar patches, efetuar cópias de segurança ou pretende delegar essas operações para o Azure?
Consoante a resposta, tem as seguintes opções:

- [Base de dados SQL do Azure](sql-database-technical-overview.md): Um totalmente gerida da base de dados motor de SQL, com base na mais recente estável Enterprise Edition do SQL Server. Este é um relacional da base de dados-como-serviço (DBaaS) alojada na cloud do Azure que se enquadra na categoria do setor de *plataforma-como-serviço (PaaS)*. Base de dados SQL tem várias opções de implementação, cada um dos quais se baseia em hardware e software que é propriedade, alojado e mantido pela Microsoft normalizado. Com base de dados SQL, pode usar recursos internos e funcionalidades que requerem uma ampla configuração quando utilizado no SQL Server (no local ou numa máquina virtual do Azure). Ao utilizar a Base de Dados SQL, paga apenas o que utilizar, dispondo de opções para aumentar verticalmente ou horizontalmente para obter um maior desempenho sem interrupções. Base de dados SQL tem as funcionalidades adicionais que não estão disponíveis no SQL Server, como elevada disponibilidade incorporada, inteligência e gestão. Base de dados SQL do Azure oferece as seguintes opções de implementação:
  
  - Como um [base de dados individual](sql-database-single-database.md) com seu próprio conjunto de recursos geridos através de um servidor de base de dados SQL. Uma base de dados é semelhante a um [bases de dados contidas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server. Esta opção é otimizada para desenvolvimento de aplicativos modernos de novas aplicações na cloud.
  - Uma [conjunto elástico](sql-database-elastic-pool.md), que é uma coleção de bases de dados com um conjunto partilhado de recursos geridos através de um servidor de base de dados SQL. Bases de dados individuais podem ser movidas para dentro e fora de um conjunto elástico. Esta opção é otimizada para desenvolvimento de aplicativos modernos de aplicações na cloud novas com a aplicação SaaS multi-inquilino.
  - [Instância gerida](sql-database-managed-instance.md), que é uma coleção de bases de dados do sistema e do usuário com um conjunto partilhado de recursos. Uma instância gerida é semelhante a uma instância dos recursos de oferta de [motor de base de dados do Microsoft SQL Server] partilhado para bases de dados e funcionalidades adicionais com âmbito de instância. Instância gerida suporta a migração de base de dados no local com o mínimo para nenhuma alteração de base de dados. Esta opção fornece todos os benefícios de PaaS de SQL Database do Azure, mas adiciona recursos que foram anteriormente disponíveis apenas em VMs de SQL. Isto inclui uma rede virtual nativa (VNet) e quase 100% de compatibilidade com SQL Server no local.
- [SQL Server em Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) cai na categoria do setor *infraestrutura-como-serviço (IaaS)* e permite-lhe executar o SQL Server numa máquina de virtual totalmente gerido na cloud do Azure. [Máquinas virtuais do SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) também é executado em hardware normalizado que é propriedade, alojado e mantido pela Microsoft. Quando utilizar o SQL Server numa VM, pode pagar apenas-como que utilizar lugar por uma licença do SQL Server já incluída numa imagem do SQL Server ou facilmente utilizar uma licença já existente. Também pode parar ou retomar a VM conforme necessário. SQL Server instalado e alojado na nuvem no Windows Server ou máquinas de virtuais do Linux (VMs) em execução no Azure, também conhecido como uma infraestrutura como serviço (IaaS). SQL Server em máquinas virtuais do Azure é uma boa opção para a migração no local bases de dados do SQL Server e a aplicações sem qualquer alteração de base de dados. Todas as versões recentes e edições do SQL Server estão disponíveis para a instalação na máquina virtual IaaS. A diferença mais significativa da base de dados SQL é que as VMs do SQL Server permitem o controlo total sobre o motor de base de dados. Pode escolher quando a aplicação de patches/manutenção será iniciada, para alterar o modelo de recuperação para simples ou em massa registados para ativar carregamento mais rápido menos log, para colocar em pausa ou iniciar o motor quando necessário, e podem personalizar totalmente o motor de base de dados do SQL Server. Com esse controle adicional vem com responsabilidade para gerir as máquinas virtuais.

As principais diferenças entre estas opções estão listadas na tabela a seguir:

| SQL Server em VM | Instância na base de dados SQL gerida | Base de dados / do conjunto elástico na base de dados SQL |
| --- | --- | --- |
|Tem controle total sobre o mecanismo do SQL Server.<br/>Até 99,95% de disponibilidade.<br/>Paridade completa com a versão correspondente do SQL Server no local.<br/>Versão do motor de base de dados fixa e bem conhecidos.<br/>Simplificam a migração do SQL Server no local.<br/>Endereço IP privado na VNet do Azure.<br/>Tem a capacidade de implementar aplicações ou serviços no anfitrião onde o SQL Server é colocado.| Compatibilidade com o SQL Server no local.<br/>99,99% de disponibilidade garantida.<br/>Cópias de segurança incorporadas local, aplicação de patches, recuperação.<br/>Mais recente versão estável do motor de base de dados.<br/>Simplificam a migração do SQL Server.<br/>Endereço IP privado na VNet do Azure.<br/>Incorporado avançada e inteligência de segurança.<br/>Alteração online de recursos (CPU/armazenamento).|Das funcionalidades mais utilizadas do SQL Server estão disponíveis.<br/>99,99% de disponibilidade garantida.<br/>Cópias de segurança incorporadas local, aplicação de patches, recuperação.<br/>Mais recente versão estável do motor de base de dados.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) para bases de dados individuais.<br/>Incorporado avançada e inteligência de segurança.<br/>Alteração online de recursos (CPU/armazenamento).|
|Tem de gerir as suas cópias de segurança e patches.<br>Precisa implementar sua própria solução de elevada disponibilidade.<br/>Existe um período de indisponibilidade ao alterar o resources(CPU/storage)|Ainda há algum número mínimo de funcionalidades do SQL Server que não estão disponíveis.<br/>Não manutenção exata a garantia de tempo (mas praticamente transparente).<br/>Compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade de base de dados.|Migração do SQL Server talvez seja difícil.<br/>Algumas funcionalidades do SQL Server não estão disponíveis.<br/>Não manutenção exata a garantia de tempo (mas praticamente transparente).<br/>Compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade de base de dados.<br/>Não é possível atribuir o endereço IP privado (pode limitar o acesso com regras de firewall).|

Saiba como cada opção de implementação se adapta a plataforma de dados da Microsoft e obtenha ajuda para encontrar a opção adequada aos seus requisitos empresariais. Quer dê prioridade à redução de custos ou a uma administração mínima acima de tudo o resto, este artigo pode ajudá-lo a decidir qual é a abordagem que melhor responde aos requisitos comerciais a que dá mais importância.

## <a name="microsofts-sql-data-platform"></a>Plataforma de dados do SQL da Microsoft

Um dos primeiros aspetos a compreender em qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar todas. A plataforma de dados da Microsoft tira partido da tecnologia do SQL Server e disponibiliza-a em máquinas físicas no local, ambientes de nuvem privada, ambientes de nuvem privada alojada de terceiros e nuvem pública. O SQL Server nas máquinas virtuais do Azure permite-lhe responder a necessidades comerciais únicas e diversas através de uma combinação de implementações alojadas na cloud e no local, utilizando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e conhecimentos em todos estes ambientes.

   ![Opções do SQL Server de nuvem: Base de dados do SQL server em IaaS ou SaaS SQL na cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Tal como ilustrado no diagrama, cada oferta pode ser caracterizada pelo nível de administração que têm sobre a infraestrutura (no eixo X) e pelo grau de eficiência de custos alcançada através da automatização e consolidação de nível de base de dados (no eixo Y).

Ao conceber uma aplicação, estão disponíveis quatro opções básicas para alojar a parte do SQL Server da aplicação:

- SQL Server em máquinas físicas não virtualizadas
- SQL Server em máquinas virtualizadas no local (nuvem privada)
- SQL Server numa Máquina Virtual do Azure (nuvem pública da Microsoft)
- Base de Dados SQL do Azure (nuvem pública da Microsoft)

As secções seguintes, vai aprender sobre o SQL Server na nuvem pública da Microsoft: Base de dados SQL do Azure e o SQL Server em VMs do Azure. Além disso, explorará motivadores de negócio comuns para determinar qual é a opção que funciona melhor para a sua aplicação.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Um olhar mais atento sobre a Base de Dados SQL do Azure e o SQL Server em VMs do Azure

Em geral, estas duas opções de SQL estão otimizadas para diferentes fins:

- **Base de Dados SQL do Azure**

Otimizado para reduzir os custos gerais de gestão para o mínimo para o aprovisionamento e gestão de muitas bases de dados. Reduz os custos correntes de administração, uma vez que não é necessário gerir as máquinas virtuais, o sistema operativo ou o software de base de dados. Não é necessário gerir as atualizações, a elevada disponibilidade ou as [cópias de segurança](sql-database-automated-backups.md). Em geral, a Base de Dados SQL do Azure permite aumentar substancialmente o número de bases de dados geridas por um único recurso de TI ou de desenvolvimento. [Conjuntos elásticos](sql-database-elastic-pool.md) também suportar arquiteturas de aplicação multi-inquilino de SaaS com as funcionalidades, incluindo isolamento de inquilino e a capacidade de dimensionar para reduzir os custos ao partilhar recursos entre bases de dados. [Instância gerida](sql-database-managed-instance.md) fornece suporte para funcionalidades com âmbito de instância, permitindo que simplificam a migração das aplicações existentes, bem como partilhar recursos entre bases de dados.

- **SQL Server em execução em VMs do Azure**

Otimizado para migrar as aplicações existentes para o Azure ou expandir aplicações no local existentes para a nuvem em implementações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais . Com o SQL Server em VMs do Azure, tem direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada na nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Estas capacidades permite-lhe criar um sistema altamente personalizado para fazer face aos requisitos de disponibilidade e desempenho específicos da sua aplicação.

A tabela seguinte resume as principais caraterísticas da Base de Dados SQL e do SQL Server em VMs do Azure:

| | Conjuntos elásticos e bases de dados individuais de base de dados SQL | Instâncias geridas de base de dados SQL |Máquinas virtuais do Azure com o SQL Server |
| --- | --- | --- |---|
| **Melhor para:** |Novas concebidas para a cloud aplicações que pretendem utilizar as funcionalidades do SQL Server estáveis mais recentes e tem restrições de tempo no desenvolvimento e marketing. | Novas aplicações ou aplicações no local existentes que pretende utilizar as funcionalidades do SQL Server estáveis mais recentes e que são migradas para a cloud com alterações mínimas.  | Aplicativos existentes que necessitam de uma migração rápida para a cloud com alterações mínimas ou sem alterações. Cenários de desenvolvimento e teste rápidos quando não pretende comprar hardware de SQL Server de não produção no local. |
|  | Equipas que necessitam de atualizações, recuperação após desastre e elevada disponibilidade para a base de dados. | Mesmo que bases de dados de base de dados SQL, únicas e em pool. | As equipes que podem configurar, ajustar bem, personalizar e gerir a elevada disponibilidade, recuperação após desastre e aplicação de patches para o SQL Server. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. |
|  | Equipas que não pretendem gerir o sistema operativo subjacente e definições de configuração. | Mesmo que bases de dados de base de dados SQL, únicas e em pool. | Precisa de um ambiente personalizado com direitos administrativos completos. |
|  | Bases de dados de até 100 TB. | Até 8 TB. | Instâncias do SQL Server com até 64 TB de armazenamento. A instância pode suportar tantas bases de dados quanto necessário. |
| **Compatibilidade** | Oferece suporte a maioria dos recursos de nível de base de dados no local. | Suporta quase todas as capacidades de nível de instância e ao nível da base de dados no local. | Suporta todas as capacidades no local. |
| **Recursos:** | Não pretende utilizar recursos de TI para a configuração e gestão da infraestrutura subjacente, mas pretende focar-se na camada da aplicação. | Mesmo que bases de dados de base de dados SQL, únicas e em pool. | Tem alguns recursos de TI para a configuração e gestão. Algumas das funcionalidades automatizadas fornecidas simplificam-no significativamente. |
| **Custo total de propriedade:** | Elimina os custos de hardware e reduz os custos administrativos. | Mesmo que bases de dados de base de dados SQL, únicas e em pool. | Elimina os custos com hardware. |
| **Continuidade do negócio:** |Para além [capacidades de infraestrutura de tolerância a falhas incorporada](sql-database-high-availability.md), a base de dados do Azure SQL fornece funcionalidades, tais como [cópias de segurança automatizadas](sql-database-automated-backups.md), [restaurodepontonotempoda](sql-database-recovery-using-backups.md#point-in-time-restore), [georrestauro](sql-database-recovery-using-backups.md#geo-restore), [georreplicação ativa](sql-database-active-geo-replication.md), e [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) para aumentar a continuidade do negócio. Para obter mais informações, consulte [Descrição geral da continuidade de negócio da Base de Dados SQL](sql-database-business-continuity.md). | Mesmo que bases de dados de base de dados SQL, únicas e em pool, mais cópias de segurança iniciada pelo utilizador e apenas de cópia estão disponível. | O SQL Server em VMs do Azure permite configurar uma solução de elevada disponibilidade e recuperação após desastre para as necessidades específicas da sua base de dados. Assim, poderá ter um sistema que está altamente otimizado para a sua aplicação. Pode testar e executar ativações pós-falha sempre que necessário. Para obter mais informações, consulte [Elevada Disponibilidade e Recuperação Após Desastre do SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Nuvem híbrida:** |A sua aplicação no local pode aceder a dados na Base de Dados SQL do Azure. | [Implementação de rede virtual nativo](sql-database-managed-instance-vnet-configuration.md) e a conectividade ao seu ambiente no local com o Express Route do Azure ou o Gateway de VPN. | Com o SQL Server em VMs do Azure, pode ter aplicações que são executadas parcialmente na nuvem e parcialmente no local. Por exemplo, pode expandir a sua rede no local e o Domínio do Active Directory para a nuvem através da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções na cloud híbrida, veja [expandir soluções de dados no local para a cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Suporta a [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um subscritor para replicar os dados. | A replicação é suportada para a instância gerida como uma funcionalidade de pré-visualização. | Suporta totalmente [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [grupos de Disponibilidade AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), serviços de integração e envio de registo para replicar dados. Além disso, as cópias de segurança do SQL Server tradicionais são totalmente suportadas |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações comerciais para escolher a Base de Dados SQL do Azure ou o SQL Server em VMs do Azure

Existem vários fatores que podem influenciar a sua decisão para escolher o PaaS ou IaaS para alojar bases de dados SQL:

- [Custo](#cost) -opção de IaaS e PaaS tanto incluir a base de preço que descrevem a infraestrutura subjacente e licenciamento. No entanto, com a opção de IaaS terá de investir mais tempo e recursos para gerir a sua base de dados, enquanto no PaaS estiver a obter estas funcionalidades de administração incluídas no preço. Opção de IaaS permite-lhe encerrar seus recursos enquanto não estiver a utilizá-los para reduzir os custos, enquanto a versão de PaaS está sempre em execução, a menos que se remover e recriar os recursos que são necessários.
- [Administração](#administration) -opções de PaaS reduzem a quantidade de tempo que terá de investir para administrar a base de dados. No entanto, ela também limita o intervalo de tarefas de administração personalizado e scripts que pode efetuar ou executar. Por exemplo, o CLR não é suportado com bancos de dados individuais ou agrupados, mas é suportado para uma instância gerida. Além disso, não existem opções de implantação no PaaS suportam a utilização de sinalizadores de rastreio.
- [Contrato de nível de serviço](#service-level-agreement-sla) -tanto de IaaS e PaaS fornecer alto, o SLA padrão da indústria. Opção de PaaS garante SLA de 99,99%, enquanto o IaaS garantias de SLA de 99,95% para a infraestrutura, o que significa que precisa implementar mecanismos adicionais para garantir a disponibilidade dos seus bancos de dados. Em casos extremos, se pretender implementar a solução de alta disponibilidade que é a correspondência PaaS, poderá ter de criar adicionais do SQL Server numa VM e configurar grupos de Disponibilidade AlwaysOn, o que poderão double o custo da base de dados.
- [Tempo para mover para o Azure](#market) -SQL Server numa VM do Azure é a correspondência exata do seu ambiente, para que a migração no local para a VM de SQL do Azure não é diferente do que mover as bases de dados a partir de um servidor no local para outro. Instância gerida permite também a migração extremamente fácil; No entanto, poderão existir algumas alterações que tem de aplicar antes de migrar para uma instância gerida.

Esses fatores serão discutidos em mais detalhes nas seções a seguir.

### <a name="cost"></a>Custo

Quer seja uma start-up com problemas de liquidez ou uma equipa numa empresa estabelecida que opera com grandes restrições orçamentais, o financiamento limitado é, muitas vezes, o principal fator quando tem de decidir como alojar as suas bases de dados. Nesta secção, vai aprender sobre a faturação e licenciamento Noções básicas no Azure com respeito a estas duas opções de base de dados relacional: Base de dados SQL e SQL Server em VMs do Azure. Também podem aprender a calcular o custo total da aplicação.

#### <a name="billing-and-licensing-basics"></a>Noções básicas de faturação e licenciamento

Atualmente, **base de dados SQL** é vendido como um serviço e está disponível com várias opções de implementação e em vários escalões de serviço com diferentes preços para os recursos, as quais são cobradas à hora a um preço fixo com base no escalão de serviço e tamanho de computação que escolher. Para obter as informações mais recentes sobre os atuais suportado escalões de serviço, tamanhos e quantidades de armazenamento, consulte de computação [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

- Com o banco de dados único de base de dados SQL, pode escolher uma camada de serviços que se adeque às suas necessidades de uma vasta gama de preços a partir de 5$ / mês para o escalão básico.
- Pode criar [conjuntos elásticos](sql-database-elastic-pool.md) partilhar recursos entre instâncias da base de dados para reduzir os custos e a utilização de acomodar picos.
- Com a instância gerida da base de dados SQL, também pode trazer a sua própria licença. Para obter mais informações sobre bring-your-own licenciamento, consulte [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/) ou utilize [Calculadora do benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **Poupe até 40%**.

Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares. Pode ajustar os escalões de serviço e tamanhos de forma a satisfazer necessidades variadas de débito de seu aplicativo de computação dinamicamente.

Com a **Base de Dados SQL**, o software de base de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](sql-database-automated-backups.md) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados.

Com o **SQL Server em VMs do Azure**, pode utilizar qualquer uma das imagens do SQL Server fornecidas pela plataforma (que inclui uma licença) ou utilizar a sua licença do SQL Server. Todas as versões (2008R2, 2012, 2014, 2016) e edições (programador, rápida, Web, Standard, Enterprise) do SQL Server suportadas estão disponíveis. Além disso, as versões Traga a sua própria licença (BYOL) das imagens estão disponíveis. Quando utiliza as imagens fornecidas pelo Azure, o custo operacional depende do tamanho da VM e a edição do SQL Server que escolher. Independentemente do tamanho VM ou edição do SQL Server, paga um custo licenciamento por minuto do SQL Server e o Windows ou Linux Server, juntamente com o custo de armazenamento do Azure para os discos VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server durante o tempo que precisar, sem comprar licenças adicionais do SQL Server. Se trouxer a sua própria licença do SQL Server para o Azure, é-lhe cobrada server e apenas os custos de armazenamento. Para obter mais informações sobre o modelo «traga a sua própria licença», consulte o artigo [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total da aplicação

Quando começar a utilizar uma plataforma na cloud, o custo de execução da sua aplicação inclui o custo para um novo desenvolvimento e os custos de administração em curso, mais os custos de serviço de plataforma de cloud pública.

**Ao utilizar a Base de Dados SQL do Azure:**

- Custos de administração altamente minimizados
- Custos de desenvolvimento de limitado para aplicativos migrados (instâncias geridas)
- Custos de serviço de base de dados SQL
- Não existem custos de aquisição de hardware

**Ao utilizar o SQL Server em VMs do Azure:**

- Custos de administração superiores
- Limitado a nenhum os custos de desenvolvimento para aplicativos migrados
- Custos de serviço de máquina virtual
- Não existem custos de aquisição de hardware

Para obter mais informações sobre preços, consulte os seguintes recursos:

- [Preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem passa tanto por descartar a complexidade de administração como pelo custo. Com IaaS e PaaS, Microsoft administra a infraestrutura subjacente e automaticamente replica todos os dados para fornecer a recuperação após desastre, configura e atualiza o software de base de dados, gere o balanceamento de carga e efetua a ativação pós-falha transparente se existir um Falha do servidor dentro de um centro de dados.

- Com o **base de dados do Azure SQL**, pode continuar a administrar a sua base de dados, mas já não precisar de gerir o motor de base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança. Além disso, a configuração de elevada disponibilidade para outro Datacenter requer configuração mínima e administração.
- Com o **SQL Server em VMs do Azure**, tem controlo total sobre o sistema operativo e a configuração da instância do SQL Server. Com uma VM, cabe-lhe a si decidir quando atualizar o sistema operativo e o software de base de dados e quando instalar software adicional, tal como o antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. O Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)

Para muitos departamentos de TI, cumprir as obrigações de tempo de atividade de um Contrato de Nível de Serviço (SLA) é uma prioridade máxima. Nesta secção, vamos ver que SLA se aplica a cada opção de alojamento de base de dados.

Para **base de dados SQL**, a Microsoft fornece um SLA de 99,99% de disponibilidade. Para obter as informações mais recentes, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para o **SQL Server em execução em VMs do Azure**, a Microsoft fornece um SLA de 99,95% de disponibilidade, que abrange apenas a Máquina Virtual. Este SLA não abrange os processos (como o SQL Server) em execução na VM e requer que aloje, pelo menos, duas instâncias de VM num conjunto de disponibilidade. Para obter as informações mais recentes, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para uma base de dados elevada disponibilidade (HA) nas VMs, deve configurar uma das opções de elevada disponibilidade suportadas no SQL Server, tal como [grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Utilizar uma opção de elevada disponibilidade suportadas não fornece um SLA adicional, mas permite-lhe alcançar uma disponibilidade de base de dados > 99,99%.

### <a name="market"></a>Tempo para mover para o Azure

**Bases de dados individuais de base de dados SQL ou conjuntos elásticos** são a solução certa para aplicações concebidas na cloud quando a produtividade do desenvolvedor e o rápido tempo de comercialização de novas soluções são essenciais. Com funcionalidade semelhante a DBA programática, é perfeita para programadores e arquitetos de nuvem, uma vez que reduz a necessidade de gerir a base de dados e o sistema operativo subjacentes.

**Instância gerida de base de dados SQL** simplifica bastante a migração das aplicações existentes para o Azure SQL Database, permitindo-lhe aproximar as aplicações de base de dados migrada para o mercado no Azure, o mais rapidamente.

**SQL Server em execução em VMs do Azure** é perfeito se as suas aplicações existentes ou novas necessitam grandes bases de dados ou de acesso a todas as funcionalidades no SQL Server ou Windows/Linux e quiser evitar o tempo e despesas de aquisição de novo hardware no local. Também é uma boa opção se pretender migrar existente no local aplicações e bases de dados para o Azure como-for - em casos em que a instância gerida da base de dados do Azure SQL não é uma boa opção. Como não é necessário alterar a apresentação, aplicativo e as camadas de dados, poupa tempo e orçamento na nova arquitetura da sua solução existente. Em vez disso, pode concentrar-se na migração de todas as suas soluções para o Azure e na execução de algumas otimizações de desempenho que possam ser exigidas pela plataforma do Azure. Para obter mais informações, consulte o artigo [Melhores Práticas de Desempenho para o SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Passos Seguintes

- Consulte [A sua primeira Base de Dados SQL do Azure](sql-database-single-database-get-started.md) para começar a utilizar a Base de Dados SQL.
- Consulte [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte [Aprovisionar uma máquina virtual do SQL Server no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) para começar a utilizar o SQL Server em VMs do Azure.
