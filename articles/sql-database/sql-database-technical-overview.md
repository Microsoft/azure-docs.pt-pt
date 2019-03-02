---
title: O que é o serviço Base de Dados SQL do Azure? | Microsoft Docs
description: 'Obtenha uma introdução à Base de Dados SQL: detalhes técnicos e capacidades do sistema de gestão de bases de dados relacionais (RDBMS) da Microsoft na nuvem.'
keywords: introdução à sql, introdução sql, o que é a base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 29a0d0679adc51cf1493d241273e3b572a7fd57d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214304"
---
# <a name="what-is-azure-sql-database-service"></a>O que é o serviço de base de dados do Azure SQL?

A Base de Dados SQL é um serviço gerido de bases de dados relacionais para fins gerais do Microsoft Azure que suporta estruturas como dados relacionados, JSON, espaciais e XML. Base de dados SQL proporciona um desempenho dimensionável de forma dinâmica dentro de dois modelos de compras diferentes: um modelo de compra baseado em vCore e um modelo de compra baseado em DTU. A Base de Dados SQL disponibiliza também opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para análises e relatórios aprofundados e [OLTP dentro da memória](sql-database-in-memory.md) para processamento transacional avançado. A Microsoft lida com a aplicação de patches e a atualização da base de código do SQL ininterruptamente e abstrai toda a gestão da infraestrutura subjacente.

> [!NOTE]
> Para obter um glossário dos termos na base de dados do Azure SQL, consulte [Glossário de termos de base de dados SQL](sql-database-glossary-terms.md)

A Base de Dados SQL do Azure oferece as seguintes opções de implementação para bases de dados SQL do Azure:

- Como um [base de dados individual](sql-database-single-database.md) com seu próprio conjunto de recursos geridos através de um servidor de base de dados SQL. Uma base de dados é semelhante a um [bases de dados contidas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server.
- Uma [conjunto elástico](sql-database-elastic-pool.md), que é uma coleção de bases de dados com um conjunto partilhado de recursos geridos através de um servidor de base de dados SQL. Bases de dados individuais podem ser movidas para dentro e fora de um conjunto elástico.
- [Instância gerida](sql-database-managed-instance.md), que é uma coleção de bases de dados do sistema e do usuário com um conjunto partilhado de recursos. Uma instância gerida é semelhante a uma instância do [motor de base de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation).

A ilustração seguinte mostra estas opções de implementação:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

A Base de Dados SQL partilha a base de código com o [motor de bases de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Com a estratégia da cloud prioritária da Microsoft, as mais recentes capacidades do SQL Server são lançadas primeiro na Base de Dados SQL e, depois, no próprio SQL Server. Esta abordagem disponibiliza-lhe as últimas capacidades do SQL Server sem sobrecarga em termos de aplicação de patches ou atualizações - com estas funcionalidades novas testadas em milhões de bases de dados. Para obter informações sobre as novas capacidades quando são anunciadas, veja:

- **[Mapa do Azure para a base de dados SQL](https://azure.microsoft.com/roadmap/?category=databases)**:

  Um local onde encontrará as novidades e futuras.

- **[Blogue de base de dados SQL do Azure](https://azure.microsoft.com/blog/topics/database)**:

  Um lugar onde os membros blog sobre as funcionalidades e notícias da base de dados SQL da Equipe do produto SQL Server.

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidade entre a base de dados SQL e o SQL Server, bem como as diferenças entre as diferentes opções de implementação de base de dados do Azure SQL, veja [funcionalidades do SQL](sql-database-features.md).

Base de dados SQL proporciona um desempenho previsível com vários tipos de recursos, escalões de serviço e tamanhos de computação que fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente incorporada, escalabilidade global e disponibilidade e segurança avançada Opções — tudo com quase sem administração. Estas capacidades permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos para a gestão de máquinas virtuais e de infraestruturas. O serviço Base de Dados SQL encontra-se atualmente em 38 datacenters em todo o mundo, sendo que são colocados online mais datacenters regularmente, o que lhe permite executar a sua base de dados num datacenter perto de si.

## <a name="scalable-performance-and-pools"></a>Dimensionar o desempenho e os conjuntos

- Bases de dados individuais, cada base de dados é isolada umas das outras e portáteis, tendo cada um com seu próprio quantidade garantida de recursos de computação, memória e armazenamento. Base de dados SQL fornece recursos de computação, memória e armazenamento diferentes para diferentes necessidades - e a capacidade de forma dinâmica [Dimensionar recursos de base de dados individual](sql-database-single-database-scale.md) e reduzir verticalmente. O [camada de serviços de hiperescala](sql-database-service-tier-hyperscale.md) (pré-visualização) para a base de dados permite-lhe dimensionar para 100 TB, com a cópia de segurança rápida e recursos de restauração.
- Com os conjuntos elásticos, pode criar novas bases de dados ou mover bases de dados individuais para um conjunto de recursos para maximizar a utilização de recursos e poupar dinheiro – e a capacidade de dinamicamente [dimensionar os recursos do conjunto elástico](sql-database-elastic-pool-scale.md) e reduzir verticalmente.
- Com as instâncias geridas, cada instância gerida está isolada das restantes instâncias com recursos garantidos. Dentro de uma instância gerida, os bancos de dados de instância partilham um conjunto de recursos - e a capacidade de forma dinâmica [Dimensionar recursos de instância gerida](sql-database-managed-instance-resource-limits.md) e reduzir verticalmente.

Pode criar a primeira aplicação numa base de dados pequena e individual a um baixo custo por mês no escalão de serviço para fins gerais e, em seguida, alterar o escalão de serviço manual ou programaticamente em qualquer altura para o escalão de serviço crítico comercial para satisfazer as necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

A escalabilidade dinâmica é diferente do dimensionamento automático. O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual sem períodos de indisponibilidade. Uma base de dados suporta a escalabilidade dinâmica manual, mas não o dimensionamento automático. Para uma experiência mais *automática*, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados. No entanto, existem scripts que podem ajudar a automatizar a escalabilidade para uma base de dados. Por exemplo, veja [utilize o PowerShell para monitorizar e dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Adquirir modelos, escalões de serviço, tamanhos de computação e quantidades de armazenamento

Base de dados SQL disponibiliza dois modelos de compras:

- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece um blend de computação, memória, recursos de e/s em três escalões de serviço para suportar cargas de trabalho de base de dados de pedidos leves. Computação tamanhos dentro de cada camada fornecem uma mistura diferentes destes recursos, ao qual pode adicionar recursos de armazenamento adicionais.
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite-lhe escolher o número de vCores a quantidade ou memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que use [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter poupanças de custos. Para obter mais informações sobre o benefício híbrido do Azure, consulte [perguntas mais frequentes sobre](#sql-database-frequently-asked-questions-faq).

  > [!IMPORTANT]
  > O [camada de serviços de hiperescala](sql-database-service-tier-hyperscale.md) para bases de dados individuais está atualmente em pré-visualização pública. Não recomendamos a execução de qualquer carga de trabalho de produção nos bancos de dados de hiperescala ainda. Não é possível atualizar uma base de dados de hiperescala para outros escalões de serviço. Para fins de teste, recomendamos que faça uma cópia da base de dados atual e atualizar a cópia para a camada de serviços de hiperescala.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os [conjuntos elásticos](sql-database-elastic-pool.md) foram concebidos para resolver este problema. O conceito é simples. Alocar recursos de desempenho para um conjunto em vez de uma base de dados individual e pagar para os recursos de desempenho coletivo do conjunto em vez de desempenho de base de dados.

   ![conjuntos elásticos](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com os conjuntos elásticos, não tem de se concentrar em ajustar o desempenho da base de dados à medida que a procura pelos recursos flutua. As bases de dados agrupadas consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados agrupadas consomem os limites do conjunto, mas não os excedem, pelo que os seus custos se mantêm previsíveis, mesmo que a utilização das bases de dados não. Além disso, pode [adicionar e remover bases de dados do conjunto](sql-database-elastic-pool-manage-portal.md), dimensionando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento controlado por si. Também pode controlar os recursos mínimos e máximos disponíveis para as bases de dados do conjunto, para garantir que nenhuma utiliza todos os recursos e que todas as bases de dados agrupadas têm uma quantidade mínima garantida de recursos. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, veja [Design Patterns for Multi-tenant SaaS Applications with SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Padrões de Design para Aplicações SaaS multi-inquilino com a Base de Dados SQL).

Os scripts podem ajudar na monitorização e no dimensionamento dos conjuntos elásticos. Por exemplo, veja [Utilizar o Azure PowerShell para monitorizar e dimensionar um conjunto elástico SQL na Base de Dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Uma instância gerida não suporta conjuntos elásticos. Em vez disso, uma instância gerida é uma coleção de bases de dados de instância que partilham recursos de instância gerida.

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados agrupadas

Pode misturar bases de dados individuais com conjuntos elásticos e alterar os escalões de serviço de bases de dados individuais e conjuntos elásticos rápida e facilmente adaptá-los à sua situação. Com o poder e a o alcance do Azure, pode combinar outros serviços do Azure com a Base de Dados SQL para satisfazer as necessidades de design da sua aplicação única, aumentar as eficiências de custos e recursos e conseguir novas oportunidades de negócio.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Capacidades alargadas de monitorização e alertas

Utilize a [monitorização de desempenho incorporada](sql-database-performance.md) e as ferramentas de [alerta](sql-database-insights-alerts-portal.md) juntamente com as classificações de desempenho. Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

- **O armazenamento do Azure**: Para arquivar grandes quantidades de telemetria a um preço baixo
- **Hub de eventos do Azure**: Para integrar a telemetria de base de dados SQL com a sua solução personalizada monitorizção ou em pipelines
- **Registos de Monitor do Azure**: Para solução de monitorização incorporada com relatórios, alertas e mitigação.

    ![arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Num ambiente tradicional do SQL Server, geralmente (pelo menos) 2 máquinas localmente definidas com cópias (sincronicamente manter) exatas dos dados (com funcionalidades como instâncias de Cluster de ativação pós-falha ou a grupos de Disponibilidade AlwaysOn) para proteger contra uma Falha de um único componente/máquina.  Isso fornece elevada disponibilidade, mas não protege contra um desastre natural destruir seu centro de dados.
 
Recuperação após desastre parte do princípio de que um evento catastrófico será geograficamente localizados suficiente para ter outra máquina/conjunto de máquinas com uma cópia dos seus dados perto de.  No SQL Server, poderia usar grupos de Disponibilidade AlwaysOn em execução no modo assíncrono para obter esta capacidade.  A velocidade da luz problemas normalmente, isto significa que as pessoas não pretende esperar até que a replicação para acontecer que distantes antes de consolidar uma transação, portanto, há a potencial perda de dados quando o fizer ativações pós-falha não planeadas.

Bases de dados no serviço premium e comercial crítico camadas já [fazer algo muito semelhante](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para a sincronização de um grupo de disponibilidade. Bases de dados nos escalões de serviço mais baixos fornecem redundância por meio de armazenamento com um [mecanismo de diferente mas equivalente](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Há uma lógica que protege contra uma falha de máquina única.  A funcionalidade de georreplicação ativa lhe a capacidade de proteger contra desastres em que uma região inteira for destruída.

As zonas de disponibilidade do Azure é um play sobre o problema de elevada disponibilidade.  Ele tenta proteger contra a falha de um único centro de dados criar numa única região.  Então, quer proteger contra a perda de energia ou de rede para um edifício. No SQL Azure, isso funcionará ao colocar as réplicas diferentes em diferentes zonas de disponibilidade (edifícios diferentes, com eficiência) e, caso contrário, está a funcionar como antes. 

Na verdade, o contrato de nível de serviço de disponibilidade de 99,99% líder da indústria do Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/), com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 x 7. A plataforma do Azure totalmente gerencia cada base de dados e não garantias de nenhuma perda de dados e alta porcentagem de disponibilidade de dados. O Azure gere automaticamente a aplicação de patches, as cópias de segurança, a replicação, a deteção de falhas, falhas potenciais subjacentes de hardware, software ou rede, a implementação de correções de erros, as ativações pós-falha, as atualizações da base de dados e outras tarefas de manutenção. A disponibilidade standard é conseguida por uma separação das camadas de armazenamento e computação. Disponibilidade de Premium é obtida com a integração de computação e armazenamento num único nó para o desempenho e, em seguida, implementar a tecnologia semelhante para grupos de Disponibilidade AlwaysOn nos bastidores. Para uma discussão completa sobre as capacidades de elevada disponibilidade da base de dados do Azure SQL, consulte [disponibilidade de base de dados SQL](sql-database-high-availability.md). Além disso, a Base de Dados SQL proporciona funcionalidades de [continuidade de negócio e escalabilidade global](sql-database-business-continuity.md):

- **[Cópias de segurança automáticas](sql-database-automated-backups.md)**:

  Base de dados SQL efetua automaticamente completas, diferenciais e backups de log de transação de bases de dados SQL do Azure que lhe permite restaurar para qualquer ponto no tempo. Para bases de dados individuais e bases de dados agrupadas, pode configurar a base de dados SQL para armazenar cópias de segurança completa da base de dados para o armazenamento do Azure para retenção de cópia de segurança de longo prazo. Para obter instâncias geridas, também pode efetuar cópias de segurança apenas de cópia para retenção de cópia de segurança de longo prazo.

- **[Restauros de ponto no tempo](sql-database-recovery-using-backups.md)**:

  Todas as opções de implementação de base de dados SQL suportam a recuperação para qualquer ponto no tempo dentro do período de retenção de cópia de segurança automática para qualquer base de dados SQL do Azure.
- **[Georreplicação ativa](sql-database-active-geo-replication.md)**:

  Base de dados e bases de dados agrupadas permitem-lhe configurar até quatro secundárias bases de dados legíveis em qualquer um mesmo ou distribuídas globalmente datacenters do Azure.  Por exemplo, se tiver uma aplicação SaaS com uma base de dados de catálogo que tem um volume elevado de transações só de leitura simultâneas, utilize a georreplicação ativa para permitir uma escala de leitura global e remover estrangulamentos na base de dados principal que se devam a cargas de trabalho de leitura. Para obter instâncias geridas, utilize grupos de ativação pós-falha automática.
- **[Grupos de ativação pós-falha automática](sql-database-auto-failover-group.md)**:

  Todas as opções de implementação de base de dados SQL permitem-lhe utilizar grupos de ativação pós-falha para ativar a elevada disponibilidade e balanceamento de carga numa escala global, incluindo georreplicação transparente e ativação pós-falha de grandes conjuntos de bases de dados, conjuntos elásticos e instâncias geridas. Grupos de ativação pós-falha permitem a criação de aplicações SaaS distribuídas globalmente com uma administração mínima sobrecarga deixando todas as monitorização complexa, encaminhamento e orquestração de ativação pós-falha para a base de dados SQL.
- **[Bases de dados com redundância de zona](sql-database-high-availability.md)**:

  Base de dados SQL permite-lhe premium de aprovisionar ou bases de dados de críticas de negócios ou conjuntos elásticos em várias zonas de disponibilidade. Uma vez que estas bases de dados e os conjuntos elásticos têm várias réplicas redundantes para elevada disponibilidade, a colocação destas réplicas em várias zonas de disponibilidade permite uma maior resiliência, incluindo a capacidade de recuperar automaticamente de falhas à escala do datacenter sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência incorporada

Com a Base de Dados SQL, pode tirar partido da inteligência incorporada que o ajuda a reduzir significativamente os custos de execução e gestão de bases de dados e que maximiza, tanto o desempenho, como a segurança, da sua aplicação. Executando milhões de cargas de trabalho dos clientes ininterruptamente, a Base de Dados SQL recolhe e processa uma gigantesca quantidade de dados de telemetria, respeitando também totalmente a privacidade dos clientes em segundo plano. Vários algoritmos avaliam continuamente os dados de telemetria, de modo a que o serviço possa aprender e adaptar-se com a sua aplicação. Com babe nestas análises, o serviço oferece recomendações de melhoria do desempenho, personalizadas à medida da sua carga de trabalho específica.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitorização e otimização de desempenho automático

A Base de Dados SQL disponibiliza informações detalhadas sobre as consultas que tem de monitorizar. Aprende os padrões da sua base de dados e permite-lhe adaptar o esquema da mesma à carga de trabalho. A Base de Dados SQL disponibiliza [recomendações de otimização de desempenho](sql-database-advisor.md), onde pode ver as ações de otimização e aplicá-las.

No entanto, a monitorização contínua de bases de dados é uma tarefa difícil e entediante, especialmente se forem muitas. As [Informações inteligentes](sql-database-intelligent-insights.md) realizam esta tarefa para si, ao monitorizar automaticamente o desempenho da Base de Dados SQL em escala e ainda o informa sobre os problemas de degradação de desempenho, identifica a causa do problema e oferece recomendações de melhorias de desempenho, sempre que possível.

Pode ser impossível gerir eficazmente um grande número de bases de dados, mesmo tendo em conta todas as ferramentas e relatórios que a Base de Dados SQL e o Azure proporcionam. Em vez de monitorizar e otimizar a sua base de dados manualmente, pode considerar utilizar a [otimização automática](sql-database-automatic-tuning.md) para delegar algumas das ações de monitorização e otimização à Base de Dados SQL. A Base de Dados SQL aplica recomendações, testa e verifica automaticamente todas as ações de otimização, para garantir que o desempenho continua a melhorar. Desta forma, a Base de Dados SQL adapta-se automaticamente à sua carga de trabalho de forma controlada e segura. A otimização automática significa que o desempenho da sua base de dados é cuidadosamente monitorizado e comparado antes e depois de cada ação de otimização e, caso não melhore, as ações são revertidas.

Hoje em dia, muitos dos nossos parceiros que executam [aplicações SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) tendo a Base de Dados SQL como base confiam na otimização de desempenho automática, para garantir que as aplicações têm sempre um desempenho estável e previsível. Para estes parceiros, esta funcionalidade reduz significativamente o risco de ocorrência de incidentes de desempenho a meio da noite. Além disso, uma vez que parte da base de clientes deles também utiliza o SQL Server, os parceiros utilizam as mesmas recomendações de indexação disponibilizadas pela Base de Dados SQL para ajudar os clientes do SQL Server.

Estão disponíveis dois aspetos de otimização automática na [Base de Dados SQL](sql-database-automatic-tuning.md):

- **Gestão de índices automática**: Identifica os índices que devem ser adicionados à sua base de dados e os que devem ser removidos.
- **Correção de planos automática**: Identifica planos problemáticos e corrige problemas de desempenho de planos do SQL (brevemente; já disponível no SQL Server 2017).

### <a name="adaptive-query-processing"></a>Processamento de consultas adaptável

Também adicionámos o conjunto de funcionalidades de [processamento de consultas adaptável](/sql/relational-databases/performance/adaptive-query-processing) à Base de Dados SQL, incluindo a execução intercalada para funções de valor de tabela com múltiplas instruções, feedback de concessão de memória no modo de lote e associações adaptativas no modo de lote. Todas estas funcionalidades de processamento de consultas adaptativo aplica técnicas de “aprendizagem e adaptação” semelhantes, o que ajuda a ir ainda mais longe na resolução de problemas de desempenho relacionados com questões de otimização de consultas historicamente complicadas.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Base de Dados SQL proporciona um conjunto de [funcionalidades de segurança e conformidade incorporadas](sql-database-security-overview.md), para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade.

### <a name="advance-threat-protection"></a>Proteção Avançada Contra Ameaças

Segurança avançada de dados é um pacote unificado para funções de segurança avançadas do SQL. Inclui a funcionalidade para detetar e classificar dados confidenciais, gerir as vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Oferece uma localização única para ativar e gerir estas capacidades.

- [Dados de deteção e classificação](sql-database-data-discovery-and-classification.md):

  Esta funcionalidade (atualmente em pré-visualização) fornece recursos incorporados ao banco de dados do Azure SQL para detetar, classificar, Etiquetar e proteger os dados confidenciais em seus bancos de dados. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites.
- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Este serviço pode detetar, monitorizar e ajudarão a remediar potenciais vulnerabilidades das bases de dados. Oferece visibilidade sobre o estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- [Deteção de ameaças](sql-database-threat-detection.md):

  Esta funcionalidade Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de sua base de dados. Monitoriza continuamente a sua base de dados quanto a atividades suspeitas e apresenta alertas de segurança imediata em potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso de base de dados anómalos. Alertas de deteção de ameaças fornecem detalhes da atividade suspeita e a ação sobre como investigar e mitigar a ameaça recomendada.

### <a name="auditing-for-compliance-and-security"></a>Auditoria para conformidade e segurança

[Auditoria](sql-database-auditing.md) faixas de base de dados eventos e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam traduzir preocupações comerciais ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Encriptação de dados

Base de dados SQL protege os seus dados ao fornecer encriptação para dados em movimento com [segurança de camada de transporte](https://support.microsoft.com/kb/3135244), para dados Inativos com [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)e para os dados em utilização com [ sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A Base de Dados SQL permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](sql-database-ssms-mfa-authentication.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="compliance-certification"></a>Certificação de conformidade

A Base de Dados SQL participa em auditorias regulares e foi certificada por várias normas de conformidade. Para obter mais informações, veja o [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade da Base de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de utilizar

Com a Base de Dados SQL, criar e manter aplicações é mais fácil e produtivo. A Base de Dados SQL permite-lhe dedicar-se ao que sabe fazer melhor: criar aplicações excelentes. Pode gerir e programar na Base de Dados SQL com as ferramentas e as competências que já tem.

- **[O portal do Azure](https://portal.azure.com/)**:

  Uma aplicação baseada na web para gerir todos os serviços do Azure
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**:

  Uma aplicação de cliente gratuita e transferível para gerir qualquer infraestrutura SQL, do SQL Server à base de dados SQL
- **[SQL Server Data Tools no Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**:

  Um aplicativo de cliente gratuita e transferível para o desenvolvimento de bancos de dados relacionais do SQL Server, bases de dados SQL do Azure, pacotes do Integration Services, modelos de dados do Analysis Services e relatórios do Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**:

  Um editor de código gratuito, transferível e código aberto para Windows, macOS e Linux que suporta extensões, incluindo o [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, base de dados do Azure SQL e SQL Data Warehouse.

A Base de Dados suporta a criação de aplicações com Python, Java, Node.js, PHP, Ruby e .NET em macOS, Linux e Windows. A Base de Dados SQL suporta as mesmas [bibliotecas de ligações](sql-database-libraries.md) que o SQL Server.

## <a name="sql-database-frequently-asked-questions-faq"></a>Base de dados SQL perguntas mais frequentes (FAQ)

### <a name="what-is-the-current-version-of-sql-database"></a>O que é a versão atual da base de dados SQL

A versão atual da base de dados SQL é V12. Versão V11 foi preterido.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Posso controlar quando ocorre um período de indisponibilidade de aplicação de patches

Não. O impacto da aplicação de patches em geral, não é perceptível se [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) na sua aplicação. Para obter mais informações sobre como preparar para eventos de manutenção planeada na sua base de dados SQL do Azure, consulte [planejamento para eventos de manutenção do Azure na base de dados do Azure SQL](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Perguntas do benefício híbrido do Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existem direitos de uso de dupla com o benefício híbrido do Azure para o SQL Server

Terá de 180 dias de direitos de uso dual da licença para garantir que as migrações executem de forma totalmente integrada. Depois desse período de 180 dias, a licença do SQL Server só pode ser utilizada na nuvem na base de dados SQL e não tem uso dual direitos no local e na cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Azure Hybrid Benefit para o SQL Server difere da mobilidade de licenças

Hoje em dia, podemos oferecer benefícios da mobilidade de licenças para clientes do SQL Server com Software Assurance, que permite a reatribuição de suas licenças para servidores compartilhados de terceiros. Este benefício pode ser utilizado no IaaS do Azure e AWS EC2.
O benefício híbrido do Azure para o SQL Server é diferente da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes de SQL EE podem obter 4 núcleos no Azure, o SKU de fins gerais para cada núcleo tiver no local para aplicativos altamente virtualizados. Mobilidade de licenças não permite quaisquer benefícios de custo especial para mover cargas de trabalho virtualizadas para a cloud.
- Ele fornece para um destino de PaaS do Azure (instância de gerida de base de dados do SQL), que é altamente compatível com o SQL Server no local

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do benefício híbrido do Azure, para o SQL Server

Os clientes da base de dados SQL terão os seguintes direitos associados com o benefício híbrido do Azure para o SQL Server:

|Requisitos de espaço de licença|O que faz o benefício híbrido do Azure para SQL Server obter?|
|---|---|
|Clientes de núcleo do SQL Server Enterprise Edition com SA|<li>Pode pagar a taxa Base no SKU de críticos de negócio ou para fins gerais</li><br><li>1 núcleo no local = 4 núcleos no SKU de fins gerais</li><br><li>1 núcleo no local = 1 núcleo na SKU crítica de negócios</li>|
|Clientes de núcleo do SQL Server Standard Edition com SA|<li>Paguem apenas taxa Base na SKU de fins gerais</li><br><li>1 núcleo no local = 1 núcleo no SKU de fins gerais</li>|
|||


## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver a equipa de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Faça perguntas de administração de banco de dados
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Colocar questões de desenvolvimento
- [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Faça perguntas técnicas
- [Comentários](https://aka.ms/sqlfeedback): Relatório de erros e pedir funcionalidades
- [Reddit](https://www.reddit.com/r/SQLServer/): Discuta o SQL Server

## <a name="next-steps"></a>Passos Seguintes

- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de preços e calculadoras de bases de dados individuais e conjuntos elásticos.
- Veja estes guias de introdução para começar:

  - [Criar uma base de dados SQL no portal do Azure](sql-database-single-database-get-started.md)  
  - [Criar uma base de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar uma base de dados SQL utilizando o PowerShell](sql-database-get-started-powershell.md)

- Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
  - [Exemplos da CLI do Azure para a Base de Dados SQL (Azure CLI samples for SQL Database)](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para a Base de Dados SQL (Azure PowerShell samples for SQL Database)](sql-database-powershell-samples.md)
