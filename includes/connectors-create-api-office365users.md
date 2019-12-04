---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789528"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de [usuários do Office 365](https://office365.com)  

Antes de poder usar sua conta de usuários do Office 365 em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta dos usuários do Office 365. Felizmente, você pode fazer isso facilmente de dentro de seu aplicativo lógico no portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta de usuários do Office 365:  

1. Para criar uma conexão com os usuários do Office 365, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, insira *usuários do Office 365* na caixa de pesquisa. Selecione o gatilho ou a ação que você deseja usar:  
   ![etapa de criação de conexão dos usuários do Office 365](./media/connectors-create-api-office365users/office365users-1.png)  
2. Se você não tiver criado nenhuma conexão com os usuários do Office 365 antes, você receberá uma solicitação para fornecer suas credenciais de usuários do Office 365. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados da sua conta dos usuários do Office 365:  
   ![Etapa de criação de conexão dos usuários do Office 365](./media/connectors-create-api-office365users/office365users-2.png)  
3. Forneça o nome de usuário e a senha dos usuários do Office 365 para autorizar seu aplicativo lógico:  
   ![Etapa de criação de conexão dos usuários do Office 365](./media/connectors-create-api-office365users/office365users-3.png)  
4. Observe que a conexão foi criada e você agora está livre para prosseguir com as outras etapas em seu aplicativo lógico:  
   ![Etapa de criação de conexão dos usuários do Office 365](./media/connectors-create-api-office365users/office365users-4.png)  

