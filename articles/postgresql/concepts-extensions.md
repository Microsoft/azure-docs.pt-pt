---
title: Usar extensões do PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único
description: Descreve a capacidade de estender a funcionalidade do seu banco de dados usando extensões no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/10/2019
ms.openlocfilehash: 383f5acb9f106bb4697433be99c53bb78d00b396
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091132"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Extensões PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único
O PostgreSQL fornece a capacidade de estender a funcionalidade do seu banco de dados usando extensões. As extensões agrupam vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido do banco de dados com um único comando. Depois de ser carregado no banco de dados, as extensões funcionam como recursos internos.

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões do PostgreSQL
As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [criar extensão](https://www.postgresql.org/docs/current/sql-createextension.html) da ferramenta psql para carregar os objetos empacotados em seu banco de dados.

O banco de dados do Azure para PostgreSQL dá suporte a um subconjunto de extensões de chave, conforme listado abaixo. Essas informações também estão disponíveis por meio `SELECT * FROM pg_available_extensions;`da execução do. Não há suporte para extensões além daquelas listadas. Você não pode criar sua própria extensão no banco de dados do Azure para PostgreSQL.

## <a name="postgres-11-extensions"></a>Extensões do postgres 11

As extensões a seguir estão disponíveis no banco de dados do Azure para servidores PostgreSQL que têm a versão 11 do Postgres. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de texto comuns no|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas como de árvore|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emulam um subconjunto de funções e pacotes do RDBMS comercial|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Mostrar estatísticas de nível de tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensão para gerenciar tabelas particionadas por hora ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Linguagem de procedimento confiável PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | Funções e tipos espaciais de PostGIS, geography e rasterização|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | gerar identificadores universais exclusivos (UUIDs)|

## <a name="postgres-10-extensions"></a>Extensões do postgres 10 

As extensões a seguir estão disponíveis no banco de dados do Azure para servidores PostgreSQL que têm a versão 10 do Postgres.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de texto comuns no|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automaticamente|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas como de árvore|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emulam um subconjunto de funções e pacotes do RDBMS comercial|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | Mostrar estatísticas de nível de tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por hora ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguagem de procedimento confiável PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.4.3           | Funções e tipos espaciais de PostGIS, geography e rasterização|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Permite inserções escalonáveis e consultas complexas para dados de série temporal|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | gerar identificadores universais exclusivos (UUIDs)|

## <a name="postgres-96-extensions"></a>Extensões do postgres 9,6 

As seguintes extensões estão disponíveis no banco de dados do Azure para servidores PostgreSQL que têm a versão postgres 9,6.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | suporte para indexação de tipos de texto comuns no|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automaticamente|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas como de árvore|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emulam um subconjunto de funções e pacotes do RDBMS comercial|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Mostrar estatísticas de nível de tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por hora ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguagem de procedimento confiável PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.2           | Funções e tipos espaciais de PostGIS, geography e rasterização|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Permite inserções escalonáveis e consultas complexas para dados de série temporal|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | gerar identificadores universais exclusivos (UUIDs)|

## <a name="postgres-95-extensions"></a>Extensões do postgres 9,5 

As seguintes extensões estão disponíveis no banco de dados do Azure para servidores PostgreSQL que têm a versão postgres 9,5.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | suporte para indexação de tipos de texto comuns no|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automaticamente|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | tipo de dados para estruturas hierárquicas como de árvore|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emulam um subconjunto de funções e pacotes do RDBMS comercial|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Mostrar estatísticas de nível de tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por hora ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.0           | Funções e tipos espaciais de PostGIS, geography e rasterização|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | gerar identificadores universais exclusivos (UUIDs)|


## <a name="pg_stat_statements"></a>pg_stat_statements
A extensão pg_stat_statements é pré-carregado em cada servidor de banco de dados do Azure para PostgreSQL para fornecer a você um meio de controlar estatísticas de execução de instruções SQL.
A configuração `pg_stat_statements.track`, que controla quais instruções são contadas pela extensão, usa como `top`padrão, o que significa que todas as instruções emitidas diretamente pelos clientes são controladas. Os dois outros níveis de controle `none` são `all`e. Essa configuração é configurável como um parâmetro de servidor por meio do [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma compensação entre as informações de execução de consulta que o pg_stat_statements fornece e o impacto no desempenho do servidor enquanto registra cada instrução SQL. Se você não estiver usando ativamente a extensão pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none`. Observe que alguns serviços de monitoramento de terceiros podem depender do pg_stat_statements para fornecer informações de desempenho de consulta, portanto, confirme se esse é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
dblink e postgres_fdw permitem que você se conecte de um servidor PostgreSQL para outro ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Ao usar essas extensões para se conectar entre os servidores do banco de dados do Azure para PostgreSQL, isso pode ser feito definindo "permitir acesso aos serviços do Azure" como ativado. Isso também é necessário se você quiser usar as extensões para fazer um loop de volta para o mesmo servidor. A configuração "permitir acesso aos serviços do Azure" pode ser encontrada na página portal do Azure para o servidor Postgres, em segurança de conexão. A ativação de "permitir acesso aos serviços do Azure" no coloca todos os IPs do Azure na lista de permissões.

Atualmente, não há suporte para conexões de saída do banco de dados do Azure para PostgreSQL, exceto para conexões com outros servidores do banco de dados do Azure para PostgreSQL.

## <a name="uuid"></a>uuid
Se você estiver planejando usar `uuid_generate_v4()` a extensão UUID-OSSP, considere comparar com `gen_random_uuid()` a extensão pgcrypto para obter os benefícios de desempenho.


## <a name="pgaudit"></a>pgAudit
A extensão pgAudit fornece log de auditoria de sessão e objeto. Para saber como usar essa extensão no banco de dados do Azure para PostgreSQL, visite o [artigo conceitos de auditoria](concepts-audit.md). 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB é um banco de dados de série temporal que é empacotado como uma extensão para PostgreSQL. O TimescaleDB fornece funções analíticas orientadas a tempo, otimizações e escalas postgres para cargas de trabalho de série temporal.

[Saiba mais sobre o TimescaleDB](https://docs.timescale.com/latest), uma marca registrada da [TIMESCALE, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Instalando o TimescaleDB
Para instalar o TimescaleDB, você precisa incluí-lo nas bibliotecas de pré-carregamento compartilhadas do servidor. Uma alteração no parâmetro de `shared_preload_libraries` postgres requer a **reinicialização do servidor** para entrar em vigor. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou o [CLI do Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> O TimescaleDB pode ser habilitado no banco de dados do Azure para PostgreSQL versões 9,6 e 10

Usando o [portal do Azure](https://portal.azure.com/):

1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

2. Na barra lateral, selecione **parâmetros do servidor**.

3. Procure o `shared_preload_libraries` parâmetro.

4. Selecione **TimescaleDB**.

5. Selecione **salvar** para preservar suas alterações. Você receberá uma notificação quando a alteração for salva. 

6. Após a notificação, **reinicie** o servidor para aplicar essas alterações. Para saber como reiniciar um servidor, consulte [reiniciar um banco de dados do Azure para o servidor PostgreSQL](howto-restart-server-portal.md).


Agora você pode habilitar TimescaleDB em seu banco de dados do Postgres. Conecte-se ao banco de dados e emita o seguinte comando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se você vir um erro, confirme que você [reiniciou o servidor depois de salvar o](howto-restart-server-portal.md) shared_preload_libraries. 

Agora você pode criar uma hipertabela TimescaleDB [do zero](https://docs.timescale.com/getting-started/creating-hypertables) ou migrar [dados existentes de série temporal no PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Passos Seguintes
Se você não vir uma extensão que gostaria de usar, informe-nos. Vote em solicitações existentes ou crie novas solicitações de comentários em nosso [Fórum de comentários](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
