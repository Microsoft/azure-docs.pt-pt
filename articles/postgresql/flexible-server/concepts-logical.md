---
title: Replicação lógica e descodagem lógica - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba mais sobre a utilização de replicação lógica e descodagem lógica na Base de Dados Azure para PostgreSQL - Servidor Flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91707868"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Replicação lógica e descodagem lógica na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

As funcionalidades de replicação lógica e descodão lógica da PostgreSQL são suportadas na Base de Dados Azure para PostgreSQL - Servidor Flexível, para a versão 11 do Postgres.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Comparando a replicação lógica e a descodagem lógica
A replicação lógica e a descodão lógica têm várias semelhanças. Ambos.
* permitir-lhe replicar dados de Postgres
* usar o [log write-ahead (WAL)](https://www.postgresql.org/docs/current/wal.html) como fonte de alterações
* usar [ranhuras de replicação lógica para](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) enviar dados. Uma ranhura representa um fluxo de alterações.
* usar a [propriedade REPLICA IDENTITY](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) de uma tabela para determinar que alterações podem ser enviadas
* não replicar alterações de DDL


As duas tecnologias têm as suas diferenças: replicação lógica 
* permite especificar uma tabela ou conjunto de tabelas a replicar
* replica dados entre instâncias PostgreSQL

Descodificação lógica 
* extratos alterações em todas as tabelas em uma base de dados 
* não pode enviar diretamente dados entre instâncias PostgreSQL


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Pré-requisitos para a replicação lógica e descodição lógica

1. Desa estale o parâmetro do servidor `wal_level` para `logical` .
2. Reinicie o servidor para aplicar a `wal_level` alteração.
3. Confirme que a sua instância PostgreSQL permite o tráfego de rede a partir do seu recurso de ligação.
4. Conceda permissões de replicação do utilizador administrativo.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Usando a replicação lógica e a descodagem lógica

### <a name="logical-replication"></a>Replicação lógica
A replicação lógica utiliza os termos "editor" e "assinante". 
* A editora é a base de dados PostgreSQL de onde está a enviar **dados.** 
* O assinante é a base de dados PostgreSQL para a qual está a enviar **dados.**

Aqui está um código de amostra que pode usar para experimentar a replicação lógica.

1. Ligue-se à base de dados dos editores. Crie uma tabela e adicione alguns dados.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Crie uma publicação para a mesa.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Ligue-se à base de dados do assinante. Crie uma mesa com o mesmo esquema que na editora.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Crie uma subscrição que se ligue à publicação que criou anteriormente.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Pode agora consultar a tabela do assinante. Verá que recebeu dados da editora.
   ```SQL
   SELECT * FROM basic;
   ```

Pode adicionar mais linhas à mesa da editora e ver as alterações no assinante.

Visite a documentação postgreSQL para saber mais sobre [a replicação lógica.](https://www.postgresql.org/docs/current/logical-replication.html)

### <a name="logical-decoding"></a>Descodificação lógica
A descodão lógica pode ser consumida através do protocolo de streaming ou da interface SQL. 

#### <a name="streaming-protocol"></a>Protocolo de streaming
Consumir alterações usando o protocolo de streaming é muitas vezes preferível. Pode criar o seu próprio consumidor/conector ou utilizar um serviço de terceiros como o [Debezium](https://debezium.io/). 

Visite a documentação wal2json por [exemplo usando o protocolo de streaming com pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>Interface SQL 
No exemplo abaixo, utilizamos a interface SQL com o plugin wal2json.
 
1. Criar uma ranhura.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Emitir comandos SQL. Por exemplo:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consumir as mudanças.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   A saída será como:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Largue a ranhura assim que terminar de a utilizar.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Visite a documentação postgreSQL para saber mais sobre [a descodagem lógica.](https://www.postgresql.org/docs/current/logicaldecoding.html)


## <a name="monitoring"></a>Monitorização
Tens de monitorizar a descodagem lógica. Qualquer ranhura de replicação nãousada deve ser largada. As ranhuras mantêm-se nos registos POSTGRES WAL e catálogos de sistemas relevantes até que as alterações tenham sido lidas. Se o seu assinante ou consumidor falhar ou não tiver sido devidamente configurado, os registos não consumados irão acumular-se e encher o seu armazenamento. Além disso, os registos não consumados aumentam o risco de transação de iD wrap. Ambas as situações podem fazer com que o servidor fique indisponível. Portanto, é fundamental que as ranhuras lógicas de replicação sejam consumidas continuamente. Se uma ranhura de replicação lógica já não for utilizada, largue-a imediatamente.

A coluna "ativa" na visão pg_replication_slots indicará se existe um consumidor ligado a uma ranhura.
```SQL
SELECT * FROM pg_replication_slots;
```

[Desafete alertas](howto-alert-on-metrics.md) sobre os **IDs de Transação Máxima Utilizados** e **armazenamento Usou** métricas flexíveis do servidor para o notificar quando os valores aumentarem os limiares normais. 

## <a name="limitations"></a>Limitações
* **Leia réplicas** - Azure Database for PostgreSQL read replicas não são atualmente suportadas para servidores flexíveis.
* **Slots e falha ha -** As ranhuras de replicação lógica no servidor primário não estão disponíveis no servidor de espera no seu AZ secundário. Isto aplica-se a si se o seu servidor utilizar a opção de alta disponibilidade redundante. Em caso de falha no servidor de espera, não estarão disponíveis slots de replicação lógicas no standby.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [opções de networking](concepts-networking.md)
* Saiba mais [sobre as extensões](concepts-extensions.md) disponíveis no servidor flexível
* Saiba mais sobre [alta disponibilidade](concepts-high-availability.md)

