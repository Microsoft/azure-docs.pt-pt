---
title: Otimizar autovacuum - Base de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode otimizar o autovacuum numa Base de Dados Azure para PostgreSQL - Servidor Único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012566"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Otimize o autovacuum numa base de dados Azure para PostgreSQL - Servidor Único

Este artigo descreve como otimizar eficazmente o autovacuum numa Base de Dados Azure para servidor PostgreSQL.

## <a name="overview-of-autovacuum"></a>Visão geral do autovacuum

O PostgreSQL utiliza o controlo de conuncy multiversão (MVCC) para permitir uma maior conucreção da base de dados. Cada atualização resulta numa inserção e eliminação, e cada eliminação resulta em linhas marcadas suavemente para eliminação. A marcação suave identifica tuples mortos que serão purgados mais tarde. Para realizar estas tarefas, a PostgreSQL executa um trabalho de vácuo.

Uma aspiração pode ser acionada manualmente ou automaticamente. Existem mais tuples mortos quando a base de dados experimenta operações de atualização ou eliminação pesadas. Menos tuples mortos existem quando a base de dados está inativa. É necessário aspirar com mais frequência quando a carga da base de dados é pesada, o que torna os trabalhos de vácuo *manualmente* inconvenientes.

O autovacuum pode ser configurado e beneficia da afinação. Os valores predefinidos que a PostgreSQL envia tentam garantir que o produto funciona em todos os tipos de dispositivos. Estes dispositivos incluem Raspberry Pis. Os valores de configuração ideais dependem do seguinte:

- Recursos totais disponíveis, como SKU e tamanho de armazenamento.
- Utilização de recursos.
- Características individuais do objeto.

## <a name="autovacuum-benefits"></a>Benefícios autovacuum

Se não aspirar de vez em quando, os tuples mortos que se acumulam podem resultar em:

- Inchar dados, como bases de dados e tabelas maiores.
- Índices sub-ideais maiores.
- Aumento de E/S.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor bloat com consultas de autovacuum

A seguinte consulta de amostra é projetada para identificar o número de tuples mortos e vivos numa tabela chamada XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Configurações de autovacuum

Os parâmetros de configuração que controlam o autovacuum baseiam-se em respostas a duas questões-chave:

- Quando deve começar?
- Quanto deve limpar depois de começar?

Aqui estão alguns parâmetros de configuração de autovacuum que pode atualizar com base nas perguntas anteriores, juntamente com algumas orientações.

Parâmetro|Descrição|Valor predefinido
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuples atualizados ou eliminados necessários para desencadear uma operação de vácuo em qualquer tabela. O padrão é de 50 tuples. Desfie este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a regulação para tabelas individuais, altere os parâmetros de armazenamento da tabela.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela para adicionar ao autovacuum_vacuum_threshold ao decidir se deve desencadear uma operação de vácuo. O padrão é 0.2, que é 20 por cento do tamanho da mesa. Desfie este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a regulação para tabelas individuais, altere os parâmetros de armazenamento da tabela.|0,2
autovacuum_vacuum_cost_limit|Especifica o valor-limite de custo utilizado nas operações automáticas de vácuo. Se for especificado -1, que é o padrão, é utilizado o valor regular vacuum_cost_limit. Se houver mais de um trabalhador, o valor é distribuído proporcionalmente entre os trabalhadores autovacuum em funcionamento. A soma dos limites para cada trabalhador não excede o valor desta variável. Desfie este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a regulação para tabelas individuais, altere os parâmetros de armazenamento da tabela.|-1
autovacuum_vacuum_cost_delay|Especifica o valor do atraso de custo utilizado nas operações automáticas de vácuo. Se for especificado -1, utiliza-se o valor regular vacuum_cost_delay. O valor predefinido é de 20 milissegundos. Desfie este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor. Para anular a regulação para tabelas individuais, altere os parâmetros de armazenamento da tabela.|20 ms
autovacuum_naptime | Especifica o atraso mínimo entre autovacuum em qualquer base de dados. Em cada ronda, o daemon examina a base de dados e emite comandos VACUUM e ANALYZE conforme necessário para as tabelas nessa base de dados. O atraso é medido em segundos. Desfie este parâmetro apenas no ficheiro postgresql.conf ou na linha de comando do servidor.| 15 s
autovacuum_max_workers | Especifica o número máximo de processos de autovacuum, que não o lançador autovacuum, que pode ser executado a qualquer momento. O padrão é três. Desa esta definição deste parâmetro apenas no início do servidor.|3

Para anular as definições para tabelas individuais, altere os parâmetros de armazenamento da tabela.

