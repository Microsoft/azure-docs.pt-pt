---
title: Capturar eventos de transmissão em fluxo através do portal do Azure - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo descreve como ativar a captura de eventos de transmissão em fluxo através dos Hubs de eventos do Azure com o portal do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 9108c52529319288fba48dbad3c6f8aa6cb5f725
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850756"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Ativar a captura de eventos de transmissão em fluxo através dos Hubs de eventos do Azure

A [Captura de Hubs de Eventos][capture-overview] do Azure permite a transmissão automática de dados nos Hubs de Eventos para um [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) à sua escolha.

Pode configurar a captura no momento de criação do hub de eventos através do [portal do Azure](https://portal.azure.com). Pode optar por capturar os dados para um contentor de [armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/) do Azure ou para uma conta do [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Para obter mais informações, veja a [Descrição geral de Captura de Hubs de Eventos][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Capturar dados para uma conta de Armazenamento do Azure  

Quando criar um hub de eventos, pode ativar a Captura clicando no botão **Ativar** no ecrã do portal **Criar Hub de Eventos**. Em seguida, especifique uma Conta de Armazenamento e um contentor clicando em **Armazenamento do Azure** na caixa **Capturar Fornecedor**. Uma vez que a Captura dos Hubs de Eventos utiliza a autenticação de serviço a serviço com o armazenamento, não é necessário especificar uma cadeia de ligação do armazenamento. O selecionador de recursos seleciona automaticamente a URI do recurso da sua conta de armazenamento. Se utilizar o Azure Resource Manager, deve fornecer essa URI explicitamente como uma cadeia de carateres.

A janela de tempo predefinida é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela de tempo para captura][1]

> [!NOTE]
> Pode ativar ou desativar a emissão de ficheiros vazios quando não existem eventos ocorrem durante a janela de captura. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Capturar dados para uma conta do Azure Data Lake Store

Para capturar os dados para o Azure Data Lake Store, crie uma conta do Data Lake Store e um hub de eventos:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Criar uma conta e pastas do Azure Data Lake Store

> [!NOTE]
> Atualmente, a funcionalidade de captura de Hubs de eventos suporta apenas Gen 1 do Azure Data Lake Store, não a Gen 2. 

1. Criar uma conta do Data Lake Store Gen 1, seguindo as instruções em [introdução ao Azure Data Lake Store com o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções da secção [Assign permissions to Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) (Atribuir permissões aos Hubs de Eventos) para criar uma pasta na conta do Data Lake Store na qual quer capturar os dados dos Hubs de Eventos e atribua permissões a esta serviço, para que possa escrever dados na sua conta do Data Lake Store.  


### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Tenha em atenção que o hub de eventos tem de estar na mesma subscrição do Azure como o Azure Data Lake Store que acabou de criar. Crie o hub de eventos, clicando no botão **Ativar** em **Captura** na página do portal **Criar Hub de Eventos**. 
2. Na página do portal **Criar Hub de Eventos**, selecione **Azure Data Lake Store** a partir da caixa **Capturar Fornecedor**.
3. Em **Selecionar Data Lake Store**, especifique a conta do Data Lake Store que criou anteriormente e no campo **Caminho do Data Lake**, introduza o caminho para a pasta de dados que criou.

    ![Selecione a conta de armazenamento do Data Lake][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura de um hub de eventos existente

Pode configurar a Captura em hubs de eventos existentes que se encontram num espaço de nomes de Hubs de Eventos. Para ativar a Captura num hub de eventos existente ou alterar as definições de Captura, clique no espaço de nomes para carregar o ecrã de descrição geral e, em seguida, clique no hub de eventos que pretende ativar ou cuja definição de Captura pretende alterar. Por último, clique na opção **Capturar** da página aberta e, em seguida, edite as definições, tal como mostrado nas imagens seguintes:

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

![Configurar o armazenamento de Blobs do Azure][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Configurar o armazenamento do Azure Data Lake][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a captura dos Hubs de Eventos ao ler a [Descrição geral de Captura de Hubs de Eventos][capture-overview].
- Também pode configurar uma Captura de Hubs de Eventos através dos modelos do Azure Resource Manager. Para obter mais informações, consulte [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Ativar a Captura através de um modelo do Azure Resource Manager).
- [Saiba como criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de Eventos como origem](store-captured-data-data-warehouse.md)
- [Introdução ao Azure Data Lake Store com o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
