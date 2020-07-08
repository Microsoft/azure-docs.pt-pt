---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789645"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [MailChimp](https://www.MailChimp.com/) 

Antes de poder utilizar a sua conta MailChimp numa aplicação Logic, tem de autorizar a aplicação Logic para se ligar à sua conta MailChimp. Felizmente, pode fazê-lo facilmente a partir da sua aplicação Logic no Portal Azure. 

Aqui estão os passos para autorizar a sua aplicação Logic a ligar-se à sua conta MailChimp:

1. Para criar uma ligação ao MailChimp, no designer de aplicações Logic, selecione **Mostrar APIs geridos pela Microsoft** na lista de drop down e, em seguida, insira o *MailChimp* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de utilizar:  
   ![Passo MailChimp 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Se ainda não criou nenhuma ligação ao MailChimp, será solicitado que forneça as suas credenciais MailChimp. Estas credenciais serão usadas para autorizar a sua aplicação Logic a ligar-se e aceder aos dados da sua conta MailChimp:  
   ![Passo MailChimp 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Forneça o seu nome de utilizador e senha mailChimp para autorizar a sua aplicação Logic:  
   ![Passo MailChimp 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Note que a ligação foi criada e agora está livre para prosseguir com os outros passos na sua aplicação Logic:  
   ![Passo MailChimp 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

