---
title: Ligue-se aos utilizadores do Office 365
description: Automatizar tarefas e fluxos de trabalho que obtenham e gerem perfis no Office 365 Utilizadores perfis utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 0361d42ea614b394142e32a9193ab1d48d1a4a6c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194245"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Obtenha e gere perfis no Office 365 Utilizadores utilizando aplicações Azure Logic

Ligue-se ao Office 365 Utilizadores para obter perfis, utilizadores de pesquisa e muito mais. Com o Office 365 Utilizadores, pode:

* Construa o seu fluxo de negócio com base nos dados que obtém do Office 365 Users. 
* Use ações que obtenham relatórios diretos, obtenha o perfil de utilizador de um gestor, e muito mais. Estas ações obtêm uma resposta e, em seguida, disponibilizam a produção para outras ações. Por exemplo, obtenha os relatórios diretos de uma pessoa e, em seguida, pegue esta informação e atualize uma base de dados na Base de Dados Azure SQL. 

Pode começar por criar agora uma aplicação lógica, ver [Criar uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-office-365-users"></a>Criar uma ligação aos utilizadores do Office 365

Quando adicionar este conector às suas aplicações lógicas, tem de iniciar sôms na sua conta de Utilizadores do Office 365 para que as Aplicações Lógicas Azure possam ligar-se à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Depois de criar a ligação, introduza as propriedades do Office 365 Users, como o ID do utilizador. A **referência REST API** neste artigo descreve estas propriedades.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição do Conector Swagger, consulte a [página de referência do conector](/connectors/officeusers/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](apis-list.md)