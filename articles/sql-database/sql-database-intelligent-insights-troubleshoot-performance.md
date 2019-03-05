---
title: Resolução de problemas de desempenho de base de dados do Azure SQL com informações inteligentes | Documentos da Microsoft
description: Informações inteligentes ajuda-o a resolver problemas de desempenho da base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fff4aa947f878974d2d0f18f373b8c0917ed7d70
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316061"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Resolução de problemas de desempenho de base de dados do Azure SQL com informações inteligentes

Esta página fornece informações sobre a base de dados do Azure SQL e detetados problemas de desempenho de instância gerida através da [informações inteligentes](sql-database-intelligent-insights.md) registo de diagnóstico de desempenho da base de dados. A telemetria de registo de diagnóstico possam ser transmitida para [registos do Azure Monitor](../azure-monitor/insights/azure-sql.md), [Event Hubs do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage), ou uma solução de terceiros para alertas personalizados de DevOps e capacidades de relatórios.

> [!NOTE]
> Para obter um rápido desempenho de base de dados SQL com informações inteligentes de guia de resolução de problemas, consulte a [recomendado a resolução de problemas de fluxo](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) fluxograma neste documento.
>

## <a name="detectable-database-performance-patterns"></a>Padrões de desempenho de bases de dados detetável

Informações inteligentes Deteta automaticamente os problemas de desempenho com os bancos de dados base de dados SQL e a instância gerida com base em tempos de espera de execução de consulta, erros ou tempos limite. Ele produz os padrões de desempenho detetados no log de diagnóstico. Padrões de desempenho detetável estão resumidos na tabela abaixo.

