---
title: O que é o serviço Base de Dados SQL do Azure?
description: 'Obtenha uma introdução à Base de Dados SQL: detalhes técnicos e capacidades do sistema de gestão de bases de dados relacionais da Microsoft (RDBMS) na nuvem.'
keywords: introdução à sql, introdução sql, o que é a base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 0bd6300f4b9dbcf76b5447a0fb58502b7aebf311
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451417"
---
# <a name="what-is-azure-sql-database"></a>O que é a Base de Dados SQL do Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Base de Dados SQL do Azure é um motor de base de dados totalmente gerido com uma plataforma como serviço (PaaS) que lida com a maioria das funções de gestão da base de dados, como a atualização, aplicação de patches, cópias de segurança e monitorização sem a participação do utilizador. A Base de Dados SQL do Azure é sempre executada na versão estável mais recente do motor de base de dados do SQL Server e no SO corrigido com 99,99% de disponibilidade. As funcionalidades de PaaS incorporadas na Base de Dados SQL do Azure permitem-lhe que se concentre nas atividades de administração e otimização da base de dados específica do domínio que são essenciais para a sua empresa.

Com a Base de Dados SQL do Azure, pode criar uma camada de armazenamento altamente disponível e de elevado desempenho para as aplicações e soluções no Azure. A BASE de Dados SQL pode ser a escolha certa para uma variedade de aplicações em nuvem modernas porque permite processar dados relacionais e [estruturas não relacionais](../multi-model-features.md), tais como gráficos, JSON, espacial e XML.

O Azure SQL Database baseia-se na versão mais recente estável do motor de base de dados do [Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). Pode utilizar funcionalidades de processamento de consultas avançadas, tais como [tecnologias de alto desempenho na memória](../in-memory-oltp-overview.md) e processamento inteligente de [consultas.](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json) Na verdade, as funcionalidades mais recentes do SQL Server são lançadas primeiro para a Base de Dados SQL e depois para o próprio SQL Server. Obtém as funcionalidades mais recentes do SQL Server, sem custos gerais para correções ou atualizações, que foram testadas em milhões de bases de dados. 

A SQL Database permite-lhe definir e escalar facilmente o desempenho dentro de dois modelos de compra diferentes: um [modelo de compra baseado em vCore](service-tiers-vcore.md) e um modelo de compra baseado em [DTU.](service-tiers-dtu.md) A Base de Dados SQL é um serviço totalmente gerido que tem uma elevada disponibilidade incorporada, cópias de segurança e outras operações de manutenção comuns. A Microsoft lida com todos os patchings e atualizações do SQL e do código do sistema operativo. Não tem de gerir a infraestrutura subjacente.

