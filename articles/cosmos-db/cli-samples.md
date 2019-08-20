---
title: Exemplos de CLI do Azure para Azure Cosmos DB
description: Exemplos de CLI do Azure – Criar e gerir contas, bases de dados, contentores, regiões e firewalls do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615577"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para o Azure Cosmos DB. As páginas de referência para todos os comandos da CLI do Azure Cosmos DB encontram-se disponíveis em [Referência da CLI do Azure](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Criar a conta, a base de dados e os contentores do Azure Cosmos DB**||
| [Criar uma conta de Azure Cosmos DB usando a API do SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma única conta de Azure Cosmos DB, banco de dados e contêiner. |
| [Criar uma conta de Azure Cosmos DB usando a API do Cosmos DB para MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta de Azure Cosmos DB, um banco de dados e uma coleção. |
| [Criar uma conta de Azure Cosmos DB usando a API do Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta de Azure Cosmos DB, banco de dados e grafo. |
| [Criar uma conta de Azure Cosmos DB usando API do Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta de Azure Cosmos DB e um banco de dados. |
| [Criar uma conta de Azure Cosmos DB usando API de Tabela](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta de Azure Cosmos DB, banco de dados e tabela. |
|**Dimensionar o Azure Cosmos DB**||
| [Dimensionar o débito do contentor](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Altera o débito aprovisionado num contentor.|
| [Replicar a conta do banco de dados Cosmos do Azure em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replica globalmente os dados da conta em várias regiões com uma prioridade de ativação pós-falha especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves de contas](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém as chaves de escrita mestra primária e secundária, e as chaves só de leitura primária e secundária da conta.|
| [Obter a cadeia de conexão para a conta Cosmos configurada com a API do Azure Cosmos DB para MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém a cadeia de conexão para conectar o aplicativo MongoDB à sua conta de Azure Cosmos DB.|
| [Voltar a gerar chaves da conta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Regenere as chaves da conta.|
| [Criar uma firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma política de controlo de acesso IP de entrada para limitar o acesso à conta a partir de um conjunto aprovado de computadores e/ou serviços cloud.|
|**Elevada disponibilidade, recuperação após desastre, cópia de segurança e restauro**||
| [Configurar política de ativação pós-falha](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Define a prioridade de ativação pós-falha de cada região em que a conta é replicada.|
|**Ligar o Azure Cosmos DB a recursos**||
| [Ligar uma aplicação Web ao Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Crie e conecte um banco de dados Cosmos do Azure e um aplicativo Web do Azure.|
|||
