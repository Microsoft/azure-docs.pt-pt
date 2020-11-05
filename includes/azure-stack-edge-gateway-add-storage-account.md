---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 0b6a6cbf51ef2ff1f1ef53b53a2b84c7a4f9510d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376272"
---
1. No [portal Azure,](https://portal.azure.com/)selecione o seu recurso Azure Stack Edge e, em seguida, vá à **Visão Geral**. O seu dispositivo deve estar online.

2. **Selecione + Adicione a conta de armazenamento** na barra de comando do dispositivo. 

   ![Adicionar uma conta de armazenamento](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. No painel de **conta de armazenamento Add Edge,** especifique as seguintes definições:

    a. Um nome único para a conta de armazenamento Edge no seu dispositivo. Os nomes das contas de armazenamento só podem conter números e letras minúsculas. Não são permitidos caracteres especiais. O nome da conta de armazenamento tem de ser único dentro do dispositivo (não em todos os dispositivos).

    b. Uma descrição opcional para a informação sobre os dados que a conta de armazenamento está a guardar.  
    
    c. Por predefinição, a conta de armazenamento Edge é mapeada para uma conta de Armazenamento Azure na nuvem e os dados da conta de armazenamento são automaticamente empurrados para a nuvem. Especifique a conta de armazenamento Azure para a qual a sua conta de armazenamento Edge está mapeada.  

    d. Em seguida, crie um novo recipiente ou selecione a partir de um recipiente existente na conta de armazenamento Azure. Todos os dados do dispositivo que são escritos na conta de armazenamento Edge são automaticamente enviados para o recipiente de armazenamento selecionado na conta de Armazenamento Azure mapeada.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Depois de todas as opções de conta de armazenamento serem especificadas, **selecione Adicionar** para criar a conta de armazenamento Edge. É notificado quando a conta de armazenamento Edge é criada com sucesso. A nova conta de armazenamento Edge é então apresentada na lista de contas de armazenamento no portal Azure. 

    
4. Se selecionar esta nova conta de armazenamento e aceder às **teclas Access,** pode encontrar o ponto final do serviço blob e o nome correspondente da conta de armazenamento. Copie estas informações pois estes valores juntamente com as teclas de acesso irão ajudá-lo a ligar-se à conta de armazenamento Edge.

    ![Adicionar uma conta de armazenamento 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Obtém as teclas de acesso [ligando-se ao dispositivo APIs local usando o Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
