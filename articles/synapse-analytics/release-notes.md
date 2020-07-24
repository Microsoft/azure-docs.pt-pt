---
title: 'Notas de lançamento: Azure Synapse Analytics (espaços de trabalho)'
description: Notas de lançamento para Azure Synapse Analytics (espaços de trabalho)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059612"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas de lançamento do Azure Synapse Analytics (pré-visualização)

Este artigo descreve limitações e problemas com a Azure Synapse Analytics (espaços de trabalho). Para obter informações [relacionadas, consulte O que é Azure Synapse Analytics (espaços de trabalho)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (espaços de trabalho) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Emissão e impacto do cliente: Os espaços de trabalho criados pela SDK não podem lançar o Synapse Studio

- Solução: Complete os seguintes passos: 
  1.    Criar espaço de trabalho executando `az synapse workspace create` .
  2.    Extrair identidade gerida por correr `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Adicione espaço de trabalho como função à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Adicione a regra da firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Próximos passos

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
* [Criar uma piscina Apache Spark](quickstart-create-apache-spark-pool-portal.md)