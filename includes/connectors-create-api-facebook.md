---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789844"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [no Facebook](https://www.facebook.com/) 

Antes de poder utilizar a sua conta de Facebook numa aplicação Logic, tem de autorizar a aplicação Logic a ligar-se à sua conta de Facebook. Felizmente, pode fazê-lo facilmente dentro da sua aplicação Logic no Portal Azure. 

Aqui estão os passos para autorizar a sua aplicação Logic a ligar-se à sua conta de Facebook:

1. Para criar uma ligação ao Facebook, no designer de aplicações Logic, selecione **Show Microsoft gerido APIs** na lista de drop down e depois introduza o *Facebook* na caixa de pesquisa. Selecione o gatilho ou a ação que pretende utilizar:  
   ![passo facebook 1](./media/connectors-create-api-facebook/facebook-1.png)
2. Se ainda não criou nenhuma ligação ao Facebook antes, será solicitado a fornecer as suas credenciais do Facebook. Estas credenciais serão usadas para autorizar a sua aplicação Logic a ligar-se e aceder aos dados da sua conta do Facebook:  
   ![passo do facebook 2](./media/connectors-create-api-facebook/facebook-2.png)
3. Forneça o seu nome de utilizador e palavra-passe do Facebook para autorizar a sua aplicação Logic:  
   ![passo facebook 3](./media/connectors-create-api-facebook/facebook-3.png)   
4. Note que a ligação foi criada e agora está livre para prosseguir com os outros passos na sua aplicação Logic:  
   ![passo 4 do facebook](./media/connectors-create-api-facebook/facebook-4.png)   

