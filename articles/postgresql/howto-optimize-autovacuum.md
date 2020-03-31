---
title: Otimizar o autovácuo - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode otimizar o autovácuo numa Base de Dados Azure para PostgreSQL - Servidor Único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770191"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Otimize o autovácuo numa Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como otimizar eficazmente o autovácuo numa Base de Dados Azure para servidor PostgreSQL.

## <a name="overview-of-autovacuum"></a>Visão geral do autovácuo
PostgreSQL utiliza controlo de condivisa multiversão (MVCC) para permitir uma maior conmoeda de base de dados. Cada atualização resulta numa inserção e eliminação, e cada eliminação resulta em linhas sendo marcadas suavemente para eliminação. A marcação suave identifica tuples mortos que serão purgados mais tarde. Para executar estas tarefas, a PostgreSQL executa um trabalho de vácuo.

Um trabalho de vácuo pode ser acionado manualmente ou automaticamente. Existem mais tuples mortos quando a base de dados experimenta uma atualização pesada ou elimina operações. Há menos tuples mortos quando a base de dados está inativa. É necessário aspirar com mais frequência quando a carga da base de dados é pesada, o que torna os trabalhos de vácuo de funcionamento *manualmente* inconvenientes.

O autovácuo pode ser configurado e beneficia da afinação. Os valores predefinidos que a PostgreSQL envia com a tentativa de garantir que o produto funciona em todos os tipos de dispositivos. Estes dispositivos incluem Raspberry Pis. Os valores de configuração ideais dependem do:
- Total de recursos disponíveis, tais como SKU e tamanho de armazenamento.
- Utilização de recursos.
- Características individuais do objeto.

## <a name="autovacuum-benefits"></a>Benefícios do autovácuo
Se não aspirar de vez em quando, as tuples mortas que se acumulam podem resultar em:
- Os dados bloat, tais como bases de dados e tabelas maiores.
- Índices sub-óptimos maiores.
- Aumento do I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor bloat com consultas de autovácuo
A seguinte consulta de amostra foi concebida para identificar o número de tuples mortos e vivos numa tabela chamada XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurações de autovácuo
Os parâmetros de configuração que controlam o autovácuo baseiam-se em respostas a duas questões-chave:
- Quando deve começar?
- Quanto deve limpar depois de começar?

Aqui estão alguns parâmetros de configuração de autovácuo que pode atualizar com base nas perguntas anteriores, juntamente com alguma orientação.

Parâmetro|Descrição|Valor predefinido
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuples atualizados ou eliminados necessários para desencadear uma operação de vácuo em qualquer tabela. O padrão é de 50 tuples. Defina este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a definição para tabelas individuais, altere os parâmetros de armazenamento da mesa.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela para adicionar à autovacuum_vacuum_threshold ao decidir se desencadeia uma operação de vácuo. O padrão é de 0,2, que é 20 por cento do tamanho da mesa. Defina este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a definição para tabelas individuais, altere os parâmetros de armazenamento da mesa.|5 por cento
autovacuum_vacuum_cost_limit|Especifica o valor-limite de custo utilizado nas operações automáticas de vácuo. Se -1 for especificado, que é o padrão, o valor vacuum_cost_limit regular é usado. Se houver mais de um trabalhador, o valor é distribuído proporcionalmente entre os trabalhadores do autovácuo em execução. A soma dos limites para cada trabalhador não excede o valor desta variável. Defina este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a definição para tabelas individuais, altere os parâmetros de armazenamento da mesa.|-1
autovacuum_vacuum_cost_delay|Especifica o valor do atraso de custoutilizado nas operações automáticas de vácuo. Se -1 for especificado, o valor vacuum_cost_delay regular é utilizado. O valor padrão é de 20 milissegundos. Defina este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a definição para tabelas individuais, altere os parâmetros de armazenamento da mesa.|20 ms
autovacuum_nap_time|Especifica o atraso mínimo entre o autovácuo em qualquer base de dados. Em cada ronda, o daemon examina a base de dados e emite comandos DE VÁCUO e ANÁLISE conforme necessário para tabelas nessa base de dados. O atraso é medido em segundos, e o padrão é de um minuto (1 min). Defina este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor.|15 s
autovacuum_max_workers|Especifica o número máximo de processos de autovácuo, com a lém do lançador de autovácuo, que podem ser executados a qualquer momento. O padrão é três. Defina este parâmetro apenas no início do servidor.|3

Para anular as definições para tabelas individuais, altere os parâmetros de armazenamento da mesa. 

