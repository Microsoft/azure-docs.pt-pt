---
title: Amostras Azure CLI para Azure Cosmos DB Core (SQL) API
description: Amostras Azure CLI para Azure Cosmos DB Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: c5ed9af0c133f746f5761d6c41a5fdb9f25589ac
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276372"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Amostras de Azure CLI para Azure Cosmos DB Core (SQL) API

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para o Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [Referência Azure CLI](/cli/azure/cosmosdb). As amostras de scripts Azure Cosmos DB CLI também podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Estas amostras requerem a versão 2.12.1 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli)

Para as amostras de Azure CLI para outras APIs ver [amostras de CLI para Cassandra](cli-samples-cassandra.md), [Amostras de CLI para API MongoDB,](cli-samples-mongodb.md) [Amostras de CLI para Gremlin,](cli-samples-gremlin.md) [Amostras de CLI para quadro](cli-samples-table.md)

## <a name="common-samples"></a>Amostras comuns

Estas amostras aplicam-se a todas as APIs Azure Cosmos DB

|Tarefa | Descrição |
|---|---|
| [Adicionar ou falhar regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, mude a prioridade de failover, desencadeie uma falha manual.|
| [Chaves de conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listar chaves de conta, chaves só de leitura, chaves regeneradoras e colar cordas de ligação.|
| [Seguro com firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos com firewall IP configurada.|
| [Garantir nova conta com pontos finais de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos e proteja-a com pontos finais de serviço.|
| [Garantir a conta existente com pontos finais de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta Cosmos para proteger com pontos finais de serviço quando a sub-rede for configurada.|
|||

## <a name="core-sql-api-samples"></a>Amostras de API core (SQL)

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, base de dados e contentor Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB, base de dados e contentor Azure Cosmos para API Core (SQL). |
| [Criar uma conta, base de dados e contentor Azure Cosmos com autoescala](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB, base de dados e contentor Azure Cosmos com escala automática para Core (SQL) API. |
| [Operações de produção](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migra entre a escala automática e a produção padrão numa base de dados e num recipiente.|
| [Bloquear recursos da supressão](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||
