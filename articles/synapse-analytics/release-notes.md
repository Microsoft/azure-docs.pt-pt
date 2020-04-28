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
ms.openlocfilehash: 059e77c063d00ef850a171507ca2e06422ade426
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191775"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas de lançamento do Azure Synapse Analytics (pré-visualização)

Este artigo descreve limitações e problemas com a Azure Synapse Analytics (espaços de trabalho). Para obter informações relacionadas, consulte [o que é azure synapse Analytics (espaços](overview-what-is.md) de trabalho)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (espaços de trabalho) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problema e impacto do cliente: Espaços de trabalho criados pela SDK não podem lançar Estúdio Synapse

- Supor: Complete os seguintes passos: 
  1.    Criar espaço de `az synapse workspace create`trabalho correndo.
  2.    Extrair identidade gerida por `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`executar .
  3.    Adicione espaço de trabalho como ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`papel à conta de armazenamento executando .
  4.    Adicione a regra ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `da firewall executando .

## <a name="next-steps"></a>Passos seguintes

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
* [Crie uma piscina apache spark](quickstart-create-apache-spark-pool.md)