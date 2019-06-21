---
title: incluir ficheiro
description: incluir ficheiro
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184522"
---
1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco. 

2. No estruturador de aplicações lógicas, introduza "github" como o filtro. 

3. Selecione o conector do GitHub e o acionador que pretende utilizar.

   ![Selecione o conector do GitHub e um acionador](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos os fluxos de trabalho de aplicação de lógica tem de começar com um acionador. Pode selecionar ações apenas quando o fluxo de trabalho de lógica já começa com um acionador. 

4. Se não tiver criado anteriormente uma ligação, escolha **iniciar sessão** para fornecer suas credenciais do GitHub quando lhe for pedido.  

   ![Inicie sessão com as suas credenciais do GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A aplicação lógica utiliza estas credenciais para autorizar a ligação e aceder aos dados para a sua conta do GitHub. 

5. Forneça o seu nome de utilizador do GitHub e a palavra-passe e, em seguida, confirmar a sua autorização.

   ![Forneça as credenciais e Confirmar autorização](./media/connectors-create-api-github/github-connector-authorize.png)   

   A ligação está agora criada no portal do Azure e está pronta a utilizar.

6. Continue a definir o seu fluxo de trabalho de aplicação lógica.

   ![Adicionar mais ações para seu fluxo de trabalho de aplicação lógica](./media/connectors-create-api-github/github-connector-logic-app.png)

