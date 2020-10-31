---
title: Amostras de Azure CLI para Azure Cosmos DB API para MongoDB
description: Amostras de Azure CLI para Azure Cosmos DB API para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41943a30f78bd906d314d6ec10fa01866708f534
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073051"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Amostras de Azure CLI para Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

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

## <a name="mongodb-api-samples"></a>Amostras de API mongodb

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, base de dados e recolha Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB, base de dados e recolha da API da AZure Cosmos. |
| [Criar uma conta Azure Cosmos, base de dados com autoescala e duas coleções com produção partilhada](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta DB Azure Cosmos, base de dados com autoescala e duas coleções com produção partilhada para a MongoDB API. |
| [Operações de produção](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migra entre a escala automática e a produção padrão numa base de dados e recolha.|
| [Bloquear recursos da supressão](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos.|
|||
