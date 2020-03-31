---
title: Event Hubs - Capture eventos de streaming usando portal Azure
description: Este artigo descreve como permitir a captura de eventos que passam pelos Hubs de Eventos Azure utilizando o portal Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187429"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Permitir a captura de eventos que transmitem através dos Hubs de Eventos Azure

A Captura de Hubs de [Eventos][capture-overview] Azure permite-lhe entregar automaticamente os dados de streaming em Event Hubs a um [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou a conta de Armazenamento de [Lagos Azure Data Gen1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) à sua escolha.

Pode configurar a captura no momento de criação do hub de eventos através do [portal do Azure](https://portal.azure.com). Pode capturar os dados para um recipiente de armazenamento Azure [Blob,](https://azure.microsoft.com/services/storage/blobs/) ou para uma conta de Armazenamento de [Lagos Azure Data Gen 1 ou Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Para obter mais informações, veja a [Descrição geral de Captura de Hubs de Eventos][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Capturar dados para armazenamento azure

Quando criar um hub de eventos, pode ativar a Captura clicando no botão **Ativar** no ecrã do portal **Criar Hub de Eventos**. Em seguida, especifique uma Conta de Armazenamento e um contentor clicando em **Armazenamento do Azure** na caixa **Capturar Fornecedor**. Uma vez que a Captura dos Hubs de Eventos utiliza a autenticação de serviço a serviço com o armazenamento, não é necessário especificar uma cadeia de ligação do armazenamento. O selecionador de recursos seleciona automaticamente a URI do recurso da sua conta de armazenamento. Se utilizar o Azure Resource Manager, deve fornecer essa URI explicitamente como uma cadeia de carateres.

A janela de tempo predefinida é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela do tempo para a captura][1]

> [!NOTE]
> Pode ativar ou desativar a emissão de ficheiros vazios quando não ocorrerem eventos durante a janela Captura. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Capture dados para Azure Data Lake Storage Gen 2 

1. Siga criar um artigo [de conta](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) de armazenamento para criar uma conta de Armazenamento Azure. Detete o espaço de **nome hierárquico** para **ativado** no separador **Avançado** para torná-lo uma conta De armazenamento de lagos De dados Azure Gen 2.
2. Ao criar um centro de eventos, faça os seguintes passos: 

    1. Selecione **On** for **Capture**. 
    2. Selecione **O Armazenamento Azure** como fornecedor de captura. A opção **Azure Data Lake Store** que você vê para o **fornecedor de captura** é para a Gen 1 do Armazenamento do Lago De Dados Azure. Para utilizar uma Gen 2 de Armazenamento de Lago de Dados Azure, selecione **Armazenamento Azure**.
    2. Selecione o botão **Select Container.** 

        ![Ativar a captura para data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecione a conta **De Armazenamento do Lago Azure Gen 2** da lista. 

    ![Selecione Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecione o **recipiente** (sistema de ficheiros no Data Lake Storage Gen 2).

    ![Selecione sistema de ficheiros no armazenamento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na página **Create Event Hub,** selecione **Criar**. 

    ![Selecione criar botão](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > O recipiente que cria num Azure Data Lake Storage Gen 2 utilizando esta interface de utilizador (UI) é mostrado em **sistemas de ficheiros** no **Storage Explorer**. Da mesma forma, o sistema de ficheiros que cria numa conta data Lake Storage Gen 2 aparece como um recipiente neste UI. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Capture dados para Azure Data Lake Storage Gen 1 

Para capturar dados para o Azure Data Lake Storage Gen 1, cria uma conta data Lake Storage Gen 1 e um centro de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Criar uma conta e pastas de armazenamento de lagos de dados Azure Gen 1

1. Crie uma conta de Armazenamento de Data Lake, seguindo as instruções em [Get started com Azure Data Lake Storage Gen 1 usando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na secção De atribuição de [permissões à](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) secção De Sessão de Centros de Eventos para criar uma pasta dentro da conta Data Lake Storage Gen 1 na qual pretende capturar os dados dos Hubs de Eventos e atribuir permissões aos Hubs de Eventos para que possa escrever dados na sua conta Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. O centro do evento deve estar na mesma subscrição azure que a conta Azure Data Lake Storage Gen 1 que criou. Crie o hub de eventos, clicando no botão **Ativar** em **Captura** na página do portal **Criar Hub de Eventos**. 
2. Na página do portal **Criar Hub de Eventos**, selecione **Azure Data Lake Store** a partir da caixa **Capturar Fornecedor**.
3. Na **Select Store** junto à lista de drop-down da Data Lake **Store,** especifique a conta Data Lake Storage Gen 1 que criou anteriormente e no campo Data Lake **Path,** insira o caminho para a pasta de dados que criou.

    ![Selecione conta de armazenamento de data lake][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura de um hub de eventos existente

Pode configurar a Captura em hubs de eventos existentes que se encontram num espaço de nomes de Hubs de Eventos. Para ativar a Captura num hub de eventos existente ou alterar as definições de Captura, clique no espaço de nomes para carregar o ecrã de descrição geral e, em seguida, clique no hub de eventos que pretende ativar ou cuja definição de Captura pretende alterar. Por último, clique na opção **Capturar** da página aberta e, em seguida, edite as definições, tal como mostrado nas imagens seguintes:

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

![Configure Armazenamento Blob Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

![Configure Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Armazenamento de lagos azure data gen 1 

![Configure Armazenamento de data lake][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a captura dos Hubs de Eventos ao ler a [Descrição geral de Captura de Hubs de Eventos][capture-overview].
- Também pode configurar uma Captura de Hubs de Eventos através dos modelos do Azure Resource Manager. Para obter mais informações, consulte [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Ativar a Captura através de um modelo do Azure Resource Manager).
- [Saiba como criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de Eventos como origem](store-captured-data-data-warehouse.md)
- [Começar com a Azure Data Lake Store usando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
