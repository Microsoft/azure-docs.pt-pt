---
title: Aceda, monitorize e gere o seu gitHub repo
description: Monitorize os eventos do GitHub e gere o seu gitHub repo criando fluxos de trabalho automatizados com apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75378454"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorize e gere o seu gitHub repo utilizando apps Azure Logic

GitHub é um serviço de hospedagem de repositório de Git baseado na web que oferece toda a funcionalidade de controlo de revisão e gestão de código fonte (SCM) distribuído em Git mais outras funcionalidades.

Para começar com o conector GitHub, [crie primeiro uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma ligação ao GitHub

Para utilizar o conector GitHub numa aplicação lógica, primeiro tem de criar uma *ligação* e, em seguida, fornecer detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição | 
| -------- | -------- | ----------- | 
| Token | Sim | Forneça as suas credenciais GitHub. |

Depois de criar a ligação, pode executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/github/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)