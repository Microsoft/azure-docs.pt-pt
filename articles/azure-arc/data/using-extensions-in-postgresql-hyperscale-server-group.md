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
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631771"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Utilize extensões PostgreSQL no seu grupo de servidores de hiperescala pós-escala do Arco Azure

PostgreSQL está no seu melhor quando o utiliza com extensões. Na verdade, um elemento chave da nossa própria funcionalidade Hyperscale é a extensão fornecida pela Microsoft `citus` que é instalada por padrão, que permite que postgres sejam transparentemente fragmentos de dados em vários nós.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Lista de extensões
Além das extensões em [`contrib`](https://www.postgresql.org/docs/12/contrib.html) , a lista de extensões presentes nos contentores do seu grupo de servidores de hiperescala pós-escala Azure Arc ativado é:
- `citus`, v: 9.4
- `pg_cron`, v: 1.2
- `plpgsql`, v: 1.0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Esta lista evolui horas extraordinárias e as atualizações serão publicadas neste documento. Ainda não é possível adicionar extensões para além das listadas acima.

Este guia terá um cenário para utilizar duas destas extensões:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Gerir extensões

### <a name="enable-extensions"></a>Permitir extensões
Este passo não é necessário para as extensões que fazem parte `contrib` de .
O formato geral do comando para permitir extensões é:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Ativar uma extensão no tempo de criação de um grupo de servidores:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Permitir uma extensão de um caso que já existe:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Obtenha a lista de extensões ativadas:
Executar qualquer um dos seguintes comandos.

##### <a name="with-azdata"></a>Com azdata
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
##### <a name="with-kubectl"></a>Com kubectl
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


### <a name="create-extensions"></a>Criar extensões:
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Obtenha a lista de extensões criada no seu grupo de servidores:
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Deixe cair uma extensão do seu grupo de servidor:
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Utilize as extensões postgis e Pg_cron

### <a name="the-postgis-extension"></a>A extensão pós-GIS

Podemos ativar a extensão PostGIS num grupo de servidores existente, ou criar um novo com a extensão já ativada:

**Permitir uma extensão no tempo de criação de um grupo de servidores:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Permitir uma extensão de um caso que já existe:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Para verificar quais as extensões instaladas, utilize o comando PostgreSQL abaixo da norma depois de ligar à instância com a sua ferramenta cliente PostgreSQL favorita como a Azure Data Studio:
```console
select * from pg_extension;
```

Para um exemplo pós-GIS, em primeiro lugar, obtenha [dados](http://duspviz.mit.edu/tutorials/intro-postgis/) de amostra do Departamento de Estudos Urbanos do MIT & Planeamento. Pode ser necessário correr `apt-get install unzip` para instalar unzip quando utilizar o VM para testes.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Vamos ligar-nos à nossa base de dados e criar a extensão PostGIS:

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

Agora, podemos combinar o PostGIS com a funcionalidade scale out, fazendo com que a tabela coffee_shops distribuída:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vamos carregar alguns dados:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E preencha o `geom` campo com a latitude e longitude codificadas corretamente no tipo de dados PostGIS: `geometry`

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Agora podemos listar os cafés mais próximos do MIT (77 Massachusetts Ave às 42.359055, -71.093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>A extensão pg_cron

Vamos ativar o nosso grupo de `pg_cron` servidores PostgreSQL, além do PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Note que isto reiniciará os nós e instalará as extensões adicionais, que podem demorar 2 a 3 minutos.

Agora podemos ligar novamente e criar a `pg_cron` extensão:

```sql
CREATE EXTENSION pg_cron;
```

Para efeitos de teste, vamos fazer uma tabela `the_best_coffee_shop` que tenha um nome aleatório da nossa tabela `coffee_shops` anterior, e definir o conteúdo da tabela:

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

>[!NOTE]
>Não é apoiado para deixar cair a `citus` extensão. A `citus` extensão é necessária para proporcionar a experiência Hyperscale.

## <a name="next-steps"></a>Passos seguintes:
- Leia a documentação em [plv8](https://plv8.github.io/)
- Ler documentação sobre [pós-GIS](https://postgis.net/)
- Ler documentação sobre [`pg_cron`](https://github.com/citusdata/pg_cron)
