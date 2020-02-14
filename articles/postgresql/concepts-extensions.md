---
title: Extensões - Base de Dados Azure para PostgreSQL - Servidor Único
description: Conheça as extensões postgres disponíveis na Base de Dados Azure para PostgreSQL - Servidor Único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201276"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Extensões PostgreSQL na Base de Dados Azure para PostgreSQL - Servidor Único
PostgreSQL fornece a capacidade de alargar a funcionalidade da sua base de dados usando extensões. As extensões agregam vários objetos SQL relacionados num único pacote que pode ser carregado ou removido da sua base de dados com um único comando. Depois de ser carregado na base de dados, as extensões funcionam como características incorporadas.

## <a name="how-to-use-postgresql-extensions"></a>Como utilizar extensões PostgreSQL
As extensões PostgreSQL devem ser instaladas na sua base de dados antes de as utilizar. Para instalar uma determinada extensão, execute o comando create [EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) da ferramenta PSQL para carregar os objetos embalados na sua base de dados.

A Base de Dados Azure para PostgreSQL suporta um subconjunto de extensões chave listadas abaixo. Esta informação também está disponível executando `SELECT * FROM pg_available_extensions;`. As extensões para além das listadas não são suportadas. Não é possível criar a sua própria extensão na Base de Dados Azure para postgreSQL.

## <a name="postgres-11-extensions"></a>Extensões postgres 11

