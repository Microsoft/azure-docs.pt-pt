---
title: Migrar dados de Cassandra para Azure Cosmos DB Cassandra API usando Blitzz
description: Saiba como migrar dados da base de dados Apache Cassandra para Azure Cosmos DB Cassandra API usando Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: c26d21e74e9808fe65890b7f4eba31ee742552a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339993"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrar dados de Cassandra para Azure Cosmos DB Cassandra Conta API usando Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho empresariais em execução em Apache Cassandra por uma variedade de razões tais como: 

* **Sem despesas gerais de gestão e monitorização:** Elimina a sobrecarga de gestão e monitorização de uma miríade de configurações através de sistemas DE, JVM e yaml e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, que inclui o custo de VM's, largura de banda, e quaisquer licenças aplicáveis. Além disso, não é preciso gerir os centros de dados, servidores, armazenamento SSD, networking e custos de eletricidade. 

* **Capacidade de utilizar código e ferramentas existentes:** A Azure Cosmos DB fornece compatibilidade de protocolo de fio com os SDKs e ferramentas existentes da Cassandra. Esta compatibilidade assegura que pode utilizar o seu código base atual com a API para Cassandra do Azure Cosmos DB, com alterações triviais.

Existem várias formas de migrar cargas de trabalho de base de dados de uma plataforma para outra. [Blitzz](https://www.blitzz.io) é uma ferramenta que oferece uma forma segura e fiável de realizar migração de tempo zero de uma variedade de bases de dados para Azure Cosmos DB. Este artigo descreve os passos necessários para migrar dados da base de dados Apache Cassandra para Azure Cosmos DB Cassandra API usando Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Benefícios usando Blitzz para migração

A solução de migração da Blitzz segue uma abordagem passo a passo para migrar cargas operacionais complexas. Seguem-se alguns dos aspectos-chave do plano de migração zero-downtime da Blitzz:

* Oferece migração automática da lógica de negócios (tabelas, índices, vistas) da base de dados Apache Cassandra para a Azure Cosmos DB. Não tens de criar esquemas manualmente.

* Blitzz oferece replicação de base de dados de alto volume e paralelo. Permite que as plataformas de origem e alvo estejam sincronizadas durante a migração utilizando uma técnica chamada Change-Data-Capture (CDC). Ao utilizar o CDC, blitzz retira continuamente um fluxo de alterações da base de dados de origem (Apache Cassandra) e aplica-a à base de dados de destino (Azure Cosmos DB).

* É tolerante a falhas e fornece exatamente uma vez que a entrega de dados mesmo durante uma falha de hardware ou software no sistema.

* Protege os dados durante o trânsito usando uma variedade de metodologias de segurança como tLS, encriptação.

## <a name="steps-to-migrate-data"></a>Passos para migrar dados

Esta secção descreve os passos necessários para configurar blitzz e migra dados da base de dados Apache Cassandra para Azure Cosmos DB.

1. A partir do computador onde planeia instalar o replicante Blitzz, adicione um certificado de segurança. Este certificado é exigido pela réplica Blitzz para estabelecer uma ligação TLS com a conta DB Azure Cosmos especificada. Pode adicionar o certificado com os seguintes passos:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Você pode obter a instalação Blitzz e os ficheiros binários, quer solicitando uma demonstração no [site blitzz](https://www.blitzz.io). Em alternativa, também pode enviar um [e-mail](mailto:success@blitzz.io) para a equipa.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Download de ferramenta réplica Blitzz":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Ficheiros replicantes blitzz":::

1. A partir do terminal CLI, configurar a configuração da base de dados de origem. Abra o ficheiro de configuração usando **`vi conf/conn/cassandra.yml`** o comando e adicione uma lista separada de vírgulas de endereços IP dos nós Cassandra, número de porta, nome de utilizador, senha e quaisquer outros detalhes necessários. Segue-se um exemplo de conteúdo no ficheiro de configuração:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="Editor de conexão Open Cassandra":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Configuração da ligação de Cassandra":::

   Depois de preencher os detalhes da configuração, guarde e feche o ficheiro.

1. Opcionalmente, pode configurar o ficheiro de filtro de base de dados de origem. O ficheiro do filtro especifica quais os esquemas ou tabelas para migrar. Abra o ficheiro de configuração utilizando **`vi filter/cassandra_filter.yml`** o comando e introduza os seguintes detalhes de configuração:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Depois de preencher os detalhes do filtro da base de dados, guarde e feche o ficheiro.

1. Em seguida, configurará a configuração da base de dados de destino. Antes de definir a configuração, [crie uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account) e, em seguida, crie um Keyspace e uma tabela para armazenar os dados migrados. Como está a migrar de Apache Cassandra para Cassandra API em Azure Cosmos DB, pode usar a mesma chave de partição que usou com a Cassandra Apache.

1. Antes de migrar os dados, aumente a produção do recipiente para a quantidade necessária para que a sua aplicação migrar rapidamente. Por exemplo, pode aumentar a produção para 100000 RUs. Escalar a produção antes de iniciar a migração irá ajudá-lo a migrar os seus dados em menos tempo.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Recipiente escala Azure Cosmos em todo":::

   Diminua a produção após a migração estar completa. Com base na quantidade de dados armazenados e RUs necessários para cada operação, pode estimar a produção necessária após a migração de dados. Para saber mais sobre como estimar as RUs necessárias, consulte a [provisão sobre contentores e bases de dados](set-throughput.md) e [estimar RU/s utilizando os artigos de planeamento de capacidades do Azure Cosmos DB.](estimate-ru-with-capacity-planner.md)

1. Obtenha o **Ponto de Contacto, a Porta, o Nome** de Utilizador e a **Palavra-Passe Primária** da sua conta Azure Cosmos a partir do painel de cordas de **ligação.** Utilizará estes valores no ficheiro de configuração.

1. A partir do terminal CLI, configurar a configuração da base de dados de destino. Abra o ficheiro de configuração usando **`vi conf/conn/cosmosdb.yml`** o comando e adicione uma lista separada de vírgula de URI anfitrião, número de porta, nome de utilizador, senha e outros parâmetros necessários. O exemplo a seguir mostra o conteúdo do ficheiro de configuração:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Em seguida, migrar os dados usando Blitzz. Pode executar o replicante Blizz no modo **completo** ou **instantâneo:**

   * **Modo completo** – Neste modo, o replicante continua a correr após a migração e ouve quaisquer alterações no sistema Apache Cassandra de origem. Se detetar alguma alteração, são replicadas na conta Azure Cosmos em tempo real.

   * **Modo snapshot** – Neste modo, pode efetuar a migração de esquemas e a replicação de dados de uma vez. A replicação em tempo real não é suportada com esta opção.

   Utilizando os dois modos acima, a migração pode ser realizada com zero tempo de inatividade. 

1. Para migrar dados, a partir do terminal de ID replicante Blitzz, executar o seguinte comando:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   A UI replicante mostra o progresso da replicação. Uma vez feita a migração de esquemas e a operação instantânea, o progresso mostra 100%. Após a conclusão da migração, pode validar os dados na base de dados target Azure Cosmos.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Produção de migração de dados cassandra":::


1. Como utilizou o modo completo de migração, pode realizar operações como inserir, atualizar ou eliminar dados na base de dados Apache Cassandra. Mais tarde validam que são replicados em tempo real na base de dados target Azure Cosmos. Após a migração, certifique-se de diminuir a produção configurada para o seu contentor Azure Cosmos.

1. Pode parar a réplica em qualquer ponto e reiniciá-la com o interruptor **de currículo.** A replicação retoma a partir do ponto em que parou sem comprometer a consistência dos dados. O seguinte comando mostra como utilizar o interruptor de currículo.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Para saber mais sobre a migração de dados para destino, migração em tempo real, consulte a [demonstração replicante blitzz.](https://www.youtube.com/watch?v=fsUhF9LUZmM)

## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando os artigos de planificador de capacidadeS do Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
