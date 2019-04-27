---
title: Exemplos da CLI do Azure para o Azure Cosmos DB
description: Exemplos de CLI do Azure – Criar e gerir contas, bases de dados, contentores, regiões e firewalls do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892630"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para o Azure Cosmos DB. As páginas de referência para todos os comandos da CLI do Azure Cosmos DB encontram-se disponíveis em [Referência da CLI do Azure](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Criar a conta, a base de dados e os contentores do Azure Cosmos DB**||
| [Criar uma conta do Azure Cosmos DB com a API de SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um único do Azure Cosmos DB conta, base de dados e de contentor. |
| [Criar uma conta do Azure Cosmos DB com a API do Cosmos DB do MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta do Azure Cosmos DB, uma base de dados e uma coleção. |
| [Criar uma conta do Azure Cosmos DB com a API do Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um único do Azure Cosmos DB conta, base de dados e gráficos. |
| [Criar uma conta do Azure Cosmos DB com a Cassandra API](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta do Azure Cosmos DB e a base de dados. |
| [Criar uma conta do Azure Cosmos DB com API de tabela](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um único do Azure Cosmos DB conta, base de dados e de tabela. |
|**Dimensionar o Azure Cosmos DB**||
| [Dimensionar o débito do contentor](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Altera o débito aprovisionado num contentor.|
| [Replicar a conta de base de dados do Azure Cosmos DB em várias regiões e configurar prioridades de ativação pós-falha](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replica globalmente os dados da conta em várias regiões com uma prioridade de ativação pós-falha especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves de contas](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém as chaves de escrita mestra primária e secundária, e as chaves só de leitura primária e secundária da conta.|
| [Obter a cadeia de ligação para a conta de Cosmos configurada com a API do Azure Cosmos DB para o MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém a cadeia de ligação para ligar a aplicação MongoDB à sua conta do Azure Cosmos DB.|
| [Voltar a gerar chaves da conta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Regenere as chaves da conta.|
| [Criar uma firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma política de controlo de acesso IP de entrada para limitar o acesso à conta a partir de um conjunto aprovado de computadores e/ou serviços cloud.|
|**Elevada disponibilidade, recuperação após desastre, cópia de segurança e restauro**||
| [Configurar política de ativação pós-falha](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Define a prioridade de ativação pós-falha de cada região em que a conta é replicada.|
|**Ligar o Azure Cosmos DB a recursos**||
| [Ligar uma aplicação Web ao Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Crie e ligue uma base de dados do Azure Cosmos DB e uma aplicação Web do Azure.|
|||
