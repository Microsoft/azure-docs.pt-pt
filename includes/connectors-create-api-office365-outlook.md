---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789603"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta [do Office 365](https://office365.com)  

Antes de usar sua conta do Office 365 em um aplicativo lógico, autorize o aplicativo lógico a se conectar à sua conta do Office 365. Você pode fazer isso facilmente no aplicativo lógico no portal do Azure.  

Autorize seu aplicativo lógico a se conectar à sua conta do Office 365 usando as seguintes etapas:

1. Crie um aplicativo lógico. No designer de aplicativos lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, digite "Office 365" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
    ![etapa de criação de conexão do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Se você ainda não tiver criado nenhuma conexão com o Office 365, será solicitado que você entre usando suas credenciais do Office 365:  
    ![Etapa de criação de conexão do Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Selecione **entrar**e insira seu nome de usuário e senha. Selecione **entrar**:  
    ![etapa de criação de conexão do Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Essas credenciais são usadas para autorizar seu aplicativo lógico a se conectar e acessar sua conta do Office 365. 
4. Observe que a conexão foi criada. Agora, continue com as outras etapas em seu aplicativo lógico:   
    ![Etapa de criação de conexão do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

