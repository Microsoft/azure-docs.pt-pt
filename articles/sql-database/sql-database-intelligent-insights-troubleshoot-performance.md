---
title: Solucionar problemas de desempenho com o Intelligent Insights
description: Intelligent Insights ajuda a solucionar problemas de desempenho do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b6ec1952d730b6515032572def65806a1ccbc0b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810373"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Solucionar problemas de desempenho do banco de dados SQL do Azure com Intelligent Insights

Esta página fornece informações sobre o banco de dados SQL do Azure e Instância Gerenciada problemas de desempenho detectados por meio do log de diagnóstico de desempenho [Intelligent insights](sql-database-intelligent-insights.md) banco de dados. A telemetria do log de diagnóstico pode ser transmitida para [Azure monitor logs](../azure-monitor/insights/azure-sql.md), [hubs de eventos do Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md), [armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage)ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

> [!NOTE]
> Para obter um rápido guia de solução de problemas de desempenho do banco de dados SQL usando Intelligent Insights, consulte o fluxograma de [solução de problemas recomendado](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) neste documento.
>

## <a name="detectable-database-performance-patterns"></a>Padrões de desempenho de banco de dados detectáveis

O Intelligent Insights detecta automaticamente problemas de desempenho com bancos de dados SQL e Instância Gerenciada com base em tempos de espera de execução de consulta, erros ou tempos limite. Ele gera padrões de desempenho detectados para o log de diagnóstico. Os padrões de desempenho detectáveis são resumidos na tabela a seguir.

