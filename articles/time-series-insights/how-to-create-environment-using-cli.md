---
title: Crie um ambiente Azure Time Series Insights Gen2 utilizando o Azure CLI - Azure Time Series Insights Gen2 | Microsoft Docs
description: Aprenda a configurar um ambiente em Azure Time Series Insights Gen2 usando o Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 07e7f21bd706d9f83d2813b0ab491b01fbc53672
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867570"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Criar um ambiente Azure Time Series Insights Gen2 utilizando o Azure CLI

Este documento irá guiá-lo através da criação de um novo Time Series Insights Gen2 Environment.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de armazenamento Azure para a loja de [frio](./concepts-storage.md#cold-store)do seu ambiente. Esta conta destina-se a retenção e análise a longo prazo para dados históricos.

> [!NOTE]
> No seu código, `mytsicoldstore` substitua-o por um nome único para a sua conta de armazenamento frio.

Primeiro, crie a conta de armazenamento:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Criar o ambiente

Agora que a conta de armazenamento é criada e o seu nome e chave de gestão são atribuídos às variáveis, executar o comando abaixo para criar o Azure Time Series Insights Environment:

> [!NOTE]
> No seu código, substitua os seguintes nomes únicos para o seu cenário:
>
> * `my-tsi-env` com o seu nome Ambiente.
> * `my-ts-id-prop` com o nome da sua propriedade de Id séries de tempo.

> [!IMPORTANT]
> O ID da Série De Tempo do seu ambiente é como uma chave de partição de base de dados. O ID da Série De Tempo também funciona como a chave principal para o seu modelo de séries de tempo.
>
> Para mais informações, consulte [as melhores práticas para escolher um ID da Série De Tempo.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Remova um ambiente de insights da série de tempo Azure

Pode utilizar o CLI Azure para eliminar um recurso individual, como um Time Series Insights Environment, ou eliminar um Grupo de Recursos e todos os seus recursos, incluindo quaisquer Ambientes de Insights de Séries tempoturais.

Para [eliminar um Time Series Insights Environments,](/cli/azure/tsi/environment#az_tsi_environment_delete)executar o seguinte comando:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Para [eliminar a conta de armazenamento,](/cli/azure/storage/account#az_storage_account_delete)executar o seguinte comando:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Para [eliminar um grupo de recursos](/cli/azure/group#az_group_delete) e todos os seus recursos, executar o seguinte comando:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [fontes de eventos de streaming](./concepts-streaming-ingestion-event-sources.md) para o seu ambiente Azure Time Series Insights Gen2.
* Saiba como se conectar a um [Hub IoT](./how-to-ingest-data-iot-hub.md)