As seguintes extensões estão disponíveis na Base de Dados Azure para servidores PostgreSQL que têm a versão 11 do Postgres. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão de extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de dados do Standardizer DOS|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | suporte para indexar tipos de dados comuns em GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | suporte à indexação de tipos de dados comuns no GiST|
> |[citexto](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo de dados para cordas de caracteres case-insensíveis|
> |[cubo](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ligar a outras bases de dados PostgreSQL dentro de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[distância da terra](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo de dados para armazenamento de conjuntos de pares (chave, valor)|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funções, operadores e suporte indexado para conjuntos 1D de inteiros|
> |[é](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipos de dados para padrões internacionais de numeração de produtos|
> |[árvore](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orato](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | mostrar informação de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | examinar a cache tampão partilhada|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensão para gerir tabelas divididas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relação pré-quente|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição da semelhança do texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Linguagem processual fidedigna PL/JavaScript (v8)|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Pós-GIS geometria, geografia e tipos e funções espaciais de raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funções PósGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificador de tigre postGIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipos e funções espaciais de topologia pós-GIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgresql remotos|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[escalade tempodb](https://docs.timescale.com/latest)                    | 1.3.2             | Permite inserções escaláveis e consultas complexas para dados da série temporal|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-10-extensions"></a>Postgres 10 extensões 

As seguintes extensões estão disponíveis na Base de Dados Azure para servidores PostgreSQL que têm a versão 10 do Postgres.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão de extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de dados do Standardizer DOS|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | suporte para indexar tipos de dados comuns em GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | suporte à indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | tipo de dados para senhas auto-encriptadas|
> |[citexto](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | tipo de dados para cordas de caracteres case-insensíveis|
> |[cubo](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | ligar a outras bases de dados PostgreSQL dentro de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[distância da terra](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | tipo de dados para armazenamento de conjuntos de pares (chave, valor)|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funções, operadores e suporte indexado para conjuntos 1D de inteiros|
> |[é](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | tipos de dados para padrões internacionais de numeração de produtos|
> |[árvore](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orato](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | mostrar informação de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | examinar a cache tampão partilhada|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerir tabelas divididas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dados de relação pré-quente|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | medição da semelhança do texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguagem processual fidedigna PL/JavaScript (v8)|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | Pós-GIS geometria, geografia e tipos e funções espaciais de raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funções PósGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geocodificador de tigre postGIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Tipos e funções espaciais de topologia pós-GIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgresql remotos|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[escalade tempodb](https://docs.timescale.com/latest)                    | 1.1.1             | Permite inserções escaláveis e consultas complexas para dados da série temporal|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-96-extensions"></a>Extensões postgres 9.6 

As seguintes extensões estão disponíveis na Base de Dados Azure para servidores PostgreSQL que têm a versão 9.6 do Postgres.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão de extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Exemplo de conjunto de dados do Standardizer DOS|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | suporte para indexar tipos de dados comuns em GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | suporte à indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | tipo de dados para senhas auto-encriptadas|
> |[citexto](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | tipo de dados para cordas de caracteres case-insensíveis|
> |[cubo](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | ligar a outras bases de dados PostgreSQL dentro de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[distância da terra](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | tipo de dados para armazenamento de conjuntos de pares (chave, valor)|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funções, operadores e suporte indexado para conjuntos 1D de inteiros|
> |[é](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | tipos de dados para padrões internacionais de numeração de produtos|
> |[árvore](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orato](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | mostrar informação de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | examinar a cache tampão partilhada|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerir tabelas divididas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dados de relação pré-quente|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | medição da semelhança do texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguagem processual fidedigna PL/JavaScript (v8)|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | Pós-GIS geometria, geografia e tipos e funções espaciais de raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funções PósGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geocodificador de tigre postGIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Tipos e funções espaciais de topologia pós-GIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgresql remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[escalade tempodb](https://docs.timescale.com/latest)                    | 1.1.1             | Permite inserções escaláveis e consultas complexas para dados da série temporal|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-95-extensions"></a>Extensões postgres 9.5 

As seguintes extensões estão disponíveis na Base de Dados Azure para servidores PostgreSQL que têm a versão 9.5 do Postgres.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão de extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Exemplo de conjunto de dados do Standardizer DOS|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | suporte para indexar tipos de dados comuns em GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | suporte à indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | tipo de dados para senhas auto-encriptadas|
> |[citexto](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | tipo de dados para cordas de caracteres case-insensíveis|
> |[cubo](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | ligar a outras bases de dados PostgreSQL dentro de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[distância da terra](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | tipo de dados para armazenamento de conjuntos de pares (chave, valor)|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funções, operadores e suporte indexado para conjuntos 1D de inteiros|
> |[é](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | tipos de dados para padrões internacionais de numeração de produtos|
> |[árvore](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orato](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | mostrar informação de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | examinar a cache tampão partilhada|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerir tabelas divididas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dados de relação pré-quente|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | medição da semelhança do texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | Pós-GIS geometria, geografia e tipos e funções espaciais de raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funções PósGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geocodificador de tigre postGIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Tipos e funções espaciais de topologia pós-GIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgresql remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | gerar identificadores universalmente únicos (UUIDs)|


## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todas as bases de dados do Azure para o servidor PostgreSQL para lhe fornecer um meio de rastrear estatísticas de execução de declarações SQL.
A definição `pg_stat_statements.track`, que controla as declarações contadas pela extensão, não `top`, significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Os outros dois níveis de rastreio são `none` e `all`. Esta definição é configurável como parâmetro de servidor através do [portal Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou do [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Existe uma compensação entre as informações de execução de consulta pg_stat_statements fornece e o impacto no desempenho do servidor à medida que regista cada declaração SQL. Se não estiver a utilizar ativamente a extensão pg_stat_statements, recomendamos que `pg_stat_statements.track` reserve para `none`. Note que alguns serviços de monitorização de terceiros podem contar com pg_stat_statements para fornecer informações de desempenho de consulta, por isso confirme se este é o caso para si ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) permitem ligar-se de um servidor PostgreSQL a outro, ou a outra base de dados no mesmo servidor. O servidor recetor tem de permitir ligações a partir do servidor de envio através da sua firewall. Ao utilizar estas extensões para ligar entre a Base de Dados Azure para servidores PostgreSQL, isso pode ser feito definindo "Permitir o acesso aos serviços Azure" à ON. Isto também é necessário se pretender utilizar as extensões para voltar ao mesmo servidor. A definição "Permitir o acesso aos serviços Azure" pode ser encontrada na página do portal Azure para o servidor Postgres, no âmbito da Segurança de Ligação. Virar "Permitir o acesso aos serviços Azure" ON coloca todos os IPs Azure na lista de autorizações.

Atualmente, as ligações de saída da Base de Dados Azure para PostgreSQL não são suportadas, exceto ligações a outras bases de dados Azure para servidores PostgreSQL.

## <a name="uuid"></a>uuid
Se estiver a planear utilizar `uuid_generate_v4()` a partir da [extensão uuid-ossp,](https://www.postgresql.org/docs/current/uuid-ossp.html)considere comparar com `gen_random_uuid()` da [extensão pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) para benefícios de desempenho.

## <a name="pgaudit"></a>pgAuditoria
A [extensão pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) fornece registo de sessão e auditoria de objetos. Para saber como utilizar esta extensão na Base de Dados Azure para PostgreSQL, visite o [artigo conceitos](concepts-audit.md)de auditoria. 

## <a name="pg_prewarm"></a>pg_prewarm
A extensão pg_prewarm carrega dados relacionais em cache. Pré-aquecer os seus caches significa que as suas consultas têm melhores tempos de resposta na primeira corrida após um recomeço. Nos Postgres 10 e abaixo, o pré-aquecimento é feito manualmente utilizando a [função pré-enxame](https://www.postgresql.org/docs/10/pgprewarm.html).

Em Postgres 11 ou superior, pode configurar o pré-aquecimento a acontecer [automaticamente](https://www.postgresql.org/docs/current/pgprewarm.html). Tem de incluir pg_prewarm na lista do parâmetro `shared_preload_libraries` e reiniciar o servidor para aplicar a alteração. Os parâmetros podem ser definidos a partir do [portal Azure,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API ou modelo ARM. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB é uma base de dados de séries temporais que é embalada como uma extensão para PostgreSQL. O TimescaleDB fornece funções analíticas orientadas para o tempo, otimizações e escalas Postgres para cargas de trabalho em séries de tempo.

[Saiba mais sobre timescaleDB](https://docs.timescale.com/latest), uma marca registada da [Timescale, Inc.](https://www.timescale.com/). A Base de Dados Azure para PostgreSQL fornece a versão de código aberto da Escala de Tempo. Para saber quais as funcionalidades da Timescale disponíveis nesta versão, consulte [a comparação de produtos timescale](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Instalação timescaleDB
Para instalar o TimescaleDB, é necessário incluí-lo nas bibliotecas de pré-carga partilhadas do servidor. Uma alteração no parâmetro `shared_preload_libraries` do Postgres requer que um **servidor reinicie** para fazer efeito. Pode alterar parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md) ou o [Azure CLI](howto-configure-server-parameters-using-cli.md).

Utilizando o [portal Azure:](https://portal.azure.com/)

1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

2. Na barra lateral, selecione **Parâmetros de servidor**.

3. Procure o parâmetro `shared_preload_libraries`.

4. **Selecione TimescaleDB**.

5. Selecione **Guardar** para preservar as suas alterações. Recebe uma notificação assim que a mudança for guardada. 

6. Após a notificação, **reinicie** o servidor para aplicar estas alterações. Para aprender a reiniciar um servidor, consulte [Reiniciar uma Base de Dados Azure para servidor PostgreSQL](howto-restart-server-portal.md).


Pode agora ativar o TimescaleDB na sua base de dados Postgres. Ligue-se à base de dados e emita o seguinte comando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se vir um erro, confirme que [reiniciou o seu servidor](howto-restart-server-portal.md) depois de guardar shared_preload_libraries. 

Agora pode criar um hiperquadro TimescaleDB [a partir do zero](https://docs.timescale.com/getting-started/creating-hypertables) ou migrar dados da série temporal [existentes no PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Restaurar uma base de dados em escala de tempo
Para restaurar uma base de dados em escala de tempo utilizando pg_dump e pg_restore, é necessário executar dois procedimentos de ajuda na base de dados de destino: `timescaledb_pre_restore()` e `timescaledb_post restore()`.

Primeiro prepare a base de dados de destino:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Agora pode sondar pg_dump na base de dados original e depois fazer pg_restore. Após a restauração, certifique-se de que executa o seguinte comando na base de dados restaurada:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Passos seguintes
Se não virem uma extensão que gostariam de usar, avisem-nos. Vote nos pedidos existentes ou crie novos pedidos de feedback no nosso fórum de [feedback.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
