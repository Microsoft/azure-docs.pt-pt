---
title: Utilizar extensões PostgreSQL
description: Utilizar extensões PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6586375d7db71274f40eb62aeb24f9daad0d7c2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688302"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Utilize extensões PostgreSQL no seu grupo de servidores de hiperescala pós-escala do Arco Azure

PostgreSQL está no seu melhor quando o utiliza com extensões. Na verdade, um elemento chave da nossa própria funcionalidade Hyperscale é a extensão fornecida pela Microsoft `citus` que é instalada por padrão, que permite que postgres sejam transparentemente fragmentos de dados em vários nós.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Extensões apoiadas
As extensões padrão [`contrib`](https://www.postgresql.org/docs/12/contrib.html) e as seguintes extensões já estão implantadas nos contentores do seu grupo de servidores de hiperescala PostgreSQL ativado:
- [`citus`](https://github.com/citusdata/citus), v: 9.4. A extensão Citus by [Citus Data](https://www.citusdata.com/) é carregada por padrão, uma vez que traz a capacidade de Hiperescala para o motor PostgreSQL. Deixar cair a extensão Citus do seu grupo de servidores de hiperescala Azure Arc PostgreSQL não é suportado.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1.2
- [`pgaudit`](https://www.pgaudit.org/), v: 1.4
- plpgsql, v: 1.0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

As atualizações desta lista serão publicadas à medida que evoluem ao longo do tempo.

> [!IMPORTANT]
> Embora possa trazer para o seu grupo de servidor uma extensão diferente das listadas acima, nesta Pré-visualização, esta não será persistiu no seu sistema. Significa que não estará disponível após o reinício do sistema e que terá de o trazer de novo.

Este guia terá um cenário para utilizar duas destas extensões:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Que extensões devem ser adicionadas ao shared_preload_libraries e criadas?

|Extensões   |Requer ser adicionado a shared_preload_libraries  |Requer ser criado |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |No       |Yes        |
|`pg_audit`     |Yes       |Yes        |
|`plpgsql`      |Yes       |Yes        |
|`postgis`      |No       |Yes        |
|`plv8`      |No       |Yes        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Adicione extensões ao shared_preload_libraries
Para mais informações sobre isso são shared_preload_libraries leia a documentação do PostgreSQL [aqui:](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)
- Este passo não é necessário para as extensões que fazem parte de `contrib`
- este passo não é necessário para extensões que não são necessárias para pré-carregar por shared_preload_libraries. Para estas extensões pode saltar o próximo parágrafo [Criar extensões](https://docs.microsoft.com/azure/azure-arc/data/using-extensions-in-postgresql-hyperscale-server-group#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Adicionar uma extensão no tempo de criação de um grupo de servidores
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Adicione uma extensão a um caso que já existe
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Mostrar a lista de extensões adicionadas a shared_preload_libraries
Executar qualquer um dos seguintes comandos.

### <a name="with-an-azdata-cli-command"></a>Com um comando Azdata CLI
```console
azdata arc postgres server show -n <server group name>
```
Desloque-se na saída e note as secções de extensões do motor nas especificações do seu grupo de servidor. Por exemplo:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Com kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Desloque-se na saída e note as secções de extensões do motor nas especificações do seu grupo de servidor. Por exemplo:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Criar extensões
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Mostrar a lista de extensões criadas
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Deixe cair uma extensão
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>A `PostGIS` extensão
Não é necessário adicionar a `PostGIS` extensão ao `shared_preload_libraries` .
Obtenha [dados](http://duspviz.mit.edu/tutorials/intro-postgis/) de amostra do Departamento de Estudos Urbanos do MIT & Planeamento. Corra `apt-get install unzip` para instalar unzip conforme necessário.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Vamos ligar-nos à nossa base de dados e criar a `PostGIS` extensão:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Se quiser utilizar uma das extensões da `postgis` embalagem (por `postgis_raster` `postgis_topology` exemplo, , `postgis_sfcgal` `fuzzystrmatch` ...) tem primeiro de criar a extensão dos pós-fungos e, em seguida, criar a outra extensão. Por `CREATE EXTENSION postgis` exemplo: `CREATE EXTENSION postgis_raster` ;

E criar o esquema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Agora, podemos combinar `PostGIS` com a funcionalidade de escala, fazendo a tabela coffee_shops distribuída:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vamos carregar alguns dados:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E preencha o `geom` campo com a latitude e longitude codificadas corretamente no tipo de `PostGIS` `geometry` dados:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Agora podemos listar os cafés mais próximos do MIT (77 Massachusetts Ave às 42.359055, -71.093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>A `pg_cron` extensão

Agora, vamos ativar o nosso grupo de `pg_cron` servidores PostgreSQL adicionando-o ao shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

O seu grupo de servidor irá reiniciar a instalação das extensões. Pode levar 2 a 3 minutos.

Agora podemos ligar novamente e criar a `pg_cron` extensão:

```sql
CREATE EXTENSION pg_cron;
```

Para efeitos de teste, vamos fazer uma tabela `the_best_coffee_shop` que tenha um nome aleatório da nossa tabela `coffee_shops` anterior, e insira o conteúdo da tabela:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Podemos usar `cron.schedule` mais algumas declarações SQL, para obter um nome de mesa aleatório (note a utilização de uma mesa temporária para armazenar um resultado de consulta distribuído), e armazená-lo em `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

E agora, uma vez por minuto, teremos um nome diferente:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Consulte a [pg_cron README](https://github.com/citusdata/pg_cron) para obter todos os detalhes sobre a sintaxe.


## <a name="next-steps"></a>Passos seguintes
- Ler documentação sobre [`plv8`](https://plv8.github.io/)
- Ler documentação sobre [`PostGIS`](https://postgis.net/)
- Ler documentação sobre [`pg_cron`](https://github.com/citusdata/pg_cron)
