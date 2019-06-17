---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149744"
---
#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* R [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) conta 

Antes de poder utilizar a sua conta do OneDrive numa aplicação lógica, autorize a aplicação lógica para ligar à sua conta do OneDrive.  Pode fazê-lo facilmente na sua aplicação lógica no portal do Azure. 

Autorize a aplicação lógica para ligar à sua conta do OneDrive através dos seguintes passos:

1. Crie uma aplicação lógica. No estruturador do Logic Apps, selecione **APIs geridas da Microsoft mostrar** na lista pendente lista e, em seguida, introduza "onedrive" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não tiver criado anteriormente todas as ligações para o OneDrive, lhe for pedido para iniciar sessão com as suas credenciais do OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **iniciar sessão**e introduza o nome de utilizador e palavra-passe. Selecione **iniciar sessão**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e acessar os dados na sua conta do OneDrive. 
4. Selecione **Sim** para autorizar a aplicação lógica para utilizar a sua conta do OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

