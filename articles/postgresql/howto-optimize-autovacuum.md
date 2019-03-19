---
title: Otimizar autovacuum numa base de dados do Azure para o servidor PostgreSQL
description: Este artigo descreve como pode otimizar autovacuum numa base de dados do Azure para o servidor PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: e1b4bf1f9fa956da7a7b0ca1521439002d1ce76b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993423"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Otimizar autovacuum numa base de dados do Azure para o servidor PostgreSQL 
Este artigo descreve como otimizar efetivamente autovacuum numa base de dados do Azure para o servidor PostgreSQL.

## <a name="overview-of-autovacuum"></a>Descrição geral do autovacuum
PostgreSQL utiliza o controlo de simultaneidade multiversion (MVCC) para permitir maior simultaneidade de base de dados. Cada atualização resulta num insert e delete e cada delete resulta em linhas que está sendo de forma recuperável marcado para eliminação. Marcação de forma recuperável identifica as tuplas Inativas que serão removidas mais tarde. Para executar estas tarefas, PostgreSQL executa uma tarefa de aspirador.

Pode ser acionada uma tarefa de aspirador manual ou automaticamente. Cadeias de identificação de mensagens não mais existem quando a base de dados ocorre no heavy update ou operações de eliminação. As tuplas de mensagens não menos existem quando a base de dados está ocioso. Precisa com mais frequência vacuum quando a carga de base de dados é pesada, que torna a execução de trabalhos de aspirador *manualmente* inconveniente.

Autovacuum pode ser configurado e os benefícios da otimização. Experimente os valores predefinidos que acompanha o PostgreSQL garantir que o produto funciona em todos os tipos de dispositivos. Estes dispositivos incluem Raspberry Pis. Os valores de configuração ideal dependem de:
- Total de recursos disponíveis, como o tamanho SKU e armazenamento.
- Utilização de recursos.
- Características de objeto individuais.

## <a name="autovacuum-benefits"></a>Benefícios de Autovacuum
Se não vacuum de tempos em tempos, as tuplas Inativas que acumularem podem resultar em:
- Dados inchaços, tais como bases de dados maiores e tabelas.
- Índices maiores inferior ao ideal.
- E/s maior.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Inchaço do monitor com consultas autovacuum
A seguinte consulta de exemplo foi concebida para identificar o número de tuplas de ativos e inativos numa tabela chamada XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurações de Autovacuum
Os parâmetros de configuração que controlam autovacuum baseiam-se em respostas para as duas questões-chave:
- Quando ele é iniciado?
- Quanto ele limpa depois de iniciar?

Aqui estão algumas configurações de autovacuum parâmetros que pode atualizar com base nas perguntas anterior, juntamente com algumas orientações.

Parâmetro|Descrição|Valor predefinido
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuplas atualizadas ou eliminadas necessários para acionar uma operação de aspirador em qualquer uma tabela. A predefinição é 50 cadeias de identificação. Defina este parâmetro só no ficheiro postgresql.conf ou na linha de comandos de servidor. Para substituir a definição para tabelas individuais, altere os parâmetros de armazenamento de tabela.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela para adicionar a autovacuum_vacuum_threshold ao decidir se quer acionar uma operação de aspirador. A predefinição é 0,2, que é de 20 por cento do tamanho da tabela. Defina este parâmetro só no ficheiro postgresql.conf ou na linha de comandos de servidor. Para substituir a definição para tabelas individuais, altere os parâmetros de armazenamento de tabela.|5 por cento
autovacuum_vacuum_cost_limit|Especifica o valor de limite de custo utilizado em operações de aspirador automática. Se for -1 é especificado, que é o padrão, é utilizado o valor de regular vacuum_cost_limit. Se existir mais do que uma função de trabalho, o valor é proporcionalmente distribuído entre os operadores de autovacuum em execução. A soma dos limites para cada função de trabalho não excede o valor da variável. Defina este parâmetro só no ficheiro postgresql.conf ou na linha de comandos de servidor. Para substituir a definição para tabelas individuais, altere os parâmetros de armazenamento de tabela.|-1
autovacuum_vacuum_cost_delay|Especifica o valor de atraso de custo utilizado em operações de aspirador automática. Se não for especificado -1, é utilizado o valor de regular vacuum_cost_delay. O valor predefinido é 20 milissegundos. Defina este parâmetro só no ficheiro postgresql.conf ou na linha de comandos de servidor. Para substituir a definição para tabelas individuais, altere os parâmetros de armazenamento de tabela.|20 ms
autovacuum_nap_time|Especifica que o atraso mínimo entre autovacuum é executado em qualquer determinada base de dados. Em cada rodada, o daemon examina o banco de dados e emite comandos ASPIRADOR e analisar, conforme necessário para tabelas no banco de dados. O atraso é medido em segundos, e a predefinição é (1 min) de um minuto. Defina este parâmetro só no ficheiro postgresql.conf ou na linha de comandos de servidor.|15 s
autovacuum_max_workers|Especifica o número máximo de processos de autovacuum, que não seja o iniciador autovacuum, que podem ser executadas ao mesmo tempo. A predefinição é três. Defina este parâmetro só no início do servidor.|3

