---
title: Descodificação lógica - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a descodificação lógica e o wal2json para a captura de dados de alteração na Base de Dados Azure para PostgreSQL - Servidor Único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522150"
---
# <a name="logical-decoding"></a>Descodificação lógica
 
[A descodificação lógica no PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) permite-lhe transmitir alterações de dados a consumidores externos. A descodificação lógica é popularmente usada para streaming de eventos e alterar cenários de captura de dados.

A descodificação lógica utiliza um plugin de saída para converter o registo de escrita de Postgres à frente (WAL) num formato legível. A Base de Dados Azure para PostgreSQL fornece dois plugins de saída: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> A descodificação lógica está em pré-visualização pública na Base de Dados Azure para PostgreSQL - Servidor Único.


## <a name="set-up-your-server"></a>Configurar o seu servidor
Para começar a usar a descodificação lógica, ative o seu servidor para guardar e transmitir o WAL. 

1. Coloque o azure.replication_support para `logical` utilizar o Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Se utilizar réplicas de leitura, o `logical` conjunto azure.replication_support também permite que as réplicas corram. Se parar de utilizar a descodificação lógica, mude a definição para `replica`. 


2. Reiniciar o servidor para aplicar as alterações.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Iniciar a descodificação lógica

A descodificação lógica pode ser consumida através de protocolo de streaming ou interface SQL. Ambos os métodos utilizam ranhuras de [replicação.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) Uma ranhura representa um fluxo de alterações a partir de uma única base de dados.

Usar uma ranhura de replicação requer privilégios de replicação do Postgres. Neste momento, o privilégio de replicação só está disponível para o utilizador administrativo do servidor. 

### <a name="streaming-protocol"></a>Protocolo de streaming
Consumir alterações utilizando o protocolo de streaming é muitas vezes preferível. Pode criar o seu próprio consumidor/ conector, ou usar uma ferramenta como [a Debezium](https://debezium.io/). 

Visite a documentação wal2json por [exemplo usando o protocolo de streaming com pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interface SQL
No exemplo abaixo, usamos a interface SQL com o plugin wal2json.
 
1. Criar uma vaga.
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

3. Consuma as mudanças.
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

4. Largue a ranhura assim que terminar de usá-la.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Slots de monitorização

Tens de monitorizar a descodificação lógica. Qualquer ranhura de replicação não utilizada deve ser largada. As faixas horárias agarram-se aos registos do Postgres WAL e aos catálogos de sistemas relevantes até que as alterações tenham sido lidas por um consumidor. Se o seu consumidor falhar ou não estiver devidamente configurado, os troncos não consumidos acumular-se-ão e encherão o seu armazenamento. Além disso, os registos não consumidos aumentam o risco de envolvimento de ID de transação. Ambas as situações podem fazer com que o servidor fique indisponível. Por isso, é fundamental que as ranhuras de replicação lógica sejam consumidas continuamente. Se uma ranhura lógica de replicação já não for utilizada, deixe-a imediatamente.

A coluna "ativa" na visão pg_replication_slots indicará se existe um consumidor ligado a uma ranhura.
```SQL
SELECT * FROM pg_replication_slots;
```

[Detete alertas](howto-alert-on-metric.md) no *Armazenamento utilizado* e o max lag através das métricas das *réplicas* para notificá-lo quando os valores aumentam os limiares normais. 

> [!IMPORTANT]
> Tens de largar ranhuras de replicação não utilizadas. Não o fazer pode levar à indisponibilidade do servidor.

## <a name="how-to-drop-a-slot"></a>Como deixar cair uma ranhura
Se não estiver a consumir ativamente uma ranhura de replicação, deve deixá-la cair.

Para deixar cair uma `test_slot` ranhura de replicação chamada sQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se parar de usar descodificação lógica, `replica` `off`mude de azul.replication_support de volta para ou . Os detalhes wal `logical` retidos por são mais verbosos, e devem ser desativados quando a descodificação lógica não está em uso. 

 
## <a name="next-steps"></a>Passos seguintes

* Visite a documentação postgres para [saber mais sobre descodificação lógica.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Contacte a [nossa equipa](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) se tiver dúvidas sobre descodificação lógica.
* Saiba mais sobre [as réplicas de leitura.](concepts-read-replicas.md)

