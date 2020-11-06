---
title: Descoding lógica - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a descodagem lógica e o wal2json para a captura de dados de alteração na Base de Dados Azure para PostgreSQL - Servidor Único
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 0e9773e5c08f9d07f76a70bc4f899acf5004d3c2
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421814"
---
# <a name="logical-decoding"></a>Descodificação lógica
 
> [!NOTE]
> A descodão lógica está em pré-visualização pública na Base de Dados Azure para PostgreSQL - Servidor Único.

[A descodão lógica no PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) permite-lhe transmitir alterações de dados a consumidores externos. A descodão lógica é popularmente usada para streaming de eventos e alterar cenários de captura de dados.

A descodagem lógica utiliza um plugin de saída para converter o registo de escrita de Postgres (WAL) num formato legível. A Azure Database for PostgreSQL fornece os plugins de saída [wal2json,](https://github.com/eulerto/wal2json) [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e pgoutput. o pgoutput é disponibilizado pela PostgreSQL da versão 10 e mais.

Para uma visão geral de como funciona a descodagem lógica postgres, [visite o nosso blog.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421) 

> [!NOTE]
> A replicação lógica utilizando a publicação/subscrição PostgreSQL não é suportada com base de dados Azure para PostgreSQL - Servidor Único.


## <a name="set-up-your-server"></a>Configurar o seu servidor 
A descodão lógica e a leitura de [réplicas](concepts-read-replicas.md) dependem tanto dos Postgres escreverem antecipadamente (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.

O servidor precisa de ser reiniciado após uma alteração deste parâmetro. Internamente, este parâmetro define os parâmetros postgres, `wal_level` `max_replication_slots` e `max_wal_senders` .

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

1. Desem azure.replication_support para `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Reinicie o servidor para aplicar a alteração.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Se estiver a executar Postgres 9.5 ou 9.6 e utilizar o acesso à rede pública, adicione a regra de firewall para incluir o endereço IP público do cliente de onde executará a replicação lógica. O nome da regra da firewall deve incluir **_replrule**. Por exemplo, *test_replrule.* Para criar uma nova regra de firewall no servidor, executar o comando [de firewall do servidor az postgres criar.](/cli/azure/postgres/server/firewall-rule) 

### <a name="using-azure-portal"></a>Com o Portal do Azure

1. Desa parte do suporte de replicação do Azure para **ser lógico**. Selecione **Save** (Guardar).

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Base de Dados Azure para PostgreSQL - Replicação - Suporte de replicação Azure":::

2. Reinicie o servidor para aplicar a alteração selecionando **Sim**.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Base de Dados Azure para PostgreSQL - Replicação - Confirme o reinício":::

3. Se estiver a executar Postgres 9.5 ou 9.6 e utilizar o acesso à rede pública, adicione a regra de firewall para incluir o endereço IP público do cliente de onde executará a replicação lógica. O nome da regra da firewall deve incluir **_replrule**. Por exemplo, *test_replrule.* Em seguida, clique em **Guardar**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Base de Dados Azure para PostgreSQL - Replicação - Adicionar regra de firewall":::

## <a name="start-logical-decoding"></a>Começar a descodição lógica

A descodão lógica pode ser consumida através do protocolo de streaming ou da interface SQL. Ambos os métodos utilizam [ranhuras de replicação.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) Uma ranhura representa um fluxo de alterações a partir de uma única base de dados.

Usar uma ranhura de replicação requer os privilégios de replicação de Postgres. Neste momento, o privilégio de replicação só está disponível para o utilizador administrativo do servidor. 

### <a name="streaming-protocol"></a>Protocolo de streaming
Consumir alterações usando o protocolo de streaming é muitas vezes preferível. Pode criar o seu próprio consumidor/conector ou utilizar uma ferramenta como [a Debezium.](https://debezium.io/) 

Visite a documentação wal2json por [exemplo usando o protocolo de streaming com pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interface SQL
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


## <a name="monitoring-slots"></a>Faixas horárias de monitorização

Tens de monitorizar a descodagem lógica. Qualquer ranhura de replicação nãousada deve ser largada. As ranhuras mantêm-se nos registos POSTGRES WAL e catálogos de sistemas relevantes até que as alterações tenham sido lidas por um consumidor. Se o seu consumidor falhar ou não tiver sido devidamente configurado, os troncos não consumados acumulam-se e enchem o seu armazenamento. Além disso, os registos não consumados aumentam o risco de transação de iD wrap. Ambas as situações podem fazer com que o servidor fique indisponível. Portanto, é fundamental que as ranhuras lógicas de replicação sejam consumidas continuamente. Se uma ranhura de replicação lógica já não for utilizada, largue-a imediatamente.

A coluna "ativa" na visão pg_replication_slots indicará se existe um consumidor ligado a uma ranhura.
```SQL
SELECT * FROM pg_replication_slots;
```

[Desave alertas](howto-alert-on-metric.md) sobre *o Armazenamento utilizado* e o Max *desfase através das métricas de réplicas* para o notificar quando os valores aumentarem para além dos limiares normais. 

> [!IMPORTANT]
> Tens de largar as ranhuras de replicação não usudas. Se não o fizer, pode levar à indisponibilidade do servidor.

## <a name="how-to-drop-a-slot"></a>Como deixar cair uma ranhura
Se não estiver a consumir ativamente uma ranhura de replicação, deve larhá-la.

Para deixar cair uma ranhura de replicação chamada `test_slot` SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se parar de descodição lógica, mude azure.replication_support para `replica` ou `off` . Os detalhes do WAL retidos são `logical` mais verbosos, e devem ser desativados quando a descodão lógica não estiver a ser utilizada. 

 
## <a name="next-steps"></a>Passos seguintes

* Visite a documentação postgres para [saber mais sobre a descodagem lógica.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Contacte a [nossa equipa](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) se tiver dúvidas sobre descodição lógica.
* Saiba mais sobre [réplicas lidas.](concepts-read-replicas.md)

