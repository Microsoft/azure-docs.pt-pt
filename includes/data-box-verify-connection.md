---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67184926"
---
Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Storage Explorer, abra o diálogo **Connect to Azure Storage.** No diálogo **Connect to Azure Storage,** selecione **Utilize um nome e uma chave da conta de armazenamento**.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o nome da **conta** e a **chave conta** (chave 1 do valor da página Connect e **cópia** na UI web local). Selecione o domínio dos pontos finais de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o ponto final do serviço blob como mostrado abaixo. Verifique a opção **Use HTTP** apenas se transferir em *http*. Se utilizar *https,* deixe a opção desmarcada. Selecione **Seguinte**.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. No diálogo **sumário de ligação,** reveja as informações fornecidas. Selecione **Ligar**.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta que adicionou com sucesso é exibida no painel esquerdo do Storage Explorer com (Externo, Outros) anexado ao seu nome. Clique em **Recipientes Blob** para ver o recipiente.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
