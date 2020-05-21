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
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658498"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas de lançamento do Azure Synapse Analytics (pré-visualização)

Este artigo descreve limitações e problemas com a Azure Synapse Analytics (espaços de trabalho). Para obter informações relacionadas, consulte [o que é azure synapse Analytics (espaços](overview-what-is.md) de trabalho)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (espaços de trabalho) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problema e impacto do cliente: Espaços de trabalho criados pela SDK não podem lançar Estúdio Synapse

- Supor: Complete os seguintes passos: 
  1.    Criar espaço de trabalho `az synapse workspace create` correndo.
  2.    Extrair identidade gerida por executar `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Adicione espaço de trabalho como papel à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Adicione a regra da firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Próximos passos

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
* [Crie uma piscina apache spark](quickstart-create-apache-spark-pool-portal.md)