---
title: Acesse, monitore e gerencie seu repositório GitHub
description: Monitorar eventos do GitHub e gerenciar seu repositório GitHub criando fluxos de trabalho automatizados com aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378454"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorar e gerenciar seu repositório GitHub usando aplicativos lógicos do Azure

O GitHub é um serviço de Hospedagem de repositório git baseado na Web que oferece todo o controle de revisão distribuído e a funcionalidade SCM (gerenciamento de código-fonte) no git, além de outros recursos.

Para começar a usar o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, você deve primeiro criar uma *conexão* e, em seguida, fornecer detalhes para essas propriedades: 

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| Certificado de | Sim | Forneça suas credenciais do GitHub. |

Depois de criar a conexão, você pode executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/github/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)