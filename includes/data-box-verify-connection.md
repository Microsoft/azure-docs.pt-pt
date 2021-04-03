---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553231"
---
Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Explorador de Armazenamento, abra o diálogo **de armazenamento Connect to Azure.** No diálogo **de armazenamento "Connect to Azure",** selecione **Utilize o nome da conta de armazenamento e a chave**.

    ![A screenshot mostra a caixa de diálogo de armazenamento Connect to Azure com o uso de um nome de conta de armazenamento e chave selecionadas.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o **nome da conta** e a **tecla Conta** (valor da chave 1 a partir da página **'Ligar e copiar'** na UI web local). Selecione o domínio dos pontos finais de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o ponto final do serviço blob como mostrado abaixo. Consulte a opção **HTTP** apenas se transferir *em http*. Se utilizar *https,* deixe a opção desmarcada. Selecione **Seguinte**.

    ![A screenshot mostra a caixa de diálogo Connect with Name e Key com valores introduzidos.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![A screenshot mostra a caixa de diálogo 'Resumo de Ligação' com o Connect selecionado.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta que adicionou com sucesso é apresentada no painel esquerdo do Storage Explorer com (Externo, Outro) anexado ao seu nome. Clique em **Recipientes Blob** para ver o recipiente.

    ![A screenshot mostra o menu Explorer com recipientes Blob selecionados.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
