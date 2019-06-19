---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184926"
---
Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Explorador de armazenamento, abra a **ligar ao armazenamento do Azure** caixa de diálogo. Na **ligar ao armazenamento do Azure** caixa de diálogo, selecione **utilize um nome de conta de armazenamento e a chave**.

    ![Dashboard de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Colar seu **nome da conta** e **chave de conta** (1 valor da chave a **Connect e a cópia** página na IU da web local). Selecione domínio de pontos finais de armazenamento como **outros (introduzir abaixo)** e, em seguida, forneça o ponto final de serviço de BLOBs, conforme mostrado abaixo. Verifique **utilizar HTTP** opção apenas se transferir através de *http*. Se utilizar *https*, deixe a opção desmarcada. Selecione **Seguinte**.

    ![Dashboard de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Na **resumo da ligação** caixa de diálogo, reveja as informações fornecidas. Selecione **Ligar**.

    ![Dashboard de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta que adicionou com êxito é apresentada no painel esquerdo do Explorador de armazenamento com (externos, outros) anexado ao respetivo nome. Clique em **contentores de BLOBs** para ver o contentor.

    ![Dashboard de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
