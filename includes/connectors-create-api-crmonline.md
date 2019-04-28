---
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119964"
---
#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* R [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) conta 

Antes de utilizar a sua conta do Dynamics numa aplicação lógica, autorize a aplicação lógica para ligar à sua conta do CRM Online. Pode fazê-lo facilmente na sua aplicação lógica no portal do Azure. 

Autorize a aplicação lógica para ligar à sua conta do CRM Online com os seguintes passos:

1. Crie uma aplicação lógica. No estruturador do Logic Apps, selecione **APIs geridas da Microsoft mostrar** na lista pendente lista e, em seguida, introduza "dynamics" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se ainda não tiver criado anteriormente todas as ligações ao Dynamics, lhe for pedido para iniciar sessão com as suas credenciais do Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Selecione **iniciar sessão**e introduza o nome de utilizador e palavra-passe. Selecione **iniciar sessão**. 
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e acessar os dados na sua conta do Dynamics. 
4. Observe que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

