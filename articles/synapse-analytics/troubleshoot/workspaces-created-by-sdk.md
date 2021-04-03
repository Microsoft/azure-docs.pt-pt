---
title: 'Resolução de problemas: Espaços de trabalho criados pela SDK não podem lançar Synapse Studio'
description: Passos para resolver espaços de trabalho criados pela SDK incapazes de lançar o Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466949"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Resolução de problemas Azure Synapse Analytics espaços de trabalho criados com SDK

Este artigo fornece etapas de resolução de problemas para o lançamento do Synapse Studio a partir de um espaço de trabalho synapse que foi criado com um kit de desenvolvimento de software (SDK).


## <a name="prerequisites"></a>Pré-requisitos

- Espaço de trabalho sinapse criado usando SDK

## <a name="workaround"></a>Solução

Para lançar o Synapse Studio a partir do seu espaço de trabalho criado pela SDK, complete os seguintes passos: 
  1.    Criar espaço de trabalho executando `az synapse workspace create` .
  2.    Extrair identificação de identidade gerida executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Grant Storage Blob Data Contributor fun para a conta de armazenamento de identidade gerida executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Adicione a regra da firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Passos seguintes

* [O que é Azure Synapse](../overview-what-is.md)
* [Introdução](../get-started.md)