## <a name="autovacuum-cost"></a>Custo do autovácuo
Aqui estão os "custos" de funcionamento de uma operação de vácuo:

- As páginas de dados em que o vácuo funciona estão bloqueadas.
- A computação e a memória são usadas quando um trabalho de vácuo está a funcionar.

Como resultado, não faça trabalhos de vácuo com demasiada frequência ou com demasiada frequência. Um trabalho de vácuo precisa de se adaptar à carga de trabalho. Teste todas as alterações do parâmetro de autovácuo devido às trocas de cada um.

## <a name="autovacuum-start-trigger"></a>Gatilho de arranque de vácuo automático
O autovácuo é acionado quando o número de tuples mortos excede autovacuum_vacuum_threshold + autovacuum_vacuum_scale_fator * reltuples. Aqui, reltuples é uma constante.

A limpeza do autovácuo deve acompanhar a carga da base de dados. Caso contrário, poderá ficar sem armazenamento e experimentar uma desaceleração geral das consultas. Amortizada ao longo do tempo, a taxa a que uma operação de vácuo limpa tuples mortos deve igualar a taxa a que são criados tuples mortos.

Bases de dados com muitas atualizações e eliminações têm mais tuples mortos e precisam de mais espaço. Geralmente, as bases de dados com muitas atualizações e exclusões beneficiam de baixos valores de autovacuum_vacuum_scale_fator e autovacuum_vacuum_threshold. Os baixos valores impedem a acumulação prolongada de tuples mortos. Pode utilizar valores mais elevados para ambos os parâmetros com bases de dados mais pequenas porque a necessidade de aspirar é menos urgente. O aspirador frequente tem o custo da computação e da memória.

O fator de escala padrão de 20% funciona bem em tabelas com uma baixa percentagem de tuples mortos. Não funciona bem em mesas com uma alta percentagem de tuples mortos. Por exemplo, numa tabela de 20 GB, este fator de escala traduz-se em 4 GB de tuples mortos. Numa mesa de 1-TB, são 200 GB de tuples mortos.

Com o PostgreSQL, pode definir estes parâmetros ao nível da tabela ou ao nível da instância. Hoje, pode definir estes parâmetros ao nível da tabela apenas na Base de Dados Azure para PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Estimar o custo do auto-vácuo
O funcionamento do autovácuo é "dispendioso", e existem parâmetros para controlar o tempo de funcionamento das operações de vácuo. Os seguintes parâmetros ajudam a estimar o custo do vácuo de funcionamento:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de vácuo lê páginas físicas e verifica se há tuples mortos. Cada página em shared_buffers é considerada como tendo um custo de 1 (vacuum_cost_page_hit). Todas as outras páginas são consideradas como tendo um custo de 20 (vacuum_cost_page_dirty), se existirem tuples mortos, ou 10 (vacuum_cost_page_miss), se não existirem tuples mortos. A operação de vácuo para quando o processo excede o autovacuum_vacuum_cost_limit. 

Após o limite ser atingido, o processo dorme durante a duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de recomeçar. Se o limite não for atingido, o autovácuo começa após o valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam a quantidade de limpeza de dados permitida por unidade de tempo. Note que os valores predefinidos são demasiado baixos para a maioria dos níveis de preços. Os valores ideais para estes parâmetros são dependentes de preços e devem ser configurados em conformidade.

O parâmetro autovacuum_max_workers determina o número máximo de processos de autovácuo que podem funcionar simultaneamente.

Com o PostgreSQL, pode definir estes parâmetros ao nível da tabela ou ao nível da instância. Hoje, pode definir estes parâmetros ao nível da tabela apenas na Base de Dados Azure para PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Otimizar o autovácuo por tabela
Pode configurar todos os parâmetros de configuração anteriores por tabela. Segue-se um exemplo:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

O autovácuo é um processo sincronizado por mesa. Quanto maior for a percentagem de tuples mortos que uma mesa tem, maior é o "custo" para o auto-vácuo. Pode dividir tabelas que tenham uma alta taxa de atualizações e elimina em várias tabelas. Dividir tabelas ajuda a paralelar ao autovácuo e a reduzir o "custo" para completar o autovácuo numa tabela. Pode também aumentar o número de trabalhadores paralelos do autovácuo para garantir que os trabalhadores sejam programados de forma liberal.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como usar e afinar o autovácuo, consulte a seguinte documentação PostgreSQL:

 - [Capítulo 18, Configuração do Servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Capítulo 24, tarefas de manutenção da base de dados de rotina](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
