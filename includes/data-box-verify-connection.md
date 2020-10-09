---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184926"
---
Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Explorador de Armazenamento, abra o diálogo **de armazenamento Connect to Azure.** No diálogo **de armazenamento "Connect to Azure",** selecione **Utilize o nome da conta de armazenamento e a chave**.

    ![Painel de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o **nome da conta** e a **tecla Conta** (valor da chave 1 a partir da página **'Ligar e copiar'** na UI web local). Selecione o domínio dos pontos finais de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o ponto final do serviço blob como mostrado abaixo. Consulte a opção **HTTP** apenas se transferir *em http*. Se utilizar *https,* deixe a opção desmarcada. Selecione **Seguinte**.

    ![Painel de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![Painel de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta que adicionou com sucesso é apresentada no painel esquerdo do Storage Explorer com (Externo, Outro) anexado ao seu nome. Clique em **Recipientes Blob** para ver o recipiente.

    ![Painel de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
