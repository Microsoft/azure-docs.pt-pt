---
title: Amostras de Azure CLI para Azure Cosmos DB MongoDB API
description: Amostras de Azure CLI para Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 7536a2e3fc32aeb330aad52625a5946e856d6646
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85556039"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Amostras de Azure CLI para Azure Cosmos DB MongoDB API

A tabela seguinte inclui links para provar scripts Azure CLI para Azure Cosmos DB MongoDB API. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [Referência Azure CLI](/cli/azure/cosmosdb). Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Atualmente só é possível criar a versão 3.2 (isto é, contas que utilizam o ponto final no formato `*.documents.azure.com` ) da API da Azure Cosmos DB para as contas MongoDB utilizando modelos powerShell, CLI e Resource Manager. Para criar a versão 3.6 das contas, utilize o portal Azure.

|Tarefa | Description |
|---|---|
| [Criar uma conta, base de dados e recolha Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB, base de dados e recolha da API da AZure Cosmos. |
| [Alteração de produção](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize RU/s numa base de dados e recolha.|
| [Adicionar ou falhar regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, mude a prioridade de failover, desencadeie uma falha manual.|
| [Chaves de conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listar chaves de conta, chaves só de leitura, chaves regeneradoras e colar cordas de ligação.|
| [Seguro com firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos com firewall IP configurada.|
| [Garantir nova conta com pontos finais de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos e proteja-a com pontos finais de serviço.|
| [Garantir a conta existente com pontos finais de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta Cosmos para proteger com pontos finais de serviço quando a sub-rede for configurada.|
| [Bloquear recursos da supressão](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||
