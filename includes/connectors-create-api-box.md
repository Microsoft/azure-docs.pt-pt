---
ms.openlocfilehash: d1db175ecc2cf61de0debc15d198d6367aae8bdd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119979"
---
### <a name="prerequisites"></a>Pré-requisitos
* R [caixa](http://box.com) conta  

Antes de poder utilizar a conta do box numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à conta do box. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à conta do box:  

1. Para criar uma ligação ao box, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *caixa* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![passo de criação de ligação de caixa](./media/connectors-create-api-box/box-1.png)  
2. Se ainda não criou quaisquer ligações a caixa antes, terá de informar para fornecer suas credenciais do box. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta da caixa:  
   ![passo de criação de ligação de caixa](./media/connectors-create-api-box/box-2.png)  
3. Forneça o seu nome de utilizador de caixa e a palavra-passe para autorizar a aplicação lógica:  
   ![passo de criação de ligação de caixa](./media/connectors-create-api-box/box-3.png)  
4. Permitir-nos ligar à caixa:  
   ![passo de criação de ligação de caixa](./media/connectors-create-api-box/box-4.png)  
5. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:  
   ![passo de criação de ligação de caixa](./media/connectors-create-api-box/box-5.png)  

