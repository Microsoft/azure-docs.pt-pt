---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093109"
---
### <a name="prerequisites"></a>Pré-requisitos
* R [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) conta  

Antes de poder utilizar a sua conta SMTP numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta de SMTP. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta de SMTP:  

1. Para criar uma ligação para o protocolo SMTP, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *SMTP* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Se ainda não criou quaisquer ligações para o protocolo SMTP antes, terá de informar fornecer as suas credenciais de SMTP. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

