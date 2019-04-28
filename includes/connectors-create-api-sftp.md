---
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130063"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) conta  

Antes de poder utilizar a sua conta do SFTP numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do SFTP. Felizmente, pode fazer isso facilmente de na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do SFTP:  

1. Para criar uma ligação para SFTP, no Estruturador da aplicação lógica, selecione **APIs geridas da Microsoft mostram** na lista pendente, em seguida, introduza *SFTP* na caixa de pesquisa. Selecione o **SFTP - quando um ficheiro é adicionado ou modificado** acionador:  
   ![Imagem de ligação online do SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se ainda não criou quaisquer ligações para SFTP antes, terá de informar fornecer as suas credenciais SFTP. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e acedam a dados da sua conta do SFTP:  
   ![Imagem de ligação online do SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe que a ligação foi criada e agora está livre para continuar com os outros passos na sua aplicação lógica:   
   ![Imagem de ligação online do SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

