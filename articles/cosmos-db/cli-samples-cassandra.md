---
title: Amostras Azure CLI para Azure Cosmos DB Cassandra API
description: Amostras Azure CLI para Azure Cosmos DB Cassandra API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2dbae196b981b792b47e6c12e500398f772d8451
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342135"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Amostras de Azure CLI para Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para o Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [Referência Azure CLI](/cli/azure/cosmosdb). As amostras de scripts Azure Cosmos DB CLI também podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Estas amostras requerem a versão 2.12.1 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Amostras comuns

Estas amostras aplicam-se a todas as APIs Azure Cosmos DB

|Tarefa | Descrição |
|---|---|
| [Adicionar ou falhar regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, mude a prioridade de failover, desencadeie uma falha manual.|
| [Chaves de conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listar chaves de conta, chaves só de leitura, chaves regeneradoras e colar cordas de ligação.|
| [Seguro com firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos com firewall IP configurada.|
| [Garantir nova conta com pontos finais de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos e proteja-a com pontos finais de serviço.|
| [Garantir a conta existente com pontos finais de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta Cosmos para proteger com pontos finais de serviço quando a sub-rede for configurada.|
|||

## <a name="cassandra-api-samples"></a>Amostras de Cassandra API

|Tarefa | Descrição |
|---|---|
| [Criar uma conta Azure Cosmos, espaço-chave e tabela](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, espaço-chave e tabela para a API de Cassandra. |
| [Criar uma conta Azure Cosmos, espaço-chave e tabela com autoescala](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, espaço-chave e tabela com autoescala para a API de Cassandra. |
| [Operações de produção](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migra entre a escala automática e a produção padrão num espaço-chave e numa tabela.|
| [Bloquear recursos da supressão](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||
