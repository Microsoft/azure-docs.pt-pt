---
title: Migrar dados da Oracle para azure Cosmos DB Cassandra API usando Blitzz
description: Aprenda a migrar dados da base de dados da Oracle para a Azure Cosmos DB Cassandra API usando blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 43d15a7252819a3e4f7635e37458b75e9b7ecca7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546276"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrar dados da Oracle para azure cosmos DB Cassandra API conta usando Blitzz

Cassandra API em Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho empresariais que estão em execução na Oracle por uma variedade de razões como:

* **Melhor escalabilidade e disponibilidade:** Elimina pontos únicos de falha, melhor escalabilidade e disponibilidade para as suas aplicações.

* **Economia significativa de custos:** Você pode economizar custo com Azure Cosmos DB, que inclui o custo de VM's, largura de banda, e quaisquer licenças Oracle aplicáveis. Além disso, não é necessário gerir os centros de dados, servidores, armazenamento sSD, networking e custos de eletricidade.

* **Sem despesas gerais de gestão e monitorização:** Como um serviço de nuvem totalmente gerido, o Azure Cosmos DB remove a sobrecarga de gestão e monitorização de uma miríade de configurações.

Existem várias formas de migrar cargas de trabalho de base de dados de uma plataforma para outra. [Blitzz](https://www.blitzz.io) é uma ferramenta que oferece uma forma segura e fiável de realizar a migração de tempo zero de uma variedade de bases de dados para O Azure Cosmos DB. Este artigo descreve os passos necessários para migrar dados da base de dados da Oracle para a Azure Cosmos DB Cassandra API usando Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Benefícios usando Blitzz para migração

A solução de migração de Blitzz segue uma abordagem passo a passo para migrar cargas de trabalho operacionais complexas. Seguem-se alguns dos aspectos-chave do plano de migração de blitzz:

* Oferece migração automática da lógica empresarial (tabelas, índices, vistas) da base de dados da Oracle para o Azure Cosmos DB. Não tens de criar esquemos manualmente.

* Blitzz oferece replicação de base de dados de alto volume e paralelo. Permite que as plataformas de origem e alvo estejam sincronizadas durante a migração utilizando uma técnica chamada Change-Data-Capture (CDC). Ao utilizar o CDC, blitzz continuamente puxa um fluxo de alterações da base de dados de origem (Oráculo) e aplica-o à base de dados de destino (Azure Cosmos DB).

* É tolerante a falhas e garante exatamente uma vez que a entrega de dados mesmo durante uma falha de hardware ou software no sistema.

* Ele protege os dados durante o trânsito usando uma variedade de metodologias de segurança como TLS/SSL, encriptação.

* Oferece serviços para converter lógica seletória complexa de negócios escrita em PL/SQL para lógica de negócio equivalente em Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Passos para migrar dados

Esta secção descreve os passos necessários para configurar blitzz e migra dados da base de dados da Oracle para o Azure Cosmos DB.

1. Do computador onde planeia instalar o replicante Blitzz, adicione um certificado de segurança. Este certificado é exigido pelo replicante Blitzz para estabelecer uma ligação TLS com a especificada conta Azure Cosmos DB. Pode adicionar o certificado com os seguintes passos:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou pode obter a instalação Blitzz e os ficheiros binários quer solicitando uma demonstração no [site da Blitzz.](https://www.blitzz.io) Em alternativa, também pode enviar um [e-mail](mailto:success@blitzz.io) para a equipa.

   ![Download de ferramenta selente blitzz](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Ficheiros replicantes blitzz](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. A partir do terminal CLI, instale a configuração da base de dados de origem. Abra o ficheiro **`vi conf/conn/oracle.yml`** de configuração utilizando o comando e adicione uma lista separada de endereços IP dos nós do oráculo, número de porta, nome de utilizador, palavra-passe e quaisquer outros detalhes necessários. O seguinte código mostra um ficheiro de configuração de exemplo:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Editor de conexão Open Oracle](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Configuração de ligação oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Depois de preencher os detalhes da configuração, guarde e feche o ficheiro.

1. Opcionalmente, pode configurar o ficheiro de filtro de base de dados de origem. O ficheiro do filtro especifica quais os esquemas ou mesas para migrar. Abra o ficheiro **`vi filter/oracle_filter.yml`** de configuração utilizando o comando e introduza os seguintes detalhes de configuração:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Depois de preencher os dados do filtro de base de dados, guarde e feche o ficheiro.

1. Em seguida, irá configurar a configuração da base de dados de destino. Antes de definir a configuração, [crie uma conta API Da API Da API do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)Cassandra . Escolha a chave de [partição certa](partitioning-overview.md#choose-partitionkey) a partir dos seus dados e, em seguida, crie um Keyspace e uma tabela para armazenar os dados migrados.

1. Antes de migrar os dados, aumente a entrada do recipiente para a quantidade necessária para a sua aplicação migrar rapidamente. Por exemplo, pode aumentar a entrada para 100000 RUs. A escalação da entrada antes de iniciar a migração irá ajudá-lo a migrar os seus dados em menos tempo. 

   ![Scale Azure Cosmos contentor em toda a](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Deve diminuir a entrada após a migração estar completa. Com base na quantidade de dados armazenados e rUs necessários para cada operação, pode estimar a produção necessária após a migração de dados. Para saber mais sobre como estimar as RUs necessárias, consulte a [provisão em recipientes e bases](set-throughput.md) de dados e calcule o [RU/s utilizando os artigos de planificador](estimate-ru-with-capacity-planner.md) de capacidade soro de capacidade do Azure Cosmos DB.

1. Obtenha o Ponto de **Contacto, Porta, Nome de Utilizador**e **Senha Primária** da sua conta Azure Cosmos a partir do painel de cordas de **ligação.** Utilizará estes valores no ficheiro de configuração.

1. A partir do terminal CLI, configurar a configuração da base de dados de destino. Abra o ficheiro **`vi conf/conn/cosmosdb.yml`** de configuração utilizando o comando e adicione uma lista separada da vírposta do anfitrião URI, número de porta, nome de utilizador, palavra-passe e outros parâmetros necessários. Segue-se um exemplo de conteúdo no ficheiro de configuração:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Em seguida, migrar os dados usando Blitzz. Pode executar o replicante Blizz em modo **completo** ou **instantâneo:**

   * **Modo completo** – Neste modo, o replicante continua a funcionar após a migração e ouve quaisquer alterações no sistema Oráculo de origem. Se detetar quaisquer alterações, são replicadas na conta-alvo Azure Cosmos em tempo real.

   * **Modo snapshot** – Neste modo, pode realizar a migração de esquemas e a replicação de dados únicos. A replicação em tempo real não é suportada com esta opção.


   Utilizando os dois modos acima, a migração pode ser realizada com zero tempo de inatividade.

1. Para migrar dados, a partir do terminal CLI replicante blitzz, executar o seguinte comando:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   A UI replicante mostra o progresso da replicação. Uma vez feita a operação de migração e instantâneo de esquemas, o progresso mostra 100%. Após a migração estar concluída, pode validar os dados na base de dados do Target Azure Cosmos.

   ![Produção de migração de dados da Oráculo](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Uma vez que utilizou o modo completo para a migração, pode efetuar operações como inserir, atualizar ou eliminar dados na base de dados da Fonte Oracle. Mais tarde pode validar que são replicados em tempo real na base de dados do target Azure Cosmos. Após a migração, certifique-se de que diminui a entrada configurada para o seu recipiente Azure Cosmos.

1. Pode parar o replicante qualquer ponto e reiniciá-lo com o interruptor **de retoma.** A replicação retoma a partir do ponto em que parou sem comprometer a consistência dos dados. O comando seguinte mostra como utilizar o interruptor de retoma.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Para saber mais sobre a migração de dados para o destino, migração em tempo real, consulte a [demonstração replicante de Blitzz.](https://www.youtube.com/watch?v=y5ZeRK5A-MI)

## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partilha](partitioning-overview.md#choose-partitionkey)
* [Estimar RU/s usando os artigos de planificador](estimate-ru-with-capacity-planner.md) de capacidade do Azure Cosmos DB