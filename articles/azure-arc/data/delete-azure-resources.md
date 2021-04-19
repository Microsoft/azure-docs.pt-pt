---
title: Eliminar recursos do Azure
description: Eliminar recursos do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716084"
---
# <a name="delete-resources-from-azure"></a>Eliminar recursos do Azure

Este artigo descreve como apagar recursos do Azure.

> [!WARNING]
> Quando elimina recursos como descrito neste artigo, estas ações são irreversíveis.

No modo de ligação indireta, eliminar uma instância de Kubernetes não irá removê-lo do Azure e eliminar uma instância do Azure não irá removê-lo de Kubernetes. Para o modo de ligação indireta, a eliminação de um recurso é um processo de dois passos e isso será melhorado no futuro. Kubernetes será a fonte da verdade e o portal será atualizado para refleti-lo.

Em alguns casos, poderá ser necessário eliminar manualmente os recursos dos serviços de dados ativados pelo Azure Arc em Azure.  Pode eliminar estes recursos utilizando qualquer uma das seguintes opções.

- [Eliminar recursos do Azure](#delete-resources-from-azure)
  - [Eliminar um grupo inteiro de recursos](#delete-an-entire-resource-group)
  - [Eliminar recursos específicos no grupo de recursos](#delete-specific-resources-in-the-resource-group)
  - [Eliminar recursos utilizando o CLI Azure](#delete-resources-using-the-azure-cli)
    - [Eliminar os recursos de instância geridos sql usando o CLI Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Eliminar recursos do grupo de servidores de hiperescala PostgreSQL usando o Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Eliminar recursos controladores de dados do Azure Arc utilizando o CLI Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Eliminar um grupo de recursos utilizando o Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Eliminar um grupo inteiro de recursos

Se tem usado um grupo de recursos específico e dedicado para serviços de dados habilitados a Azure Arc e pretende eliminar *tudo* dentro do grupo de recursos, pode eliminar o grupo de recursos que irá apagar tudo o que está dentro dele.  

Pode eliminar um grupo de recursos no portal Azure fazendo o seguinte:

- Navegue pelo grupo de recursos no portal Azure onde foram criados os recursos dos serviços de dados Azure.
- Clique no botão eliminar o **grupo de recursos.**
- Confirme a eliminação introduzindo o nome do grupo de recursos e clique em **Eliminar**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Eliminar recursos específicos no grupo de recursos

Pode eliminar recursos específicos de serviços de dados ativados pelo Azure Arc num grupo de recursos no portal Azure, fazendo o seguinte:

- Navegue pelo grupo de recursos no portal Azure onde foram criados os recursos dos serviços de dados Azure.
- Selecione todos os recursos a eliminar.
- Clique no botão Eliminar.
- Confirme a eliminação digitando 'sim' e clique em **Eliminar**.

## <a name="delete-resources-using-the-azure-cli"></a>Eliminar recursos utilizando o CLI Azure

Pode eliminar recursos específicos de serviços de dados ativados a Azure Arc utilizando o CLI Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Eliminar os recursos de instância geridos sql usando o CLI Azure

Para eliminar os recursos de instância gerido sql do Azure utilizando o Azure CLI substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Eliminar recursos do grupo de servidores de hiperescala PostgreSQL usando o Azure CLI

Para eliminar um recurso do grupo de servidores PostgreSQL Hyperscale do Azure utilizando o Azure CLI substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Eliminar recursos controladores de dados do Azure Arc utilizando o CLI Azure

> [!NOTE]
> Antes de eliminar um controlador de dados Azure Arc, deve eliminar todos os recursos de caso de base de dados que está a gerir.

Para eliminar um controlador de dados Azure Arc do Azure utilizando o CLI Azure substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Eliminar um grupo de recursos utilizando o Azure CLI

Também pode utilizar o CLI Azure para [eliminar um grupo de recursos](../../azure-resource-manager/management/delete-resource-group.md).