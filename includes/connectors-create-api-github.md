---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999552"
---
1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica em branco. 

2. No Logic Apps Designer, insira "github" como filtro. 

3. Selecione o conector GitHub e o gatilho que pretende utilizar.

   ![Selecione o conector GitHub e um gatilho](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos os fluxos de trabalho de aplicações lógicas devem começar com um gatilho. Só pode selecionar ações quando o seu fluxo de trabalho lógico já começa com um gatilho. 

4. Se não criou previamente uma ligação, escolha **Iniciar sessão** para poder fornecer as suas credenciais GitHub quando solicitado.  

   ![Inscreva-se com as suas credenciais GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A sua aplicação lógica utiliza estas credenciais para autorizar a ligação e o acesso aos dados da sua conta GitHub. 

5. Forneça o seu nome de utilizador e senha do GitHub e confirme a sua autorização.

   ![Fornecer credenciais e confirmar autorização](./media/connectors-create-api-github/github-connector-authorize.png)   

   A sua ligação está agora criada no portal Azure e está pronta a ser utilizada.

6. Continue a definir o fluxo de trabalho da sua aplicação lógica.

   ![Adicione mais ações ao seu fluxo de trabalho de aplicações lógicas](./media/connectors-create-api-github/github-connector-logic-app.png)

