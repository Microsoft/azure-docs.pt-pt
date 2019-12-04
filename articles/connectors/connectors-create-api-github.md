---
title: Conectar-se ao GitHub
description: Monitorar eventos do GitHub com APIs REST do GitHub e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789750"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Conectar-se ao GitHub de aplicativos lógicos do Azure

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