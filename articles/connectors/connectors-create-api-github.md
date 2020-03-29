---
title: Acesso, monitor e gestão do seu repo GitHub
description: Monitorize eventos GitHub e gerencie o seu repo GitHub criando fluxos de trabalho automatizados com aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378454"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorize e gerencie o seu repo GitHub usando aplicações lógicas azure

O GitHub é um serviço de hospedagem repositório git baseado na Web que oferece toda a funcionalidade de controlo de revisão e gestão de códigos de origem (SCM) distribuída em Git mais outras funcionalidades.

Para começar com o conector GitHub, [crie primeiro uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma ligação ao GitHub

Para utilizar o conector GitHub numa aplicação lógica, primeiro deve criar uma *ligação* e, em seguida, fornecer detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição | 
| -------- | -------- | ----------- | 
| Certificado de | Sim | Forneça as suas credenciais GitHub. |

Depois de criar a ligação, pode executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência do conector](/connectors/github/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)