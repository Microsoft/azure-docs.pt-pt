---
title: Otimizar o autovácuo-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode otimizar a vácuo autoaspira em um banco de dados do Azure para PostgreSQL-servidor único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770191"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Otimizar o vácuo autoaspirar em um banco de dados do Azure para PostgreSQL-servidor único
Este artigo descreve como otimizar com eficiência a vácuo autoaspiração em um servidor de banco de dados do Azure para PostgreSQL.

## <a name="overview-of-autovacuum"></a>Visão geral do autovácuo
O PostgreSQL usa o MVCC (controle de simultaneidade multiversão) para permitir uma maior simultaneidade de banco de dados. Cada atualização resulta em um Insert e Delete, e cada Delete resulta em linhas sendo marcadas de maneira flexível para exclusão. A marcação reversível identifica tuplas inativas que serão limpas posteriormente. Para executar essas tarefas, o PostgreSQL executa um trabalho de vácuo.

Um trabalho de vácuo pode ser disparado manualmente ou automaticamente. Há mais tuplas inativas quando o banco de dados experimenta operações de atualização ou exclusão pesadas. Existem menos tuplas inativas quando o banco de dados está ocioso. Você precisa se aspirar com mais frequência quando a carga do banco de dados é pesada, o que torna a execução de trabalhos de vácuo *manualmente* inconvenientes.

A vácuo automática pode ser configurada e se beneficiar do ajuste. Os valores padrão fornecidos pelo PostgreSQL com o tentam garantir que o produto funcione em todos os tipos de dispositivos. Esses dispositivos incluem o Raspberry pis. Os valores de configuração ideais dependem do:
- Total de recursos disponíveis, como SKU e tamanho do armazenamento.
- Uso de recursos.
- Características de objeto individual.

## <a name="autovacuum-benefits"></a>Benefícios do autovácuo
Se você não se aspirar de tempos em tempos, as tuplas inativas que se acumulam podem resultar em:
- Inchar de dados, como tabelas e bancos de dados maiores.
- Índices subideals maiores.
- Aumento de e/s.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitorar o inchar com consultas autovácuo
A consulta de exemplo a seguir foi criada para identificar o número de tuplas ativas e ativas em uma tabela chamada XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurações de vácuo
Os parâmetros de configuração que controlam a vácuo automática baseiam-se em respostas a duas perguntas principais:
- Quando deveria começar?
- Quanto ele deve ser limpo depois de começar?

Aqui estão alguns parâmetros de configuração do autovácuo que você pode atualizar com base nas perguntas anteriores, juntamente com algumas diretrizes.

Parâmetro|Descrição|Valor predefinido
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuplas atualizadas ou excluídas necessárias para disparar uma operação de vácuo em uma única tabela. O padrão é 50 tuplas. Defina esse parâmetro somente no arquivo PostgreSQL. conf ou na linha de comando do servidor. Para substituir a configuração de tabelas individuais, altere os parâmetros de armazenamento da tabela.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela a ser adicionada ao autovacuum_vacuum_threshold ao decidir se deseja disparar uma operação de vácuo. O padrão é 0,2, que é 20 por cento do tamanho da tabela. Defina esse parâmetro somente no arquivo PostgreSQL. conf ou na linha de comando do servidor. Para substituir a configuração de tabelas individuais, altere os parâmetros de armazenamento da tabela.|5 por cento
autovacuum_vacuum_cost_limit|Especifica o valor de limite de custo usado em operações de vácuo automáticas. Se-1 for especificado, que é o padrão, o valor de vacuum_cost_limit regular será usado. Se houver mais de um trabalho, o valor será distribuído proporcionalmente entre os trabalhos do autovácuo em execução. A soma dos limites de cada trabalho não excede o valor dessa variável. Defina esse parâmetro somente no arquivo PostgreSQL. conf ou na linha de comando do servidor. Para substituir a configuração de tabelas individuais, altere os parâmetros de armazenamento da tabela.|-1
autovacuum_vacuum_cost_delay|Especifica o valor de atraso de custo usado em operações de vácuo automáticas. Se-1 for especificado, o valor de vacuum_cost_delay regular será usado. O valor padrão é 20 milissegundos. Defina esse parâmetro somente no arquivo PostgreSQL. conf ou na linha de comando do servidor. Para substituir a configuração de tabelas individuais, altere os parâmetros de armazenamento da tabela.|20 ms
autovacuum_nap_time|Especifica o atraso mínimo entre as execuções do autovácuo em um determinado banco de dados. Em cada rodada, o daemon examina o banco de dados e emite comandos de vácuo e de análise, conforme necessário, para tabelas nesse banco de dados. O atraso é medido em segundos e o padrão é um minuto (1 min). Defina esse parâmetro somente no arquivo PostgreSQL. conf ou na linha de comando do servidor.|15 s
autovacuum_max_workers|Especifica o número máximo de processos de vácuo automáticas, além do iniciador autovácuo, que pode ser executado a qualquer momento. O padrão é três. Defina esse parâmetro somente na inicialização do servidor.|3

