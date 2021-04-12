---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185577"
---
Se esta for a primeira vez que utiliza o Storage Explorer, tem de executar os seguintes passos.

1. A partir da barra de comando superior, vá a **Editar > Target Azure Stack APIs**.

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Reinicie o Explorador de Armazenamento para que as alterações entrem em vigor.


Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Storage Explorer, selecione contas de armazenamento. Clique com o botão direito e selecione a opção **'Ligar ao Azure Storage'.** 

    ![Configure Explorador de Armazenamento 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. No diálogo **de armazenamento "Connect to Azure",** selecione **Utilize o nome da conta de armazenamento e a chave**.

    ![Configure Explorador de Armazenamento 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. No **diálogo "Ligar com Nome e Chave",** tome os seguintes passos:

    1. Introduza um nome de exibição para a sua conta de armazenamento Edge. 
    2. Forneça o nome da conta de armazenamento Edge.
    3. Cole a chave de acesso que obteve do dispositivo APIs local via Azure Resource Manager.
    4. Selecione o domínio de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o sufixo do ponto final de serviço blob no formato: `<appliance name>.<DNSdomain>` . 
    5. **Consulte a** opção HTTP como a transferência é mais de *http*. 
    6. Selecione **Seguinte**.

    ![Configure Explorador de Armazenamento 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![Configure Explorador de Armazenamento 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. A conta que adicionou com sucesso é apresentada no painel esquerdo do Storage Explorer com (Externo, Outro) anexado ao seu nome. Selecione **Recipientes Blob** para ver o recipiente.

    ![Ver recipientes blob](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

O próximo passo para verificar é que a transferência de dados está realmente a funcionar corretamente sobre esta ligação.

Tome os seguintes passos para carregar os dados na sua conta de armazenamento Edge no dispositivo e deve ser automaticamente diferenciado para a conta de Armazenamento Azure mapeada.

1. Selecione o recipiente ao qual pretende carregar os dados na sua conta de armazenamento Edge. Selecione **Upload** e, em seguida, selecione **upload de ficheiros**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. No diálogo de **ficheiros upload,** navegue para e selecione os ficheiros que pretende carregar. Selecione **Seguinte**.

    ![Verificar transferência de dados 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Verifique se os ficheiros foram carregados. Os ficheiros enviados aparecem no contentor.

    ![Verificar transferência de dados 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Em seguida, irá ligar-se à conta de Armazenamento Azure que foi mapeada para esta conta de armazenamento Edge. Quaisquer dados que sejam enviados para a conta de armazenamento Edge devem ser automaticamente hierárquicos para a conta de Armazenamento Azure. 
    
    Para obter o fio de ligação para a conta de Armazenamento Azure, aceda à **conta Azure Storage > as teclas Access** e copie a cadeia de ligação.

    ![Verificar transferência de dados 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Utilize o fio de ligação para fixar à conta de Armazenamento Azure.  

    ![Verificar transferência de dados 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![Verificar transferência de dados 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Verá que os ficheiros que carregou na conta de armazenamento Edge foram transferidos para a conta de Armazenamento Azure.

    ![Verificar transferência de dados 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
