---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202809"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) conta  

Antes de poder utilizar a sua conta FTP numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta FTP. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta FTP:  

1. Para criar uma ligação para FTP, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *FTP* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![Passo de criação de ligação de FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Se ainda não criou quaisquer ligações a FTP antes, terá de informar fornecer as suas credenciais FTP. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta FTP:  
   ![Passo de criação de ligação de FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:  
   ![Passo de criação de ligação de FTP](./media/connectors-create-api-ftp/ftp-3.png)  

