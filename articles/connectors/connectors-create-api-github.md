---
title: Ligue-se ao GitHub - Azure Logic Apps | Documentos da Microsoft
description: Monitorizar eventos de GitHub com as APIs REST do GitHub e o Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462604"
---
# <a name="connect-to-github"></a>Ligue-se ao GitHub

GitHub é um baseada na web Git repositório serviço de alojamento que oferece toda o controlo de revisão distribuída e de funcionalidade de gestão (SCM) do código de origem no Git além de outros recursos.

Para começar a utilizar o conector do GitHub [criar uma aplicação lógica pela primeira vez](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma ligação para o GitHub

Para utilizar o conector do GitHub numa aplicação lógica, primeiro tem de criar uma *ligação* e, em seguida, forneça detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição | 
| -------- | -------- | ----------- | 
| Certificado de | Sim | Forneça as credenciais do GitHub. |

Depois de criar a ligação, pode executar as ações e ouvir os disparadores descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para acionadores e ações definidas no Swagger e limites de, reveja os [detalhes do conector](/connectors/github/).

## <a name="find-more-connectors"></a>Localizar conectores mais

* Reveja os [lista de conectores](apis-list.md).