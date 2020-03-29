---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789690"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder utilizar a sua conta OneDrive numa aplicação lógica, autorize a aplicação lógica a ligar-se à sua conta OneDrive.  Pode fazê-lo facilmente dentro da sua aplicação lógica no portal Azure. 

Autorize a sua aplicação lógica a ligar-se à sua conta OneDrive utilizando os seguintes passos:

1. Criar uma aplicação lógica. No designer de Aplicações Lógicas, selecione **Show Microsoft geriu APIs** na lista de descidas e, em seguida, insira "onedrive" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não criou nenhuma ligação ao OneDrive, é-lhe solicitado que assine as suas credenciais OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **Iniciar sessão**e insira o seu nome de utilizador e senha. Selecione **Iniciar sessão:**  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciais são usadas para autorizar a sua aplicação lógica para se conectar e aceder aos dados na sua conta OneDrive. 
4. Selecione **Sim** para autorizar a aplicação lógica para usar a sua conta OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Reparem que a ligação foi criada. Agora, proceda com os outros passos da sua aplicação lógica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