Para substituir as definições para tabelas individuais, altere os parâmetros de armazenamento de tabela. 

## <a name="autovacuum-cost"></a>Custo de Autovacuum
Seguem-se a "custos" de execução de uma operação de aspirador:

- As páginas de dados que é executado o aspirador estão bloqueadas.
- Computação e memória, são utilizados quando uma tarefa de aspirador está em execução.

Como resultado, não execute aspirador tarefas ou com uma frequência excessiva ou demasiado com pouca frequência. Uma tarefa de aspirador tem de se adaptar à carga de trabalho. Teste todas as alterações de parâmetro autovacuum devido às compensações de cada um deles.

## <a name="autovacuum-start-trigger"></a>Acionador de início de Autovacuum
Autovacuum é acionado quando o número de tuplas Inativos exceder autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Aqui, reltuples é uma constante.

Limpeza de autovacuum deve manter-se a carga de base de dados. Caso contrário, poderá ficar sem armazenamento e lentidão geral nas consultas. Amortizado ao longo do tempo, a taxa a que uma operação de aspirador limpa as tuplas de mensagens não deve ser igual a taxa a que as tuplas de mensagens não são criadas.

Bases de dados com muitas atualizações e eliminações têm mais tuplas Inativas e precisam de mais espaço. Em geral, bases de dados com muitos atualiza e elimina o benefício dos valores de autovacuum_vacuum_scale_factor e autovacuum_vacuum_threshold. Os valores de baixos impedem prolongada acumulação de cadeias de identificação Inativas. Pode utilizar valores mais altos para ambos os parâmetros com bancos de dados mais pequenos, como a necessidade de vacuuming é menos urgente. Vacuuming frequentes impacta a computação e memória.

O fator de dimensionamento predefinido de 20 por cento funciona bem em tabelas com um baixo percentual de cadeias de identificação inativos. Ele não funciona bem em tabelas com uma alta porcentagem de tuplas Inativas. Por exemplo, numa tabela 20 GB, o fator de escala traduz-se 4 GB de tuplas Inativas. Numa tabela de 1 TB, é 200 GB de cadeias de identificação Inativas.

Com o PostgreSQL, pode definir estes parâmetros no nível de instância ou nível de tabela. Hoje em dia, pode definir estes parâmetros no nível da tabela apenas na base de dados do Azure para PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Calcular o custo de autovacuum
Autovacuum em execução é "dispendioso", e há parâmetros para controlar o tempo de execução de operações de aspirador. Os seguintes parâmetros de ajudar a estimar o custo da execução aspirador:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de aspirador lê páginas físicas e verifica a existência de tuplas Inativas. Todas as páginas no shared_buffers é considerada como têm um custo de 1 (vacuum_cost_page_hit). Todas as outras páginas são consideradas como têm um custo de 20 (vacuum_cost_page_dirty), se existem tuplas inativas ou 10 (vacuum_cost_page_miss), se não existem cadeias de identificação de mensagens não existem. A operação de aspirador termina quando o processo excede o autovacuum_vacuum_cost_limit. 

Após ter sido atingido o limite, o processo permanecerá suspenso durante a duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de iniciar novamente. Se não for atingido o limite, autovacuum começa depois do valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam quanto limpeza de dados é permitida por unidade de tempo. Tenha em atenção que os valores predefinidos são demasiado baixos para a maioria dos escalões de preço. Os valores ideal para esses parâmetros são dependentes de escalão de preços e devem ser configurados em conformidade.

O parâmetro autovacuum_max_workers determina o número máximo de processos de autovacuum que podem ser executadas em simultâneo.

Com o PostgreSQL, pode definir estes parâmetros no nível de instância ou nível de tabela. Hoje em dia, pode definir estes parâmetros no nível da tabela apenas na base de dados do Azure para PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Otimizar autovacuum por tabela
Pode configurar todos os parâmetros de configuração anterior por tabela. Segue-se um exemplo:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum é um processo síncrono por tabela. A maior percentagem de tuplas Inativas que tem uma tabela, maior o "Custo" para autovacuum. Pode dividir as tabelas que têm uma taxa elevada de atualizações e eliminações por várias tabelas. A divisão de tabelas de ajuda para paralelizar autovacuum e reduzir o "Custo" para concluir autovacuum numa tabela. Também pode aumentar o número de trabalhadores paralelos autovacuum para garantir que os trabalhadores estão agendados livremente.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como utilizar e otimizar autovacuum, consulte a seguinte documentação de PostgreSQL:

 - [Capítulo 18, configuração do servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Capítulo 24, tarefas de manutenção de base de dados de rotina](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