| Padrões de desempenho detectáveis | Descrição do banco de dados SQL do Azure e pools elásticos | Descrição para bancos de dados no Instância Gerenciada |
| :------------------- | ------------------- | ------------------- |
| [Atingir limites de recursos](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | O consumo de recursos disponíveis (DTUs), threads de trabalho de banco de dados ou sessões de logon de banco de dados disponíveis na assinatura monitorado atingiu os limites. Isso está afetando o desempenho do banco de dados SQL. | O consumo de recursos de CPU está atingindo Instância Gerenciada limites. Isso está afetando o desempenho do banco de dados. |
| [Aumento da carga de trabalho](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detectado um aumento de carga de trabalho ou acumulação contínua de carga de trabalho no banco de dados. Isso está afetando o desempenho do banco de dados SQL. | O aumento da carga de trabalho foi detectado. Isso está afetando o desempenho do banco de dados. |
| [Pressão de memória](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Os trabalhadores que solicitaram concessões de memória precisam esperar por alocações de memória para quantidades estatisticamente significativas. Ou uma maior acumulação de trabalhadores que solicitou concessões de memória existe. Isso está afetando o desempenho do banco de dados SQL. | Os trabalhadores que solicitaram concessões de memória estão aguardando alocações de memória para uma quantidade de tempo estatisticamente significativa. Isso está afetando o desempenho do banco de dados. |
| [Bloqueio](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Foi detectado um bloqueio excessivo de banco de dados afetando o desempenho do banco de dados SQL. | Foi detectado um bloqueio excessivo de banco de dados afetando o desempenho do banco de dados. |
| [Maior MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A opção de grau máximo de paralelismo (MAXDOP) mudou afetando a eficiência da execução da consulta. Isso está afetando o desempenho do banco de dados SQL. | A opção de grau máximo de paralelismo (MAXDOP) mudou afetando a eficiência da execução da consulta. Isso está afetando o desempenho do banco de dados. |
| [Contenção de Pagelatch](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Vários threads estão tentando simultaneamente acessar as mesmas páginas de buffer de dados na memória, resultando em tempos de espera maiores e causando contenção de pagelatch. Isso está afetando o desempenho do banco de dados SQL. | Vários threads estão tentando simultaneamente acessar as mesmas páginas de buffer de dados na memória, resultando em tempos de espera maiores e causando contenção de pagelatch. Isso está afetando o desempenho do banco de dados. |
| [Índice ausente](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | O índice ausente foi detectado afetando o desempenho do banco de dados SQL. | O índice ausente foi detectado, afetando o desempenho do banco de dados. |
| [Nova consulta](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Foi detectada uma nova consulta afetando o desempenho geral do banco de dados SQL. | Foi detectada uma nova consulta afetando o desempenho geral do banco de dados. |
| [Maior estatística de espera](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Tempos de espera de banco de dados maiores detectados afetando o desempenho do banco de dados SQL. | Tempos de espera de banco de dados aumentados que afetam o desempenho do banco de dados. |
| [Contenção de TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários threads estão tentando acessar o mesmo recurso TempDB causando um afunilamento. Isso está afetando o desempenho do banco de dados SQL. | Vários threads estão tentando acessar o mesmo recurso TempDB causando um afunilamento. Isso está afetando o desempenho do banco de dados. |
| [Escassez de DTU do pool elástico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A escassez de eDTUs disponíveis no pool elástico está afetando o desempenho do banco de dados SQL. | Não disponível para Instância Gerenciada, pois ele usa o modelo vCore. |
| [Regressão do plano](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Novo plano ou uma alteração na carga de trabalho de um plano existente foi detectada. Isso está afetando o desempenho do banco de dados SQL. | Novo plano ou uma alteração na carga de trabalho de um plano existente foi detectada. Isso está afetando o desempenho do banco de dados. |
| [Alteração do valor de configuração no escopo do banco de dados](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | A alteração de configuração no banco de dados SQL foi detectada afetando o desempenho do banco de dados. | Foi detectada alteração de configuração no banco de dados que afeta o desempenho do banco de dados. |
| [Cliente lento](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | O cliente de aplicativo lento não pode consumir a saída do banco de dados rápido o suficiente. Isso está afetando o desempenho do banco de dados SQL. | O cliente de aplicativo lento não pode consumir a saída do banco de dados rápido o suficiente. Isso está afetando o desempenho do banco de dados. |
| [Downgrade de tipo de preço](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A ação de downgrade do tipo de preço diminuiu os recursos disponíveis. Isso está afetando o desempenho do banco de dados SQL. | A ação de downgrade do tipo de preço diminuiu os recursos disponíveis. Isso está afetando o desempenho do banco de dados. |

> [!TIP]
> Para otimizar o desempenho contínuo do banco de dados SQL, habilite o [ajuste automático do banco de dados SQL do Azure](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência interna do banco de dados SQL monitora continuamente o banco de dados SQL, ajusta os índices automaticamente e aplica as correções do plano de execução da consulta.
>

A seção a seguir descreve os padrões de desempenho detectáveis em mais detalhes.

## <a name="reaching-resource-limits"></a>Atingir limites de recursos

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável combina problemas de desempenho relacionados ao alcance de limites de recursos, limites de trabalho e limites de sessão disponíveis. Após a detecção desse problema de desempenho, um campo de descrição do log de diagnóstico indica se o problema de desempenho está relacionado aos limites de recurso, de trabalho ou de sessão.

Os recursos no banco de dados SQL normalmente são referidos a recursos de [DTU](sql-database-what-is-a-dtu.md) ou [vCore](sql-database-service-tiers-vcore.md) . O padrão de atingir limites de recursos é reconhecido quando detectada degradação de desempenho de consulta é causada por um dos limites de recursos medidos.

O recurso de limites de sessão denota o número de logons simultâneos disponíveis para o banco de dados SQL. Esse padrão de desempenho é reconhecido quando os aplicativos que estão conectados aos bancos de dados SQL atingiram o número de logons simultâneos disponíveis para ele. Se os aplicativos tentarem usar mais sessões do que as disponíveis em um banco de dados, o desempenho da consulta será afetado.

Atingir os limites de trabalho é um caso específico de atingir os limites de recursos, pois os trabalhadores disponíveis não são contados no uso de DTU ou vCore. Atingir os limites de trabalho em um banco de dados pode causar o aumento de tempos de espera específicos do recurso, o que resulta em degradação do desempenho da consulta.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera hashes de consulta de consultas que afetaram o desempenho e os percentuais de consumo de recursos. Você pode usar essas informações como um ponto de partida para otimizar a carga de trabalho do banco de dados. Em particular, você pode otimizar as consultas que afetam a degradação do desempenho adicionando índices. Ou você pode otimizar aplicativos com uma distribuição de carga de trabalho mais uniforme. Se não for possível reduzir as cargas de trabalho ou fazer otimizações, considere aumentar o tipo de preço da sua assinatura do banco de dados SQL para aumentar a quantidade de recursos disponíveis.

Se você tiver atingido os limites de sessão disponíveis, poderá otimizar seus aplicativos reduzindo o número de logons feitos no banco de dados. Se não for possível reduzir o número de logons de seus aplicativos para o banco de dados, considere aumentar o tipo de preço do seu banco de dados. Ou você pode dividir e mover seu banco de dados para vários bancos de dados para uma distribuição de carga de trabalho mais equilibrada.

Para obter mais sugestões sobre como resolver limites de sessão, consulte [como lidar com os limites de logons máximos do banco de dados SQL](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.

## <a name="workload-increase"></a>Aumento da carga de trabalho

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho identifica os problemas causados por um aumento de carga de trabalho ou, em sua forma mais grave, uma acumulação de carga de trabalho.

Essa detecção é feita por meio de uma combinação de várias métricas. A métrica básica medida é detectar um aumento na carga de trabalho em comparação com a linha de base da carga de trabalho anterior. A outra forma de detecção é baseada na medição de um grande aumento nos threads de trabalho ativos que é grande o suficiente para afetar o desempenho da consulta.

Em sua forma mais grave, a carga de trabalho pode ser continuamente compilada devido à incapacidade do banco de dados SQL de lidar com a carga de trabalho. O resultado é um tamanho de carga de trabalho que cresce continuamente, que é a condição de empilhamento de carga de trabalho. Devido a essa condição, o tempo que a carga de trabalho aguarda para execução aumenta. Essa condição representa um dos problemas de desempenho de banco de dados mais graves. Esse problema é detectado por meio do monitoramento do aumento no número de threads de trabalho anulados. 

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera o número de consultas cuja execução aumentou e o hash de consulta da consulta com a maior contribuição para o aumento da carga de trabalho. Você pode usar essas informações como um ponto de partida para otimizar a carga de trabalho. A consulta identificada como o maior colaborador do aumento da carga de trabalho é especialmente útil como ponto de partida.

Você pode considerar distribuir as cargas de trabalho mais uniformemente para o banco de dados. Considere otimizar a consulta que está afetando o desempenho adicionando índices. Você também pode distribuir sua carga de trabalho entre vários bancos de dados. Se essas soluções não forem possíveis, considere aumentar o tipo de preço da sua assinatura do banco de dados SQL para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Pressão de memória

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho atual do banco de dados causado por pressão de memória ou, em sua forma mais grave, uma condição de empilhamento de memória, em comparação com a linha de base de desempenho dos últimos sete dias.

A pressão de memória denota uma condição de desempenho na qual há um grande número de threads de trabalho solicitando concessões de memória no banco de dados SQL. O alto volume causa uma alta condição de utilização de memória na qual o banco de dados SQL não consegue alocar a memória com eficiência para todos os trabalhos que a solicitam. Um dos motivos mais comuns para esse problema está relacionado à quantidade de memória disponível para o banco de dados SQL por um lado. Por outro lado, um aumento na carga de trabalho causa o aumento nos threads e na pressão da memória.

A forma mais grave de pressão de memória é a condição de empilhamento de memória. Essa condição indica que um número maior de threads de trabalho está solicitando concessões de memória do que as consultas que liberam a memória. Esse número de threads de trabalho que solicitam concessões de memória também pode ser continuamente aumentado (empilhando up) porque o mecanismo de banco de dados SQL não pode alocar memória com eficiência suficiente para atender à demanda. A condição de empilhamento de memória representa um dos problemas de desempenho de banco de dados mais graves.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera os detalhes do repositório de objetos de memória com o auxiliar (ou seja, thread de trabalho) marcado como o motivo mais alto para o uso de memória alto e carimbos de data/hora relevantes. Você pode usar essas informações como base para a solução de problemas. 

Você pode otimizar ou remover consultas relacionadas aos auxiliares com o maior uso de memória. Você também pode verificar se não está consultando dados que não planeja usar. A prática recomendada é sempre usar uma cláusula WHERE em suas consultas. Além disso, recomendamos que você crie índices não clusterizados para buscar os dados em vez de examiná-los.

Você também pode reduzir a carga de trabalho otimizando ou distribuindo-a em vários bancos de dados. Ou você pode distribuir sua carga de trabalho entre vários bancos de dados. Se essas soluções não forem possíveis, considere aumentar o tipo de preço da sua assinatura do banco de dados SQL para aumentar a quantidade de recursos de memória disponíveis para o banco de dados.

Para obter sugestões de solução de problemas adicionais, consulte [concessões de memória reflexão sobre: o misterioso SQL Server consumidor de memória com muitos nomes](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Bloqueio

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica degradação no desempenho do banco de dados atual no qual o bloqueio excessivo do banco de dados é detectado em comparação com a linha de base de desempenho dos últimos sete dias. 

No RDBMS moderno, o bloqueio é essencial para implementar sistemas multithread em que o desempenho é maximizado com a execução de vários trabalhadores simultâneos e transações de banco de dados paralelas sempre que possível. O bloqueio neste contexto refere-se ao mecanismo de acesso interno no qual apenas uma única transação pode acessar exclusivamente as linhas, páginas, tabelas e arquivos necessários e não competir com outra transação para recursos. Quando a transação que bloqueou os recursos para uso é feita com elas, o bloqueio desses recursos é liberado, o que permite que outras transações acessem os recursos necessários. Para obter mais informações sobre bloqueio, consulte [Bloquear no mecanismo de banco de dados](https://msdn.microsoft.com/library/ms190615.aspx).

Se as transações executadas pelo mecanismo SQL estiverem esperando por períodos prolongados de tempo para acessar recursos bloqueados para uso, esse tempo de espera causará a lentidão do desempenho de execução da carga de trabalho. 

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera detalhes de bloqueio que você pode usar como base para a solução de problemas. Você pode analisar as consultas de bloqueio relatadas, ou seja, as consultas que apresentam a degradação do desempenho de bloqueio e removê-las. Em alguns casos, você pode ter êxito na otimização das consultas de bloqueio.

A maneira mais simples e segura de mitigar o problema é manter as transações curtas e reduzir a superfície de bloqueio das consultas mais caras. Você pode dividir um grande lote de operações em operações menores. A prática recomendada é reduzir a superfície de bloqueio de consulta tornando a consulta o mais eficiente possível. Reduza grandes verificações porque elas aumentam as chances de deadlocks e afetam negativamente o desempenho geral do banco de dados. Para consultas identificadas que causam bloqueio, você pode criar novos índices ou adicionar colunas ao índice existente para evitar verificações de tabela. 

Para obter mais sugestões, consulte [como resolver problemas de bloqueio causados por escalonamento de bloqueios no SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Maior MAXDOP

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que um plano de execução de consulta escolhido foi paralelizado mais do que deveria ter sido. O otimizador de consulta do banco de dados SQL pode melhorar o desempenho da carga de trabalho executando consultas em paralelo para acelerar as coisas sempre que possível. Em alguns casos, os trabalhadores paralelos que processam uma consulta passam mais tempo esperando uns aos outros para sincronizar e mesclar os resultados em comparação à execução da mesma consulta com menos trabalhadores paralelos, ou até mesmo em alguns casos, em comparação com um único thread de trabalho.

O sistema especialista analisa o desempenho atual do banco de dados em comparação com o período da linha de base. Determina se uma consulta em execução anteriormente está em execução mais lenta do que antes porque o plano de execução de consulta é mais paralelizado do que deveria ser.

A opção de configuração de servidor MAXDOP no banco de dados SQL é usada para controlar quantos núcleos de CPU podem ser usados para executar a mesma consulta em paralelo. 

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera hashes de consulta relacionados a consultas para as quais a duração da execução aumentou porque elas foram paralelizadas mais do que deveriam ter sido. O log também gera tempos de espera CXP. Esse tempo representa a hora em que um único thread do organizador/Coordenador (thread 0) está aguardando que todos os outros threads sejam concluídos antes de mesclar os resultados e avançar. Além disso, o log de diagnóstico gera os tempos de espera que as consultas de mau desempenho estavam aguardando na execução geral. Você pode usar essas informações como base para a solução de problemas.

Primeiro, otimize ou simplifique consultas complexas. A prática recomendada é dividir trabalhos de lote longos em menores. Além disso, certifique-se de que você criou índices para dar suporte às suas consultas. Você também pode impor manualmente o grau máximo de paralelismo (MAXDOP) para uma consulta que foi sinalizada como de baixo desempenho. Para configurar essa operação usando o T-SQL, consulte [Configurar a opção de configuração de servidor MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

A definição da opção de configuração de servidor MAXDOP como zero (0) como um valor padrão denota que o banco de dados SQL pode usar todos os núcleos de CPU lógicos disponíveis para paralelizar threads para executar uma única consulta. Definir MAXDOP como um (1) indica que apenas um núcleo pode ser usado para uma única execução de consulta. Em termos práticos, isso significa que o paralelismo está desativado. Dependendo do caso por caso, núcleos disponíveis para o banco de dados e informações de log de diagnóstico, você pode ajustar a opção MAXDOP para o número de núcleos usados para execução de consulta paralela que pode resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Contenção de Pagelatch

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica a degradação do desempenho da carga de trabalho do banco de dados atual devido à contenção de pagelatch em comparação com a linha de base de carga de trabalho de sete dias.

Travas são mecanismos de sincronização leves usados pelo banco de dados SQL para habilitar multithreading. Eles garantem a consistência das estruturas na memória que incluem índices, páginas de dados e outras estruturas internas.

Há muitos tipos de travas disponíveis no banco de dados SQL. Para fins de simplicidade, travas de buffer são usadas para proteger páginas na memória no pool de buffers. Travas de e/s são usadas para proteger páginas ainda não carregadas no pool de buffers. Sempre que os dados são gravados ou lidos em uma página no pool de buffers, um thread de trabalho precisa adquirir uma trava de buffer para a página primeiro. Sempre que um thread de trabalho tenta acessar uma página que ainda não está disponível no pool de buffers na memória, é feita uma solicitação de e/s para carregar as informações necessárias do armazenamento. Essa sequência de eventos indica uma forma mais grave de degradação do desempenho.

A contenção nas travas de página ocorre quando vários threads tentam simultaneamente adquirir travas na mesma estrutura na memória, o que introduz um tempo de espera maior para a execução da consulta. No caso de contenção de e/s de pagelatch, quando os dados precisam ser acessados do armazenamento, esse tempo de espera é ainda maior. Isso pode afetar consideravelmente o desempenho da carga de trabalho. A contenção de Pagelatch é o cenário mais comum de threads aguardando uns aos outros e competindo por recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera detalhes de contenção de pagelatch. Você pode usar essas informações como base para a solução de problemas.

Como um pagelatch é um mecanismo de controle interno do banco de dados SQL, ele determina automaticamente quando usá-los. Decisões de aplicativo, incluindo design de esquema, podem afetar o comportamento de pagelatch devido ao comportamento determinístico de travas.

Um método para lidar com a contenção de trava é substituir uma chave de índice sequencial por uma chave não sequencial para distribuir uniformemente inserções em um intervalo de índice. Normalmente, uma coluna à esquerda no índice distribui a carga de trabalho proporcionalmente. Outro método a ser considerado é o particionamento de tabela. Criar um esquema de particionamento de hash com uma coluna computada em uma tabela particionada é uma abordagem comum para mitigar a contenção excessiva de trava. No caso da contenção de e/s pagelatch, a introdução de índices ajuda a atenuar esse problema de desempenho. 

Para obter mais informações, consulte [diagnosticar e resolver a contenção de trava em SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (download de PDF).

## <a name="missing-index"></a>Índice ausente

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica a degradação de desempenho da carga de trabalho do banco de dados atual em comparação com a linha de base de sete dias anteriores devido a um índice ausente.

Um índice é usado para acelerar o desempenho de consultas. Ele fornece acesso rápido a dados de tabela, reduzindo o número de páginas de conjunto de dados que precisam ser visitadas ou verificadas.

Consultas específicas que causaram degradação de desempenho são identificadas por meio dessa detecção para a qual a criação de índices seria benéfica para o desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera hashes de consulta para as consultas que foram identificadas para afetar o desempenho da carga de trabalho. Você pode criar índices para essas consultas. Você também pode otimizar ou remover essas consultas se elas não forem necessárias. Uma boa prática de desempenho é evitar a consulta de dados que você não usa.

> [!TIP]
> Você sabia que a inteligência interna do banco de dados SQL pode gerenciar automaticamente os índices de melhor desempenho para seus bancos de dados?
>
> Para otimizar o desempenho contínuo do banco de dados SQL, recomendamos que você habilite o [ajuste automático do banco de dados SQL](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência interna do banco de dados SQL monitora continuamente o banco de dados SQL e ajusta e cria índices automaticamente para seus bancos.
>

## <a name="new-query"></a>Nova consulta

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho indica que é detectada uma nova consulta que está sendo executada de maneira ruim e afeta o desempenho da carga de trabalho em comparação com a linha de base de desempenho de sete dias.

Escrever uma consulta de bom desempenho às vezes pode ser uma tarefa desafiadora. Para obter mais informações sobre como escrever consultas, consulte [escrevendo consultas SQL](https://msdn.microsoft.com/library/bb264565.aspx). Para otimizar o desempenho de consulta existente, consulte [ajuste de consulta](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera informações até duas novas consultas que mais consomem CPU, incluindo seus hashes de consulta. Como a consulta detectada afeta o desempenho da carga de trabalho, você pode otimizar sua consulta. A prática recomendada é recuperar apenas os dados que você precisa usar. Também recomendamos o uso de consultas com uma cláusula WHERE. Também recomendamos que você simplifique consultas complexas e divida-as em consultas menores. Outra prática recomendada é dividir as consultas de lote grandes em consultas de lote menores. A introdução de índices para novas consultas é normalmente uma boa prática para atenuar esse problema de desempenho.

Considere usar o [análise de desempenho de consultas do banco de dados SQL do Azure](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Maior estatística de espera

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação do desempenho da carga de trabalho na qual as consultas de mau desempenho são identificadas em comparação com a linha de base de carga de trabalho de sete dias anterior.

Nesse caso, o sistema não pode classificar as consultas de mau desempenho em qualquer outra categoria padrão que possa ser detectada, mas detectou a estatística de espera responsável pela regressão. Portanto, ele os considera como consultas com *Estatísticas de espera maiores*, em que a estatística de espera responsável pela regressão também é exposta. 

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera informações sobre o aumento de detalhes de tempo de espera e hashes de consulta das consultas afetadas.

Como o sistema não conseguiu identificar com êxito a causa raiz das consultas de mau desempenho, as informações de diagnóstico são um bom ponto de partida para a solução de problemas manual. Você pode otimizar o desempenho dessas consultas. Uma prática recomendada é buscar apenas os dados que você precisa usar e para simplificar e dividir consultas complexas em partes menores. 

Para obter mais informações sobre como otimizar o desempenho da consulta, consulte [ajuste de consulta](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contenção de TempDB

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição de desempenho de banco de dados em que um afunilamento de threads tentando acessar os recursos tempDB existe. (Essa condição não está relacionada a e/s.) O cenário típico para esse problema de desempenho é centenas de consultas simultâneas que criam, usam e depois descartam pequenas tabelas tempDB. O sistema detectou que o número de consultas simultâneas usando as mesmas tabelas tempDB aumentou com um significado estatístico suficiente para afetar o desempenho do banco de dados em comparação com a linha de base de desempenho dos últimos sete dias.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera detalhes de contenção de tempDB. Você pode usar as informações como o ponto de partida para a solução de problemas. Há duas coisas que você pode buscar para aliviar esse tipo de contenção e aumentar a taxa de transferência da carga de trabalho geral: você pode parar de usar as tabelas temporárias. Você também pode usar tabelas com otimização de memória. 

Para obter mais informações, consulte [Introduction to Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Escassez de DTU do pool elástico

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma degradação no desempenho atual da carga de trabalho do banco de dados em comparação com a linha de base de sete dias anterior. É devido à falta de DTUs disponíveis no pool elástico de sua assinatura. 

Os recursos no banco de dados SQL são normalmente chamados de [recursos de DTU](sql-database-purchase-models.md#dtu-based-purchasing-model), que consistem em uma medida combinada de recursos de CPU e e/s (dados e e/s de log de transações). Os [recursos do pool elástico do Azure](sql-database-elastic-pool.md) são usados como um pool de recursos de eDTU disponíveis compartilhados entre vários bancos de dados para fins de dimensionamento. Quando os recursos de eDTU disponíveis em seu pool elástico não são suficientemente grandes para dar suporte a todos os bancos de dados no pool, um problema de desempenho de escassez de DTU do pool elástico é detectado pelo sistema.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera informações sobre o pool elástico, lista os principais bancos de dados que consomem DTU e fornece um percentual do DTU do pool usado pelo banco de dados de consumo superior.

Como essa condição de desempenho está relacionada a vários bancos de dados usando o mesmo pool de eDTUs no pool elástico, as etapas de solução de problemas se concentram nos principais bancos de dados que consomem DTU. Você pode reduzir a carga de trabalho nos bancos de dados que mais consomem, o que inclui a otimização das consultas de alto consumo nesses bancos de dados. Você também pode garantir que não esteja consultando dados que você não usa. Outra abordagem é otimizar os aplicativos usando os principais bancos de dados que consomem DTU e redistribuir a carga de trabalho entre vários bancos de dados.

Se a redução e a otimização da carga de trabalho atual em seus principais bancos de dados que consomem DTU não forem possíveis, considere aumentar o tipo de preço do pool elástico. Esse aumento resulta no aumento das DTUs disponíveis no pool elástico.

## <a name="plan-regression"></a>Regressão do plano

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável denota uma condição na qual o banco de dados SQL utiliza um plano de execução de consulta de qualidade inferior. O plano de qualidade inferior geralmente causa uma maior execução de consulta, o que leva a tempos de espera mais longos para as consultas atuais e outras.

O banco de dados SQL determina o plano de execução de consulta com o menor custo para uma execução de consulta. À medida que o tipo de consultas e cargas de trabalho mudam, às vezes os planos existentes não são mais eficientes, ou talvez o banco de dados SQL não tenha uma boa avaliação. Como uma questão de correção, os planos de execução de consulta podem ser forçados manualmente.

Esse padrão de desempenho detectável combina três casos diferentes de regressão de plano: nova regressão do plano, regressão do plano antigo e carga de trabalho alterada de planos existentes. O tipo específico de regressão de plano que ocorreu é fornecido na propriedade *Details* no log de diagnóstico.

A nova condição de regressão do plano refere-se a um estado no qual o banco de dados SQL começa a executar um novo plano de execução de consulta que não é tão eficiente quanto o plano antigo. A condição de regressão do plano antigo refere-se ao estado quando o banco de dados SQL alterna do uso de um plano novo e mais eficiente para o plano antigo, o que não é tão eficiente quanto o novo plano. Os planos existentes de regressão de carga de trabalho se referem ao estado em que os planos antigos e novos são alternados continuamente, com o saldo indo mais para o plano de mau desempenho.

Para obter mais informações sobre regressões de plano, consulte [o que é regressão de plano em SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera os hashes de consulta, boa ID de plano, ID de plano inválida e IDs de consulta. Você pode usar essas informações como base para a solução de problemas.

Você pode analisar qual plano é melhor desempenho para suas consultas específicas que você pode identificar com os hashes de consulta fornecidos. Depois de determinar qual plano funciona melhor para suas consultas, você pode forçá-lo manualmente. 

Para obter mais informações, consulte [saiba como SQL Server impede regressões de plano](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Você sabia que a inteligência interna do banco de dados SQL pode gerenciar automaticamente os planos de execução de consulta de melhor desempenho para seus bancos de dados?
>
> Para otimizar o desempenho contínuo do banco de dados SQL, recomendamos que você habilite o [ajuste automático do banco de dados SQL](sql-database-automatic-tuning.md). Esse recurso exclusivo de inteligência interna do banco de dados SQL monitora continuamente o banco de dados SQL e ajusta e cria automaticamente os planos de execução de consulta de melhor desempenho para seus bancos de dados.
>

## <a name="database-scoped-configuration-value-change"></a>Alteração do valor de configuração no escopo do banco de dados

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que uma alteração na configuração no escopo do banco de dados causa a regressão de desempenho que é detectada em comparação com o comportamento de carga de trabalho do banco de dados de sete dias anterior. Esse padrão denota que uma alteração recente feita na configuração no escopo do banco de dados não parece ser benéfica ao desempenho do banco de dados.

As alterações de configuração no escopo do banco de dados podem ser definidas para cada banco de dados individual. Essa configuração é usada em uma base caso a caso para otimizar o desempenho individual do banco de dados. As seguintes opções podem ser configuradas para cada banco de dados individual: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e limpar PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Resolução de problemas

O log de diagnóstico gera alterações de configuração no escopo do banco de dados que foram feitas recentemente que causaram uma degradação de desempenho em comparação com o comportamento de carga de trabalho de sete dias anterior. Você pode reverter as alterações de configuração para os valores anteriores. Você também pode ajustar valor por valor até que o nível de desempenho desejado seja atingido. Você pode copiar valores de configuração de escopo de banco de dados de um banco de dados semelhante com desempenho satisfatório. Se não for possível solucionar o problema de desempenho, reverta para os valores padrão do banco de dados SQL padrão e tente ajustar o início dessa linha de base.

Para obter mais informações sobre como otimizar a configuração no escopo do banco de dados e a sintaxe do T-SQL na alteração da configuração, consulte [ALTER DATABASE-scopeed Configuration (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente lento

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o cliente que usa o banco de dados SQL não pode consumir a saída do banco de dados tão rápido quanto o banco de dados envia os resultados. Como o banco de dados SQL não está armazenando os resultados das consultas executadas em um buffer, ele fica lento e aguarda o cliente consumir as saídas de consulta transmitidas antes de continuar. Essa condição também pode estar relacionada a uma rede que não é suficientemente rápida para transmitir saídas do banco de dados SQL para o cliente consumidor.

Essa condição será gerada somente se uma regressão de desempenho for detectada em comparação com o comportamento de carga de trabalho do banco de dados de sete dias anterior. Esse problema de desempenho será detectado somente se ocorrer uma degradação de desempenho estatisticamente significativa em comparação com o comportamento de desempenho anterior.

### <a name="troubleshooting"></a>Resolução de problemas

Esse padrão de desempenho detectável indica uma condição do lado do cliente. A solução de problemas é necessária no aplicativo do cliente ou na rede do lado do cliente. O log de diagnóstico gera os hashes de consulta e os tempos de espera que parecem estar esperando o mais para o cliente consumi-los nas últimas duas horas. Você pode usar essas informações como base para a solução de problemas.

Você pode otimizar o desempenho do seu aplicativo para o consumo dessas consultas. Você também pode considerar possíveis problemas de latência de rede. Como o problema de degradação do desempenho foi baseado na alteração na linha de base de desempenho dos últimos sete dias, você pode investigar se as alterações recentes do aplicativo ou da condição de rede causaram esse evento de regressão de desempenho. 

## <a name="pricing-tier-downgrade"></a>Downgrade de tipo de preço

### <a name="what-is-happening"></a>O que está acontecendo

Esse padrão de desempenho detectável indica uma condição em que o tipo de preço da sua assinatura do banco de dados SQL foi desatualizado. Devido à redução de recursos (DTUs) disponíveis para o banco de dados, o sistema detectou uma queda no desempenho do banco de dados atual em comparação com a linha de base dos últimos sete dias.

Além disso, pode haver uma condição na qual o tipo de preço da sua assinatura do banco de dados SQL tenha sido rebaixado e, em seguida, atualizado para uma camada mais alta em um curto período de tempo. A detecção dessa degradação de desempenho temporária é emitida na seção de detalhes do log de diagnóstico como um downgrade de tipo de preço e uma atualização.

### <a name="troubleshooting"></a>Resolução de problemas

Se você reduziu o tipo de preço e, portanto, as DTUs disponíveis para o banco de dados SQL e estiver satisfeito com o desempenho, não há nada que você precise fazer. Se você reduziu seu tipo de preço e não está satisfeito com o desempenho do banco de dados SQL, reduza suas cargas de trabalho de banco de dados ou considere aumentar o tipo de preço para um nível mais alto.

## <a name="recommended-troubleshooting-flow"></a>Fluxo de solução de problemas recomendado

 Siga o fluxograma para obter uma abordagem recomendada para solucionar problemas de desempenho usando Intelligent Insights.

Acesse Intelligent Insights por meio do portal do Azure acessando Análise de SQL do Azure. Tente localizar o alerta de desempenho de entrada e selecione-o. Identifique o que está acontecendo na página detecções. Observe a análise da causa raiz fornecida do problema, o texto da consulta, as tendências do tempo de consulta e a evolução do incidente. Tente resolver o problema usando a recomendação Intelligent Insights para atenuar o problema de desempenho. 

[fluxograma de solução de problemas de ![](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para baixar uma versão em PDF.

Geralmente, a Intelligent Insights precisa de uma hora para executar a análise da causa raiz do problema de desempenho. Se você não conseguir localizar seu problema no Intelligent Insights e for essencial para você, use o Repositório de Consultas para identificar manualmente a causa raiz do problema de desempenho. (Normalmente, esses problemas têm menos de uma hora.) Para obter mais informações, consulte [monitorar o desempenho usando o repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os conceitos de [Intelligent insights](sql-database-intelligent-insights.md) .
- Use o [Intelligent insights log de diagnóstico de desempenho do banco de dados SQL do Azure](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitore o [banco de dados SQL do Azure usando análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [coletar e consumir dados de log de seus recursos do Azure](../azure-monitor/platform/resource-logs-overview.md).
