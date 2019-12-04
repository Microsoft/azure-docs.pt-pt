---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789759"
---
1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco. 

2. No designer de aplicativos lógicos, insira "GitHub" como filtro. 

3. Selecione o conector do GitHub e o gatilho que você deseja usar.

   ![Selecione o conector do GitHub e um gatilho](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos os fluxos de trabalho do aplicativo lógico devem começar com um gatilho. Você pode selecionar ações somente quando seu fluxo de trabalho lógico já começa com um gatilho. 

4. Se você não criou uma conexão anteriormente, escolha **entrar** para poder fornecer suas credenciais do GitHub quando solicitado.  

   ![Entre com suas credenciais do GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Seu aplicativo lógico usa essas credenciais para autorizar a conexão e o acesso a dados para sua conta do GitHub. 

5. Forneça o nome de usuário e a senha do GitHub e confirme sua autorização.

   ![Fornecer credenciais e confirmar autorização](./media/connectors-create-api-github/github-connector-authorize.png)   

   Sua conexão agora é criada no portal do Azure e está pronta para uso.

6. Continue definindo o fluxo de trabalho do aplicativo lógico.

   ![Adicionar mais ações ao seu fluxo de trabalho do aplicativo lógico](./media/connectors-create-api-github/github-connector-logic-app.png)