Para substituir as configurações de tabelas individuais, altere os parâmetros de armazenamento da tabela. 

## <a name="autovacuum-cost"></a>Custo do autovácuo
Aqui estão os "custos" da execução de uma operação de vácuo:

- As páginas de dados nas quais a vácuo é executada estão bloqueadas.
- A computação e a memória são usadas quando um trabalho de vácuo está em execução.

Como resultado, não execute trabalhos de vácuo com muita frequência ou com pouca frequência. Um trabalho de vácuo precisa se adaptar à carga de trabalhos. Testar todas as alterações do parâmetro autovácuo devido às compensações de cada uma.

## <a name="autovacuum-start-trigger"></a>Gatilho de início do autovácuo
A vácuo automática é disparada quando o número de tuplas inativas excede autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Aqui, reltuples é uma constante.

A limpeza do autovácuo deve acompanhar a carga do banco de dados. Caso contrário, você pode ficar sem armazenamento e experimentar uma lentidão geral em consultas. Amortizado ao longo do tempo, a taxa na qual uma operação de vácuo limpa as tuplas inativas deve ser igual à taxa em que as tuplas inativas são criadas.

Os bancos de dados com muitas atualizações e exclusões têm mais tuplas inativas e precisam de mais espaço. Em geral, os bancos de dados com muitas atualizações e exclusões se beneficiam de valores baixos de autovacuum_vacuum_scale_factor e autovacuum_vacuum_threshold. Os valores baixos impedem a acumulação prolongada de tuplas inativas. Você pode usar valores mais altos para ambos os parâmetros com bancos de dados menores, pois a necessidade de aspiração é menos urgente. A aspiração frequente vem com o custo da computação e da memória.

O fator de escala padrão de 20 por cento funciona bem em tabelas com uma porcentagem baixa de tuplas inativas. Ele não funciona bem em tabelas com uma alta porcentagem de tuplas inativas. Por exemplo, em uma tabela de 20 GB, esse fator de escala é convertido em 4 GB de tuplas inativas. Em uma tabela de 1 TB, são 200 GB de tuplas mortas.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou de instância. Hoje, você pode definir esses parâmetros no nível de tabela somente no banco de dados do Azure para PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Estimar o custo do autovácuo
A execução da autovácuo é "dispendiosa" e há parâmetros para controlar o tempo de execução de operações de vácuo. Os parâmetros a seguir ajudam a estimar o custo da execução do vácuo:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de vácuo lê páginas físicas e verifica se há tuplas inativas. Cada página em shared_buffers é considerada um custo de 1 (vacuum_cost_page_hit). Todas as outras páginas são consideradas com um custo de 20 (vacuum_cost_page_dirty), se existirem tuplas inativas ou 10 (vacuum_cost_page_miss), se não existirem tuplas inativas. A operação de vácuo para quando o processo excede o autovacuum_vacuum_cost_limit. 

Depois que o limite é atingido, o processo é suspenso pela duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de ser iniciado novamente. Se o limite não for atingido, a vácuo iniciará após o valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam a quantidade de dados permitida por unidade de tempo. Observe que os valores padrão são muito baixos para a maioria dos tipos de preço. Os valores ideais para esses parâmetros são dependentes do tipo de preço e devem ser configurados de acordo.

O parâmetro autovacuum_max_workers determina o número máximo de processos do autovácuo que podem ser executados simultaneamente.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou de instância. Hoje, você pode definir esses parâmetros no nível de tabela somente no banco de dados do Azure para PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Otimizar autovácuo por tabela
Você pode configurar todos os parâmetros de configuração anteriores por tabela. Segue-se um exemplo:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

O autovácuo é um processo síncrono por tabela. Quanto maior a porcentagem de tuplas inativas que uma tabela tem, maior o "custo" para a vácuo. Você pode dividir tabelas que têm uma alta taxa de atualizações e exclusões em várias tabelas. A divisão de tabelas ajuda a paralelizar o autovácuo e a reduzir o "custo" para concluir o vácuo automático em uma tabela. Você também pode aumentar o número de trabalhadores paralelos do autovácuo para garantir que os trabalhadores sejam livremente agendados.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como usar e ajustar o autovácuo, confira a seguinte documentação do PostgreSQL:

 - [Capítulo 18, configuração do servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Capítulo 24, tarefas rotineiras de manutenção de banco de dados](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
