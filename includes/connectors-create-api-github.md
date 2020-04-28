---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789759"
---
1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco. 

2. No Logic Apps Designer, introduza "github" como filtro. 

3. Selecione o conector GitHub e o gatilho que pretende utilizar.

   ![Selecione o conector GitHub e um gatilho](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos os fluxos de trabalho de aplicações lógicas devem começar com um gatilho. Só é possível selecionar ações quando o fluxo de trabalho da lógica já começa com um gatilho. 

4. Se não criou previamente uma ligação, escolha **o Sign in** para que possa fornecer as suas credenciais GitHub quando solicitado.  

   ![Inscreva-se com as suas credenciais GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A sua aplicação lógica utiliza estas credenciais para autorizar a ligação e o acesso a dados para a sua conta GitHub. 

5. Forneça o seu nome de utilizador GitHub e senha e confirme a sua autorização.

   ![Fornecer credenciais e confirmar a autorização](./media/connectors-create-api-github/github-connector-authorize.png)   

   A sua ligação está agora criada no portal Azure e está pronta para ser utilizada.

6. Continue a definir o fluxo de trabalho da sua aplicação lógica.

   ![Adicione mais ações ao fluxo de trabalho da sua aplicação lógica](./media/connectors-create-api-github/github-connector-logic-app.png)

