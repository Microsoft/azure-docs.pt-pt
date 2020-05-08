---
title: Capture dados de Hubs de Eventos para Azure Data Lake Storage Gen1
description: Use o Azure Data Lake Storage Gen1 para capturar dados que são recebidos pelo Azure Event Hubs.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5cbcdc5cb9713432be5b52898d956506d80d6ea0
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690674"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Use o Azure Data Lake Storage Gen1 para capturar dados dos Centros de Eventos

Saiba como usar o Azure Data Lake Storage Gen1 para capturar os dados recebidos pelo Azure Event Hubs.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar um, consulte Iniciar com [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Um espaço de nome de Hubs de Eventos.** Para obter instruções, consulte Criar um espaço de [nome sinuoso](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)do Event Hubs . Certifique-se de que a conta Data Lake Storage Gen1 e o espaço de nome sem nome do Event Hubs estão na mesma subscrição do Azure.


## <a name="assign-permissions-to-event-hubs"></a>Atribuir permissões aos Centros de Eventos

Nesta secção, cria-se uma pasta dentro da conta onde pretende capturar os dados dos Centros de Eventos. Também atribui permissões aos Centros de Eventos para que possa escrever dados numa conta gen1 de armazenamento de data lake. 

1. Abra a conta Data Lake Storage Gen1 onde pretende capturar dados dos Centros de Eventos e, em seguida, clique no **Data Explorer**.

    ![Explorador de dados gen1 de armazenamento de lago de dados](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Explorador de dados gen1 de armazenamento de lago de dados")

1.  Clique em **New Folder** e, em seguida, introduza um nome para pasta onde pretende capturar os dados.

    ![Criar uma nova pasta no Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Criar uma nova pasta no Data Lake Storage Gen1")

1. Atribua permissões na raiz do Data Lake Storage Gen1. 

    a. Clique no **Data Explorer,** selecione a raiz da conta Gen1 de Armazenamento de Data Lake e, em seguida, clique em **Access**.

    ![Atribua permissões para a raiz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Atribua permissões para a raiz de Data Lake Storage Gen1")

    b. Em **'Acesso',** clique em **Adicionar,** clique `Microsoft.EventHubs`em Selecionar utilizador ou **grupo,** e depois procure por . 

    ![Atribua permissões para a raiz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribua permissões para a raiz de Data Lake Storage Gen1")
    
    Clique em **Selecionar**.

    c. Em **permissões**de atribuição, clique em **Selecionar Permissões**. Definir **permissões** para **executar**. **Desloque-se a** **esta pasta e a todas as crianças**. Definir **Adicionar quanto** a uma entrada de permissão de acesso e uma entrada de **permissão por defeito**.

    > [!IMPORTANT]
    > Ao criar uma nova hierarquia de pastas para capturar dados recebidos pelo Azure Event Hubs, esta é uma forma fácil de garantir o acesso à pasta de destino.  No entanto, adicionar permissões a todas as crianças de uma pasta de alto nível com muitos ficheiros e pastas infantis pode demorar muito tempo.  Se a sua pasta de raiz contiver um grande número de ficheiros e pastas, pode ser mais rápido adicionar permissões **executar** individualmente `Microsoft.EventHubs` a cada pasta no caminho para a sua pasta de destino final. 

    ![Atribua permissões para a raiz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Atribua permissões para a raiz de Data Lake Storage Gen1")

    Clique em **OK**.

1. Atribuir permissões para a pasta sob a conta Data Lake Storage Gen1 onde pretende capturar dados.

    a. Clique no **Data Explorer,** selecione a pasta na conta Data Lake Storage Gen1 e, em seguida, clique em **Access**.

    ![Atribuir permissões para a pasta Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Atribuir permissões para a pasta Data Lake Storage Gen1")

    b. Em **'Acesso',** clique em **Adicionar,** clique `Microsoft.EventHubs`em Selecionar utilizador ou **grupo,** e depois procure por . 

    ![Atribuir permissões para a pasta Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a pasta Data Lake Storage Gen1")
    
    Clique em **Selecionar**.

    c. Em **permissões**de atribuição, clique em **Selecionar Permissões**. Definir **permissões** para **ler, escrever** e **executar.** **Desloque-se a** **esta pasta e a todas as crianças**. Por fim, despemos **quanto** a uma entrada de autorização de **acesso e uma entrada de permissão por defeito**.

    ![Atribuir permissões para a pasta Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Atribuir permissões para a pasta Data Lake Storage Gen1")
    
    Clique em **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configure Hubs de eventos para capturar dados para Data Lake Storage Gen1

Nesta secção, você cria um Event Hub dentro de um espaço de nome de Event Hubs. Também configura o Hub de Eventos para capturar dados para uma conta De armazenamento de lagos Azure Data Gen1. Esta secção pressupõe que já criou um espaço de nome sinuoso do Event Hubs.

1. A partir do painel **de visão geral** do espaço de nome do Event Hubs, clique em + Centro de **Eventos**.

    ![Criar Hub de Eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Criar Hub de Eventos")

1. Forneça os seguintes valores para configurar os Centros de Eventos para capturar dados para data Lake Storage Gen1.

    ![Criar Hub de Eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Criar Hub de Eventos")

    a. Forneça um nome para o Centro de Eventos.
    
    b. Para este tutorial, coloque a **Contagem de Partição** e a **Retenção de Mensagens** nos valores predefinidos.
    
    c. Definir **a captura** para **on**. Detete a **Janela** do Tempo (com que frequência capturar) e **a Janela de Tamanho** (tamanho de dados para capturar). 
    
    d. Para **capturar fornecedor,** selecione **Azure Data Lake Store** e, em seguida, selecione a conta Data Lake Storage Gen1 que criou anteriormente. Para **Data Lake Path,** introduza o nome da pasta que criou na conta Data Lake Storage Gen1. Basta fornecer o caminho relativo para a pasta.

    e. Deixe os **formatos** de nome de ficheiro de captura da Amostra para o valor predefinido. Esta opção rege a estrutura da pasta que é criada sob a pasta de captura.

    f. Clique em **Criar**.

## <a name="test-the-setup"></a>Testar a configuração

Agora pode testar a solução enviando dados para o Azure Event Hub. Siga as instruções no Enviar eventos para Os Centros de [Eventos Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Assim que começar a enviar os dados, vê os dados refletidos no Data Lake Storage Gen1 utilizando a estrutura da pasta que especificou. Por exemplo, vê uma estrutura de pasta, como mostra a seguinte imagem, na sua conta Data Lake Storage Gen1.

![Dados do EventHub da amostra em Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Dados do EventHub da amostra em Data Lake Storage Gen1")

> [!NOTE]
> Mesmo que não tenha mensagens a entrar em Centros de Eventos, o Event Hubs escreve ficheiros vazios apenas com os cabeçalhos na conta Data Lake Storage Gen1. Os ficheiros são escritos ao mesmo tempo que forneceu durante a criação dos Centros de Eventos.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analisar dados em Data Lake Storage Gen1

Uma vez que os dados estão em Data Lake Storage Gen1, você pode executar trabalhos analíticos para processar e recolher os dados. Consulte o [USQL Avro Exemplo](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) sobre como fazê-lo usando o Azure Data Lake Analytics.
  

## <a name="see-also"></a>Consulte também
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Copiar dados de Blobs de Armazenamento Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
