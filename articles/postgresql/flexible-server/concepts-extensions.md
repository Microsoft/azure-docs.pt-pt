---
title: Extensões - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba mais sobre as extensões postgres disponíveis na Base de Dados Azure para PostgreSQL - Servidor Flexível
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 70f54fc111bfd9443f988619cb2b86303fd3f07b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443391"
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
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Exemplo de conjunto de dados do Standardizer US de endereço|
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
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.2             | Programador de emprego para PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | dados de relação pré-guerra|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | medição de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e informações de visibilidade ao nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pglogico](https://github.com/2ndQuadrant/pglogical)                        | 2.3.2             | Replicação lógica de PostgreSQL|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Linguagens processuais PL/pgSQL|
> |[pós-gígis](https://www.postgis.net/)                      | 3.0.0           | Geometria pós-GIS, geografia |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | Tipos e funções de raster pós-GIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | Funções SFCGAL pós-gis|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | Geocoder de tigre pós-GIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Tipos e funções espaciais de topologia pós-GIS|
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
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de dados do Standardizer US de endereço|
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
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.2             | Programador de emprego para PostgreSQL|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relação pré-guerra|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | rastrear estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e informações de visibilidade ao nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funções criptográficas|
> |[pglogico](https://github.com/2ndQuadrant/pglogical)                        | 2.3.2             | Replicação lógica de PostgreSQL|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio de nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagens processuais PL/pgSQL|
> |[pós-gígis](https://www.postgis.net/)                      | 2.5.1           | Geometria pós-GIS, geografia e funções espaciais raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funções SFCGAL pós-gis|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocoder de tigre pós-GIS e geocodificador invertido|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipos e funções espaciais de topologia pós-GIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores postgreSQL remotos|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informação sobre certificados SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Método TABLESAMPLE que aceita o número de linhas como limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Método TABLESAMPLE que aceita o tempo em milissegundos como limite|
> |[não-acento](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | gerar identificadores universalmente únicos (UUIDs)|


## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) permitem-lhe ligar de um servidor PostgreSQL a outro, ou a outra base de dados no mesmo servidor. O servidor de envio precisa de permitir ligações de saída ao servidor recetor. O servidor recetor precisa de permitir ligações a partir do servidor de envio.

Recomendamos a implementação dos seus servidores com [integração VNet](concepts-networking.md) se pretender utilizar estas duas extensões. Por defeito, a integração VNet permite ligações entre servidores no VNET. Também pode optar por utilizar [grupos de segurança de rede VNet](../../virtual-network/manage-network-security-group.md) para personalizar o acesso.


## <a name="pg_prewarm"></a>pg_prewarm

A extensão pg_prewarm carrega os dados relacionais em cache. Pré-emaraçar os caches significa que as suas consultas têm melhores tempos de resposta na primeira corrida após um recomeço. A funcionalidade auto-pré-guerra não está atualmente disponível na Base de Dados Azure para PostgreSQL - Servidor Flexível.

## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todas as bases de dados Azure para servidor flexível PostgreSQL para fornecer-lhe um meio de rastrear as estatísticas de execução de declarações SQL.
A definição `pg_stat_statements.track` , que controla as declarações contadas pela extensão, não tem `top` padrão, o que significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Os outros dois níveis de rastreio são `none` `all` e. Esta definição é configurável como parâmetro do servidor.

Existe uma compensação entre a informação de execução de consultas pg_stat_statements fornece e o impacto no desempenho do servidor à medida que regista cada declaração SQL. Se não estiver a utilizar ativamente a extensão pg_stat_statements, recomendamos que se desemalte `pg_stat_statements.track` `none` . Note que alguns serviços de monitorização de terceiros podem contar com pg_stat_statements para fornecer insights de desempenho de consulta, por isso confirme se este é o caso para si ou não.


## <a name="next-steps"></a>Passos seguintes

Se não vir uma extensão que gostaria de usar, avise-nos. Vote nos pedidos existentes ou crie novos pedidos de feedback no nosso [fórum de comentários.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)