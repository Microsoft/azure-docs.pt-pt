---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789528"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [de 365 utilizadores do Office](https://office365.com)  

Antes de poder utilizar a sua conta de Utilizadores do Office 365 numa aplicação Logic, tem de autorizar a aplicação Logic a ligar-se à sua conta de Utilizadores do Office 365. Felizmente, pode fazê-lo facilmente dentro da sua aplicação Logic no Portal Azure.  

Aqui estão os passos para autorizar a sua aplicação Logic a ligar-se à sua conta de Utilizadores do Office 365:  

1. Para criar uma ligação ao Office 365 Users, no designer de aplicações Logic, selecione **Show Microsoft gerido APIs** na lista de descida seletiva e introduza o *Office 365 Users* na caixa de pesquisa. Selecione o gatilho ou a ação que pretende utilizar:  
   ![Passo de criação de ligação de utilizadores do Office 365](./media/connectors-create-api-office365users/office365users-1.png)  
2. Se ainda não criou nenhuma ligação ao Office 365 Users, será solicitado a fornecer as credenciais de Utilizadores do Office 365. Estas credenciais serão usadas para autorizar a sua aplicação Logic a ligar-se e aceder aos dados da sua conta de Utilizadores do Office 365:  
   ![Passo de criação de ligação de utilizadores do Office 365](./media/connectors-create-api-office365users/office365users-2.png)  
3. Forneça o seu nome de utilizador e senha de utilizador do Office 365 Para autorizar a sua aplicação Logic:  
   ![Passo de criação de ligação de utilizadores do Office 365](./media/connectors-create-api-office365users/office365users-3.png)  
4. Note que a ligação foi criada e agora está livre para prosseguir com os outros passos na sua aplicação Logic:  
   ![Passo de criação de ligação de utilizadores do Office 365](./media/connectors-create-api-office365users/office365users-4.png)  

