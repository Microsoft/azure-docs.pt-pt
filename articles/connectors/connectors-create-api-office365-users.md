---
title: Conectar-se aos usuários do Office 365
description: Automatizar tarefas e fluxos de trabalho que obtêm e gerenciam perfis em perfis de usuários do Office 365 usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666861"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Obter e gerenciar perfis em usuários do Office 365 usando aplicativos lógicos do Azure

Conecte-se aos usuários do Office 365 para obter perfis, Pesquisar usuários e muito mais. Com os usuários do Office 365, você pode:

* Crie seu fluxo de negócios com base nos dados obtidos de usuários do Office 365. 
* Use ações que obtenham relatórios diretos, obtenha o perfil de usuário de um gerente e muito mais. Essas ações obtêm uma resposta e tornam a saída disponível para outras ações. Por exemplo, obtenha os subordinados diretos de uma pessoa e, em seguida, Pegue essas informações e atualize um banco de dados SQL Azure. 

Você pode começar criando um aplicativo lógico agora, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Criar uma conexão com os usuários do Office 365

Ao adicionar esse conector aos seus aplicativos lógicos, você deve entrar na sua conta de usuários do Office 365 para que os aplicativos lógicos do Azure possam se conectar à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Depois de criar a conexão, insira as propriedades dos usuários do Office 365, como a ID de usuário. A **referência da API REST** neste artigo descreve essas propriedades.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição do Swagger do conector, examine a [página de referência do conector](/connectors/officeusers/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](apis-list.md)