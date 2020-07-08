---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789690"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder utilizar a sua conta OneDrive numa aplicação lógica, autorize a aplicação lógica para se ligar à sua conta OneDrive.  Pode fazê-lo facilmente dentro da sua aplicação lógica no portal Azure. 

Autorize a sua aplicação lógica para ligar à sua conta OneDrive utilizando os seguintes passos:

1. Criar uma aplicação lógica. No designer de Aplicações Lógicas, selecione **Mostrar APIs geridos pela Microsoft** na lista de drop down e, em seguida, insira "onedrive" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não criou nenhuma ligação ao OneDrive, é-lhe pedido que faça sê-lo utilizando as suas credenciais OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **Iniciar sôm,** e insira o seu nome de utilizador e senha. Selecione **Iniciar sção em**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciais são usadas para autorizar a sua aplicação lógica a ligar-se e aceder aos dados na sua conta OneDrive. 
4. Selecione **Sim** para autorizar a aplicação lógica a utilizar a sua conta OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Note que a ligação foi criada. Agora, proceda com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

