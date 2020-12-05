---
title: Extensões – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Descreve a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões na Base de Dados Azure para PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620907"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões postgreSQL em Azure Database for PostgreSQL – Hyperscale (Citus)

O PostgreSQL proporciona a capacidade de prolongar a funcionalidade da sua base de dados ao utilizar extensões. As extensões permitem a agregação de vários objetos SQL relacionados num único pacote que pode ser carregado ou removido da sua base de dados com um único comando. Depois do carregamento para a base de dados, as extensões podem funcionar como funcionalidades incorporadas. Para obter mais informações sobre extensões PostgreSQL, consulte [os objetos relacionados com o pacote numa extensão.](https://www.postgresql.org/docs/current/static/extend-extensions.html)

## <a name="use-postgresql-extensions"></a>Utilizar extensões PostgreSQL

As extensões PostgreSQL devem ser instaladas na sua base de dados antes de as poder utilizar. Para instalar uma extensão específica, executar o comando [EXTENSÃO CREATE](https://www.postgresql.org/docs/current/static/sql-createextension.html) a partir da ferramenta psql para carregar os objetos embalados na sua base de dados.

A Azure Database for PostgreSQL - Hyperscale (Citus) suporta atualmente um subconjunto de extensões-chave como listado aqui. Extensões que não as listadas não são suportadas. Não é possível criar a sua própria extensão com a Base de Dados Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões suportadas pela Base de Dados Azure para PostgreSQL

As tabelas seguintes listam as extensões postgreSQL padrão que são atualmente suportadas pela Base de Dados Azure para PostgreSQL. Esta informação também está disponível através da `SELECT * FROM pg_available_extensions;` execução.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fornece um tipo de corda de caráter insensível a caso. |
> | [cubo](https://www.postgresql.org/docs/current/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fornece uma estrutura de dados HyperLogLog. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares de valores-chave. |
> | [é](https://www.postgresql.org/docs/current/static/isn.html) | Fornece tipos de dados para normas internacionais de numeragem de produto. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objetos grandes. |
> | [Rio Itree](https://www.postgresql.org/docs/current/static/ltree.html) | Fornece um tipo de dados para estruturas hierárquicas semelhantes a árvores. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [tdigest](https://github.com/tvondra/tdigest) | Tipo de dados para a acumulação on-line de estatísticas baseadas em classificação, tais como quânticos e meios aparados. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Digite para o top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para inteiros. |
> | [dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento de sinónimo alargado. |
> | [não-acento](https://www.postgresql.org/docs/current/static/unaccent.html) | Um dicionário de pesquisa de texto que remove sotaques (sinais diacríticos) de lexemes. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para campos de autoincreção. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornece um meio para calcular distâncias de grande círculo na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e distância entre cordas. |
> | [inserir \_ nome de utilizador](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para rastrear quem mudou uma mesa. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornece funções e operadores para manipular matrizes isentas de nulos de inteiros. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para rastrear a última vez de modificação. |
> | [pg \_ partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gere as mesas divididas por tempo ou ID. |
> | [pg \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança do texto alfanumérico com base na correspondência de trigramas. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornece funções criptográficas. |
> | [refinar](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções de implementação da integridade referencial (obsoleta). |
> | análise de sessão \_ | Funções para consulta de matrizes de hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornece funções que manipulam mesas inteiras, incluindo crosstab. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Desencadeou notificações de alteração. |
> | [viagem no tempo](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementar viagens no tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Gera identificadores universalmente únicos (UUIDs). |

### <a name="hyperscale-citus-extensions"></a>Extensões de hiperescala (Citus)

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [coentro](https://github.com/citusdata/citus) | Citus distribuído base de dados. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [florescer](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso bloom - índice baseado em ficheiros de assinatura. |
> | [\_btree gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornece aulas de operador de gin de amostra que implementam comportamento semelhante a árvore B para certos tipos de dados. |
> | [btree \_ gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam árvore B. |

### <a name="language-extensions"></a>Extensões linguísticas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguagens processuais pl/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta ligações a outras bases de dados PostgreSQL dentro de uma sessão de base de dados. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão. |
> | [arquivo \_ fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Invólucro de dados estranhos para acesso a ficheiros planos. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas de base de dados a um nível baixo. |
> | [pg \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo na cache de tampão compartilhado em tempo real. |
> | [pg \_ cron](https://github.com/citusdata/pg_cron) | Agendador de emprego para PostgreSQL. |
> | [pg \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg \_ pré-guerra](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornece uma forma de carregar os dados de relação na cache do tampão. |
> | [pg \_ declarações de stat \_](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Consulte a secção "pg_stat_statements" para obter informações sobre esta extensão. |
> | [pg \_ visibilidade](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e a informação de visibilidade ao nível da página. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio ao nível da linha. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas de nível de tuple. |
> | [postgres \_ fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Invólucro de dados estrangeiros utilizado para aceder a dados armazenados em servidores postgresQL externos. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informação sobre certificados TLS/SSL. |
> | [\_linhas de sistema tsm \_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como limite. |
> | [\_tempo do sistema tsm \_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como limite. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta de XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões pós-GIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS,](https://www.postgis.net/)topologia pós-gis, \_ \_ geocodificador de tigre pós-gis, \_ pós-gis \_ sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | \_padronizador de endereço, endereço \_ de dados de padronizador \_ \_ nos | Usado para analisar um endereço em elementos constituintes. Usado para apoiar o passo de normalização do endereço geocoding. |
> | postgis \_ sfcgal | Funções SFCGAL pós-GIS. |
> | \_ \_ geocodere de tigre pós-ggis | Geocoder de tigre pós-GIS e geocodificador invertido. |
> | topologia pós-gis \_ | Tipos e funções espaciais de topologia pós-GIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [ \_ \_ extensão de declarações pg stat](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todas as bases de dados do Azure para o servidor PostgreSQL para fornecer-lhe um meio de rastrear as estatísticas de execução de declarações SQL.

A definição `pg_stat_statements.track` controla as declarações contadas pela extensão. O incumprimento é de `top` , o que significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Os outros dois níveis de rastreio são `none` `all` e. Esta definição é configurável como parâmetro de servidor através do [portal Azure](./howto-configure-server-parameters-using-portal.md) ou do [CLI Azure](./howto-configure-server-parameters-using-cli.md).

Há uma troca entre a informação de execução de consulta pg_stat_statements fornece e o efeito no desempenho do servidor à medida que regista cada declaração SQL. Se não estiver a utilizar ativamente a extensão pg_stat_statements, recomendamos que se desemalte `pg_stat_statements.track` `none` . Alguns serviços de monitorização de terceiros podem contar com pg_stat_statements para fornecer insights de desempenho de consulta, por isso confirme se este é o caso para si ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw

Pode utilizar dblink e postgres \_ fdw para ligar de um servidor PostgreSQL a outro, ou a outra base de dados no mesmo servidor.  O servidor recetor precisa de permitir ligações a partir do servidor de envio através da sua firewall.  Para utilizar estas extensões para ligar entre a Base de Dados Azure para servidores PostgreSQL ou grupos de servidores de Hiperescala (Citus), deteta **os serviços e recursos do Azure para aceder a este grupo de servidores (ou servidor)** a ON.  Também precisa de ligar esta definição se pretender utilizar as extensões para voltar ao mesmo servidor.
Os **serviços e recursos do Allow Azure para aceder a esta** definição de grupo de servidor podem ser encontrados na página do portal Azure para o grupo servidor Hyperscale (Citus) em **rede**.  Atualmente, as ligações de saída da Base de Dados Azure para servidor single postgreSQL e hiperescala (Citus) não são suportadas, exceto para ligações a outros grupos de servidores Azure Database para servidores PostgreSQL e grupos de servidores de Hiperescala (Citus).