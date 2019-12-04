---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789645"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do [MailChimp](https://www.MailChimp.com/) 

Antes de poder usar sua conta do MailChimp em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do MailChimp. Felizmente, você pode fazer isso facilmente de dentro de seu aplicativo lógico no portal do Azure. 

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do MailChimp:

1. Para criar uma conexão com o MailChimp, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, digite *MailChimp* na caixa de pesquisa. Selecione o gatilho ou a ação que você deseja usar:  
   ![etapa 1 do MailChimp](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Se você não tiver criado nenhuma conexão com o MailChimp antes, você receberá uma solicitação para fornecer suas credenciais do MailChimp. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados da sua conta do MailChimp:  
   ![Etapa 2 do MailChimp](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Forneça seu nome de usuário e senha do MailChimp para autorizar seu aplicativo lógico:  
   ![Etapa 3 do MailChimp](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Observe que a conexão foi criada e você agora está livre para prosseguir com as outras etapas em seu aplicativo lógico:  
   ![Etapa 4 do MailChimp](./media/connectors-create-api-mailchimp/mailchimp-4.png)

