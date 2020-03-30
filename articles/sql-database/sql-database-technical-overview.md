---
title: O que é o serviço Base de Dados SQL do Azure?
description: 'Obtenha uma introdução à Base de Dados SQL: detalhes técnicos e capacidades do sistema de gestão relacional da Base de Dados da Microsoft (RDBMS) na nuvem.'
keywords: introdução à sql, introdução sql, o que é a base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 209b4136678e6f04666b4a2b6180f4768bf6afc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500826"
---
# <a name="what-is-the-azure-sql-database-service"></a>O que é o serviço Base de Dados SQL do Azure?

A Base de Dados Azure SQL é uma base de dados relacional de uso geral, fornecida como um serviço gerido. Com ele, pode criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para as aplicações e soluções em Azure. A Base de Dados SQL pode ser a escolha certa para uma variedade de aplicações modernas em nuvem porque permite processar dados relacionais e [estruturas não relacionais](sql-database-multi-model-features.md), tais como gráficos, JSON, espacial e XML.

Baseia-se na versão mais recente do motor de base de dados do [Microsoft SQL Server.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) Pode utilizar funcionalidades avançadas de processamento de consultas, tais como [tecnologias de alta performance na memória](sql-database-in-memory.md) e processamento inteligente de [consultas.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) De facto, as mais recentes capacidades do SQL Server são lançadas primeiro para a Base de Dados SQL e, em seguida, para o próprio SQL Server. Obtém-se as mais recentes capacidades do SQL Server sem sobrecarga para remendar ou atualizar, testadas em milhões de bases de dados. 

