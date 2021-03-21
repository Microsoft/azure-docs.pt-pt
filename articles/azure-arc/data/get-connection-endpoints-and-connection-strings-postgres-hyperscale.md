---
title: Obtenha pontos finais de ligação e forme cordas de conexão para o seu grupo de servidor de hiperescala pós-escala pós-escala ativado pelo Arco
titleSuffix: Azure Arc enabled data services
description: Obtenha pontos finais de ligação e forme cordas de conexão para o seu grupo de servidor de hiperescala pós-escala pós-escala ativado pelo Arco
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670346"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Obtenha pontos finais de ligação e forme cordas de conexão para o seu grupo de servidor de hiperescala pós-escala pós-escala ativado pelo Arco

Este artigo explica como pode recuperar os pontos finais de ligação para o seu grupo de servidor e como forma cordas de ligação que utilizará com as suas aplicações e/ou ferramentas.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obtenha pontos finais de ligação:

### <a name="from-cli-with-azdata"></a>Do CLI com azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Ligue-se ao seu Controlador de Dados do Arco:
- Se já tiver uma sessão aberta sobre o anfitrião do Controlador de Dados do Arco: Executar o seguinte comando:
```console
azdata login
```

- Se não tiver uma sessão aberta sobre o anfitrião do Controlador de Dados do Arco: executar o seguinte comando 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. Mostrar os pontos finais de ligação
Execute o seguinte comando:
```console
azdata arc postgres endpoint list -n <server group name>
```
Por exemplo:
```console
azdata arc postgres endpoint list -n postgres01
```

Mostra a lista de pontos finais: o ponto final postgreSQL que utiliza para ligar a sua aplicação e utilizar os pontos finais da base de dados, Kibana e Grafana para análise e monitorização de registos. Por exemplo: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Utilize estes pontos finais para:
- Forme as suas cordas de ligação e conecte-se com as ferramentas ou aplicações do seu cliente
- Aceder aos dashboards do Grafana e Kibana a partir do browser

Por exemplo, pode utilizar o ponto final chamado _PostgreSQL Instance_ para se conectar com o psql ao seu grupo de servidor. Por exemplo:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - A palavra-passe do utilizador _de postgres_ indicado no ponto final denominado "_PostgreSQL Instance_" é a palavra-passe que escolheu ao implementar o grupo de servidor.
> - Sobre a azdata: o arrendamento associado à sua ligação dura cerca de 10 horas. Depois disso, tens de voltar a ligar-te. Se o seu contrato de arrendamento tiver expirado, receberá a seguinte mensagem de erro quando tentar executar um comando com azdata (com outros dados): _ERROR: (401)_ 
>  _Reason: Headers de_ resposta HTTP não 
>  _autorizados: HTTPHeaderDict ({'Date': 'Sun, 06 set 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '_ Basic 
>  _realm="Credenciais de login_ necessárias", erro do portador="invalid_token", error_description="O token está expirado"}}_Quando isso acontece, é necessário voltar a ligar-se aos dados como explicado acima.

## <a name="from-cli-with-kubectl"></a>De CLI com kubectl
- Se o seu grupo de servidor é da versão 12 do Postgres (padrão), então o seguinte comando:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Se o seu grupo de servidor é da versão 11 do Postgres, então o seguinte comando:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

Estes comandos produzirão saídas como a de baixo. Pode utilizar essa informação para formar as suas cadeias de ligação:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Cadeias de ligação de formulários:
Utilize a tabela abaixo dos modelos de cordas de ligações para o seu grupo de servidor. Em seguida, pode copiar/colar e personalizá-los conforme necessário:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Passos seguintes
- Leia sobre [a escala (adicionar nós de trabalhador)](scale-out-postgresql-hyperscale-server-group.md) o seu grupo de servidor
- Leia sobre [o escalonamento para cima ou para baixo (aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) o seu grupo de servidor


