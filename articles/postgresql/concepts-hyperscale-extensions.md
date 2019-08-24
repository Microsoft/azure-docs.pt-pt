---
title: Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – Citus (hiperescala)
description: Descreve a capacidade de estender a funcionalidade do banco de dados usando extensões no banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: aabcb0b0d01d821c529803927dacec448c923745
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998023"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

O PostgreSQL fornece a capacidade de estender a funcionalidade do seu banco de dados usando extensões. As extensões permitem agrupar vários objetos SQL relacionados juntos em um único pacote que pode ser carregado ou removido do banco de dados com um único comando. Depois de ser carregado no banco de dados, as extensões podem funcionar como recursos internos. Para obter mais informações sobre extensões PostgreSQL, consulte [empacotar objetos relacionados em uma extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Usar extensões do PostgreSQL

As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [criar extensão](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) da ferramenta psql para carregar os objetos empacotados em seu banco de dados.

Banco de dados do Azure para PostgreSQL – a visualização de hiperescala (Citus) atualmente dá suporte a um subconjunto de extensões de chave, conforme listado aqui. Não há suporte para extensões que não aquelas listadas. Você não pode criar sua própria extensão com o banco de dados do Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do banco de dados do Azure para PostgreSQL

As tabelas a seguir listam as extensões do PostgreSQL padrão que atualmente têm suporte no banco de dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio `SELECT * FROM pg_available_extensions;`da execução do.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave-valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece tipos de dados para padrões de numeração de produtos internacionais. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objeto grande. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas hierárquicas de árvore. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Tipo para Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento estendido de sinônimo. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) de lexemas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para incrementar campos automaticamente. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular grandes distâncias de círculo na superfície da terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e distância entre cadeias de caracteres. |
> | [inserir\_nome de usuário](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para controlar quem alterou uma tabela. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes sem nulo de inteiros. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para controlar a hora da última modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções criptográficas. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia tabelas particionadas por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a similaridade de texto alfanumérico com base na correspondência de trigrama. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para implementar a integridade referencial (obsoleto). |
> | análise\_de sessão | Funções para consultar matrizes hstore. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Notificações de alteração disparadas. |
> | [viagem de viagens](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementação de viagem de tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera identificadores universais exclusivos (UUIDs). |

### <a name="hyperscale-extensions"></a>Extensões de hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Banco de dados distribuído Citus. |
> | rebalanceador de fragmento\_ | Equilibre dados com segurança em um grupo de servidores em caso de adição ou remoção de nós. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [cair](https://www.postgresql.org/docs/current/bloom.html) | Incair no método de acesso – índice baseado em arquivo de assinatura. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece exemplos de classes de operadores iniciar que implementam comportamento de árvore B para determinados tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice de totais que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de dados estrangeiros para acesso de arquivo simples. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas do banco de dados em um nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache de buffer compartilhado em tempo real. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Agendador de trabalhos para PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma maneira de carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para controlar as estatísticas de execução de todas as instruções SQL executadas por um servidor. Consulte a seção "pg_stat_statements" para obter informações sobre essa extensão. |
> | [visibilidade\_da PG](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine a VM (mapa de visibilidade) e as informações de visibilidade no nível da página. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio em nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas de nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | O wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados SSL. |
> | [linhas\_do\_sistema TSM](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como um limite. |
> | [hora\_do\_sistema do TSM](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como um limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criar índices hipotéticos que não custam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que dá suporte a conexões com outros bancos de dados PostgreSQL de dentro de uma sessão de Database. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), topologia\_PostGIS, PostGIS\_Tiger\_geocodificador, PostGIS\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | endereço\_padronizador, endereço\_do padronizador\_\_de endereços dos EUA | Usado para analisar um endereço em elementos constituintes. Usado para dar suporte à etapa de normalização de endereço geocodification. |
> | postgis\_sfcgal | PostGIS SFCGAL functions. |
> | postgis\_tiger\_geocoder | Geocodificador PostGIS Tiger e reverso geocodificador. |
> | topologia\_PostGIS | Tipos e funções espaciais de topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão\_de\_instruções de instrução PG](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregadas em cada servidor de banco de dados do Azure para PostgreSQL para fornecer a você meios de controlar estatísticas de execução de instruções SQL.

A configuração `pg_stat_statements.track` controla quais instruções são contadas pela extensão. Ele usa como `top`padrão, o que significa que todas as instruções emitidas diretamente pelos clientes são controladas. Os dois outros níveis de controle `none` são `all`e. Essa configuração é configurável como um parâmetro de servidor por meio do [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma compensação entre as informações de execução de consulta que o pg_stat_statements fornece e o efeito no desempenho do servidor enquanto registra cada instrução SQL. Se você não estiver usando ativamente a extensão pg_stat_statements, recomendamos que você `pg_stat_statements.track` defina `none`como. Alguns serviços de monitoramento de terceiros podem depender do pg_stat_statements para fornecer informações de desempenho de consulta, portanto, confirme se esse é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
Você pode usar dblink e postgres_fdw para se conectar de um servidor PostgreSQL para outro ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Para usar essas extensões para se conectar entre os servidores do banco de dados do Azure para PostgreSQL, defina **permitir acesso aos serviços do Azure** como ativado. Você também precisará ativar essa configuração se quiser usar as extensões para fazer um loop de volta para o mesmo servidor. A configuração **permitir acesso aos serviços do Azure** pode ser encontrada na página portal do Azure para o servidor postgres em **segurança de conexão**. Desativando **permitir o acesso aos serviços do Azure** em listas de permissões todos os IPS do Azure.

Atualmente, não há suporte para conexões de saída do banco de dados do Azure para PostgreSQL, exceto para conexões com outros servidores do banco de dados do Azure para PostgreSQL.
