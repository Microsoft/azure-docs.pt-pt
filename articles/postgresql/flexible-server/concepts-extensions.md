---
title: Extensões - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba mais sobre as extensões postgres disponíveis na Base de Dados Azure para PostgreSQL - Servidor Flexível
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938520"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Extensões PostgreSQL na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

O PostgreSQL fornece a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões. As extensões juntam vários objetos SQL relacionados num único pacote que pode ser carregado ou removido da sua base de dados com um comando. Após ser carregado para a base de dados, as extensões funcionam como funcionalidades incorporadas.

## <a name="how-to-use-postgresql-extensions"></a>Como utilizar extensões PostgreSQL
As extensões PostgreSQL devem ser instaladas na sua base de dados antes de as poder utilizar. Para instalar uma extensão específica, executar o comando [DE EXTENSÃO CREATE.](https://www.postgresql.org/docs/current/sql-createextension.html) Este comando coloca os objetos embalados na sua base de dados.

A Azure Database for PostgreSQL suporta um subconjunto de extensões-chave conforme listado abaixo. Esta informação também está disponível através da `SHOW azure.extensions;` execução. As extensões não listadas neste documento não são suportadas na Base de Dados Azure para PostgreSQL - Servidor Flexível. Não é possível criar ou carregar a sua própria extensão na Base de Dados Azure para PostgreSQL.


## <a name="postgres-12-extensions"></a>Extensões postgres 12

As seguintes extensões estão disponíveis na Base de Dados Azure para PostgreSQL - Servidores Flexíveis que têm postgres versão 12. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | funções para verificar a integridade da relação|
> |[florescer](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | bloom access method - índice baseado em ficheiros de assinatura|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | apoio à indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | apoio à indexação de tipos de dados comuns no GiST|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | tipo de dados para cadeias de caracteres insensíveis a casos|
> |[cubo](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | ligar a outras bases de dados PostgreSQL a partir de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para processamento de sinónimo alargado|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | agregador inteiro e enumerador. (Obsoleto)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | funções, operadores e suporte de índice para matrizes 1-D de inteiros|
> |[é](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | tipos de dados para normas internacionais de numeragem de produtos|
> |[Rio Itree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | inspecionar o conteúdo das páginas de base de dados a um nível baixo|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | examinar a cache tampão compartilhado|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | dados de relação pré-guerra|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | medição de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e informações de visibilidade ao nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Linguagens processuais PL/pgSQL|
> |[pós-gígis](https://www.postgis.net/)                      | 3.0.0           | Geometria pós-GIS, geografia e funções espaciais raster|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgreSQL remotos|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informação sobre certificados SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  Método TABLESAMPLE que aceita o número de linhas como limite|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Método TABLESAMPLE que aceita o tempo em milissegundos como limite|
> |[não-acento](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-11-extensions"></a>Extensões postgres 11

As seguintes extensões estão disponíveis na Base de Dados Azure para PostgreSQL - Servidores Flexíveis que têm postgres versão 11. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | funções para verificar a integridade da relação|
> |[florescer](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | bloom access method - índice baseado em ficheiros de assinatura|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | apoio à indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | apoio à indexação de tipos de dados comuns no GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo de dados para cadeias de caracteres insensíveis a casos|
> |[cubo](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ligar a outras bases de dados PostgreSQL a partir de uma base de dados|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para processamento de sinónimo alargado|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcular grandes distâncias de círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinar semelhanças e distância entre cordas|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | agregador inteiro e enumerador. (Obsoleto)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funções, operadores e suporte de índice para matrizes 1-D de inteiros|
> |[é](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipos de dados para normas internacionais de numeragem de produtos|
> |[Rio Itree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | inspecionar o conteúdo das páginas de base de dados a um nível baixo|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | examinar a cache tampão compartilhado|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relação pré-guerra|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e informações de visibilidade ao nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagens processuais PL/pgSQL|
> |[pós-gígis](https://www.postgis.net/)                      | 2.5.1           | Geometria pós-GIS, geografia e funções espaciais raster|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgreSQL remotos|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informação sobre certificados SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Método TABLESAMPLE que aceita o número de linhas como limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Método TABLESAMPLE que aceita o tempo em milissegundos como limite|
> |[não-acento](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|


## <a name="pg_prewarm"></a>pg_prewarm

A extensão pg_prewarm carrega os dados relacionais em cache. Pré-emaraçar os caches significa que as suas consultas têm melhores tempos de resposta na primeira corrida após um recomeço. A funcionalidade auto-pré-guerra não está atualmente disponível na Base de Dados Azure para PostgreSQL - Servidor Flexível.


## <a name="next-steps"></a>Passos seguintes

Se não vir uma extensão que gostaria de usar, avise-nos. Vote nos pedidos existentes ou crie novos pedidos de feedback no nosso [fórum de comentários.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)