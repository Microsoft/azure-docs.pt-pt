---
title: Extensões – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Descreve a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões na Base de Dados Azure para PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012767"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões postgreSQL em Azure Database for PostgreSQL – Hyperscale (Citus)

O PostgreSQL proporciona a capacidade de prolongar a funcionalidade da sua base de dados ao utilizar extensões. As extensões permitem a agregação de vários objetos SQL relacionados num único pacote que pode ser carregado ou removido da sua base de dados com um único comando. Depois do carregamento para a base de dados, as extensões podem funcionar como funcionalidades incorporadas. Para obter mais informações sobre extensões PostgreSQL, consulte [os objetos relacionados com o pacote numa extensão.](https://www.postgresql.org/docs/current/static/extend-extensions.html)

## <a name="use-postgresql-extensions"></a>Utilizar extensões PostgreSQL

As extensões PostgreSQL devem ser instaladas na sua base de dados antes de as poder utilizar. Para instalar uma extensão específica, executar o comando [EXTENSÃO CREATE](https://www.postgresql.org/docs/current/static/sql-createextension.html) a partir da ferramenta psql para carregar os objetos embalados na sua base de dados.

A Azure Database for PostgreSQL - Hyperscale (Citus) suporta atualmente um subconjunto de extensões-chave como listado aqui. Extensões que não as listadas não são suportadas. Não é possível criar a sua própria extensão com a Base de Dados Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões suportadas pela Base de Dados Azure para PostgreSQL

As tabelas seguintes listam as extensões postgreSQL padrão que são atualmente suportadas pela Base de Dados Azure para PostgreSQL. Esta informação também está disponível através da `SELECT * FROM pg_available_extensions;` execução.

As versões de cada extensão instalada num grupo de servidores por vezes diferem com base na versão de PostgreSQL (11, 12 ou 13). As tabelas listam versões de extensão por versão de base de dados.

### <a name="citus-extension"></a>Extensão citus

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [coentro](https://github.com/citusdata/citus) | Citus distribuído base de dados. | 9.5-1 | 9.5-1 | 10.0-2 |

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fornece um tipo de corda de caráter insensível a caso. | 1.5 | 1.6 | 1.6 |
> | [cubo](https://www.postgresql.org/docs/current/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fornece uma estrutura de dados HyperLogLog. | 2.14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares de valores-chave. | 1.5 | 1.6 | 1.7 |
> | [é](https://www.postgresql.org/docs/current/static/isn.html) | Fornece tipos de dados para normas internacionais de numeragem de produto. | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objetos grandes. | 1.1 | 1.1 | 1.1 |
> | [Rio Itree](https://www.postgresql.org/docs/current/static/ltree.html) | Fornece um tipo de dados para estruturas hierárquicas semelhantes a árvores. | 1.1 | 1.1 | 1.2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Tipo de dados para a acumulação on-line de estatísticas baseadas em classificação, tais como quânticos e meios aparados. | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Digite para o top-n JSONB. | 2.2.2 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para inteiros. | 1.0 | 1.0 | 1.0 |
> | [dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento de sinónimo alargado. | 1.0 | 1.0 | 1.0 |
> | [não-acento](https://www.postgresql.org/docs/current/static/unaccent.html) | Um dicionário de pesquisa de texto que remove sotaques (sinais diacríticos) de lexemes. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para campos de autoincreção. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornece um meio para calcular distâncias de grande círculo na superfície da Terra. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e distância entre cordas. | 1.1 | 1.1 | 1.1 |
> | [inserir \_ nome de utilizador](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para rastrear quem mudou uma mesa. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornece funções e operadores para manipular matrizes isentas de nulos de inteiros. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para rastrear a última vez de modificação. | 1.0 | 1.0 | 1.0 |
> | [pg \_ partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gere as mesas divididas por tempo ou ID. | 4.1 | 4.4.1 | 4.4.1 |
> | [pg \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança do texto alfanumérico com base na correspondência de trigramas. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornece funções criptográficas. | 1.3 | 1.3 | 1.3 |
> | [refinar](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções de implementação da integridade referencial (obsoleta). | 1.0 | 1.0 | 1.0 |
> | análise de sessão \_ | Funções para consulta de matrizes de hstore. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornece funções que manipulam mesas inteiras, incluindo crosstab. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Desencadeou notificações de alteração. | 1.0 | 1.0 | 1.0 |
> | [viagem no tempo](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementar viagens no tempo. | 1.0 | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Gera identificadores universalmente únicos (UUIDs). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [florescer](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso bloom - índice baseado em ficheiros de assinatura. | 1.0 | 1.0 | 1.0 |
> | [\_btree gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornece aulas de operador de gin de amostra que implementam comportamento semelhante a árvore B para certos tipos de dados. | 1.3 | 1.3 | 1.3 |
> | [btree \_ gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam árvore B. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Extensões linguísticas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguagens processuais pl/pgSQL. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. | 1.1 | 1.2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta ligações a outras bases de dados PostgreSQL dentro de uma sessão de base de dados. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão. | 1.2 | 1.2 | 1.2 |
> | [arquivo \_ fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Invólucro de dados estranhos para acesso a ficheiros planos. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas de base de dados a um nível baixo. | 1.7 | 1.7 | 1.8 |
> | [pg \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo na cache de tampão compartilhado em tempo real. | 1.3 | 1.3 | 1.3 |
> | [pg \_ cron](https://github.com/citusdata/pg_cron) | Agendador de emprego para PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [pg \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). | 1.2 | 1.2 | 1.2 |
> | [pg \_ pré-guerra](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornece uma forma de carregar os dados de relação na cache do tampão. | 1.2 | 1.2 | 1.2 |
> | [pg \_ declarações de stat \_](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Consulte a secção "pg_stat_statements" para obter informações sobre esta extensão. | 1.6 | 1.7 | 1.8 |
> | [pg \_ visibilidade](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e a informação de visibilidade ao nível da página. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio ao nível da linha. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas de nível de tuple. | 1.5 | 1.5 | 1.5 |
> | [postgres \_ fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Invólucro de dados estrangeiros utilizado para aceder a dados armazenados em servidores postgresQL externos. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão.| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informação sobre certificados TLS/SSL. | 1.2 | 1.2 | 1.2 |
> | [\_linhas de sistema tsm \_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como limite. | 1.0 | 1.0 | 1.0 |
> | [\_tempo do sistema tsm \_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como limite. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta de XPath e XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>Extensões pós-GIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS,](https://www.postgis.net/)topologia pós-gis, \_ \_ geocodificador de tigre pós-gis, \_ pós-gis \_ sfcgal | Objetos espaciais e geográficos para PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_padronizador de endereço, endereço \_ de dados de padronizador \_ \_ nos | Usado para analisar um endereço em elementos constituintes. Usado para apoiar o passo de normalização do endereço geocoding. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis \_ sfcgal | Funções SFCGAL pós-GIS. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_ \_ geocodere de tigre pós-ggis | Geocoder de tigre pós-GIS e geocodificador invertido. | 2.5.1 | 3.0.3 | 3.0.3 |
> | topologia pós-gis \_ | Tipos e funções espaciais de topologia pós-GIS. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [ \_ \_ extensão de declarações pg stat](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todas as bases de dados do Azure para o servidor PostgreSQL para fornecer-lhe um meio de rastrear as estatísticas de execução de declarações SQL.

A definição `pg_stat_statements.track` controla as declarações contadas pela extensão. O incumprimento é de `top` , o que significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Os outros dois níveis de rastreio são `none` `all` e. Esta definição é configurável como parâmetro de servidor através do [portal Azure](./howto-configure-server-parameters-using-portal.md) ou do [CLI Azure](./howto-configure-server-parameters-using-cli.md).

Há uma troca entre a informação de execução de consulta pg_stat_statements fornece e o efeito no desempenho do servidor à medida que regista cada declaração SQL. Se não estiver a utilizar ativamente a extensão pg_stat_statements, recomendamos que se desemalte `pg_stat_statements.track` `none` . Alguns serviços de monitorização de terceiros podem contar com pg_stat_statements para fornecer insights de desempenho de consulta, por isso confirme se este é o caso para si ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw

Pode utilizar dblink e postgres \_ fdw para ligar de um servidor PostgreSQL a outro, ou a outra base de dados no mesmo servidor.  O servidor recetor precisa de permitir ligações a partir do servidor de envio através da sua firewall.  Para utilizar estas extensões para ligar entre a Base de Dados Azure para servidores PostgreSQL ou grupos de servidores de Hiperescala (Citus), deteta **os serviços e recursos do Azure para aceder a este grupo de servidores (ou servidor)** a ON.  Também precisa de ligar esta definição se pretender utilizar as extensões para voltar ao mesmo servidor.
Os **serviços e recursos do Allow Azure para aceder a esta** definição de grupo de servidor podem ser encontrados na página do portal Azure para o grupo servidor Hyperscale (Citus) em **rede**.  Atualmente, as ligações de saída da Base de Dados Azure para servidor single postgreSQL e hiperescala (Citus) não são suportadas, exceto para ligações a outros grupos de servidores Azure Database para servidores PostgreSQL e grupos de servidores de Hiperescala (Citus).
