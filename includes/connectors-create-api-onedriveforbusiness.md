---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102918"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [OneDrive para Negócios](https://OneDrive.com) 

Antes de poder utilizar a sua conta OneDrive para negócios com aplicações lógicas, tem de autorizar as Aplicações Lógicas a ligarem-se à sua conta OneDrive para negócios no portal Azure.

Siga estes passos para autorizar as Aplicações Lógicas a ligarem-se à sua conta OneDrive para negócios:  

1. Inicie sessão no Portal do Azure. 

1. Nos **serviços Azure**, selecione **Logic Apps**. Em seguida, selecione o nome da sua aplicação lógica na lista.

1. No menu da sua aplicação lógica, selecione **o designer de aplicações Logic** em **Ferramentas de Desenvolvimento.**

1. No Logic Apps Designer, selecione **Mostrar APIs geridos pela Microsoft** na lista de drop-down e, em seguida, insira o *OneDrive for Business* na caixa de pesquisa. Selecione o gatilho ou ação para usar:  

   ![Screenshot do Logic Apps Designer, mostrando o gatilho de recorrência com o OneDrive para ações de API de negócios.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Se ainda não criou nenhuma ligação ao OneDrive para negócios, siga o pedido para fornecer as suas credenciais OneDrive para Negócios. Estas credenciais são usadas para autorizar a sua aplicação lógica a aceder aos dados da sua conta OneDrive para negócios:  

   ![Screenshot do Logic Apps Designer, mostrando o pedido de inscrição para o OneDrive para negócios.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Forneça o seu nome de utilizador e palavra-passe OneDrive para autorizar a sua aplicação lógica:  

   ![Screenshot da página de sposição oneDrive para business, mostrando o pedido de inscrição.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. A ligação está agora listada no degrau. Selecione guardar e, em seguida, continue a criar a sua aplicação lógica. 

   ![Screenshot do Logic Apps Designer, mostrando o gatilho com a ligação OneDrive para negócios listada.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
