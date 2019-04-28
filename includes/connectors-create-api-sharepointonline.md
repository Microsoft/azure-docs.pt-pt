---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 0cabc58d856c09accd9b1924fe63d6518b1cb9ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130001"
---
Para se ligar aos **SharePoint Online**, tem de fornecer a sua identidade (nome de utilizador e palavra-passe, smart credenciais de cartão, etc.) ao SharePoint Online. Assim que foi autenticado, pode continuar a utilizar o conector do SharePoint Online na sua aplicação lógica. 

Enquanto no designer da sua aplicação lógica, siga estes passos para iniciar sessão no SharePoint para criar o **ligação** para utilização na sua aplicação lógica:

1. Introduza o SharePoint na caixa de pesquisa e aguarde que a procura para devolver todos os acionadores e ações relacionadas com o SharePoint Online:   
   ![Configurar o SharePoint][1]  
2. Selecione o **o SharePoint Online - quando é criado um ficheiro** acionador  
3. Selecione **iniciar sessão no SharePoint Online**:   
   ![Configurar o SharePoint][2]    
4. Forneça as suas credenciais do SharePoint para iniciar sessão autenticar com o SharePoint   
   ![Configurar o SharePoint][3]     
5. Depois de concluída a autenticação, será redirecionado para a sua aplicação lógica. Isso é tudo, a ligação foi criada. Tenha em atenção a mensagem na parte inferior que indica que está agora ligado ao SharePoint.  
   ![Configurar o SharePoint][4]  
6. Em seguida, pode adicionar outros acionadores e ações que precisa de executar a aplicação lógica.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