A Base de Dados SQL permite-lhe definir e escalar facilmente o desempenho dentro de dois modelos de compra diferentes: um [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e um modelo de compra baseado em [DTU](sql-database-service-tiers-dtu.md). A Base de Dados SQL é um serviço totalmente gerido que tem uma alta disponibilidade incorporada, backups e outras operações de manutenção comuns. A Microsoft lida com todas as remendas e atualizações do Código SQL e do sistema operativo. Não tens de gerir a infraestrutura subjacente.

> [!NOTE]
> Para termos relevantes e respetivas definições, consulte os termos da Base de [Dados SQL .](sql-database-glossary-terms.md)

## <a name="deployment-models"></a>Modelos de implementação

A Base de Dados SQL do Azure oferece as seguintes opções de implementação para bases de dados SQL do Azure:

![Diagrama de opções de implementação](./media/sql-database-technical-overview/deployment-options.png)

- [A base de dados única](sql-database-single-database.md) representa uma base de dados totalmente gerida e isolada. Pode utilizar esta opção se tiver aplicações e microserviços modernos em nuvem que necessitem de uma única fonte de dados fiável. Uma única base de dados é semelhante a uma [base de dados contida](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) no Microsoft [SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [A instância gerida](sql-database-managed-instance.md) é uma instância totalmente gerida do Motor de Base de Dados do [Servidor Microsoft SQL](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Contém um conjunto de bases de dados que podem ser usadas em conjunto. Utilize esta opção para uma migração fácil das bases de dados do SQL Server no local para a nuvem Azure, e para aplicações que precisam de utilizar as funcionalidades de base de dados que o SQL Server Database Engine fornece.
- [O elástico](sql-database-elastic-pool.md) é uma coleção de bases de [dados únicas](sql-database-single-database.md) com um conjunto partilhado de recursos, como CPU ou memória. As bases de dados individuais podem ser movidas para dentro e para fora de uma piscina elástica.

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidades entre a Base de Dados SQL e o Servidor SQL, bem como as diferenças entre diferentes opções de implementação da Base de Dados Azure SQL, consulte as funcionalidades da Base de [Dados SQL](sql-database-features.md).

A Base de Dados SQL proporciona um desempenho previsível com vários tipos de recursos, níveis de serviço e tamanhos de computação. Proporciona escalabilidade dinâmica sem tempo de inatividade, otimização inteligente incorporada, escalabilidade global e disponibilidade, e opções de segurança avançadas. Estas capacidades permitem-lhe focar-se no rápido desenvolvimento de aplicações e acelerar o seu tempo-para-mercado, em vez de gerir máquinas virtuais e infraestruturas. O serviço de base de dados SQL está atualmente em 38 datacenters em todo o mundo, para que possa executar a sua base de dados num datacenter perto de si.

## <a name="scalable-performance-and-pools"></a>Dimensionar o desempenho e os conjuntos

Pode definir a quantidade de recursos atribuídos. 
- Com bases de dados únicas, cada base de dados é isolada das outras e é portátil. Cada um tem a sua própria quantidade garantida de computação, memória e recursos de armazenamento. A quantidade de recursos atribuídos à base de dados é dedicada a essa base de dados, e não é partilhada com outras bases de dados em Azure. Pode aumentar dinamicamente os recursos de base de [dados individuais](sql-database-single-database-scale.md) para cima e para baixo. A opção de base de dados única fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, pode obter 1 a 80 vCores, ou 32 GB a 4 TB. O [nível de serviço de hiperescala](sql-database-service-tier-hyperscale.md) para uma única base de dados permite-lhe escalar para 100 TB, com capacidades de backup e restauro rápidos.
- Com piscinas elásticas, pode atribuir recursos que são partilhados por todas as bases de dados da piscina. Pode criar uma nova base de dados ou mover as bases de dados únicas existentes para um conjunto de recursos para maximizar a utilização de recursos e economizar dinheiro. Esta opção também lhe dá a capacidade de escalar dinamicamente [os recursos de piscina elástica](sql-database-elastic-pool-scale.md) para cima e para baixo.
- Com casos geridos, cada instância gerida está isolada de outros casos com recursos garantidos. Num caso gerido, as bases de dados por exemplo partilham um conjunto de recursos. Pode [sondar](sql-database-managed-instance-resource-limits.md) dinamicamente os recursos de instância geridos para cima e para baixo.

Pode construir a sua primeira aplicação numa pequena base de dados individual a um custo baixo por mês no nível de serviço de uso geral. Em seguida, pode alterar o seu nível de serviço manualou programáticamente a qualquer momento para o nível de serviço crítico do negócio, para atender às necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Paga-se apenas os recursos de que precisa quando se precisa deles.

*A escalabilidade dinâmica* é diferente da *escala automática.* O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual sem períodos de indisponibilidade. A opção de base de dados única suporta a escalabilidade dinâmica manual, mas não a escala automática. Para uma experiência mais automática, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados. Outra opção é usar scripts que podem ajudar a automatizar a escalabilidade para uma única base de dados. Por exemplo, consulte [o Use PowerShell para monitorizar e escalar uma única base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

A Base de Dados SQL oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite-lhe escolher o número de vCores, a quantidade de memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite utilizar o [Azure Hybrid Benefit para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter poupanças de custos. Para obter mais informações sobre o Azure Hybrid Benefit, consulte a secção "Perguntas Frequentes" mais tarde neste artigo.
- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de recursos computacionais, de memória e de I/O em três níveis de serviço, para suportar a luz a cargas de trabalho pesadas da base de dados. Os tamanhos computacionais dentro de cada nível fornecem uma mistura diferente destes recursos, aos quais você pode adicionar recursos de armazenamento adicionais.
- O [modelo servidor escala](sql-database-serverless.md) automaticamente a computação com base na procura de carga de trabalho, e as contas para a quantidade de computação usada por segundo. O nível de computação sem servidor também interrompe automaticamente as bases de dados durante os períodos inativos quando apenas o armazenamento é faturado, e retoma automaticamente as bases de dados quando a atividade retorna.

### <a name="service-tiers"></a>Escalões de serviço

A Base de Dados Azure SQL oferece três níveis de serviço que são projetados para diferentes tipos de aplicações:
- Modelo de serviço Geral [Objetivo/Standard](sql-database-service-tier-general-purpose.md) concebido para cargas de trabalho comuns. Oferece opções de cálculo e armazenamento equilibradas orientadas para o orçamento.
- Nível de serviço [Business Critical/Premium](sql-database-service-tier-business-critical.md) projetado para aplicações OLTP com elevada taxa de transação e Menor Latência Em/O. Oferece a maior resiliência às falhas usando várias réplicas isoladas.
- [Nível](sql-database-service-tier-hyperscale.md) de serviço de hiperescala projetado para uma base de dados OLTP muito grande e a capacidade de armazenamento em escala automática e computação em escala fluidamente.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. Padrões de utilização imprevisíveis podem dificultar a gestão de custos e o seu modelo de negócio. Os [conjuntos elásticos](sql-database-elastic-pool.md) foram concebidos para resolver este problema. Você aloca recursos de desempenho a uma piscina em vez de uma base de dados individual. Você paga pelos recursos de desempenho coletivo da piscina em vez de um desempenho único na base de dados.

   ![Gráfico que mostra piscinas elásticas em edições básicas, standard e premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com piscinas elásticas, não precisa de se concentrar em ligar para cima e para baixo o desempenho da base de dados, uma vez que a procura de recursos flutua. As bases de dados de conjunto consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados reunidas consomem mas não excedem os limites da piscina, pelo que o seu custo permanece previsível mesmo que o uso individual de bases de dados não o faça.

Pode adicionar e remover bases de [dados para a piscina,](sql-database-elastic-pool-manage-portal.md)dimensionando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento que controla. Também pode controlar os recursos mínimos e máximos disponíveis para bases de dados na piscina, para garantir que nenhuma base de dados na piscina utilize todos os recursos da piscina, e que cada base de dados reunida tem uma quantidade mínima garantida de recursos. Para saber mais sobre padrões de design para software como um serviço (SaaS) aplicações que usam piscinas elásticas, consulte padrões de [design para aplicações SaaS multi-inquilinos com Base de Dados SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Os scripts podem ajudar na monitorização e no dimensionamento dos conjuntos elásticos. Por exemplo, consulte [use powerShell para monitorizar e escalar uma piscina elástica SQL na Base de Dados Azure SQL](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Um caso gerido não suporta piscinas elásticas. Pelo contrário, um caso gerido é uma coleção de bases de dados de instâncias que partilham recursos de instânciageridos.

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados de conjunto

Pode misturar bases de dados individuais com piscinas elásticas e alterar os níveis de serviço de bases de dados individuais e piscinas elásticas para se adaptar à sua situação. Também pode misturar e combinar outros serviços Azure com a Base de Dados SQL para atender às suas necessidades únicas de design de aplicações, impulsionar o custo e eficiência de recursos e desbloquear novas oportunidades de negócio.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Capacidades alargadas de monitorização e alertas

A Base de Dados Azure SQL fornece funcionalidades avançadas de monitorização e resolução de problemas que o ajudam a obter informações mais profundas sobre as características da carga de trabalho. Estas funcionalidades e ferramentas incluem:
 - As capacidades de monitorização incorporadas fornecidas pela versão mais recente do Motor de Base de Dados do Servidor SQL. Permitem-lhe encontrar insights de desempenho em tempo real. 
 - Capacidades de monitorização paaS fornecidas pelo Azure que lhe permitem monitorizar e resolver problemas com um grande número de casos de bases de dados.

[A Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), uma funcionalidade de monitorização incorporada do SQL Server, regista o desempenho das suas consultas em tempo real e permite identificar os potenciais problemas de desempenho e os consumidores de recursos de topo. A finação automática e as recomendações fornecem conselhos sobre as consultas com o desempenho regredido e índices em falta ou duplicados. A sintonização automática na Base de Dados SQL permite-lhe aplicar manualmente as scripts que podem corrigir os problemas, ou deixar a Base de Dados SQL aplicar a correção. A Base de Dados SQL também pode testar e verificar se a correção proporciona algum benefício, e reter ou reverter a mudança dependendo do resultado. Além da Query Store e das capacidades de afinação automática, pode utilizar [DMVs padrão e XEvent](sql-database-monitoring-with-dmvs.md) para monitorizar o desempenho da carga de trabalho.

O Azure fornece ferramentas [de monitorização](sql-database-performance-guidance.md) e [alerta](sql-database-insights-alerts-portal.md) de desempenho incorporadas, combinadas com classificações de desempenho, que lhe permitem monitorizar o estado de milhares de bases de dados. Utilizando estas ferramentas, pode avaliar rapidamente o impacto da escala para cima ou para baixo, com base nas suas necessidades de desempenho atuais ou projetadas. Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

- **Armazenamento Azure**: Para arquivar grandes quantidades de telemetria por um pequeno preço.
- **Hubs de eventos Azure**: Para integrar a telemetria da Base de Dados SQL com a sua solução de monitorização personalizada ou oleodutos quentes.
- **Registos do Monitor Azure**: Para uma solução de monitorização incorporada com capacidades de comunicação, alerta e mitigação.

![Diagrama da arquitetura de monitorização de Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Num ambiente tradicional do SQL Server, geralmente tem pelo menos duas máquinas configuradas localmente. Estas máquinas têm cópias exatas, sincronizadamente mantidas, dos dados para proteger contra uma falha de uma única máquina ou componente. Este ambiente proporciona uma elevada disponibilidade, mas não protege contra um desastre natural que destrói o seu datacenter.

A recuperação de desastres pressupõe que um evento catastrófico está geograficamente localizado o suficiente para ter outra máquina ou conjunto de máquinas com uma cópia dos seus dados longe. No Servidor SQL, pode utilizar sempre grupos de disponibilidade a funcionar em modo de sincronização para obter esta capacidade. Muitas vezes as pessoas não querem esperar que a replicação aconteça tão longe antes de cometer uma transação, por isso há potencial para a perda de dados quando se faz falhas não planeadas.

As bases de dados nos níveis de serviço críticos premium e empresariais já [fazem algo muito semelhante](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) à sincronização de um grupo de disponibilidade. As bases de dados em níveis de serviço mais baixos fornecem redundância através do armazenamento utilizando um [mecanismo diferente, mas equivalente.](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) A lógica incorporada ajuda a proteger-se contra uma única falha na máquina. A funcionalidade de georeplicação ativa dá-lhe a capacidade de proteger contra desastres onde toda uma região é destruída.

As Zonas de Disponibilidade Azure tentam proteger-se contra a paralisação de um único edifício de datacenter numa única região. Ajuda-o a proteger-se contra a perda de energia ou rede para um edifício. Na Base de Dados SQL, coloca as diferentes réplicas em diferentes zonas de disponibilidade (edifícios diferentes, efetivamente).

De facto, o acordo de nível de serviço [(SLA)](https://azure.microsoft.com/support/legal/sla/) do Azure, alimentado por uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação a funcionar 24 horas por dia, 7 dias por semana. A plataforma Azure gere totalmente todas as bases de dados, e não garante nenhuma perda de dados e uma elevada percentagem de disponibilidade de dados. O Azure lida automaticamente com patching, backups, replicação, deteção de falhas, falhas de hardware, software ou rede subjacentes, implementação de correções de bugs, falhas, atualizações de bases de dados e outras tarefas de manutenção. A disponibilidade standard é conseguida por uma separação das camadas de armazenamento e computação. A disponibilidade premium é conseguida integrando a computação e armazenamento num único nó para o desempenho, e, em seguida, implementando tecnologia semelhante aos Grupos Sempre Na Disponibilidade. Para uma discussão completa sobre as elevadas capacidades de disponibilidade da Base de Dados Azure SQL, consulte a disponibilidade da Base de [Dados SQL](sql-database-high-availability.md). 

Além disso, a Base de Dados SQL fornece funcionalidades de continuidade de negócio sinuosa [e de escalabilidade global.](sql-database-business-continuity.md) Estas incluem:

- [Backups automáticos:](sql-database-automated-backups.md)

  A Base de Dados SQL executa automaticamente cópias de segurança completas, diferenciais e de registo de transações das bases de dados SQL para permitir restaurar a qualquer momento. Para bases de dados individuais e bases de dados agréns, pode configurar a Base de Dados SQL para armazenar cópias de dados completas no Armazenamento Azure para retenção de cópias de segurança a longo prazo. Para casos geridos, também pode efetuar cópias de cópia apenas para retenção de backup a longo prazo.

- [Restauros ponto-a-tempo:](sql-database-recovery-using-backups.md)

  Todas as opções de implementação da Base de Dados SQL suportam a recuperação a qualquer momento dentro do período de retenção automática de cópias de segurança para qualquer base de dados SQL.
- [Geo-replicação ativa:](sql-database-active-geo-replication.md)

  A base de dados única e as opções de bases de dados reunidas permitem configurar até quatro bases de dados secundárias legíveis nos mesmos centros de dados azure distribuídos globalmente. Por exemplo, se tiver uma aplicação SaaS com uma base de dados de catálogo que tenha um elevado volume de transações apenas de leitura simultâneas, utilize geo-replicação ativa para permitir a escala global de leitura. Isto remove estrangulamentos nas primárias que devem ler cargas de trabalho. Para casos geridos, utilize grupos de auto-falha.
- [Grupos de falha automática:](sql-database-auto-failover-group.md)

  Todas as opções de implementação da Base de Dados SQL permitem-lhe utilizar grupos failover para permitir uma elevada disponibilidade e equilíbrio de carga à escala global. Isto inclui geo-replicação transparente e failover de grandes conjuntos de bases de dados, piscinas elásticas e instâncias geridas. Os grupos failover permitem a criação de aplicações SaaS distribuídas globalmente, com despesas mínimas de administração. Isto deixa toda a complexa monitorização, encaminhamento e falha na orquestração para a Base de Dados SQL.
- [Bases de dados redundantes](sql-database-high-availability.md)em zonas:

  A Base de Dados SQL permite-lhe fornecer bases de dados críticas premium ou empresariais ou piscinas elásticas em várias zonas de disponibilidade. Como estas bases de dados e piscinas elásticas têm múltiplas réplicas redundantes para alta disponibilidade, colocar estas réplicas em múltiplas zonas de disponibilidade proporciona maior resiliência. Isto inclui a capacidade de recuperar automaticamente das falhas de escala do datacenter, sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência incorporada

Com a Base de Dados SQL, obtém-se uma inteligência incorporada que o ajuda a reduzir drasticamente os custos de funcionamento e gestão de bases de dados, e isso maximiza tanto o desempenho como a segurança da sua aplicação. Executando milhões de cargas de trabalho de clientes 24 horas por dia, a SQL Database recolhe e processa uma quantidade massiva de dados de telemetria, respeitando também totalmente a privacidade do cliente. Vários algoritmos avaliam continuamente os dados da telemetria para que o serviço possa aprender e adaptar-se com a sua aplicação.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitorização e otimização de desempenho automático

A Base de Dados SQL disponibiliza informações detalhadas sobre as consultas que tem de monitorizar. A Base de Dados SQL aprende sobre os padrões da sua base de dados e permite-lhe adaptar o seu esquema de base de dados à sua carga de trabalho. A Base de Dados SQL disponibiliza [recomendações de otimização de desempenho](sql-database-advisor.md), onde pode ver as ações de otimização e aplicá-las.

No entanto, monitorizar constantemente uma base de dados é uma tarefa difícil e aborrecida, especialmente quando se trata de muitas bases de dados. [A Intelligent Insights](sql-database-intelligent-insights.md) faz este trabalho para si, monitorizando automaticamente o desempenho da Base de Dados SQL em escala. Informa-o sobre problemas de degradação do desempenho, identifica a causa principal de cada edição, e fornece recomendações de melhoria de desempenho sempre que possível.

Gerir um grande número de bases de dados pode ser impossível de fazer de forma eficiente, mesmo com todas as ferramentas e relatórios disponíveis que a Base de Dados SQL e o Azure fornecem. Em vez de monitorizar e afinar manualmente a sua base de dados, poderá considerar delegar algumas das ações de monitorização e afinação na Base de Dados SQL utilizando a [afinação automática](sql-database-automatic-tuning.md). A Base de Dados SQL aplica automaticamente recomendações, testes e verifica cada uma das suas ações de afinação para garantir que o desempenho continua a melhorar. Desta forma, a Base de Dados SQL adapta-se automaticamente à sua carga de trabalho de forma controlada e segura. A afinação automática significa que o desempenho da sua base de dados é cuidadosamente monitorizado e comparado antes e depois de cada ação de afinação. Se o desempenho não melhorar, a ação de afinação é revertida.

Muitos dos nossos parceiros que executam [aplicações multi-inquilinos SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) no topo da Base de Dados SQL estão contando com a sintonização automática de desempenho para garantir que as suas aplicações têm sempre um desempenho estável e previsível. Para estes parceiros, esta funcionalidade reduz significativamente o risco de ocorrência de incidentes de desempenho a meio da noite. Além disso, como parte da sua base de clientes também usa o SQL Server, estão a usar as mesmas recomendações de indexação fornecidas pela SQL Database para ajudar os seus clientes DoQL Server.

Dois aspetos de afinação automática estão disponíveis na Base de [Dados SQL:](sql-database-automatic-tuning.md)

- **Gestão de índices automática**: identifica os índices que devem ser adicionados à sua base de dados e os que devem ser removidos.
- **Correção automática do plano**: Identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consultas adaptável

Pode utilizar o processamento de [consultas adaptáveis,](/sql/relational-databases/performance/intelligent-query-processing)incluindo a execução intercalada para funções de tabela multi-statement, feedback de concessão de memória do modo de lote e uniões adaptáveis do modo de lote. Cada uma destas funcionalidades de processamento de consultas adaptáveis aplica técnicas semelhantes de "aprender e adaptar", ajudando a resolver ainda mais problemas de desempenho relacionados com problemas de otimização de consultas historicamente intrigantes.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Base de Dados SQL proporciona um conjunto de [funcionalidades de segurança e conformidade incorporadas](sql-database-security-overview.md), para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Microsoft certificou a Base de Dados Azure SQL (todas as opções de implementação) contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)onde poderá encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

### <a name="advance-threat-protection"></a>Proteção contra ameaças antecipadas

A segurança avançada de dados é um pacote unificado para capacidades avançadas de segurança SQL. Inclui funcionalidade para descobrir e classificar dados sensíveis, gerir as vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Fornece uma única localização para permitir e gerir estas capacidades.

- [Descoberta e classificação de dados:](sql-database-data-discovery-and-classification.md)

  Esta funcionalidade fornece capacidades incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. Fornece visibilidade no estado de classificação da sua base de dados e rastreia o acesso a dados sensíveis dentro da base de dados e além das suas fronteiras.
- [Avaliação da vulnerabilidade:](sql-vulnerability-assessment.md)

  Este serviço pode descobrir, rastrear e ajudá-lo a remediar potenciais vulnerabilidades de bases de dados. Oferece visibilidade sobre o estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- [Deteção de ameaças:](sql-database-threat-detection.md)

  Esta funcionalidade deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar a sua base de dados. Monitoriza continuamente a sua base de dados quanto a atividades suspeitas e apresenta alertas de segurança imediata em potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso de base de dados anómalos. Os alertas de deteção de ameaças fornecem detalhes da atividade suspeita, e recomendam medidas sobre como investigar e mitigar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria para conformidade e segurança

[A auditoria](sql-database-auditing.md) rastreia os eventos da base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure. A auditoria pode ajudá-lo a manter o cumprimento regulamentar, compreender a atividade da base de dados e obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Encriptação de dados

A Base de Dados SQL ajuda a proteger os seus dados fornecendo encriptação. Para dados em movimento, utiliza segurança da camada de [transporte](https://support.microsoft.com/kb/3135244). Para dados em repouso, utiliza [encriptação de dados transparente.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) Para dados utilizados, [utiliza-se sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A Base de Dados SQL permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Ative Directory suporta a [autenticação de vários fatores](sql-database-ssms-mfa-authentication.md) para aumentar a segurança dos dados e da aplicação, ao mesmo tempo que suporta um único processo de entrada.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de utilizar

Com a Base de Dados SQL, criar e manter aplicações é mais fácil e produtivo. A Base de Dados SQL permite-lhe dedicar-se ao que sabe fazer melhor: criar aplicações excelentes. Pode gerir e desenvolver na Base de Dados SQL utilizando ferramentas e competências que já possui.

- [O portal Azure:](https://portal.azure.com/)

  Uma aplicação baseada na web para gerir todos os serviços Azure.
- [Estúdio de Gestão de Servidores SQL:](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

  Uma aplicação de cliente gratuita e transferível para gerir qualquer infraestrutura SQL, desde o SQL Server até à Base de Dados SQL.
- Ferramentas de dados do [servidor SQL em estúdio visual:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

  Uma aplicação gratuita e transferível para o desenvolvimento de bases de dados relacionais do SQL Server, bases de dados SQL, pacotes de Serviços de Integração, modelos de dados de Serviços de Análise e relatórios de Serviços de Informação.
- [Código de estúdio visual:](https://code.visualstudio.com/docs)

  Um editor de código gratuito, descarregado e de código aberto para Windows, macOS e Linux. Suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta do Microsoft SQL Server, Azure SQL Database e Azure SQL Data Warehouse.

A Base de Dados SQL suporta aplicações de construção com Python, Java, Node.js, PHP, Ruby e .NET em macOS, Linux e Windows. A Base de Dados SQL suporta as mesmas [bibliotecas de ligações](sql-database-libraries.md) que o SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Base de Dados SQL frequentemente fez perguntas

### <a name="what-is-the-current-version-of-sql-database"></a>Qual é a versão atual da Base de Dados SQL?

A versão atual da Base de Dados SQL é V12. A versão V11 foi reformada.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando orindo tempo de inatividade?

Não. O impacto do patching geralmente não é percetível se você empregar lógica de [retry](sql-database-develop-overview.md#resiliency) na sua app. Para mais informações, consulte planeamento para eventos de manutenção Azure na Base de [Dados Azure SQL](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Perguntas de Benefício Híbrido Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existem direitos de dupla utilização com o Azure Hybrid Benefit para o Servidor SQL?

Tem 180 dias de direitos de dupla utilização da licença para garantir que as migrações estão a funcionar sem problemas. Após esse período de 180 dias, só pode utilizar a licença SQL Server na nuvem na Base de Dados SQL. Já não tens direitos de uso duplo no local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como é que o Azure Hybrid Benefit para o SQL Server difere da mobilidade da licença?

Oferecemos benefícios de mobilidade de licença para clientes SQL Server com Garantia de Software. Isto permite a reatribuição das suas licenças para os servidores partilhados de um parceiro. Pode utilizar este benefício no Azure IaaS e no AWS EC2.

O Benefício Híbrido Azure para o Servidor SQL difere da mobilidade da licença em duas áreas-chave:

- Proporciona benefícios económicos para a deslocação de cargas de trabalho altamente virtualizadas para o Azure. Os clientes da SQL Server Enterprise Edition podem obter quatro núcleos em Azure no General Purpose SKU para cada núcleo que possuam no local para aplicações altamente virtualizadas. A mobilidade de licenças não permite quaisquer benefícios especiais para o deslocamento de cargas de trabalho virtualizadas para a nuvem.
- Fornece um destino PaaS no Azure (SQL Database managed instance) que é altamente compatível com o SQL Server no local.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido Azure para o Servidor SQL?

Os clientes da Base de Dados SQL têm os seguintes direitos associados ao Benefício Híbrido Azure para o Servidor SQL:

|Pegada de licença|O que é que o Azure Hybrid Benefit para o SQL Server te dá?|
|---|---|
|SQL Server Enterprise Edition clientes principais com SA|<li>Pode pagar a taxa base em propósito geral ou SKU crítico de negócios</li><br><li>1 núcleo no local = 4 núcleos em Fins Gerais SKU</li><br><li>1 núcleo no local = 1 núcleo em Business Critical SKU</li>|
|Clientes centrais da SQL Server Standard Edition com SA|<li>Pode pagar a taxa base apenas em Fins Gerais SKU</li><br><li>1 núcleo no local = 1 núcleo em Fins Gerais SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver a equipa de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Pergunte à administração da base de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Faça perguntas sobre o desenvolvimento.
- [Fóruns MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Faça perguntas técnicas.
- [Feedback](https://aka.ms/sqlfeedback): Reportar bugs e solicitar recurso.
- [Reddit](https://www.reddit.com/r/SQLServer/): Discuta o servidor SQL.

## <a name="next-steps"></a>Passos seguintes

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para comparações de custos e calculadoras relativas a bases de dados individuais e piscinas elásticas.
- Veja estes quickstarts para começar:

  - [Criar uma base de dados SQL no portal do Azure](sql-database-single-database-get-started.md)  
  - [Criar uma base de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar uma base de dados SQL utilizando o PowerShell](sql-database-get-started-powershell.md)

- Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
  - [Exemplos da CLI do Azure para a Base de Dados SQL (Azure CLI samples for SQL Database)](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para a Base de Dados SQL (Azure PowerShell samples for SQL Database)](sql-database-powershell-samples.md)

- Para obter informações sobre novas capacidades à medida que são anunciadas, consulte [o Azure Roadmap para a Base de Dados SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog Azure SQL Database,](https://azure.microsoft.com/blog/topics/database)onde os membros da equipa de produtos SQL Server blog sobre notícias e funcionalidades da Base de Dados SQL.