| Padrões de desempenho detetável | Descrição para a base de dados do Azure SQL e conjuntos elásticos | Descrição para bases de dados na instância gerida |
| :------------------- | ------------------- | ------------------- |
| [Alcance limites de recursos](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Consumo de recursos disponíveis (DTUs), threads de trabalho de base de dados ou sessões de início de sessão de banco de dados disponíveis na subscrição monitorizada atingiu os limites. Isto está a afetar o desempenho da base de dados SQL. | Consumo de recursos de CPU está a atingir os limites de instância gerida. Isto está a afetar o desempenho da base de dados. |
| [Aumento de carga de trabalho](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detetada o aumento de carga de trabalho ou de acumulação contínua da carga de trabalho no banco de dados. Isto está a afetar o desempenho da base de dados SQL. | Foi detetado o aumento de carga de trabalho. Isto está a afetar o desempenho da base de dados. |
| [Pressão de memória](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Os operadores que solicitou concessões de memória tem de aguardar que as alocações de memória para quantidades significativas de ponto de vista estatístico de tempo. Ou existe uma acumulação de aumento de trabalhadores que solicitou concessões de memória. Isto está a afetar o desempenho da base de dados SQL. | Os operadores que solicitaram concessões de memória estão a aguardar para alocações de memória para um ponto de vista estatístico considerável de tempo. Isto está a afetar o desempenho da base de dados. |
| [Bloqueio](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Bloqueio excessivo da base de dados foi detetada a afetar o desempenho da base de dados SQL. | Bloqueio excessivo da base de dados foi detetada a afetar o desempenho da base de dados. |
| [MAXDOP maior](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | O grau máximo da opção de paralelismo (MAXDOP) foi alterada que afetam a eficiência de execução da consulta. Isto está a afetar o desempenho da base de dados SQL. | O grau máximo da opção de paralelismo (MAXDOP) foi alterada que afetam a eficiência de execução da consulta. Isto está a afetar o desempenho da base de dados. |
| [Pagelatch contenção](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Vários threads em simultâneo estão a tentar acessar as páginas de memória intermédia de dados na memória mesmo resulta em tempos de espera maior e fazendo com que pagelatch contenção. Isto está a afetar a base de dados do SQL o desempenho. | Vários threads em simultâneo estão a tentar acessar as páginas de memória intermédia de dados na memória mesmo resulta em tempos de espera maior e fazendo com que pagelatch contenção. Isto está a afetar o desempenho da base de dados. |
| [Índice em falta](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Índice ausente foi detetado que afetam o desempenho de base de dados SQL. | Índice ausente foi detetado que afetam o desempenho da base de dados. |
| [Nova consulta](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Nova consulta foi detetada que afetam o desempenho geral da base de dados SQL. | Nova consulta foi detetada que afetam o desempenho geral da base de dados. |
| [Estatística de aumento de espera](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Tempos de espera de base de dados aumentada foram detetados que afetam o desempenho de base de dados SQL. | Tempos de espera de base de dados aumentada foram detetados que afetam o desempenho da base de dados. |
| [Contenção do TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários threads estão a tentar aceder ao mesmo recurso de TempDB provocar um estrangulamento. Isto está a afetar o desempenho da base de dados SQL. | Vários threads estão a tentar aceder ao mesmo recurso de TempDB provocar um estrangulamento. Isto está a afetar o desempenho da base de dados. |
| [Conjunto elástico insuficiência de DTU](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Falta de eDTUs disponíveis num conjunto elástico está a afetar o desempenho da base de dados SQL. | Não está disponível para a instância gerida como ele usa o modelo de vCore. |
| [Regressão do plano](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detetado o novo plano ou uma alteração na carga de trabalho de um plano existente. Isto está a afetar o desempenho da base de dados SQL. | Foi detetado o novo plano ou uma alteração na carga de trabalho de um plano existente. Isto está a afetar o desempenho da base de dados. |
| [Alteração do valor de configuração do âmbito de base de dados](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Alteração de configuração na base de dados SQL foi detetada que afetam o desempenho da base de dados. | Alteração de configuração na base de dados foi detetada que afetam o desempenho da base de dados. |
| [Cliente lentas](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Cliente de lenta do aplicativo é não é possível consumir o resultado da base de dados rápido o bastante. Isto está a afetar o desempenho da base de dados SQL. | Cliente de lenta do aplicativo é não é possível consumir o resultado da base de dados rápido o bastante. Isto está a afetar o desempenho da base de dados. |
| [Mudança para versão anterior do escalão de preço](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Ação de mudança para versão anterior do escalão de preço reduzido recursos disponíveis. Isto está a afetar o desempenho da base de dados SQL. | Ação de mudança para versão anterior do escalão de preço reduzido recursos disponíveis. Isto está a afetar o desempenho da base de dados. |

> [!TIP]
> Para a otimização de desempenho contínuo de base de dados SQL, ative [otimização automática da base de dados do Azure SQL](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência incorporada da base de dados SQL continuamente monitoriza a base de dados SQL, automaticamente adaptável índices e aplica-se as correções de plano de execução de consulta.
>

A secção seguinte descreve os padrões de desempenho detetável mais detalhadamente.

## <a name="reaching-resource-limits"></a>Alcance limites de recursos

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável combina os problemas de desempenho relacionados com a atingir os limites de recursos disponíveis, limites de trabalho e limites de sessão. Depois deste problema de desempenho é detectado, um campo de descrição do registo de diagnóstico indica se o problema de desempenho está relacionado com recursos, trabalho ou os limites de sessão.

Recursos na base de dados SQL, normalmente, são referidos [DTU](sql-database-what-is-a-dtu.md) ou [vCore](sql-database-service-tiers-vcore.md) recursos. O padrão de atingir os limites de recursos é reconhecido quando detectado degradação do desempenho de consulta é causada por atingir qualquer um dos limites de recursos de medida.

O recurso de limites de sessão indica o número de inícios de sessão simultâneos disponíveis para a base de dados SQL. Este padrão de desempenho é reconhecido quando aplicativos que estão ligados às bases de dados SQL atingiu o número de inícios de sessão simultâneos disponíveis para a base de dados. Se as aplicações tentam utilizar mais sessões que estão disponíveis numa base de dados, o desempenho de consulta é afetado.

Chegar aos limites de trabalho é um caso específico de atingir os limites de recursos porque trabalhadores disponíveis não são contabilizados na utilização DTU ou vCore. Chegar aos limites de trabalho numa base de dados pode fazer com que o surgimento de tempos de espera de recursos específicos, que resulta na degradação do desempenho de consulta.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz hashes de consulta de consultas afetado o desempenho e percentagens de consumo de recursos. Pode usar essas informações como ponto de partida para otimizar a sua carga de trabalho de base de dados. Em particular, pode otimizar as consultas que afetam a degradação do desempenho através da adição de índices. Ou pode otimizar aplicativos com um distribuição de carga de trabalho ainda mais. Se não for possível reduzir as cargas de trabalho ou fazer otimizações, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos disponíveis.

Se atingiu os limites de sessão disponíveis, pode otimizar seus aplicativos, reduzindo o número de inícios de sessão feitas no banco de dados. Se não for possível reduzir o número de inícios de sessão das suas aplicações para a base de dados, considere aumentar o escalão de preço da base de dados. Ou pode dividir e mover a base de dados em várias bases de dados para uma distribuição mais com balanceamento de carga de trabalho.

Para obter mais sugestões sobre como resolver os limites de sessão, consulte [como lidar com os limites de inícios de sessão de máximos de base de dados SQL](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Ver [limita a visão geral dos recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.

## <a name="workload-increase"></a>Aumento de carga de trabalho

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho identifica problemas causados por um aumento de carga de trabalho ou, em sua forma mais grave, pile-up uma carga de trabalho.

Esta deteção é feita através de uma combinação de várias métricas. A métrica básica medida está a detetar um aumento na carga de trabalho em comparação com a última linha de base de carga de trabalho. A outra forma de deteção baseia-se na medição de maior aumento no threads de trabalho do Active Directory que seja suficientemente grande para afetar o desempenho de consulta.

Em sua forma mais grave, a carga de trabalho pode continuamente acumuladas devido a incapacidade de base de dados SQL, para processar a carga de trabalho. O resultado é um tamanho de carga de trabalho em contínuo crescimento, o que é a condição de pile-up da carga de trabalho. Devido a esta condição, que aumenta o tempo que a carga de trabalho aguarda para execução. Esta condição representa um dos problemas de desempenho de base de dados mais graves. Este problema foi detetado com a monitorização do aumento no número de threads de trabalho abortadas. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico devolve o número de consultas cuja execução aumentou e o hash de consulta da consulta com o maior contributo para o aumento de carga de trabalho. Pode usar essas informações como ponto de partida para otimizar a carga de trabalho. A consulta identificada como o maior Contribuidor para o aumento de carga de trabalho é especialmente útil como ponto de partida.

Pode optar por distribuir as cargas de trabalho mais uniformemente à base de dados. Considere também otimizar a consulta que está a afetar o desempenho através da adição de índices. Também poderá distribuir a carga de trabalho entre várias bases de dados. Se essas soluções não são possíveis, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Pressão de memória

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho indica degradação no desempenho da base de dados atual causado por pressão de memória ou em sua forma mais grave uma condição de pile-up de memória, em comparação comparada a linha de base de desempenho de sete dias anteriores.

Pressão de memória indica uma condição de desempenho em que há um grande número de threads de trabalho memória solicitante concede na base de dados SQL. O grande volume faz com que uma condição de utilização de memória elevada na qual a base de dados SQL é não é possível alocar a memória com eficiência para todos os trabalhadores que solicitação-la. Uma das razões mais comuns para este problema está relacionado com a quantidade de memória disponível para a base de dados SQL por um lado. Por outro lado, um aumento na carga de trabalho faz com que o aumento em threads de trabalho e a pressão de memória.

A forma mais grave de pressão de memória é a condição de pile-up de memória. Esta condição indica que um número mais alto de threads de trabalho está a solicitar concessões de memória que existem consultas liberar a memória. Este número de threads de trabalho memória solicitante concede também pode ser continuamente aumentando (acumulando) porque o motor de base de dados SQL é não é possível alocar a memória com eficiência suficiente para satisfazer a procura. A condição de pile-up de memória representa um dos problemas de desempenho de base de dados mais graves.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os detalhes de arquivo de objeto de memória com o funcionário (ou seja, o thread de trabalho) marcada como o motivo mais alto para utilização elevada da memória e carimbos de data / hora relevante. Pode usar essas informações como base para resolução de problemas. 

Pode otimizar ou remover consultas relacionadas com a clerks com o uso de memória mais elevado. Também pode tornar-se de que não são consultar os dados que não pretende utilizar. Uma prática recomendada é sempre usar uma cláusula WHERE em suas consultas. Além disso, recomendamos que crie índices não agrupados a busca os dados em vez de lê-lo.

Também pode reduzir a carga de trabalho ao otimizar ou distribuí-lo ao longo de várias bases de dados. Ou pode distribuir a carga de trabalho entre várias bases de dados. Se essas soluções não são possíveis, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos de memória disponíveis para a base de dados.

Para obter sugestões de resolução de problemas adicionais, consulte [memória concede mediação: O consumidor de memória do SQL Server misterioso com muitos nomes](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Bloqueio

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho indica degradação no desempenho da base de dados atual no qual bloqueio excessivo da base de dados é detetado em comparação com a linha de base de desempenho de sete dias anteriores. 

No RDBMS modernos, bloqueio seja essencial para a implementação de sistemas multithread em que o desempenho é maximizado ao executar várias funções de trabalho simultâneas e transações paralelas de bases de dados sempre que possível. Bloqueio neste contexto refere-se para o mecanismo de acesso interno em que apenas uma única transação pode acessar exclusivamente as linhas, páginas, tabelas e ficheiros que são necessários e não compitam com outra transação para os recursos. Quando a transação que os recursos para utilização de bloqueado é feita com eles, o bloqueio desses recursos for lançado, que permite que outras transações aceder aos recursos necessários. Para obter mais informações sobre o bloqueio, consulte [bloquear no motor de base de dados](https://msdn.microsoft.com/library/ms190615.aspx).

Se as transações executadas pelo mecanismo do SQL estão a aguardar por longos períodos de tempo para aceder a recursos sejam bloqueados para uso, o tempo de espera faz com que o abrandamento de desempenho de execução da carga de trabalho. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os detalhes de bloqueios que pode utilizar como base para resolução de problemas. Pode analisar as consultas de bloqueios comunicadas, ou seja, as consultas que apresentam a degradação do desempenho de bloqueio, e removê-los. Em alguns casos, poderá ser bem sucedida na otimização as consultas de bloqueios.

A forma mais simples e mais segura para mitigar o problema é manter transações curtas e reduzir os requisitos de espaço de bloqueio das consultas mais caras. Pode dividir-se um lote grande de operações nas operações menores. É boa prática reduzir os requisitos de espaço de bloqueio de consulta, fazendo com que a consulta mais eficiente possível. Reduza análises de grandes dimensões porque aumentar as chances de deadlocks e afetar negativamente o desempenho geral da base de dados. Para consultas de identificados causam o bloqueio, pode criar índices novo ou adicionar colunas para o índice existente para evitar as análises de tabela. 

Para obter mais sugestões, consulte [como resolver problemas de bloqueio causados por escalonamento de bloqueio no SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>MAXDOP maior

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição em que um plano de execução de consultas escolheu estava em paralelo mais do que deveria ser. O otimizador de consultas de base de dados SQL pode melhorar o desempenho da carga de trabalho, executar consultas em paralelo para acelerar as coisas, sempre que possível. Em alguns casos, uma consulta de processamento de trabalhadores paralelos gastar mais tempo a aguardar entre si para sincronizar e intercalar os resultados em comparação comparados a mesma consulta em execução com menos funções de trabalho de paralelas, ou mesmo em alguns casos em comparação com um só thread de trabalho.

O sistema de especialista analisa o desempenho da base de dados atual, em comparação comparado o período de linha de base. Determina se uma consulta que estava em execução está a ser executado mais lentamente do que antes porque o plano de execução da consulta é paralelizado mais do que deve ser.

A opção de configuração de servidor MAXDOP na base de dados SQL é utilizada para controlar o número de núcleos de CPU podem ser utilizados para executar a mesma consulta em paralelo. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os hashes de consulta relacionados às consultas para o qual a duração de execução aumentou porque eles foram paralelizados mais do que deveria. O registo de saídas também CXP tempos de espera. Desta vez representa o tempo de um thread do organizador/coordenador único (thread 0) está à espera que todos os outros threads seja concluída antes dos resultados de mesclagem e mover em frente. Além disso, o registo de diagnóstico produz os tempos de espera que as consultas de desempenho fraco estão a aguardar na execução geral. Pode usar essas informações como base para resolução de problemas.

Em primeiro lugar, otimizar ou simplificar as consultas complexas. Uma prática recomendada é dividir os trabalhos de lote longo em menores. Além disso, certifique-se de que criou índices para dar suporte a suas consultas. Também manualmente pode impor o grau máximo de paralelismo (MAXDOP) para uma consulta que foi sinalizada como desempenho fraco. Para configurar esta operação usando T-SQL, consulte [configurar a opção de configuração de servidor MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Definir o servidor MAXDOP a opção de configuração para zero (0) como um valor predefinido indica que o banco de dados SQL pode usar todos os núcleos de CPU lógicos disponíveis a paralelização de threads para executar uma única consulta. A definição MAXDOP para um (1) indica que apenas um núcleo pode ser utilizado para a execução de uma única consulta. Em termos práticos, isso significa que o paralelismo está desativado. Dependendo da base de caso por caso, informações de registo de núcleos disponíveis para a base de dados e diagnóstico, pode ajustar a opção MAXDOP para o número de núcleos utilizado para execução paralela da consulta que poderá resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Pagelatch contenção

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho indica a degradação de desempenho de carga de trabalho de base de dados atual devido à contenção de pagelatch em comparação comparada a linha de base de carga de trabalho de sete dias anteriores.

Travas são mecanismos de sincronização simples utilizados pela base de dados SQL para ativar o multithreading. Eles garantem a consistência de estruturas de dentro da memória que incluem índices, páginas de dados e outras estruturas internas.

Existem muitos tipos de travas disponíveis na base de dados SQL. Para fins de simplicidade, travas de buffer são utilizadas para proteger páginas na memória no pool de buffer. Travas de e/s são utilizadas para proteger páginas ainda não foi carregadas para o pool de buffer. Sempre que os dados são escritos para ou ler a partir de uma página no pool de buffers, um thread de trabalho tem de adquirir um bloqueio temporário de memória intermédia para a página em primeiro lugar. Sempre que um thread de trabalho tenta acessar uma página que não esteja disponível no pool de buffer na memória, uma solicitação de e/s é feita ao carregar as informações necessárias a partir do armazenamento. Esta sequência de eventos indica um formulário mais grave de degradação do desempenho.

Contenção nas travas de página ocorre quando vários threads simultaneamente tentarem adquirir travas sobre a mesma estrutura na memória, o que introduz um tempo de espera maior para a execução da consulta. No caso de contenção de e/s de pagelatch, quando os dados têm de ser acedidos a partir de armazenamento, este tempo de espera é ainda maior. Ele pode afetar consideravelmente o desempenho da carga de trabalho. Contenção de Pagelatch é o cenário mais comum de threads a aguardar entre si e competindo para recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz pagelatch detalhes de contenção. Pode usar essas informações como base para resolução de problemas.

Como um pagelatch é um mecanismo de controle interno da base de dados SQL, ele determina, automaticamente quando utilizá-los. Decisões de aplicação, incluindo o design do esquema podem afetar o comportamento de pagelatch devido ao comportamento determinístico travas.

É um método para manipular a contenção de bloqueio temporário substituir uma chave de índice seqüencial com uma chave nonsequential para distribuir uniformemente os inserções num intervalo de índice. Normalmente, uma coluna à esquerda no índice distribui a carga de trabalho proporcionalmente. Outro método a considerar é o particionamento de tabela. A criação de um hash de esquema com uma coluna calculada numa tabela particionada de particionamento é uma abordagem comum para mitigar a contenção de bloqueio temporário excessiva. No caso de contenção de e/s de pagelatch, apresentando índices ajuda a mitigar este problema de desempenho. 

Para obter mais informações, consulte [diagnosticar e resolver bloquear temporariamente a contenção no SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (download do PDF).

## <a name="missing-index"></a>Índice em falta

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho indica a atual degradação de desempenho de carga de trabalho da base de dados em comparação comparada a linha de base de sete dias nos últimos devido a um índice em falta.

Um índice é usado para acelerar o desempenho das consultas. Ele fornece acesso rápido aos dados da tabela, reduzindo o número de páginas de conjunto de dados que precisam de ser acedidos ou analisados.

Consultas específicas que causou uma degradação do desempenho são identificadas por meio desta deteção para as quais criar índices representaria uma vantagem para o desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz hashes de consulta para as consultas que foram identificadas para afetar o desempenho da carga de trabalho. Pode criar índices para estas consultas. Também pode otimizar ou remover estas consultas, se não forem necessários. É uma prática de bom desempenho evitar a consulta de dados que não usa.

> [!TIP]
> Sabia que inteligência incorporada da base de dados SQL pode gerenciar automaticamente os índices com melhor desempenho das suas bases de dados?
>
> Para a otimização de desempenho contínuo de base de dados SQL, recomendamos que ative [otimização automática da base de dados SQL](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência incorporada da base de dados SQL continuamente monitoriza a base de dados SQL e automaticamente adaptável e cria índices para seus bancos de dados.
>

## <a name="new-query"></a>Nova Consulta

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho indica que uma nova consulta foi detetada que está com desempenho ruim e afetar o desempenho da carga de trabalho em comparação comparado a linha de base de desempenho de sete dias.

Escrever uma consulta de bom desempenho, às vezes, pode ser uma tarefa desafiadora. Para obter mais informações sobre como escrever consultas, consulte [consultas SQL escrever](https://msdn.microsoft.com/library/bb264565.aspx). Para otimizar o desempenho da consulta existente, consulte [ajuste de consultas](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Resolução de problemas

O diagnóstico registar informações de saídas até duas novas a maioria dos consumo de CPU consultas, incluindo os hashes de consulta. Uma vez que a consulta detetada afeta o desempenho da carga de trabalho, pode otimizar a sua consulta. É boa prática obter apenas os dados que tem de utilizar. Também recomendamos que utilize consultas com uma cláusula WHERE. Recomendamos também que simplificar consultas complexas e dividi-los em consultas mais pequenas. Outra prática recomendada é dividir consultas em lote de grandes dimensões em consultas de lote mais pequenas. Introdução ao índices para novas consultas é normalmente uma boa prática para atenuar este problema de desempenho.

Considere a utilização [do Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Estatística de aumento de espera

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma degradação do desempenho de carga de trabalho em que são identificadas consultas fraco desempenho, em comparação com a linha de base de carga de trabalho de sete dias anteriores.

Neste caso, o sistema não é possível classificar as consultas de desempenho fraco em outras categorias de desempenho detetável padrão, mas detetada a estatística de espera responsável pela regressão. Por conseguinte, considera-as como consultas com *aumentou as estatísticas de espera*, onde também é exposta a estatística de espera responsável pela regressão. 

### <a name="troubleshooting"></a>Resolução de problemas

O diagnóstico registar informações de saídas em detalhes do tempo de espera maior e hashes de consulta das consultas afetadas.

Uma vez que o sistema com êxito não foi possível identificar a causa de raiz para as consultas de fraco desempenho, as informações de diagnóstico são um bom ponto de partida para solucionar problemas manualmente. Pode otimizar o desempenho dessas consultas. É uma boa prática obter apenas os dados que necessários para utilizar e para simplificar e dividir consultas complexas em menores. 

Para obter mais informações sobre a otimização do desempenho de consultas, consulte [ajuste de consultas](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contenção do TempDB

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição de desempenho da base de dados em que existe um afunilamento de threads que tentam aceder a recursos de tempDB. (Esta condição não está relacionado com e/s.) O cenário típico para este problema de desempenho está situada a centenas de consultas em simultâneo que todos os criarem, utilizarem e, em seguida, remover tabelas pequenas tempDB. O sistema detetou que o número de consultas em simultâneo, usando as tabelas de tempDB mesmo aumentado com significado estatístico suficiente para afetar o desempenho de base de dados em comparação comparado a linha de base de desempenho de sete dias anteriores.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os detalhes de contenção do tempDB. Pode utilizar as informações como o ponto de partida para resolução de problemas. Existem duas coisas que pode buscar a aliviar esse tipo de contenção e aumentar o débito da carga de trabalho geral: Pode deixar de utilizar as tabelas temporárias. Também pode usar tabelas com otimização de memória. 

Para obter mais informações, consulte [introdução às tabelas com otimização de memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Conjunto elástico insuficiência de DTU

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma degradação no desempenho de carga de trabalho de base de dados atual em comparação comparada a linha de base nos últimos sete dias. É devido a falta de disponíveis DTUs do conjunto elástico da sua subscrição. 

Recursos na base de dados SQL são normalmente denominados [recursos DTU](sql-database-purchase-models.md#dtu-based-purchasing-model), que consistem numa medida combinada de CPU e e/s (transações e dados de log e/s) de recursos. [Recursos do conjunto elástico do Azure](sql-database-elastic-pool.md) são utilizadas como agrupamento de recursos eDTU disponíveis partilhadas entre várias bases de dados para fins de dimensionamento. Quando os recursos eDTU disponíveis no seu conjunto elástico não são suficientemente grandes para suportar todas as bases de dados no conjunto, um problema de desempenho de insuficiência DTU do conjunto elástico é detetado pelo sistema.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz informações sobre o conjunto elástico, apresenta uma lista de bases de dados de consumo de DTU superior e fornece uma percentagem de DTU do conjunto utilizado pela base de dados estão consumindo a parte superior.

Uma vez que esta condição de desempenho está relacionado com várias bases de dados com o mesmo conjunto de eDTUs do conjunto elástico, os passos de resolução de problemas nos concentrar nas bases de dados de consumo de DTU superior. Pode reduzir a carga de trabalho nas bases de dados estão consumindo a parte superior, que inclui a otimização das consultas estão consumindo a parte superior nessas bases de dados. Também pode garantir que não são consultar os dados que não usa. Outra abordagem é otimizar aplicações ao utilizar as bases de dados de consumo de DTU superior e redistribuir a carga de trabalho entre várias bases de dados.

Se redução e a Otimização da carga de trabalho atual nas bases de dados de consumo de DTU principais não são possíveis, considere aumentar o seu escalão de preço de conjunto elástico. Aumento resulta no aumento das DTUs disponíveis no conjunto elástico.

## <a name="plan-regression"></a>Regressão do plano

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição em que a base de dados SQL utiliza um plano de execução de consultas inferior ao ideal. O plano inferior ao ideal normalmente faz com que a execução de consultas maior, que leva a esperar mais vezes para as consultas atuais e outras.

A base de dados do SQL determina o plano de execução de consulta com o menor custo para uma execução da consulta. Como o tipo de alteração de consultas e cargas de trabalho, por vezes, os planos existentes não são mais eficientes ou talvez a base de dados SQL não fez uma boa avaliação. Como uma questão de correção, planos de execução de consulta podem ser manualmente forçados.

Este padrão de desempenho detetável combina três casos diferentes de regressão do plano: nova regressão do plano, regressão do plano antigo e planos alterados da carga de trabalho. O tipo específico de regressão do plano que ocorreram é fornecido na *detalhes* propriedade no registo de diagnóstico.

A nova condição de regressão do plano de suporte de dados refere-se num Estado em que a base de dados SQL inicia um novo plano de execução da consulta que não seja tão eficiente quanto o plano antigo em execução. A condição de regressão do plano antigo refere-se para o estado para a base de dados SQL muda da utilização de um plano de novo e mais eficiente para o plano antigo, o que não é tão eficiente quanto o novo plano. A regressão de carga de trabalho de planos alterados existente refere-se para o estado em que o antigo e os novos planos continuamente alternam, com o balanço vai mais para o plano de fraco desempenho.

Para obter mais informações sobre regressões de plano, consulte [o que é o plano de regressão no SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico devolve o hashes de consulta, o ID do plano bom, a ID do plano ruim e o IDs de consulta. Pode usar essas informações como base para resolução de problemas.

Pode analisar o plano de onde é melhor efetuar para as suas consultas específicas que possa identificar com os hashes de consulta fornecidos. Depois de determinar que plano funciona melhor para as suas consultas, pode forçá-lo manualmente. 

Para obter mais informações, consulte [Saiba como o SQL Server impede regressões de plano](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Sabia que os planos de execução de consulta com melhor desempenho para as bases de dados pode gerenciar automaticamente a inteligência incorporada da base de dados SQL?
>
> Para a otimização de desempenho contínuo de base de dados SQL, recomendamos que ative [otimização automática da base de dados SQL](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência incorporada da base de dados SQL continuamente monitoriza a base de dados SQL e automaticamente adaptável e cria os planos de execução para as bases de dados de consulta com melhor desempenho.
>

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor de configuração do âmbito de base de dados

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição em que uma alteração na configuração de âmbito de base de dados faz com que regressão de desempenho que é detetado em comparação comparada o comportamento de carga de trabalho de base de dados de sete dias anteriores. Este padrão indica que uma alteração recente feita para a configuração de âmbito de base não parece ser vantajoso para desempenho da sua base de dados.

Alterações de configuração de âmbito de base de dados podem ser definidas para cada base de dados individual. Esta configuração é utilizada numa base caso a caso, para otimizar o desempenho individual da base de dados. As seguintes opções podem ser configuradas para cada base de dados individual: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e PROCEDURE_CACHE CLARA.

### <a name="troubleshooting"></a>Resolução de problemas

O diagnóstico de registo alterações de configuração de âmbito de base de saídas, foram feitas recentemente que causou uma degradação do desempenho em comparação comparada o comportamento de carga de trabalho de sete dias anteriores. Pode reverter as alterações de configuração para os valores anteriores. Também pode otimizar valor por valor até que seja atingido o nível de desempenho pretendidos. Pode copiar os valores de configuração de âmbito de base de dados de uma base de dados semelhante com desempenho satisfatório. Se não for possível resolver o desempenho, reverter para os valores predefinidos do padrão de base de dados SQL e tente ajustar a partir desta linha de base.

Para obter mais informações sobre como otimizar a configuração do âmbito de base de dados e a sintaxe de T-SQL sobre a alteração da configuração, consulte [configuração do âmbito de base de dados Alter (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente lentas

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição em que o cliente utilizando a base de dados SQL não é possível consumir o resultado da base de dados o mais rápido que a base de dados envia os resultados. Porque a base de dados SQL não é armazenar os resultados das consultas executadas num buffer, mais lento e aguarda que o cliente consumir os resultados de consulta transmitidos antes de continuar. Esta condição também pode estar relacionadas com a uma rede que não é suficientemente rápida o suficiente para transmitir as saídas da base de dados SQL para o cliente de consumo.

Esta condição é gerada apenas se uma regressão de desempenho é detectada em comparação com o comportamento de carga de trabalho de base de dados de sete dias anteriores. Este problema de desempenho foi detetado o apenas se ocorrer uma degradação do desempenho estatisticamente significativo em comparação com comportamento anterior de desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

Este padrão de desempenho detetável indica uma condição de lado do cliente. Resolução de problemas é necessária na aplicação do lado do cliente ou de rede do lado do cliente. O registo de diagnóstico produz os hashes de consulta e tempos de espera que parecem que está aguardando o máximo para o cliente aceder aos mesmos nas últimas duas horas. Pode usar essas informações como base para resolução de problemas.

Pode otimizar o desempenho da sua aplicação para consumo dessas consultas. Também pode considerar os problemas de latência de rede possível. Uma vez que o problema de degradação do desempenho se baseava em alteração na última linha de base do desempenho de sete dias, pode investigar se as alterações recentes de aplicação ou na rede da condição causado este evento de regressão de desempenho. 

## <a name="pricing-tier-downgrade"></a>Mudança para versão anterior do escalão de preço

### <a name="what-is-happening"></a>O que está acontecendo

Este padrão de desempenho detetável indica uma condição em que o escalão de preço da sua subscrição da base de dados SQL foi mudado. Devido à redução de recursos (DTUs) disponíveis para a base de dados, o sistema detetou uma queda no desempenho da base de dados atual, em comparação comparado a linha de base nos últimos sete dias.

Além disso, pode haver uma condição em que o escalão de preço da sua subscrição da base de dados SQL foi alterada uma versão anterior e, em seguida, atualizado para um escalão mais elevado num curto período de tempo. Deteção este temporária de degradação do desempenho é debitada na secção de detalhes do registo de diagnóstico, como uma mudança para versão anterior do escalão de preços e a atualização.

### <a name="troubleshooting"></a>Resolução de problemas

Se reduziu o escalão de preço e, portanto, as DTUs disponíveis para a base de dados SQL e estiver satisfeito com o desempenho, não há nada que precisa fazer. Se reduziu o escalão de preço e estiver não satisfeitos com o desempenho de base de dados SQL, reduza as cargas de trabalho de base de dados ou considere aumentar o escalão de preço para um nível superior.

## <a name="recommended-troubleshooting-flow"></a>Recomenda a resolução de problemas de fluxo

 Siga o fluxograma de uma abordagem recomendada para resolver problemas de desempenho através da utilização de informações inteligentes.

Acesso a informações inteligentes através do portal do Azure acedendo a análise de SQL do Azure. Tente localizar o alerta de desempenho de entrada e selecioná-lo. Identifica o que está acontecendo na página de detecções. Observe a análise da causa de raiz fornecido do problema, texto da consulta, tendências de tempo de consulta e evolução de incidente. Tentativa de resolver o problema ao utilizar a recomendação de informações inteligentes para mitigar o problema de desempenho. 

[![Fluxograma de resolução de problemas](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para transferir uma versão em PDF.

Informações inteligentes, normalmente, precisa de uma hora de tempo para executar a análise de causa raiz do problema de desempenho. Se não conseguir localizar o seu problema em informações inteligentes e é fundamental para si, utilize o Store de consulta para manualmente identificar a causa de raiz do problema de desempenho. (Normalmente, estes problemas são menos de um horas depois.) Para obter mais informações, consulte [monitorizar o desempenho ao utilizar o Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais [informações inteligentes](sql-database-intelligent-insights.md) conceitos.
- Utilize o [registo de diagnóstico de desempenho do Intelligent Insights Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitor [base de dados do Azure SQL através de análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Aprenda a [recolher e consumir dados de registo dos seus recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
