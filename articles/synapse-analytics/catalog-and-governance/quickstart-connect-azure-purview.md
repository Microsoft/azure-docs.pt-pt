---
title: Ligar uma Conta do Azure Purview 
description: Ligue uma Conta Azure a um espaço de trabalho da Sinapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: cb6a63e4ead45d7751d1d3442793478cfdcd71c0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676345"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>QuickStart: Ligue uma conta Azure Purview a um espaço de trabalho synapse 


Neste arranque rápido, registará uma Conta Azure Purview para um espaço de trabalho da Sinapse. Essa ligação permite-lhe descobrir os ativos do Azure Purview e interagir com eles através das capacidades da Synapse. 

Pode executar as seguintes tarefas na Sinapse: 
- Use a caixa de pesquisa no topo para encontrar os ativos do Purview com base em palavras-chave 
- Compreender os dados com base em metadados, linhagens, anotações 
- Ligue esses dados ao seu espaço de trabalho com serviços ligados ou conjuntos de dados de integração 
- Analise esses conjuntos de dados com Synapse Apache Spark, Synapse SQL e Data Flow 

## <a name="prerequisites"></a>Pré-requisitos 
- [Conta Azure Purview](../../purview/create-catalog-portal.md) 
- [Espaço de trabalho sinapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Inscreva-se num espaço de trabalho da Sinapse 

Vá  [https://web.azuresynapse.net](https://web.azuresynapse.net) e inscreva-se no seu espaço de trabalho. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Permissões para ligar uma conta Azure Purview 

- Para ligar uma Conta Azure Purview a um espaço de trabalho synapse, precisa de uma função **contributiva** no espaço de trabalho synapse do portal Azure IAM e precisa de acesso a essa Conta Azure Purview.

## <a name="connect-an-azure-purview-account"></a>Ligar uma Conta do Azure Purview  

- No espaço de trabalho da Sinapse, vá ao **Manage**  ->  **Azure Purview.** Selecione **Ligar a uma conta Desemis.** 
- Pode escolher **a partir da subscrição Azure** ou entrar **manualmente.** **A partir da subscrição do Azure,** pode selecionar a conta a que tem acesso. 
- Uma vez ligado, deverá poder ver o nome da conta 'Face ao's Purview na **conta Azure Purview**. 
- Pode utilizar a barra de pesquisa no centro superior do espaço de trabalho Synapse para procurar dados. 

## <a name="nextsteps"></a>Próximos passos 

[Registar e digitalizar os ativos da Azure Synapse em Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Descobrir, ligar e explorar dados no Azure Synapse Analytics com o Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