## <a name="autovacuum-cost"></a>Custo do autovacuum

Aqui estão os "custos" de funcionamento de uma operação de vácuo:

- As páginas de dados em que o vácuo funciona estão bloqueadas.
- O cálculo e a memória são utilizados quando um trabalho de vácuo está a funcionar.

Como resultado, não faça trabalhos de vácuo com demasiada frequência ou com demasiada frequência. Um trabalho de vácuo tem de se adaptar à carga de trabalho. Teste todas as alterações dos parâmetros de autovacuum devido às trocas de cada um.

## <a name="autovacuum-start-trigger"></a>Gatilho de arranque de autovacuum

O autovacuum é acionado quando o número de tuples mortos excede autovacuum_vacuum_threshold + autovacuum_vacuum_scale_fator * reltuples. Aqui, os reltuples são uma constante.

A limpeza do autovacuum deve acompanhar a carga da base de dados. Caso contrário, poderá ficar sem armazenamento e experimentar uma desaceleração geral das consultas. Amortizada ao longo do tempo, a velocidade a que uma operação de vácuo limpa os tuples mortos deve ser igual à velocidade a que são criados tuples mortos.

As bases de dados com muitas atualizações e eliminações têm mais tuples mortos e precisam de mais espaço. Geralmente, as bases de dados com muitas atualizações e eliminações beneficiam de baixos valores de autovacuum_vacuum_scale_fator e autovacuum_vacuum_threshold. Os valores baixos impedem a acumulação prolongada de tuples mortos. Pode utilizar valores mais elevados para ambos os parâmetros com bases de dados mais pequenas porque a necessidade de aspirar é menos urgente. A aspiração frequente tem o custo do cálculo e da memória.

O fator de escala padrão de 20% funciona bem em tabelas com uma baixa percentagem de tuples mortos. Não funciona bem em mesas com uma alta percentagem de tuples mortos. Por exemplo, numa tabela de 20 GB, este fator de escala traduz-se em 4 GB de tuples mortos. Numa mesa de 1-TB, são 200 GB de tuples mortos.

Com o PostgreSQL, pode definir estes parâmetros ao nível da tabela ou ao nível de instância. Hoje em dia, pode definir estes parâmetros ao nível da tabela apenas na Base de Dados Azure para PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Estimativa do custo do autovacuum

A execução do autovacuum é "dispendiosa", e existem parâmetros para controlar o tempo de funcionamento das operações de vácuo. Os seguintes parâmetros ajudam a estimar o custo do vácuo de funcionamento:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de vácuo lê páginas físicas e verifica se há tuples mortos. Cada página em shared_buffers é considerada como tendo um custo de 1 (vacuum_cost_page_hit). Todas as outras páginas são consideradas como tendo um custo de 20 (vacuum_cost_page_dirty), se existirem tuples mortos, ou 10 (vacuum_cost_page_miss), se não existirem tuples mortos. A operação de vácuo para quando o processo excede o autovacuum_vacuum_cost_limit.

Após o limite ser atingido, o processo acomoda-se durante a duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de recomeçar. Se o limite não for atingido, o autovacuum começa após o valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam a quantidade de limpeza de dados permitida por unidade de tempo. Note que os valores predefinidos são demasiado baixos para a maioria dos níveis de preços. Os valores ideais para estes parâmetros são dependentes do nível de preços e devem ser configurados em conformidade.

O parâmetro autovacuum_max_workers determina o número máximo de processos de autovacuum que podem ser executados simultaneamente.

Com o PostgreSQL, pode definir estes parâmetros ao nível da tabela ou ao nível de instância. Hoje em dia, pode definir estes parâmetros ao nível da tabela apenas na Base de Dados Azure para PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Otimizar o autovacuum por tabela

Pode configurar todos os parâmetros de configuração anteriores por tabela. Eis um exemplo:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum é um processo sincronizado por tabela. Quanto maior for a percentagem de tuples mortos que uma mesa tem, maior o "custo" para o autovacuum. Pode dividir tabelas com uma alta taxa de atualizações e elimina-se em várias tabelas. A divisão de mesas ajuda a paralelizar o autovacuum e reduzir o "custo" para completar o autovacuum numa mesa. Pode também aumentar o número de trabalhadores paralelos a autovacuum para garantir que os trabalhadores são liberalmente programados.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar e sintonizar o autovacuum, consulte a seguinte documentação PostgreSQL:

- [Capítulo 18, Configuração do servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Capítulo 24, Tarefas de manutenção de bases de dados de rotina](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
