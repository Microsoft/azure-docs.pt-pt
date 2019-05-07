---
title: Extensões do PostgreSQL na base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização)
description: Descreve a capacidade de estender a funcionalidade da sua base de dados com extensões na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 32870f37781b4161de692af91c79fe47efb3737e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077324"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Extensões do PostgreSQL na base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização)

PostgreSQL fornece a capacidade de estender a funcionalidade da sua base de dados a utilizar extensões. As extensões permitem para agrupar vários objetos relacionados do SQL em conjunto num único pacote que pode ser carregado ou removido da sua base de dados com um único comando. Após a ser carregado no banco de dados, as extensões podem funcionar tal como as funcionalidades incorporadas. Para obter mais informações sobre as extensões do PostgreSQL, consulte [empacotamento objetos relacionados numa extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Como utilizar as extensões do PostgreSQL

Extensões do PostgreSQL tem de estar instaladas na base de dados antes de poder utilizá-los. Para instalar uma extensão específica, execute o [criar extensão](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) comando da ferramenta de psql para carregar os objetos em pacote na sua base de dados.

Base de dados do Azure para PostgreSQL atualmente suporta um subconjunto das extensões de chave conforme indicado abaixo. Além dos indicados não são suportadas; Não é possível criar sua própria extensão com base de dados do Azure para o serviço PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões suportadas pela base de dados do Azure para PostgreSQL

As tabelas seguintes listam as extensões de PostgreSQL padrão que são atualmente suportadas pela base de dados do Azure para PostgreSQL. Estas informações também estão disponíveis através da execução `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres de maiúsculas e minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados de cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenamento de conjuntos de pares chave/valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacional. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objeto grande. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas de árvore hierárquicas. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar os segmentos de linha ou intervalos de ponto flutuante. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Tipo de top n JSONB. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para os campos que incrementam automaticamente. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular distâncias grande ciclo na superfície da terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar as semelhanças e a distância entre cadeias de caracteres. |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para controlar quem alterou uma tabela. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador de número inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes null livres de números inteiros. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para controlar a hora da última modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções criptográficas. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gere as tabelas particionadas ao tempo ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de alfanumérico texto com base na correspondência de trigram. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para a implementação de integridade referencial (obsoleta). |
> | sessão\_analytics | Funções para consultar o hstore matrizes. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo tabela cruzada. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Acionada notificações de alteração. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | As funções para a implementação de tempo de viagem. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera identificadores exclusivos universalmente (UUIDs não). |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa em texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento de sinónimos expandida. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove os acentos (sinais diacríticos) de lexemes. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso de bloom - ficheiro de assinatura índice baseado em. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece exemplo GIN operador classes que implementam a árvore B, como o comportamento para determinados tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice essência que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL carregável linguagem de procedimento. |

### <a name="hyperscale-extensions"></a>Extensões de Hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Base de dados distribuída de Citus. |
> | partição horizontal\_rebalancer | Reequilibrar com segurança a dados num grupo de servidor em caso de nó adição ou remoção. |

### <a name="miscellaneous-extensions"></a>Diversas extensões

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para o PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade de relação. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de dados externo para acesso de arquivo simples. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione os conteúdos de páginas de base de dados num nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que acontece no cache de buffer compartilhado em tempo real. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Programador de tarefas para o PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma forma de carregar dados de relação para o cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para controlar as estatísticas de execução de todas as instruções SQL executadas por um servidor. (Veja abaixo para uma observação sobre esta extensão). |
> | [PG\_visibilidade](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e informações de visibilidade de nível de página. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueios ao nível da linha. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para que mostra estatísticas de nível de cadeias de identificação. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externo utilizado para aceder aos dados armazenados em servidores de PostgreSQL externos. (Veja abaixo para uma observação sobre esta extensão).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados SSL. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE que aceita o número de linhas e um limite. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE que aceita o tempo em milissegundos, como um limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criação de índices hipotéticos que não de custos da CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta ligações a outros bancos de dados PostgreSQL de dentro de uma sessão de banco de dados. (Veja abaixo para uma observação sobre esta extensão). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta de XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões de PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | endereço\_standardizer, endereço\_standardizer\_dados\_-nos | Utilizado para analisar um endereço em elementos que constituem. Utilizados para suportar o passo de normalização de endereço de geocodificação. |
> | postgis\_sfcgal | Funções de PostGIS SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder e geocoder inversa. |
> | postgis\_topologia | PostGIS tipos geográficos de topologia e funções. |


## <a name="pgstatstatements"></a>pg_stat_statements
O [pg\_stat\_extensão instruções](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) é pré-carregada em cada base de dados do Azure para o servidor PostgreSQL para lhe fornecer um meio de controlar as estatísticas de execução de instruções SQL.
A definição `pg_stat_statements.track`, que controla quais declarações são contabilizadas por extensão, a predefinição é `top`, que significa que todas as declarações emitidas diretamente por clientes são controladas. Os dois outros controle se níveis são `none` e `all`. Esta definição pode ser configurada como um parâmetro de servidor através da [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou o [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma compensação entre as informações de execução de consulta fornece pg_stat_statements e o impacto no desempenho do servidor como registos de cada instrução de SQL. Se não estiver ativamente a utilizar a extensão de pg_stat_statements, recomendamos que defina `pg_stat_statements.track` para `none`. Tenha em atenção que alguns terceiros monitorizar os serviços podem contar com pg_stat_statements para fornecer informações de desempenho de consulta, por isso, confirme se for este o caso para ou não.

## <a name="dblink-and-postgresfdw"></a>dblink e postgres_fdw
dblink e postgres_fdw permitem-lhe ligar a partir de um servidor PostgreSQL para outro, ou para outra base de dados no mesmo servidor. O servidor de recebimento tem de permitir ligações de servidor de envio através da sua firewall. Ao usar essas extensões para estabelecer ligação entre a base de dados do Azure para servidores PostgreSQL, isso pode ser feito através da definição "Permitir acesso aos serviços do Azure" on. Ele também é necessário se pretender utilizar as extensões para voltar ao mesmo servidor. A definição "Permitir acesso aos serviços do Azure" pode ser encontrada na página do portal do Azure para o servidor Postgres, em segurança de ligação. Ativar "Permitir acesso aos serviços do Azure" nas listas de permissões de todos os IPs do Azure.

Atualmente, as ligações de saída da base de dados do Azure para PostgreSQL não são suportadas, exceto para ligações para outra base de dados do Azure para servidores PostgreSQL.
