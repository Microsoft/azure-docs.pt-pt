---
title: Conectar-se ao GitHub – aplicativos lógicos do Azure
description: Monitorar eventos do GitHub com APIs REST do GitHub e aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050905"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Conectar-se ao GitHub de aplicativos lógicos do Azure

O GitHub é um serviço de Hospedagem de repositório git baseado na Web que oferece todo o controle de revisão distribuído e a funcionalidade SCM (gerenciamento de código-fonte) no git, além de outros recursos.

Para começar a usar o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, você deve primeiro criar uma *conexão* e, em seguida, fornecer detalhes para essas propriedades: 

| Propriedade | Requerido | Descrição | 
| -------- | -------- | ----------- | 
| Certificado de | Sim | Forneça suas credenciais do GitHub. |

Depois de criar a conexão, você pode executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/github/).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)