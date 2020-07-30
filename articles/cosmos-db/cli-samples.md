---
title: Amostras de Azure CLI para Azure Cosmos DB
description: Amostras de Azure CLI para Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.openlocfilehash: 43e4b0ae2e7a33cc1b66784bf8e7545b4ed202f1
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431377"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para o Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [Referência Azure CLI](/cli/azure/cosmosdb). As amostras de scripts Azure Cosmos DB CLI também podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Estas amostras requerem a versão 2.9.1 do CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli)

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
| [Alteração de produção](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s numa base de dados e num contentor.|
| [Bloquear recursos da supressão](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||

## <a name="mongodb-api-samples"></a>Amostras de API mongodb

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, base de dados e recolha Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB, base de dados e recolha da API da AZure Cosmos. |
| [Criar uma conta Azure Cosmos, base de dados com autoescala e duas coleções com produção partilhada](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, base de dados com autoescala e duas coleções com produção partilhada para a MongoDB API. |
| [Alteração de produção](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s numa base de dados e recolha.|
| [Bloquear recursos da supressão](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||

## <a name="cassandra-api-samples"></a>Amostras de Cassandra API

|Tarefa | Descrição |
|---|---|
| [Criar uma conta Azure Cosmos, espaço-chave e tabela](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, espaço-chave e tabela para a API de Cassandra. |
| [Criar uma conta Azure Cosmos, espaço-chave e tabela com autoescala](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, espaço-chave e tabela com autoescala para a API de Cassandra. |
| [Alteração de produção](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s em um espaço e tabela de chaves.|
| [Bloquear recursos da supressão](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||

## <a name="gremlin-api-samples"></a>Amostras de API de Gremlin

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, base de dados e gráfico Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, base de dados e gráfico para a API gremlin. |
| [Criar uma conta, base de dados e gráfico Azure Cosmos com autoescala](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, base de dados e gráfico com escala automática para a API de Gremlin. |
| [Alteração de produção](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s numa base de dados e gráfico.|
| [Bloquear recursos da supressão](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||

## <a name="table-api-samples"></a>Amostras de API de tabela

|Tarefa | Descrição |
|---|---|
| [Criar uma conta e tabela Azure Cosmos](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos e tabela para API de tabela. |
| [Criar uma conta e tabela Azure Cosmos com autoescala](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos e tabela com autoescala para API de tabela. |
| [Alteração de produção](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s em uma mesa.|
| [Bloquear recursos da supressão](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||
