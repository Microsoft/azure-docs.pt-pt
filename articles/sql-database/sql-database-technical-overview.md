---
title: O que é o serviço Base de Dados SQL do Azure?
description: 'Obtenha uma introdução ao banco de dados SQL: detalhes técnicos e recursos do RDBMS (sistema de gerenciamento de banco de dados relacional) da Microsoft na nuvem.'
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
ms.openlocfilehash: d22408904ae21bc7512d4ebe8792666227893337
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687021"
---
# <a name="what-is-the-azure-sql-database-service"></a>O que é o serviço Base de Dados SQL do Azure?

O banco de dados SQL do Azure é um banco de dados relacional de uso geral, fornecido como um serviço gerenciado. Com ele, você pode criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para os aplicativos e soluções no Azure. O banco de dados SQL pode ser a escolha certa para uma variedade de aplicativos de nuvem modernos, pois permite processar dados relacionais e [estruturas não relacionais](sql-database-multi-model-features.md), como grafos, JSON, espaciais e XML.

Ele se baseia na versão estável mais recente do [mecanismo de banco de dados Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Você pode usar recursos avançados de processamento de consulta, como [tecnologias de alto desempenho na memória](sql-database-in-memory.md) e [processamento de consulta inteligente](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Na verdade, os recursos mais recentes de SQL Server são liberados primeiro para o banco de dados SQL e, em seguida, para SQL Server si mesmo. Você Obtém os recursos de SQL Server mais recentes sem sobrecarga para aplicação de patch ou atualização, testados em milhões de bancos de dados. 

O banco de dados SQL permite que você defina e dimensione facilmente o desempenho em dois modelos de compra diferentes: um [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e um [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md). O banco de dados SQL é um serviço totalmente gerenciado que tem alta disponibilidade interna, backups e outras operações de manutenção comuns. A Microsoft trata de todas as correções e atualizações do código do sistema operacional e do SQL. Você não precisa gerenciar a infraestrutura subjacente.

> [!NOTE]
> Para termos relevantes e suas definições, consulte o [Glossário de termos do banco de dados SQL](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modelos de implementação

A Base de Dados SQL do Azure oferece as seguintes opções de implementação para bases de dados SQL do Azure:

![Diagrama de opções de implantação](./media/sql-database-technical-overview/deployment-options.png)

- Um [banco de dados individual](sql-database-single-database.md) representa um banco de dados isolado e totalmente gerenciado. Você pode usar essa opção se tiver aplicativos de nuvem modernos e microservices que precisam de uma única fonte de dados confiável. Um banco de dados individual é semelhante a um [banco de dados independente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) no [mecanismo de banco de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- A [instância gerenciada](sql-database-managed-instance.md) é uma instância totalmente gerenciada do [mecanismo de banco de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Ele contém um conjunto de bancos de dados que podem ser usados juntos. Use essa opção para a migração fácil de bancos de dados locais SQL Server para a nuvem do Azure e para aplicativos que precisam usar os recursos de banco de dados que SQL Server Mecanismo de Banco de Dados fornece.
- O [pool elástico](sql-database-elastic-pool.md) é uma coleção de [bancos de dados individuais](sql-database-single-database.md) com um conjunto compartilhado de recursos, como CPU ou memória. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico.

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o banco de dados SQL e SQL Server, bem como as diferenças entre as diferentes opções de implantação do banco de dados SQL do Azure, consulte [recursos do banco de dados SQL](sql-database-features.md).

O banco de dados SQL fornece desempenho previsível com vários tipos de recursos, camadas de serviço e tamanhos de computação. Ele fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente interna, escalabilidade e disponibilidade globais e opções de segurança avançadas. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e na aceleração do tempo de colocação no mercado, em vez de gerenciar máquinas virtuais e infraestrutura. No momento, o serviço de banco de dados SQL está em 38 data centers em todo o mundo, para que você possa executar seu banco de dados em um datacenter perto de você.

## <a name="scalable-performance-and-pools"></a>Dimensionar o desempenho e os conjuntos

Você pode definir a quantidade de recursos atribuídos. 
- Com bancos de dados individuais, cada um deles é isolado de outros e é portátil. Cada uma tem sua própria quantidade garantida de recursos de computação, memória e armazenamento. A quantidade de recursos atribuídos ao banco de dados é dedicada a esse banco de dados, e não é compartilhada com outros banco de dados no Azure. Você pode dimensionar dinamicamente os [recursos de banco de dados individual](sql-database-single-database-scale.md) para cima e para baixo. A opção de banco de dados individual fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, você pode obter de 1 a 80 vCores ou 32 GB a 4 TB. A [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md) para um único banco de dados permite que você dimensione para 100 TB, com recursos rápidos de backup e restauração.
- Com os pools elásticos, você pode atribuir recursos que são compartilhados por todos os bancos de dados no pool. Você pode criar um novo banco de dados ou movê-los para um pool de recursos para maximizar o uso de recursos e economizar dinheiro. Essa opção também oferece a capacidade de dimensionar dinamicamente os [recursos do pool elástico](sql-database-elastic-pool-scale.md) para cima e para baixo.
- Com instâncias gerenciadas, cada instância gerenciada é isolada de outras instâncias com recursos garantidos. Em uma instância gerenciada, os bancos de dados de instância compartilham um conjunto de recursos. Você pode dimensionar dinamicamente os [recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md) para cima e para baixo.

Você pode criar seu primeiro aplicativo em um banco de dados pequeno e único com um baixo custo por mês na camada de serviço de uso geral. Você pode alterar sua camada de serviço manualmente ou programaticamente a qualquer momento para a camada de serviço crítica para os negócios, para atender às necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente à mudança dos requisitos de recursos. Você paga apenas pelos recursos de que precisa quando precisa deles.

A *escalabilidade dinâmica* é diferente do *dimensionamento automático*. O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual sem períodos de indisponibilidade. A opção banco de dados único dá suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para obter uma experiência mais automática, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco. Outra opção é usar scripts que podem ajudar a automatizar a escalabilidade de um único banco de dados. Para obter um exemplo, consulte [usar o PowerShell para monitorar e dimensionar um único banco de dados](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

O banco de dados SQL oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade de memória e a quantidade e a velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter economias de custos. Para obter mais informações sobre o Benefício Híbrido do Azure, consulte a seção "perguntas frequentes" mais adiante neste artigo.
- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de computação, memória e recursos de e/s em três camadas de serviço, para dar suporte a cargas de trabalho leves de banco de dados. Os tamanhos de computação em cada camada fornecem uma combinação diferente desses recursos, à qual você pode adicionar recursos de armazenamento adicionais.
- O [modelo sem servidor](sql-database-serverless.md) dimensiona automaticamente a computação com base na demanda de carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

### <a name="service-tiers"></a>Escalões de serviço

O banco de dados SQL do Azure oferece três camadas de serviço que são projetadas para diferentes tipos de aplicativos:
- Camada de serviço [uso geral/Standard](sql-database-service-tier-general-purpose.md) projetada para cargas de trabalho comuns. Ele oferece opções de armazenamento e computação balanceadas e voltadas para o orçamento.
- Camada de serviço [comercialmente crítico/Premium](sql-database-service-tier-business-critical.md) projetada para aplicativos OLTP com alta taxa de transação e e/s de latência mais baixa. Ele oferece a resiliência mais alta para falhas usando várias réplicas isoladas.
- Camada de serviço de [hiperescala](sql-database-service-tier-hyperscale.md) projetada para banco de dados OLTP muito grande e a capacidade de dimensionar automaticamente o armazenamento e dimensionar a computação de forma fluida. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. Padrões de uso imprevisíveis podem dificultar o gerenciamento de custos e seu modelo de negócios. Os [conjuntos elásticos](sql-database-elastic-pool.md) foram concebidos para resolver este problema. Você aloca recursos de desempenho para um pool em vez de um banco de dados individual. Você paga pelos recursos de desempenho coletivo do pool em vez de para o desempenho de um banco de dados individual.

   ![Gráfico que mostra pools elásticos nas edições Basic, Standard e Premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com os conjuntos elásticos, não tem de se concentrar em ajustar o desempenho da base de dados à medida que a procura pelos recursos flutua. As bases de dados de conjunto consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados de conjunto consomem os limites do conjunto, mas não os excedem, pelo que os seus custos se mantêm previsíveis, mesmo que a utilização das bases de dados não.

Você pode [Adicionar e Remover bancos de dados para o pool](sql-database-elastic-pool-manage-portal.md), dimensionando seu aplicativo de alguns bancos de dados para milhares, tudo isso dentro de um orçamento que você controla. Você também pode controlar os recursos mínimo e máximo disponíveis para bancos de dados no pool, para garantir que nenhum banco de dados no pool use todos os recursos de pool e que cada banco de dados em pool tenha uma quantidade mínima garantida de recursos. Para saber mais sobre padrões de design para aplicativos SaaS (software como serviço) que usam pools elásticos, consulte [padrões de design para aplicativos SaaS multilocatários com o banco de dados SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Os scripts podem ajudar na monitorização e no dimensionamento dos conjuntos elásticos. Para obter um exemplo, consulte [usar o PowerShell para monitorar e dimensionar um pool elástico do SQL no banco de dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Uma instância gerenciada não dá suporte A pools elásticos. Em vez disso, uma instância gerenciada é uma coleção de bancos de dados de instância que compartilham recursos de instância gerenciada.

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados de conjunto

Você pode misturar bancos de dados individuais com pools elásticos e alterar as camadas de serviço de bancos de dados individuais e pools elásticos para se adaptar à sua situação. Você também pode misturar e combinar outros serviços do Azure com o banco de dados SQL para atender às suas necessidades exclusivas de design de aplicativo, impulsionar as eficiências de custo e recursos e desbloquear novas oportunidades de negócios.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Capacidades alargadas de monitorização e alertas

O banco de dados SQL do Azure fornece recursos avançados de monitoramento e solução de problemas que ajudam você a obter informações mais aprofundadas sobre as características da carga de trabalho. Esses recursos e ferramentas incluem:
 - Os recursos de monitoramento internos fornecidos pela versão mais recente do SQL Server Mecanismo de Banco de Dados. Eles permitem que você encontre informações de desempenho em tempo real. 
 - Recursos de monitoramento de PaaS fornecidos pelo Azure que permitem que você monitore e solucione problemas de um grande número de instâncias de banco de dados.

[Repositório de consultas](sql-database-operate-query-store.md), um recurso interno de monitoramento de SQL Server, registra o desempenho de suas consultas em tempo real e permite que você identifique os possíveis problemas de desempenho e os principais consumidores de recursos. O ajuste automático e as recomendações fornecem conselhos sobre as consultas com o desempenho regressivo e índices ausentes ou duplicados. O ajuste automático no banco de dados SQL permite que você aplique manualmente os scripts que podem corrigir os problemas ou deixe que o banco de dados SQL aplique a correção. O banco de dados SQL também pode testar e verificar se a correção fornece algum benefício e reter ou reverter a alteração dependendo do resultado. Além dos recursos de Repositório de Consultas e de ajuste automático, você pode usar [DMVs e XEvent](sql-database-monitoring-with-dmvs.md) padrão para monitorar o desempenho da carga de trabalho.

O Azure fornece ferramentas [internas de monitoramento](sql-database-performance.md) e [alerta](sql-database-insights-alerts-portal.md) de desempenho, combinadas com classificações de desempenho, que permitem monitorar o status de milhares de bancos de dados. Usando essas ferramentas, você pode avaliar rapidamente o impacto de escalar ou reduzir verticalmente, com base em suas necessidades de desempenho atuais ou projetadas. Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

- **Armazenamento do Azure**: para arquivar grandes quantidades de telemetria a um preço baixo.
- **Hubs de eventos do Azure**: para integrar a telemetria do banco de dados SQL com sua solução de monitoramento Personalizada ou pipelines quentes.
- **Logs de Azure monitor**: para uma solução de monitoramento interna com relatórios, alertas e recursos de mitigação.

![Diagrama da arquitetura de monitoramento do Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Em um ambiente de SQL Server tradicional, você geralmente tem pelo menos duas máquinas configuradas localmente. Essas máquinas possuem cópias exatas, em sincronia, mantidas dos dados para proteger contra uma falha de um único computador ou componente. Esse ambiente fornece alta disponibilidade, mas não protege contra um desastre natural destruindo seu datacenter.

A recuperação de desastres pressupõe que um evento catastrófico seja traduzido geograficamente para ter outro computador ou conjunto de computadores com uma cópia dos seus dados de longe. No SQL Server, você pode usar Always On grupos de disponibilidade em execução no modo assíncrono para obter esse recurso. Muitas vezes, as pessoas não querem esperar que a replicação ocorra antes de confirmar uma transação, portanto, há potencial para perda de dados quando você faz failovers não planejados.

Os bancos de dados nas camadas de serviço Premium e comercialmente crítico já [fazem algo muito semelhante](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) à sincronização de um grupo de disponibilidade. Os bancos de dados em camadas de serviço inferiores fornecem redundância por meio do armazenamento usando um [mecanismo diferente, mas equivalente](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). A lógica interna ajuda a proteger contra uma falha de máquina única. O recurso de replicação geográfica ativa oferece a capacidade de proteger contra desastres em que uma região inteira é destruída.

Zonas de Disponibilidade do Azure tenta se proteger contra a interrupção de um único datacenter criando dentro de uma única região. Ele ajuda você a se proteger contra a perda de energia ou rede para um prédio. No banco de dados SQL, você coloca as diferentes réplicas em diferentes zonas de disponibilidade (prédios diferentes, efetivamente).

Na verdade, o [SLA (](https://azure.microsoft.com/support/legal/sla/) contrato de nível de serviço) do Azure, equipado com uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução 24/7. A plataforma Azure gerencia totalmente todos os bancos de dados e garante que não haja perda e um alto percentual de disponibilidade de dados. O Azure lida automaticamente com patches, backups, replicação, detecção de falhas, hardware potencial subjacente, software ou falhas de rede, implantação de correções de bugs, failovers, atualizações de banco de dados e outras tarefas de manutenção. A disponibilidade standard é conseguida por uma separação das camadas de armazenamento e computação. A disponibilidade Premium é obtida com a integração da computação e do armazenamento em um único nó para o desempenho e, em seguida, a implementação de uma tecnologia semelhante à Always On grupos de disponibilidade. Para obter uma discussão completa sobre os recursos de alta disponibilidade do banco de dados SQL do Azure, consulte [disponibilidade do banco de dados SQL](sql-database-high-availability.md). 

Além disso, o banco de dados SQL fornece recursos internos de [continuidade de negócios e escalabilidade global](sql-database-business-continuity.md) . As regiões incluem:

- [Backups automáticos](sql-database-automated-backups.md):

  O banco de dados SQL executa automaticamente backups completos, diferenciais e de log de transações de bancos de dados SQL para permitir que você restaure para qualquer ponto no tempo. Para bancos de dados individuais e bancos de dados em pool, você pode configurar o banco de dados SQL para armazenar backups de banco de dados completos no armazenamento do Azure para retenção de backup de longo prazo. Para instâncias gerenciadas, você também pode executar backups somente cópia para retenção de backup de longo prazo.

- [Restaurações point-in-time](sql-database-recovery-using-backups.md):

  Todas as opções de implantação de banco de dados SQL dão suporte à recuperação para qualquer ponto no tempo dentro do período de retenção de backup automático de qualquer banco de dados SQL.
- [Replicação geográfica ativa](sql-database-active-geo-replication.md):

  As opções de banco de dados individual e bancos de dados em pool permitem que você configure até quatro bancos de dados secundários legíveis no mesmo ou em datacenters do Azure distribuídos globalmente. Por exemplo, se você tiver um aplicativo SaaS com um banco de dados de catálogo que tem um alto volume de transações simultâneas somente leitura, use a replicação geográfica ativa para habilitar a escala de leitura global. Isso remove afunilamentos no primário que são devido a cargas de trabalho de leitura. Para instâncias gerenciadas, use grupos de failover automático.
- [Grupos de failover automático](sql-database-auto-failover-group.md):

  Todas as opções de implantação do banco de dados SQL permitem que você use grupos de failover para habilitar a alta disponibilidade e o balanceamento de carga em escala global. Isso inclui a replicação geográfica transparente e o failover de grandes conjuntos de bancos de dados, pools elásticos e instâncias gerenciadas. Os grupos de failover permitem a criação de aplicativos SaaS distribuídos globalmente, com sobrecarga de administração mínima. Isso deixa toda a orquestração de monitoramento, roteamento e failover complexos para o banco de dados SQL.
- [Bancos de dados com redundância de zona](sql-database-high-availability.md):

  O banco de dados SQL permite a você provisionar bancos de dados Premium ou comercialmente críticos ou pools elásticos em várias zonas de disponibilidade. Como esses bancos de dados e pools elásticos têm várias réplicas redundantes para alta disponibilidade, colocar essas réplicas em várias zonas de disponibilidade fornece maior resiliência. Isso inclui a capacidade de recuperar automaticamente das falhas de escala do datacenter, sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência incorporada

Com o banco de dados SQL, você obtém inteligência interna que ajuda a reduzir drasticamente os custos de execução e gerenciamento de bancos de dados e que maximiza o desempenho e a segurança do seu aplicativo. Executando milhões de cargas de trabalho de clientes em todo o tempo, o banco de dados SQL coleta e processa uma grande quantidade de data de telemetria, ao mesmo tempo em que respeita totalmente a privacidade do cliente. Vários algoritmos avaliam continuamente os dados de telemetria para que o serviço possa aprender e se adaptar ao seu aplicativo.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitorização e otimização de desempenho automático

A Base de Dados SQL disponibiliza informações detalhadas sobre as consultas que tem de monitorizar. O banco de dados SQL aprende sobre seus padrões de banco de dados e permite que você adapte seu esquema de banco de dados à sua carga de trabalho. A Base de Dados SQL disponibiliza [recomendações de otimização de desempenho](sql-database-advisor.md), onde pode ver as ações de otimização e aplicá-las.

No entanto, o monitoramento constante de um banco de dados é uma tarefa difícil e entediante, especialmente ao lidar com muitos bancos de dados. [Intelligent insights](sql-database-intelligent-insights.md) faz esse trabalho para você monitorando automaticamente o desempenho do banco de dados SQL em escala. Ele informa sobre problemas de degradação de desempenho, identifica a causa raiz de cada problema e fornece recomendações de melhoria de desempenho quando possível.

O gerenciamento de um grande número de bancos de dados pode ser impossível de fazer com eficiência, mesmo com todas as ferramentas e relatórios disponíveis que o banco de dados SQL e o Azure fornecem. Em vez de monitorar e ajustar seu banco de dados manualmente, você pode considerar a delegação de algumas das ações de monitoramento e ajuste ao banco de dados SQL usando o [ajuste automático](sql-database-automatic-tuning.md). O banco de dados SQL aplica automaticamente recomendações, testes e verifica cada uma de suas ações de ajuste para garantir que o desempenho continue a melhorar. Dessa forma, o banco de dados SQL se adapta automaticamente à sua carga de trabalho de forma controlada e segura. O ajuste automático significa que o desempenho do banco de dados é monitorado cuidadosamente e comparado antes e depois de cada ação de ajuste. Se o desempenho não melhorar, a ação de ajuste será revertida.

Muitos de nossos parceiros que executam [aplicativos SaaS multilocatários](sql-database-design-patterns-multi-tenancy-saas-applications.md) sobre o banco de dados SQL estão contando com o ajuste de desempenho automático para garantir que seus aplicativos sempre tenham desempenho estável e previsível. Para estes parceiros, esta funcionalidade reduz significativamente o risco de ocorrência de incidentes de desempenho a meio da noite. Além disso, como parte de sua base de clientes também usa SQL Server, eles estão usando as mesmas recomendações de indexação fornecidas pelo banco de dados SQL para ajudar seus SQL Server clientes.

Dois aspectos de ajuste automático estão [disponíveis no banco de dados SQL](sql-database-automatic-tuning.md):

- **Gestão de índices automática**: identifica os índices que devem ser adicionados à sua base de dados e os que devem ser removidos.
- **Correção automática de plano**: identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consultas adaptável

Você pode usar o [processamento de consulta adaptável](/sql/relational-databases/performance/intelligent-query-processing), incluindo a execução intercalada para funções com valor de tabela de várias instruções, comentários de concessão de memória de modo de lote e junções adaptáveis de modo de lote. Todas estas funcionalidades de processamento de consultas adaptativo aplica técnicas de “aprendizagem e adaptação” semelhantes, o que ajuda a ir ainda mais longe na resolução de problemas de desempenho relacionados com questões de otimização de consultas historicamente complicadas.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Base de Dados SQL proporciona um conjunto de [funcionalidades de segurança e conformidade incorporadas](sql-database-security-overview.md), para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Microsoft certificou o banco de dados SQL do Azure (todas as opções de implantação) contra vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

### <a name="advance-threat-protection"></a>Proteção avançada contra ameaças

A segurança de dados avançada é um pacote unificado para recursos avançados de segurança do SQL. Ele inclui a funcionalidade de descoberta e classificação de dados confidenciais, o gerenciamento de vulnerabilidades de banco de dado e a detecção de atividades anormais que podem indicar uma ameaça ao seu banco de dados. Ele fornece um único local para habilitar e gerenciar esses recursos.

- [Descoberta e classificação de dados](sql-database-data-discovery-and-classification.md):

  Esse recurso fornece recursos incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos. Ele fornece visibilidade sobre o estado de classificação do banco de dados e controla o acesso a dados confidenciais no banco e além de suas bordas.
- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Esse serviço pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. Oferece visibilidade sobre o estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- [Detecção de ameaças](sql-database-threat-detection.md):

  Esse recurso detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitoriza continuamente a sua base de dados quanto a atividades suspeitas e apresenta alertas de segurança imediata em potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso de base de dados anómalos. Os alertas de detecção de ameaças fornecem detalhes da atividade suspeita e recomendam ações sobre como investigar e mitigar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria para conformidade e segurança

A [auditoria](sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, compreender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança.

### <a name="data-encryption"></a>Encriptação de dados

O banco de dados SQL ajuda a proteger os seus dados fornecendo criptografia. Para dados em movimento, ele usa a [segurança da camada de transporte](https://support.microsoft.com/kb/3135244). Para dados em repouso, ele usa a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Para dados em uso, ele usa o [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A Base de Dados SQL permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory dá suporte à [autenticação multifator](sql-database-ssms-mfa-authentication.md) para aumentar a segurança de dados e aplicativos, ao mesmo tempo em que dá suporte a um processo de logon único.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de utilizar

Com a Base de Dados SQL, criar e manter aplicações é mais fácil e produtivo. A Base de Dados SQL permite-lhe dedicar-se ao que sabe fazer melhor: criar aplicações excelentes. Você pode gerenciar e desenvolver no banco de dados SQL usando as ferramentas e as habilidades que você já tem.

- [O portal do Azure](https://portal.azure.com/):

  Um aplicativo baseado na Web para gerenciar todos os serviços do Azure.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Um aplicativo cliente gratuito e que pode ser baixado para gerenciar qualquer infraestrutura do SQL, de SQL Server para o banco de dados SQL.
- [SQL Server Data Tools no Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Um aplicativo cliente gratuito e que pode ser baixado para o desenvolvimento de SQL Server bancos de dados relacionais, bancos de dados SQL, pacotes de Integration Services, modelos de Analysis Services e relatórios de Reporting Services.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Um editor de código aberto gratuito, que pode ser baixado para Windows, macOS e Linux. Ele dá suporte a extensões, incluindo a [extensão MSSQL](https://aka.ms/mssql-marketplace) para consultar Microsoft SQL Server, banco de dados SQL do azure e SQL data warehouse do Azure.

O banco de dados SQL dá suporte à criação de aplicativos com Python, Java, Node. js, PHP, Ruby e .NET no macOS, Linux e Windows. A Base de Dados SQL suporta as mesmas [bibliotecas de ligações](sql-database-libraries.md) que o SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Perguntas frequentes sobre o banco de dados SQL

### <a name="what-is-the-current-version-of-sql-database"></a>Qual é a versão atual do banco de dados SQL?

A versão atual do banco de dados SQL é V12. A versão v11 foi desativada.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando ocorre a aplicação de um tempo de inatividade?

Não. O impacto da aplicação de patch geralmente não é perceptível se você [empregar lógica de repetição](sql-database-develop-overview.md#resiliency) em seu aplicativo. Para obter mais informações, consulte [planejando eventos de manutenção do Azure no banco de dados SQL do Azure](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Perguntas Benefício Híbrido do Azures

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com Benefício Híbrido do Azure para SQL Server?

Você tem 180 dias de direitos de uso duplos da licença para garantir que as migrações estejam funcionando sem problemas. Após esse período de 180 dias, você só poderá usar a licença SQL Server na nuvem no banco de dados SQL. Você não tem mais direitos de uso duplo local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças?

Oferecemos benefícios de mobilidade de licença para SQL Server clientes com Software Assurance. Isso permite a reatribuição de suas licenças para os servidores compartilhados de um parceiro. Você pode usar esse benefício no Azure IaaS e AWS EC2.

Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes do SQL Server Enterprise Edition podem obter quatro núcleos no Azure no SKU do Uso Geral para cada núcleo que eles possuem localmente para aplicativos altamente virtualizados. A mobilidade de licenças não permite nenhum benefício de custo especial para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece um destino de PaaS no Azure (instância gerenciada do banco de dados SQL) que é altamente compatível com SQL Server local.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido do Azure para SQL Server?

Os clientes do banco de dados SQL têm os seguintes direitos associados ao Benefício Híbrido do Azure para SQL Server:

|Quantidade de licenças|O que Benefício Híbrido do Azure para SQL Server você consegue?|
|---|---|
|Clientes do SQL Server Enterprise Edition core com SA|<li>Pode pagar a taxa base em Uso Geral ou Comercialmente Crítico SKU</li><br><li>1 núcleo local = 4 núcleos no Uso Geral SKU</li><br><li>1 núcleo local = 1 núcleo no Comercialmente Crítico SKU</li>|
|Clientes do SQL Server Standard Edition core com SA|<li>Pode pagar a taxa base somente em Uso Geral SKU</li><br><li>1 núcleo local = 1 núcleo no Uso Geral SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver a equipa de engenharia do SQL Server

- [Intercâmbio de pilha do DBA](https://dba.stackexchange.com/questions/tagged/sql-server): faça perguntas sobre administração de banco de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): faça perguntas de desenvolvimento.
- [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): faça perguntas técnicas.
- [Comentários](https://aka.ms/sqlfeedback): Relatar bugs e solicitar o recurso.
- [Reddit](https://www.reddit.com/r/SQLServer/): discutir SQL Server.

## <a name="next-steps"></a>Passos seguintes

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para obter comparações de custo e calculadoras referentes a bancos de dados individuais e pools elásticos.
- Consulte estes guias de início rápido para começar:

  - [Criar uma base de dados SQL no portal do Azure](sql-database-single-database-get-started.md)  
  - [Criar uma base de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar uma base de dados SQL utilizando o PowerShell](sql-database-get-started-powershell.md)

- Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
  - [Exemplos da CLI do Azure para a Base de Dados SQL (Azure CLI samples for SQL Database)](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para a Base de Dados SQL (Azure PowerShell samples for SQL Database)](sql-database-powershell-samples.md)

- Para obter informações sobre novos recursos conforme eles são anunciados, consulte o [roteiro do Azure para o banco de dados SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/topics/database), no qual SQL Server os membros da equipe do produto sobre notícias e recursos do banco de dados SQL.

