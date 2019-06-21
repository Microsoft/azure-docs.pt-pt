---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 845b27b8efd66de87ec08e0b5b81bcc332dffdfb
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202717"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Twilio
* Um número de telefone Twilio verificado que pode receber a SMS
* Um número de telefone Twilio verificado que pode enviar uma SMS

> [!NOTE]
> Se estiver a utilizar uma conta de avaliação do Twilio, só pode enviar SMS para **verificado** números de telefone.  
> 
> 

Antes de poder utilizar a sua conta do Twilio numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do Twilio. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure. 

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do Twilio:

1. Para criar uma ligação ao Twilio, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *Twilio* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Se ainda não criou quaisquer ligações ao Twilio antes, terá de informar para fornecer suas credenciais do Twilio. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Terá do **id da conta Twilio** e **token de acesso do Twilio** do dashboard do Twilio, por isso, inicie sessão para a sua conta do Twilio agora pegar essas duas informações:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio e o Logic apps usar nomes diferentes para identificar estes dois tipos de informação. Eis como tem mapeá-los para a caixa de diálogo do Logic apps: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Selecione o **criar ligação** botão:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

