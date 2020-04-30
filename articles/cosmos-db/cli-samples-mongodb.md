---
title: Amostras azure CLI para Azure Cosmos DB MongoDB API
description: Amostras azure CLI para Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77524568"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Amostras azure CLI para Azure Cosmos DB MongoDB API

A tabela seguinte inclui links para a amostra de scripts Azure CLI para API Azure Cosmos DB MongoDB. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [referência Azure CLI](/cli/azure/cosmosdb). Todas as amostras de script soro D DD Do Azure Cosmos podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Atualmente só pode criar versão 3.2 (isto é, contas `*.documents.azure.com`usando o ponto final no formato) da API do Azure Cosmos DB para contas MongoDB utilizando modelos PowerShell, CLI e Resource Manager. Para criar 3.6 versão de contas, utilize o portal Azure.

| |  |
|---|---|
| [Criar uma conta Azure Cosmos, base de dados e recolha](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta Azure Cosmos DB, base de dados e recolha para a API MongoDB. |
| [Alterar a entrada](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualizar RU/s numa base de dados e recolha.|
| [Adicionar ou falhar regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, mude a prioridade de failover, desencadeie uma falha manual.|
| [Chaves de conta e cordas de ligação](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Lista as teclas da conta, as chaves apenas de leitura, as chaves regeneradas e as cordas de ligação da lista.|
| [Seguro com firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos com firewall IP configurada.|
| [Garantir nova conta com pontos finais de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta Cosmos e proteja com pontos finais de serviço.|
| [Conta segura existente com pontos finais de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta Cosmos para garantir com pontos finais de serviço quando a subnet for configurada.|
|||
