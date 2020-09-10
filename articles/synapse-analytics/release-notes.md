---
title: 'Notas de lançamento: Azure Synapse Analytics (pré-visualização de espaços de trabalho)'
description: Notas de lançamento para Azure Synapse Analytics (pré-visualização de espaços de trabalho)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c8212af727a47ab05936c8b59c781cd49c5b0ba6
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669692"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Notas de lançamento do Azure Synapse Analytics (pré-visualização de espaços de trabalho)

Este artigo descreve limitações e problemas com a Azure Synapse Analytics (espaços de trabalho). Para obter informações [relacionadas, consulte O que é Azure Synapse Analytics (espaços de trabalho)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>CLI do Azure

- Emissão e impacto do cliente: Os espaços de trabalho criados pela SDK não podem lançar o Synapse Studio

- Solução: Complete os seguintes passos: 
  1.    Criar espaço de trabalho executando `az synapse workspace create` .
  2.    Extrair identificação de identidade gerida executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Adicione espaço de trabalho como função à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Adicione a regra da firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Passos seguintes

* [O que é Azure Synapse](overview-what-is.md)
* [Introdução](get-started.md)
* [FAQ](overview-faq.md)
