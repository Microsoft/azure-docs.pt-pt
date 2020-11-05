---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 3e4edc55834d322dede36e9f429f5af7a4a79846
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375973"
---
Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Explorador de Armazenamento, abra o diálogo **de armazenamento Connect to Azure.** No diálogo **de armazenamento "Connect to Azure",** selecione **Utilize o nome da conta de armazenamento e a chave**.

    ![Painel de caixa de dados](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o **nome da conta** e a **tecla Conta** (valor da chave 1 a partir da página **'Ligar e copiar'** na UI web local). Selecione o domínio dos pontos finais de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o ponto final do serviço blob como mostrado abaixo. Consulte a opção **HTTP** apenas se transferir *em http*. Se utilizar *https,* deixe a opção desmarcada. Selecione **Seguinte**.

    ![Painel de instrumentos da Caixa de Dados 2](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Connect** (Ligar).

    ![Painel de instrumentos da Caixa de Dados 3](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta que adicionou com sucesso é apresentada no painel esquerdo do Storage Explorer com (Externo, Outro) anexado ao seu nome. Clique em **Recipientes Blob** para ver o recipiente.

    ![Painel de instrumentos da Caixa de Dados 4](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
