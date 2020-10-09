---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89087932"
---
Se esta for a primeira vez que utiliza o Storage Explorer, tem de executar os seguintes passos.

1. A partir da barra de comando superior, vá a **Editar > Target Azure Stack APIs**.

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Reinicie o Explorador de Armazenamento para que as alterações entrem em vigor.


Siga estes passos para ligar à conta de armazenamento e verificar a ligação.

1. No Storage Explorer, selecione contas de armazenamento. Clique com o botão direito e selecione a opção **'Ligar ao Azure Storage'.** 

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. No diálogo **de armazenamento "Connect to Azure",** selecione **Utilize o nome da conta de armazenamento e a chave**.

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. No **diálogo "Ligar com Nome e Chave",** tome os seguintes passos:

    1. Introduza um nome de exibição para a sua conta de armazenamento Edge. 
    2. Forneça o nome da conta de armazenamento Edge.
    3. Cole a chave de acesso que obteve do dispositivo APIs local via Azure Resource Manager.
    4. Selecione o domínio de armazenamento como **Outro (insira abaixo)** e, em seguida, forneça o sufixo do ponto final de serviço blob no formato: `<appliance name>.<DNSdomain>` . 
    5. **Consulte a** opção HTTP como a transferência é mais de *http*. 
    6. Selecione **Seguinte**.

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![Configure explorador de armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. A conta que adicionou com sucesso é apresentada no painel esquerdo do Storage Explorer com (Externo, Outro) anexado ao seu nome. Selecione **Recipientes Blob** para ver o recipiente.

    ![Ver recipientes blob](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

O próximo passo para verificar é que a transferência de dados está realmente a funcionar corretamente sobre esta ligação.

Tome os seguintes passos para carregar os dados na sua conta de armazenamento Edge no dispositivo e deve ser automaticamente diferenciado para a conta de Armazenamento Azure mapeada.

1. Selecione o recipiente ao qual pretende carregar os dados na sua conta de armazenamento Edge. Selecione **Upload** e, em seguida, selecione **upload de ficheiros**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. No diálogo de **ficheiros upload,** navegue para e selecione os ficheiros que pretende carregar. Selecione **Seguinte**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Verifique se os ficheiros foram carregados. Os ficheiros enviados aparecem no contentor.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Em seguida, irá ligar-se à conta de Armazenamento Azure que foi mapeada para esta conta de armazenamento Edge. Quaisquer dados que sejam enviados para a conta de armazenamento Edge devem ser automaticamente hierárquicos para a conta de Armazenamento Azure. 
    
    Para obter o fio de ligação para a conta de Armazenamento Azure, aceda à **conta Azure Storage > as teclas Access** e copie a cadeia de ligação.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Utilize o fio de ligação para fixar à conta de Armazenamento Azure.  

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. No diálogo 'Resumo de **Ligação',** reveja as informações fornecidas. Selecione **Ligar**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Verá que os ficheiros que carregou na conta de armazenamento Edge foram transferidos para a conta de Armazenamento Azure.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)