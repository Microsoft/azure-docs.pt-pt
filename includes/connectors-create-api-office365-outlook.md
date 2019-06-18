---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149720"
---
#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [Office 365](https://office365.com) conta  

Antes de utilizar a sua conta Office 365 numa aplicação lógica, autorize a aplicação lógica para ligar à sua conta do Office 365. Pode fazê-lo facilmente na sua aplicação lógica no portal do Azure.  

Autorize a aplicação lógica para ligar à sua conta do Office 365 através dos seguintes passos:

1. Crie uma aplicação lógica. No estruturador do Logic Apps, selecione **APIs geridas da Microsoft mostrar** na lista pendente lista e, em seguida, introduza "office 365" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Se ainda não tiver criado anteriormente todas as ligações ao Office 365, lhe for pedido para iniciar sessão com as suas credenciais do Office 365:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Selecione **iniciar sessão**e introduza o nome de utilizador e palavra-passe. Selecione **iniciar sessão**:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e aceder à sua conta do Office 365. 
4. Observe que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:   
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

