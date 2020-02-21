---
title: Extensões – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Descreve a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485408"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões PostgreSQL na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

O PostgreSQL fornece a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões. As extensões permitem agregar vários objetos SQL relacionados num único pacote que pode ser carregado ou removido da sua base de dados com um único comando. Depois de ser carregado na base de dados, as extensões podem funcionar como características incorporadas. Para obter mais informações sobre extensões PostgreSQL, consulte [objetos relacionados com o pacote numa extensão](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Utilize extensões PostgreSQL

As extensões PostgreSQL devem ser instaladas na sua base de dados antes de as utilizar. Para instalar uma determinada extensão, execute o comando create [EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) da ferramenta psql para carregar os objetos embalados na sua base de dados.

A Base de Dados Azure para PostgreSQL - Hyperscale (Citus) suporta atualmente um subconjunto de extensões-chave listadas aqui. As extensões que não as listadas não são apoiadas. Não é possível criar a sua própria extensão com base de dados Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões suportadas pela Base de Dados Azure para PostgreSQL

As tabelas seguintes listam as extensões postgresql padrão que são atualmente suportadas pela Base de Dados Azure para PostgreSQL. Esta informação também está disponível executando `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citexto](https://www.postgresql.org/docs/current/static/citext.html) | Fornece um tipo de corda de caracteres insensíveis. |
> | [cubo](https://www.postgresql.org/docs/current/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares de valor-chave. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fornece uma estrutura de dados HyperLogLog. |
> | [é](https://www.postgresql.org/docs/current/static/isn.html) | Fornece tipos de dados para padrões internacionais de numeração de produtos. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objetos grandes. |
> | [árvore](https://www.postgresql.org/docs/current/static/ltree.html) | Fornece um tipo de dados para estruturas hierárquicas semelhantes a árvores. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Digite para Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [ditado\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para inteiros. |
> | [ditado\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento de sinónimo alargado. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) de lexemes. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para campos de incremento automático. |
> | [distância da terra](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornece um meio para calcular distâncias de grande círculo na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e distância entre cordas. |
> | [inserir\_nome de utilizador](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para rastrear quem mudou uma mesa. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornece funções e operadores para manipular matrizes sem fim de inteiros. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para rastrear o último tempo de modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornece funções criptográficas. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gere tabelas divididas por tempo ou identificação. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança do texto alfanumérico com base na correspondência de trigramas. |
> | [refinador](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para a implementação da integridade referencial (obsoleta). |
> | sessão\_análise | Funções para consulta de matrizes de hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornece funções que manipulam mesas inteiras, incluindo crosstab. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Desencadeou notificações de alteração. |
> | [viagem no tempo](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementar viagens no tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Gera identificadores universalmente únicos (UUIDs). |

### <a name="hyperscale-extensions"></a>Extensões de hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus distribuído base de dados. |
> | reequilibrador\_de fragmentos | Reequilibre com segurança os dados num grupo de servidores em caso de adição ou remoção do nó. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [florescer](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso bloom - índice baseado em ficheiros de assinatura. |
> | [btree gin\_](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornece classes de operador de GIN de amostra que implementam comportamento semelhante a árvore B para certos tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões linguísticas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguagem processual pl/pgSQL loadable. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para postgresQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [arquivo\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Invólucro de dados estrangeiros para acesso a ficheiros planos. |
> | [inspeção de página](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas de base de dados a um nível baixo. |
> | [pg\_cache tampão](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo na cache tampão partilhada em tempo real. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Programador de emprego para PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_pré-quente](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornece uma forma de carregar os dados da relação na cache do tampão. |
> | [pg\_declarações\_stat](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Consulte a secção "pg_stat_statements" para obter informações sobre esta extensão. |
> | [pg\_visibilidade](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e as informações de visibilidade ao nível da página. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio ao nível da linha. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas ao nível do tuple. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Invólucro de dados estrangeiros usado para aceder a dados armazenados em servidores postgresQL externos. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informação sobre certificados SSL. |
> | [tsm\_sistema\_linhas](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como um limite. |
> | [sistema de\_tsm\_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como um limite. |
> | [hipopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criar índices hipotéticos que não custam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta ligações a outras bases de dados PostgreSQL a partir de uma sessão de base de dados. Consulte a secção "dblink e postgres_fdw" para obter informações sobre esta extensão. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões postGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topologia, postgis\_tigre\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | endereço\_standardizador, endereço\_padronizador\_dados\_nós | Usado para analisar um endereço em elementos constituintes. Usado para apoiar a geocodificação passo de normalização. |
> | postgis\_sfcgal | Funções PósGIS SFCGAL. |
> | postgis\_tigre\_geocodificador | Geocodificador de tigre pósGIS e geocodificador invertido. |
> | postgis\_topologia | Tipos e funções espaciais de topologia pósGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
O [pg\_a extensão de demonstrações\_](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todas as bases de dados do Azure para o servidor PostgreSQL para lhe fornecer um meio de rastrear estatísticas de execução de declarações SQL.

A definição `pg_stat_statements.track` controla as declarações contadas pela extensão. Não se aplica à `top`, o que significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Os outros dois níveis de rastreio são `none` e `all`. Esta definição é configurável como parâmetro de servidor através do [portal Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou do [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma troca entre a informação de execução de consulta pg_stat_statements fornece e o efeito no desempenho do servidor à medida que regista cada declaração sQL. Se não estiver a utilizar ativamente a extensão pg_stat_statements, recomendamos que `pg_stat_statements.track` `none`. Alguns serviços de monitorização de terceiros podem contar com pg_stat_statements para fornecer informações de desempenho de consulta, por isso confirme se este é o caso para si ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
Pode utilizar dblink e postgres_fdw para ligar de um servidor PostgreSQL a outro, ou a outra base de dados no mesmo servidor. O servidor recetor tem de permitir ligações a partir do servidor de envio através da sua firewall. Para utilizar estas extensões para ligar entre a Base de Dados Azure para servidores PostgreSQL, detete **te desperte o acesso aos serviços Azure** para ON. Também precisa de ligar esta definição se pretender utilizar as extensões para voltar ao mesmo servidor. A definição de **serviços De acesso ao Azure** pode ser encontrada na página do portal Azure para o servidor Postgres no âmbito da Segurança de **Ligação**. Virar **Permitir o acesso aos serviços Azure** em whitelists todos os IPs Azure.

Atualmente, as ligações de saída da Base de Dados Azure para PostgreSQL não são suportadas, exceto ligações a outras bases de dados Azure para servidores PostgreSQL.
