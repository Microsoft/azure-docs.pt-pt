---
title: Notas de versão
description: Notas de lançamento para Azure Synapse Analytics (espaços de trabalho)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423860"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas de lançamento do Azure Synapse Analytics (pré-visualização)

Este artigo descreve limitações e problemas com a Azure Synapse Analytics (espaços de trabalho). Para obter informações relacionadas, consulte [o que é azure synapse Analytics (espaços](overview-what-is.md) de trabalho)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (espaços de trabalho) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problema e impacto do cliente: Espaços de trabalho criados pela SDK não podem lançar Estúdio Synapse

- Supor: Complete os seguintes passos: 
  1.    Criar espaço de `az synapse workspace create`trabalho executando 2.    Extrair identidade gerida id executando`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Adicione espaço de trabalho como papel à conta de armazenamento executando` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Adicionar regra de firewall executando` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Passos seguintes

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool.md)
* [Use o SQL a pedido](quickstart-sql-on-demand.md)
* [Crie uma piscina apache spark](quickstart-create-apache-spark-pool.md)