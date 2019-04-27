---
title: Capturar dados dos Hubs de eventos em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilize a geração 1 de armazenamento do Azure Data Lake para capturar dados dos Hubs de eventos
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879582"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Utilize a geração 1 de armazenamento do Azure Data Lake para capturar dados dos Hubs de eventos

Saiba como utilizar o Azure Data Lake Storage Gen1 para capturar os dados recebidos pelo Event Hubs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Um espaço de nomes de Hubs de eventos**. Para obter instruções, consulte [criar um espaço de nomes de Hubs de eventos](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Certifique-se de que a conta de geração 1 do Data Lake Storage e o espaço de nomes de Hubs de eventos estão na mesma subscrição do Azure.


## <a name="assign-permissions-to-event-hubs"></a>Atribuir permissões aos Hubs de eventos

Nesta secção, vai criar uma pasta dentro da conta em que pretende capturar os dados dos Hubs de eventos. Também atribuir permissões aos Hubs de eventos para que possa escrever dados para uma conta de geração 1 de armazenamento do Data Lake. 

1. Abra a conta de geração 1 de armazenamento do Data Lake onde pretende capturar dados dos Hubs de eventos e, em seguida, clique em **Data Explorer**.

    ![Explorador de dados do Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Explorador de dados de geração 1 de armazenamento do Data Lake")

1.  Clique em **nova pasta** e, em seguida, introduza um nome para a pasta onde pretende capturar os dados.

    ![Criar uma nova pasta no Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "criar uma nova pasta na geração 1 de armazenamento do Data Lake")

1. Atribua permissões na raiz de geração 1 de armazenamento do Data Lake. 

    a. Clique em **Data Explorer**, selecione a raiz da conta do Data Lake Storage Gen1 e, em seguida, clique em **acesso**.

    ![Atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake")

    b. Sob **acesso**, clique em **Add**, clique em **selecionar utilizador ou grupo**e, em seguida, procure `Microsoft.EventHubs`. 

    ![Atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake")
    
    Clique em **Selecionar**.

    c. Sob **atribuir permissões**, clique em **selecione permissões**. Definir **permissões** ao **executar**. Definir **adicionar ao** ao **esta pasta e todos os filhos**. Definir **adicionar como** ao **uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    > [!IMPORTANT]
    > Ao criar uma nova hierarquia de pasta para capturar os dados recebidos pelo Event Hubs do Azure, esta é uma forma fácil de garantir o acesso para a pasta de destino.  No entanto, a adicionar permissões a todos os filhos de uma pasta de nível superior com muitos ficheiros e pastas subordinados poderá demorar muito tempo.  Se a sua pasta de raiz contém um grande número de ficheiros e pastas, talvez seja mais rápido adicionar **Execute** permissões para `Microsoft.EventHubs` individualmente para cada pasta no caminho para a pasta de destino final. 

    ![Atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "atribuir permissões para a raiz de geração 1 de armazenamento do Data Lake")

    Clique em **OK**.

1. Atribua permissões para a pasta com a conta do Data Lake Storage Gen1 onde pretende capturar os dados.

    a. Clique em **Data Explorer**, selecione a pasta na conta do Data Lake Storage Gen1 e, em seguida, clique em **acesso**.

    ![Atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake")

    b. Sob **acesso**, clique em **Add**, clique em **selecionar utilizador ou grupo**e, em seguida, procure `Microsoft.EventHubs`. 

    ![Atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake")
    
    Clique em **Selecionar**.

    c. Sob **atribuir permissões**, clique em **selecione permissões**. Definir **permissões** ao **ler, escrever,** e **executar**. Definir **adicionar ao** ao **esta pasta e todos os filhos**. Por fim, defina **adicionar como** ao **uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    ![Atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "atribuir permissões para a pasta de geração 1 de armazenamento do Data Lake")
    
    Clique em **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configurar a Hubs de eventos para capturar dados para a geração 1 de armazenamento do Data Lake

Nesta secção, vai criar um Hub de eventos dentro de um espaço de nomes de Hubs de eventos. Também é configurar o Hub de eventos para capturar dados para uma conta de geração 1 de armazenamento do Azure Data Lake. Esta secção assume que já criou um espaço de nomes de Hubs de eventos.

1. Partir do **descrição geral** painel de espaço de nomes dos Hubs de eventos, clique em **+ Hub de eventos**.

    ![Criar Hub de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "criar Hub de eventos")

1. Forneça os valores seguintes para configurar a Hubs de eventos para capturar dados para a geração 1 de armazenamento do Data Lake.

    ![Criar Hub de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "criar Hub de eventos")

    a. Forneça um nome para o Hub de eventos.
    
    b. Para este tutorial, defina **contagem de partições** e **retenção de mensagens** para os valores predefinidos.
    
    c. Definir **capturar** ao **no**. Definir o **janela de tempo** (como com frequência capturar) e **janela de tamanho** (tamanho de dados para capturar). 
    
    d. Para **capturar fornecedor**, selecione **do Azure Data Lake Store** e, em seguida, selecione a conta de geração 1 do Data Lake armazenamento que criou anteriormente. Para **caminho do Data Lake**, introduza o nome da pasta que criou na conta de geração 1 de armazenamento do Data Lake. Apenas terá de fornecer o caminho relativo para a pasta.

    e. Deixe o **formatos de nome de arquivo de captura de exemplo** para o valor predefinido. Esta opção controla a estrutura de pastas criada sob a pasta de captura.

    f. Clique em **Criar**.

## <a name="test-the-setup"></a>A configuração de teste

Agora pode testar a solução ao enviar dados para o Hub de eventos do Azure. Siga as instruções em [enviar eventos para Hubs de eventos](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Depois de começar a enviar os dados, ver os dados serão refletidas na geração 1 de armazenamento do Data Lake com a pasta de estrutura que especificou. Por exemplo, verá uma estrutura de pastas, como mostrado na captura de ecrã seguinte, na sua conta de geração 1 de armazenamento do Data Lake.

![Exemplo de dados de EventHub na geração 1 de armazenamento do Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "dados de exemplo EventHub na geração 1 de armazenamento do Data Lake")

> [!NOTE]
> Mesmo que não tem mensagens recebidas nos Hubs de eventos, os Hubs de eventos escreve ficheiros vazios com apenas os cabeçalhos para a conta de geração 1 de armazenamento do Data Lake. Os ficheiros são escritos no mesmo intervalo de tempo que forneceu ao criar os Hubs de eventos.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analisar dados de geração 1 de armazenamento do Data Lake

Quando os dados estiverem na geração 1 de armazenamento do Data Lake, pode executar tarefas analíticas para o processo e processe os dados. Ver [u-SQL Avro exemplo](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) sobre como fazer isto com o Azure Data Lake Analytics.
  

## <a name="see-also"></a>Consulte também
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
