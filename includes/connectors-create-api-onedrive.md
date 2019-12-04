---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789690"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta do [onedrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder usar sua conta do OneDrive em um aplicativo lógico, autorize o aplicativo lógico a se conectar à sua conta do OneDrive.  Você pode fazer isso facilmente no aplicativo lógico no portal do Azure. 

Autorize seu aplicativo lógico a se conectar à sua conta do OneDrive usando as seguintes etapas:

1. Crie um aplicativo lógico. No designer de aplicativos lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, insira "onedrive" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se você ainda não tiver criado nenhuma conexão com o OneDrive, será solicitado que você entre usando suas credenciais do OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **entrar**e insira seu nome de usuário e senha. Selecione **entrar**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Essas credenciais são usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados em sua conta do OneDrive. 
4. Selecione **Sim** para autorizar o aplicativo lógico a usar sua conta do onedrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que a conexão foi criada. Agora, continue com as outras etapas em seu aplicativo lógico:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

