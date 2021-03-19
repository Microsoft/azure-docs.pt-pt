---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040253"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder utilizar a sua conta OneDrive com aplicações lógicas, tem de autorizar as Aplicações Lógicas a ligarem-se à sua conta OneDrive no portal Azure.

Siga estes passos para autorizar as Aplicações Lógicas a ligarem-se à sua conta OneDrive:  

1. Inicie sessão no portal do Azure. 

1. Nos **serviços Azure**, selecione **Logic Apps**. Em seguida, selecione o nome da sua aplicação lógica na lista.

1. No menu da sua aplicação lógica, selecione **o designer de aplicações Logic** em **Ferramentas de Desenvolvimento.**

1. No Logic Apps Designer, selecione **Mostrar APIs geridos pela Microsoft** na lista de drop-down e, em seguida, insira o *OneDrive* na caixa de pesquisa. Selecione o gatilho ou ação para usar:

   ![Screenshot do Logic Apps Designer, mostrando a lista de ações da API oneDrive para adicionar.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Se ainda não criou nenhuma ligação ao OneDrive, siga o pedido para iniciar sômagem utilizando as suas credenciais OneDrive:  

   ![Screenshot do Logic Apps Designer, mostrando o pedido de inscrição para a API OneDrive.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Selecione **Iniciar sôm,** e insira o seu nome de utilizador e senha. Selecione **Iniciar sção em**: 

   ![Screenshot da página de sindes de scontabilidade da Microsoft, para autorização da API OneDrive.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Estas credenciais são usadas para autorizar a sua aplicação lógica a aceder aos dados na sua conta OneDrive. 

4. Selecione **Sim** para autorizar a aplicação lógica a utilizar a sua conta OneDrive:  

   ![Screenshot da autorização de conta da Microsoft para Apps Lógicas, mostrando ações permitidas.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. A ligação está agora listada no degrau. Selecione guardar e, em seguida, continue a criar a sua aplicação lógica. 

   ![Screenshot do Logic Apps Designer, mostrando editor de ação com ligação OneDrive API.](./media/connectors-create-api-onedrive/onedrive-5.png)
