---
title: Event Hubs - Capture eventos de streaming usando o portal Azure
description: Este artigo descreve como permitir a captura de eventos através do Azure Event Hubs utilizando o portal Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8c89a6e56c2d0bc7b7ff09d5fd63737009239eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653956"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Permitir a captura de eventos transmitidos através dos Hubs de Eventos do Azure

O Azure [Event Hubs Capture][capture-overview] permite-lhe entregar automaticamente os dados de streaming em Event Hubs para um [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Storage Gen1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) à sua escolha.

Pode configurar a captura no momento de criação do hub de eventos através do [portal do Azure](https://portal.azure.com). Pode capturar os dados num recipiente de armazenamento Azure [Blob](https://azure.microsoft.com/services/storage/blobs/) ou numa conta [Azure Data Lake Storage Gen 1 ou Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Para obter mais informações, veja a [Descrição geral de Captura de Hubs de Eventos][capture-overview].

> [!IMPORTANT]
> A conta de armazenamento de destino (Azure Storage ou Azure Data Lake Storage) deve estar na mesma subscrição que o centro de eventos.

## <a name="capture-data-to-azure-storage"></a>Capturar dados para o Armazenamento Azure

Quando criar um hub de eventos, pode ativar a Captura clicando no botão **Ativar** no ecrã do portal **Criar Hub de Eventos**. Em seguida, especifique uma Conta de Armazenamento e um contentor clicando em **Armazenamento do Azure** na caixa **Capturar Fornecedor**. Uma vez que a Captura dos Hubs de Eventos utiliza a autenticação de serviço a serviço com o armazenamento, não é necessário especificar uma cadeia de ligação do armazenamento. O selecionador de recursos seleciona automaticamente a URI do recurso da sua conta de armazenamento. Se utilizar o Azure Resource Manager, deve fornecer essa URI explicitamente como uma cadeia de carateres.

A janela de tempo predefinida é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela de tempo para captura][1]

> [!NOTE]
> Pode ativar ou desativar ficheiros vazios quando não ocorrerem eventos durante a janela Captura. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Capturar dados para Azure Data Lake Storage Gen 2 

1. Siga Criar um artigo [de conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) para criar uma conta de Armazenamento Azure. Desconfie **o espaço hierárquico** para **ativar** no separador **Avançado** para torná-lo uma conta Azure Data Lake Storage Gen 2.
2. Ao criar um centro de eventos, faça os seguintes passos: 

    1. Selecione **on** for **Capture**. 
    2. Selecione **Azure Storage** como fornecedor de captura. A opção **Azure Data Lake Store** que você vê para o **fornecedor Capture** é para a Gen 1 de Azure Data Lake Storage. Para utilizar um Grupo Gen 2 de Azure Data Lake Storage, selecione **Azure Storage**.
    2. Selecione o botão **'Selecionar recipiente'.** 

        ![Permitir a captura para data lake storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecione a conta **Azure Data Lake Storage Gen 2** da lista. 

    ![Selecione Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecione o **recipiente** (sistema de ficheiros na Data Lake Storage Gen 2).

    ![Selecione o sistema de ficheiros no armazenamento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na página **'Criar Centro de Eventos',** selecione **Criar**. 

    ![Selecione Criar botão](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > O recipiente que cria num Azure Data Lake Storage Gen 2 utilizando esta interface de utilizador (UI) é mostrado nos **sistemas de ficheiros** no **Storage Explorer**. Da mesma forma, o sistema de ficheiros que cria numa conta de Data Lake Storage Gen 2 aparece como um recipiente nesta UI. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Capturar dados para Azure Data Lake Storage Gen 1 

Para capturar dados para a Azure Data Lake Storage Gen 1, você cria uma conta Desatado Desejamento do Lago de Dados Gen 1 e um centro de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Criar uma conta Escamas de Armazenamento de Dados Azure Data Lake Gen 1 e pastas

1. Crie uma conta de armazenamento de data lake, seguindo as instruções em [Começar com a Azure Data Lake Storage Gen 1 utilizando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na secção ['Atribuir permissões' aos Centros de Eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para criar uma pasta dentro da conta Data Lake Storage Gen 1 na qual pretende capturar os dados dos Centros de Eventos e atribuir permissões aos Centros de Eventos para que possa escrever dados na sua conta Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. O centro de eventos deve estar na mesma subscrição do Azure Data Lake Storage Gen 1 que criou. Crie o hub de eventos, clicando no botão **Ativar** em **Captura** na página do portal **Criar Hub de Eventos**. 
2. Na página do portal **Criar Hub de Eventos**, selecione **Azure Data Lake Store** a partir da caixa **Capturar Fornecedor**.
3. Na **Select Store** ao lado da lista de drop-down da Data Lake **Store,** especifique a conta Deseiora de Armazenamento de Data Lake Gen 1 que criou anteriormente, e no campo Data Lake **Path,** insira o caminho para a pasta de dados que criou.

    ![Selecione conta de armazenamento do Lago de Dados][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura de um hub de eventos existente

Pode configurar a Captura em hubs de eventos existentes que se encontram num espaço de nomes de Hubs de Eventos. Para ativar a Captura num hub de eventos existente ou alterar as definições de Captura, clique no espaço de nomes para carregar o ecrã de descrição geral e, em seguida, clique no hub de eventos que pretende ativar ou cuja definição de Captura pretende alterar. Por último, clique na opção **Capturar** da página aberta e, em seguida, edite as definições, tal como mostrado nas imagens seguintes:

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

![Configure Armazenamento de bolhas de azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

![Configurar Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Configurar armazenamento do lago de dados Azure][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a captura dos Hubs de Eventos ao ler a [Descrição geral de Captura de Hubs de Eventos][capture-overview].
- Também pode configurar uma Captura de Hubs de Eventos através dos modelos do Azure Resource Manager. Para obter mais informações, consulte [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Ativar a Captura através de um modelo do Azure Resource Manager).
- [Saiba como criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de Eventos como origem](store-captured-data-data-warehouse.md)
- [Começa com a Azure Data Lake Store usando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
