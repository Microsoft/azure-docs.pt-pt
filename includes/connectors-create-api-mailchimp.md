---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 752c43604349a2361a8f5b26cd6d0bce7b516bc0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149762"
---
### <a name="prerequisites"></a>Pré-requisitos
* R [MailChimp](https://www.MailChimp.com/) conta 

Antes de poder utilizar a sua conta do MailChimp numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do MailChimp. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure. 

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do MailChimp:

1. Para criar uma ligação ao MailChimp, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *MailChimp* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![Passo 1 da MailChimp](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Se ainda não criou quaisquer ligações a MailChimp antes, obter-lhe-á pedido para fornecer suas credenciais do MailChimp. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta do MailChimp:  
   ![Passo 2 da MailChimp](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Forneça o seu nome de utilizador do MailChimp e a palavra-passe para autorizar a aplicação lógica:  
   ![Passo 3 da MailChimp](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:  
   ![Passo 4 da MailChimp](./media/connectors-create-api-mailchimp/mailchimp-4.png)