Se é novo na Base de Dados Azure SQL, consulte o vídeo da *Base de Dados Azure SQL* da nossa série de [vídeos Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>Modelos de implementação

A Azure SQL Database fornece as seguintes opções de implantação para uma base de dados:

- [Uma base de dados única](single-database-overview.md) representa uma base de dados totalmente gerida e isolada. Poderá utilizar esta opção se tiver aplicações em nuvem modernas e microserviços que necessitem de uma única fonte de dados fiável. Uma única base de dados é semelhante a uma [base de dados contida](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) no motor de base de [dados SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- [A piscina elástica](elastic-pool-overview.md) é uma coleção de bases de [dados individuais](single-database-overview.md) com um conjunto partilhado de recursos, como CPU ou memória. Bases de dados individuais podem ser transferidas para dentro e para fora de uma piscina elástica.

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidades entre a Base de Dados SQL e o SQL Server, bem como as diferenças entre as diferentes opções de Base de Dados Azure SQL, consulte [as funcionalidades da Base de Dados SQL](features-comparison.md).

A SQL Database oferece um desempenho previsível com vários tipos de recursos, níveis de serviço e tamanhos de cálculo. Proporciona uma escalabilidade dinâmica sem tempo de inatividade, otimização inteligente incorporada, escalabilidade e disponibilidade globais, e opções avançadas de segurança. Estas capacidades permitem-lhe focar-se no rápido desenvolvimento de apps e acelerar o seu tempo para o mercado, em vez de gerir máquinas virtuais e infraestruturas. A BASE de Dados SQL está atualmente em 38 datacenters em todo o mundo, para que possa executar a sua base de dados num datacenter perto de si.

## <a name="scalable-performance-and-pools"></a>Dimensionar o desempenho e os conjuntos

Pode definir a quantidade de recursos atribuídos. 
- Com bases de dados individuais, cada base de dados é isolada de outras e é portátil. Cada um tem a sua própria quantidade garantida de recursos de computação, memória e armazenamento. A quantidade de recursos atribuídos à base de dados é dedicada a essa base de dados, e não é partilhada com outras bases de dados em Azure. Pode escalar dinamicamente [os recursos de uma única base de dados](single-database-scale.md) para cima e para baixo. A opção de base de dados única fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, pode obter 1 a 80 vCores, ou 32 GB a 4 TB. O [nível de serviço de hiperescala](service-tier-hyperscale.md) para bases de dados individuais permite escalar até 100 TB, com capacidades rápidas de backup e restauro.
- Com piscinas elásticas, você pode atribuir recursos que são partilhados por todas as bases de dados na piscina. Pode criar uma nova base de dados ou mover as bases de dados únicas existentes para um conjunto de recursos para maximizar o uso de recursos e economizar dinheiro. Esta opção também lhe dá a capacidade de [escalar dinamicamente os recursos elásticos da piscina](elastic-pool-scale.md) para cima e para baixo.

Pode construir a sua primeira aplicação numa base de dados pequena e única a um custo baixo por mês no nível de serviço para fins gerais. Em seguida, pode alterar o seu nível de serviço manualmente ou programáticamente a qualquer momento para o nível de serviço crítico de negócios, para atender às necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Só pagas os recursos de que precisas quando precisas deles.

*A escalabilidade dinâmica* é diferente da *escala automática.* O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual sem períodos de indisponibilidade. A opção de base de dados única suporta a escalabilidade dinâmica manual, mas não a escala automática. Para uma experiência mais automática, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados. Outra opção é usar scripts que possam ajudar a automatizar a escalabilidade para uma única base de dados. Por exemplo, consulte [Use PowerShell para monitorizar e escalar uma única base de dados](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

A SQL Database oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore permite-lhe](service-tiers-vcore.md) escolher o número de vCores, a quantidade de memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use [Azure Hybrid Benefit para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter economia de custos. Para obter mais informações sobre o Benefício Híbrido Azure, consulte a secção "Perguntas Frequentes" mais tarde neste artigo.
- O [modelo de compra baseado em DTU](service-tiers-dtu.md) oferece uma mistura de recursos de computação, memória e E/S em três níveis de serviço, para suportar cargas de dados leves a pesadas bases de dados. Os tamanhos de cálculo dentro de cada nível fornecem uma mistura diferente destes recursos, aos quais pode adicionar recursos de armazenamento adicionais.
- O [modelo sem servidor](serverless-tier-overview.md) escala automaticamente o cálculo com base na procura de carga de trabalho e as contas da quantidade de cálculo utilizada por segundo. O nível de computação sem servidor também interrompe automaticamente as bases de dados durante períodos inativos quando apenas o armazenamento é faturado e retoma automaticamente as bases de dados quando a atividade retorna.

### <a name="service-tiers"></a>Escalões de serviço

A Azure SQL Database oferece três níveis de serviço projetados para diferentes tipos de aplicações:
- [Nível de serviço Geral Finalidade/Padrão](service-tier-general-purpose.md) concebido para cargas de trabalho comuns. Oferece opções de computação e armazenamento equilibradas orientadas para o orçamento.
- Nível de serviço [Critical/Premium da Empresa](service-tier-business-critical.md) projetado para aplicações OLTP com alta taxa de transação e menor I/O de latência. Oferece a maior resiliência às falhas utilizando várias réplicas isoladas.
- Nível de serviço [de hiperescala](service-tier-hyperscale.md) projetado para uma base de dados OLTP muito grande e a capacidade de autoescalar o armazenamento e a escala de cálculo fluidamente.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. Padrões de utilização imprevisíveis podem dificultar a gestão dos custos e do seu modelo de negócio. Os [conjuntos elásticos](elastic-pool-overview.md) foram concebidos para resolver este problema. Aloque recursos de desempenho a uma piscina em vez de uma base de dados individual. Você paga pelos recursos de desempenho coletivo da piscina em vez de pelo desempenho de uma única base de dados.

   ![Gráfico que mostra piscinas elásticas em edições básicas, padrão e premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Com piscinas elásticas, não precisa de se concentrar em marcar o desempenho da base de dados para cima e para baixo à medida que a procura de recursos flutua. As bases de dados de conjunto consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados agram mas não excedem os limites da piscina, pelo que o seu custo permanece previsível mesmo que o uso individual da base de dados não o faça.

Pode [adicionar e remover bases de dados para a piscina,](elastic-pool-overview.md)escalando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento que controla. Você também pode controlar os recursos mínimos e máximos disponíveis para bases de dados na piscina, para garantir que nenhuma base de dados na piscina usa todos os recursos do pool, e que cada base de dados agrida tem uma quantidade mínima garantida de recursos. Para saber mais sobre padrões de design para software como aplicações de serviço (SaaS) que usam piscinas elásticas, consulte [padrões de design para aplicações SaaS multi-inquilinos com Base de Dados SQL](saas-tenancy-app-design-patterns.md).

Os scripts podem ajudar na monitorização e no dimensionamento dos conjuntos elásticos. Por exemplo, consulte [Use PowerShell para monitorizar e escalar uma piscina elástica na Base de Dados Azure SQL](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados de conjunto

Pode misturar bases de dados individuais com piscinas elásticas e alterar os níveis de serviço de bases de dados individuais e piscinas elásticas para se adaptar à sua situação. Também pode misturar e combinar outros serviços Azure com a SQL Database para atender às suas necessidades únicas de design de aplicações, impulsionar custos e eficiências de recursos e desbloquear novas oportunidades de negócio.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Capacidades alargadas de monitorização e alertas

A Azure SQL Database fornece funcionalidades avançadas de monitorização e resolução de problemas que o ajudam a obter informações mais profundas sobre as características da carga de trabalho. Estas funcionalidades e ferramentas incluem:
 - As capacidades de monitorização incorporadas fornecidas pela versão mais recente do motor de base de dados SQL Server. Permitem-lhe encontrar insights de desempenho em tempo real. 
 - Capacidades de monitorização paaS fornecidas pelo Azure que lhe permitem monitorizar e resolver problemas num grande número de casos de base de dados.

[A Query Store](/sql/relational-databases/performance/best-practice-with-the-query-store), uma funcionalidade de monitorização do SQL Server incorporada, regista o desempenho das suas consultas em tempo real e permite identificar os potenciais problemas de desempenho e os consumidores de recursos de topo. A sintonização automática e recomendações fornecem aconselhamento sobre as consultas com o desempenho regressado e índices em falta ou duplicados. A sintonização automática na Base de Dados SQL permite-lhe aplicar manualmente os scripts que podem corrigir os problemas, ou deixar a SQL Database aplicar a correção. A BASE de Dados SQL também pode testar e verificar se a correção proporciona algum benefício, e reter ou reverter a alteração dependendo do resultado. Além da Loja de Consultas e das capacidades de afinação automática, pode utilizar [DMVs standard e XEvent](monitoring-with-dmvs.md) para monitorizar o desempenho da carga de trabalho.

O Azure fornece ferramentas de monitorização e [alerta](alerts-insights-configure-portal.md) [de desempenho incorporadas,](performance-guidance.md) combinadas com classificações de desempenho, que permitem monitorizar o estado de milhares de bases de dados. Utilizando estas ferramentas, pode avaliar rapidamente o impacto da escala para cima ou para baixo, com base nas suas necessidades de desempenho atuais ou projetadas. Além disso, a BASE de Dados SQL pode [emitir métricas e registos de recursos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para uma monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

- **Armazenamento Azure**: Para arquivar grandes quantidades de telemetria por um pequeno preço.
- **Azure Event Hubs**: Para integrar a telemetria SQL Database com a sua solução de monitorização personalizada ou gasodutos quentes.
- **Registos do Monitor Azure**: Para uma solução de monitorização incorporada com capacidades de reporte, alerta e mitigação.

![Diagrama da arquitetura de monitorização de Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

A Azure SQL Database permite que o seu negócio continue a operar durante as interrupções. Num ambiente tradicional do SQL Server, geralmente tem pelo menos duas máquinas configurados localmente. Estas máquinas têm cópias exatas e sincronizadas dos dados para proteger contra uma falha de uma única máquina ou componente. Este ambiente proporciona alta disponibilidade, mas não protege contra um desastre natural que destrói o seu datacenter.

A recuperação de desastres pressupõe que um evento catastrófico esteja geograficamente localizado o suficiente para ter outra máquina ou conjunto de máquinas com uma cópia dos seus dados longe. No SQL Server, pode utilizar grupos sempre em disponibilidade em funcionamento no modo async para obter esta capacidade. Muitas vezes, as pessoas não querem esperar que a replicação aconteça tão longe antes de cometer uma transação, por isso há potencial para a perda de dados quando se faz falhas não planeadas.

As bases de dados nos níveis de serviço Premium e Business Critical já [fazem algo semelhante](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) à sincronização de um grupo de disponibilidade. As bases de dados em níveis de serviço mais baixos proporcionam redundância através do armazenamento utilizando um [mecanismo diferente, mas equivalente.](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) A lógica incorporada ajuda a proteger contra uma única falha na máquina. A funcionalidade de geo-replicação ativa dá-lhe a capacidade de se proteger contra desastres onde toda uma região é destruída.

As Zonas de Disponibilidade Azure tentam proteger-se contra a interrupção de um único edifício de datacenter dentro de uma única região. Ajuda-o a proteger-se contra a perda de energia ou rede para um edifício. Na Base de Dados SQL, coloca as diferentes réplicas em diferentes zonas de disponibilidade (diferentes edifícios, efetivamente).

De facto, o acordo de nível de serviço [(SLA)](https://azure.microsoft.com/support/legal/sla/) da Azure, alimentado por uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação a funcionar 24 horas por dia. A plataforma Azure gere totalmente todas as bases de dados, e garante nenhuma perda de dados e uma elevada percentagem de disponibilidade de dados. O Azure lida automaticamente com patching, backups, replicação, deteção de falhas, potenciais falhas de hardware, software ou rede subjacentes, implementando correções de bugs, falhas, atualizações de bases de dados e outras tarefas de manutenção. A disponibilidade standard é conseguida por uma separação das camadas de armazenamento e computação. A disponibilidade premium é conseguida através da integração do computação e armazenamento num único nó para o desempenho e, em seguida, implementando tecnologia semelhante a Always On Availability Groups. Para uma discussão completa sobre as elevadas capacidades de disponibilidade da Base de Dados Azure SQL, consulte [a disponibilidade da Base de Dados SQL](high-availability-sla.md). 

Além disso, a SQL Database fornece funcionalidades de continuidade de negócios incorporadas e de [escalabilidade global.](business-continuity-high-availability-disaster-recover-hadr-overview.md) Estas incluem:

- [Backups automáticos:](automated-backups-overview.md)

  A SQL Database realiza automaticamente cópias de segurança completas, diferenciais e de registo de transações de bases de dados para permitir restaurar a qualquer ponto no tempo. Para bases de dados individuais e bases de dados agrárias, pode configurar a Base de Dados SQL para armazenar cópias de dados completas no Azure Storage para retenção de backup a longo prazo. Para casos geridos, também pode efetuar cópias de segurança apenas para retenção de backup a longo prazo.

- [Restauros pontuais:](recovery-using-backups.md)

  Todas as opções de implementação da base de dados SQL suportam a recuperação a qualquer ponto no tempo dentro do período automático de retenção de backup para qualquer base de dados.
- [Geo-replicação ativa:](active-geo-replication-overview.md)

  As opções de bases de dados únicas e de bases de dados agrárias permitem configurar até quatro bases de dados secundárias legíveis nos mesmos centros de dados Azure distribuídos globalmente. Por exemplo, se tiver uma aplicação SaaS com uma base de dados de catálogo que tenha um grande volume de transações de leitura simultânea, utilize a geo-replicação ativa para permitir a escala de leitura global. Isto remove estrangulamentos nas primárias que são devidos à leitura de cargas de trabalho. Para casos geridos, utilize grupos de falha automática.
- [Grupos de falha automática:](auto-failover-group-overview.md)

  Todas as opções de implementação da Base de Dados SQL permitem-lhe utilizar grupos de failover para permitir uma elevada disponibilidade e equilíbrio de carga à escala global. Isto inclui a geo-replicação transparente e o fracasso de grandes conjuntos de bases de dados, piscinas elásticas e instâncias geridas. Os grupos de failover permitem a criação de aplicações SaaS distribuídas globalmente, com despesas mínimas de administração. Isto deixa toda a complexa monitorização, encaminhamento e orquestração falhada para a Base de Dados SQL.
- [Bases de dados redundantes de zonas:](high-availability-sla.md)

  A BASE de Dados SQL permite-lhe aloquir bases de dados Premium ou Business Critical ou piscinas elásticas em várias zonas de disponibilidade. Como estas bases de dados e piscinas elásticas têm múltiplas réplicas redundantes para alta disponibilidade, colocar estas réplicas em várias zonas de disponibilidade proporciona maior resiliência. Isto inclui a capacidade de recuperar automaticamente das falhas da escala do datacenter, sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência incorporada

Com a BASE de Dados SQL, obtém informações incorporadas que o ajudam a reduzir drasticamente os custos de funcionamento e gestão de bases de dados, e que maximiza tanto o desempenho como a segurança da sua aplicação. Executando milhões de cargas de trabalho de clientes 24 horas por dia, a SQL Database recolhe e processa uma quantidade massiva de dados de telemetria, respeitando também a privacidade do cliente. Vários algoritmos avaliam continuamente os dados da telemetria para que o serviço possa aprender e adaptar-se com a sua aplicação.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitorização e otimização de desempenho automático

A Base de Dados SQL disponibiliza informações detalhadas sobre as consultas que tem de monitorizar. A SQL Database aprende sobre os seus padrões de base de dados e permite-lhe adaptar o seu esquema de base de dados à sua carga de trabalho. A Base de Dados SQL disponibiliza [recomendações de otimização de desempenho](database-advisor-implement-performance-recommendations.md), onde pode ver as ações de otimização e aplicá-las.

No entanto, monitorizar constantemente uma base de dados é uma tarefa difícil e aborrecida, especialmente quando se lida com muitas bases de dados. [A Intelligent Insights](intelligent-insights-overview.md) faz este trabalho para si, monitorizando automaticamente o desempenho da Base de Dados SQL à escala. Informa-o sobre problemas de degradação de desempenho, identifica a causa principal de cada problema, e fornece recomendações de melhoria de desempenho sempre que possível.

Gerir um grande número de bases de dados pode ser impossível de fazer de forma eficiente, mesmo com todas as ferramentas e relatórios disponíveis que a SQL Database e a Azure fornecem. Em vez de monitorizar e afinar manualmente a sua base de dados, poderá considerar delegar algumas das ações de monitorização e afinação à Base de Dados SQL utilizando [a sintonização automática](automatic-tuning-overview.md). A SQL Database aplica automaticamente recomendações, testes e verifica cada uma das suas ações de afinação para garantir que o desempenho continua a melhorar. Desta forma, a SQL Database adapta-se automaticamente à sua carga de trabalho de forma controlada e segura. A sintonização automática significa que o desempenho da sua base de dados é cuidadosamente monitorizado e comparado antes e depois de cada ação de afinação. Se o desempenho não melhorar, a ação de afinação é revertida.

Muitos dos nossos parceiros que gerem [aplicações multi-arrendatários SaaS](saas-tenancy-app-design-patterns.md) no topo da SQL Database estão a contar com a afinação automática do desempenho para garantir que as suas aplicações têm sempre um desempenho estável e previsível. Para estes parceiros, esta funcionalidade reduz significativamente o risco de ocorrência de incidentes de desempenho a meio da noite. Além disso, como parte da sua base de clientes também utiliza o SQL Server, estão a usar as mesmas recomendações de indexação fornecidas pela SQL Database para ajudar os seus clientes do SQL Server.

Dois aspetos de afinação automática estão [disponíveis na Base de Dados SQL:](automatic-tuning-overview.md)

- **Gestão de índices automática**: identifica os índices que devem ser adicionados à sua base de dados e os que devem ser removidos.
- **Correção automática do plano**: Identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consultas adaptável

Pode utilizar [o processamento de consultas adaptativas](/sql/relational-databases/performance/intelligent-query-processing), incluindo a execução intercalada para funções multi-declarações valorizadas pela tabela, feedback de concessão de memória de modo de lote e uniões adaptativas do modo de lote. Cada uma destas funcionalidades de processamento de consulta adaptativa aplica técnicas semelhantes de "aprender e adaptar", ajudando a resolver ainda mais problemas de desempenho relacionados com problemas de otimização de consultas historicamente intratáveis.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Base de Dados SQL proporciona um conjunto de [funcionalidades de segurança e conformidade incorporadas](../../active-directory/identity-protection/concept-identity-protection-security-overview.md), para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Microsoft certificou a Base de Dados Azure SQL (todas as opções de implementação) contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)onde pode encontrar a lista mais atual de certificações de conformidade da Base de Dados SQL.

### <a name="advance-threat-protection"></a>Proteção de ameaças antecipadas

O Azure Defender for SQL é um pacote unificado para capacidades avançadas de segurança SQL. Inclui funcionalidades para gerir as vulnerabilidades da sua base de dados e detetar atividades anómalas que possam indicar uma ameaça à sua base de dados. Fornece uma única localização para ativar e gerir estas capacidades.

- [Avaliação da vulnerabilidade:](sql-vulnerability-assessment.md)

  Este serviço pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. Fornece visibilidade sobre o seu estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as defesas da sua base de dados.
- [Deteção de ameaças:](threat-detection-configure.md)

  Esta funcionalidade deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar a sua base de dados. Monitoriza continuamente a sua base de dados em busca de atividades suspeitas e apresenta alertas de segurança imediatos em casos de potenciais vulnerabilidades, ataques de injeção SQL e padrões anómalos de acesso a bases de dados. Os alertas de deteção de ameaças proporcionam detalhes sobre a atividade suspeita e recomendam ações de investigação e mitigação da ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria para conformidade e segurança

[A auditoria](../../azure-sql/database/auditing-overview.md) rastreia os eventos da base de dados e escreve-os para um registo de auditoria na sua conta de armazenamento Azure. A auditoria pode ajudar a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que podem indicar preocupações comerciais ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Encriptação de dados

A Base de Dados SQL ajuda a proteger os dados ao proporcionar a encriptação. Para os dados em movimento, utiliza [a segurança da camada de transporte.](https://support.microsoft.com/kb/3135244) Para dados em repouso, utiliza [encriptação de dados transparentes.](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) Para os dados utilizados, utiliza [Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Deteção e classificação de dados

[A descoberta e classificação](data-discovery-and-classification-overview.md) de dados fornece capacidades incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. Fornece visibilidade no estado de classificação da sua base de dados e rastreia o acesso a dados sensíveis dentro da base de dados e além das suas fronteiras.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A Base de Dados SQL permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços Microsoft com a [integração do Azure Active Directory](authentication-aad-overview.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Ative Directory suporta a [autenticação de vários fatores](authentication-mfa-ssms-overview.md) para aumentar a segurança de dados e aplicações, ao mesmo tempo que suporta um único processo de inscrição.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de utilizar

Com a Base de Dados SQL, criar e manter aplicações é mais fácil e produtivo. A Base de Dados SQL permite-lhe dedicar-se ao que sabe fazer melhor: criar aplicações excelentes. Pode gerir e desenvolver na SQL Database utilizando ferramentas e competências que já tem.

|Ferramenta|Descrição|
|:---|:---|
|[O portal do Azure](https://portal.azure.com/)|Uma aplicação baseada na web para gerir todos os serviços Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Uma ferramenta de base de dados transversal que funciona no Windows, macOS e Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Uma aplicação gratuita e transferível para o cliente para gerir qualquer infraestrutura SQL, desde o SQL Server até à BASE de Dados SQL.|
|[Ferramentas de dados do servidor SQL no Estúdio Visual](/sql/ssdt/download-sql-server-data-tools-ssdt)|Uma aplicação gratuita e transferível para o cliente para o desenvolvimento de bases de dados relacionais do SQL Server, bases de dados na Base de Dados Azure SQL, pacotes de Serviços de Integração, modelos de dados de serviços de análise e relatórios de Serviços de Relatórios.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Um editor de código gratuito, transferível e aberto para Windows, macOS e Linux. Suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta do Microsoft SQL Server, Azure SQL Database e Azure Azure Synapse Analytics.|

A SQL Database suporta aplicações de construção com Python, Java, Node.js, PHP, Ruby e .NET em macOS, Linux e Windows. A Base de Dados SQL suporta as mesmas [bibliotecas de ligações](connect-query-content-reference-guide.md#libraries) que o SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SqL Database frequentemente fez perguntas

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando ocorre o tempo de inatividade?

Não. O impacto do patching geralmente não é percetível se [você empregar a lógica de repreensão](develop-overview.md#resiliency) na sua app. Para mais informações, consulte [Os eventos de manutenção do Plano para Azure na Base de Dados Azure SQL](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver a equipa de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Pergunte a questões de administração de bases de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Faça perguntas de desenvolvimento.
- [Microsoft Q&Uma página de perguntas](/answers/topics/azure-synapse-analytics.html): Faça perguntas técnicas.
- [Feedback](https://aka.ms/sqlfeedback): Reportar bugs e funcionalidades de pedido.
- [Reddit](https://www.reddit.com/r/SQLServer/): Discuta o SQL Server.

## <a name="next-steps"></a>Passos seguintes

- Consulte [a página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para comparações de custos e calculadoras relativas a bases de dados individuais e piscinas elásticas.
- Veja estes quickstarts para começar:

  - [Criar uma base de dados no portal do Azure](single-database-create-quickstart.md)  
  - [Criar uma base de dados com o Azure CLI](az-cli-script-samples-content-guide.md)
  - [Criar uma base de dados utilizando o PowerShell](powershell-script-content-guide.md)

- Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
  - [Exemplos da CLI do Azure para a Base de Dados SQL (Azure CLI samples for SQL Database)](az-cli-script-samples-content-guide.md)
  - [Exemplos do Azure PowerShell para a Base de Dados SQL (Azure PowerShell samples for SQL Database)](powershell-script-content-guide.md)

- Para obter informações sobre novas capacidades à medida que forem anunciadas, consulte [o Roteiro Azure para a Base de Dados SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database), onde os membros da equipa de produtos do SQL Server blog sobre notícias e funcionalidades da BASE de Dados SQL SQL.