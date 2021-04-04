---
title: Capturar dados de Centros de Eventos para Azure Data Lake Storage Gen1
description: Saiba como usar a Azure Data Lake Storage Gen1 para capturar dados recebidos pelo Azure Event Hubs. Comece por verificar os pré-requisitos.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578245"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Use a Azure Data Lake Storage Gen1 para capturar dados de Centros de Eventos

Saiba como usar a Azure Data Lake Storage Gen1 para capturar dados recebidos pelo Azure Event Hubs.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Uma conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Um espaço de nomes de Centros de Eventos.** Para obter instruções, consulte [Criar um espaço de nomes de Centros de Eventos](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Certifique-se de que a conta De armazenamento de Dados Gen1 e o espaço de nomes do Event Hubs estão na mesma subscrição do Azure.


## <a name="assign-permissions-to-event-hubs"></a>Atribuir permissões aos Centros de Eventos

Nesta secção, cria-se uma pasta dentro da conta onde pretende capturar os dados dos Centros de Eventos. Também atribui permissões a Event Hubs para que possa escrever dados numa conta gen1 de armazenamento de data lake. 

1. Abra a conta De armazenamento de dados Gen1 onde pretende capturar dados a partir de Centros de Eventos e, em seguida, clique no **Data Explorer**.

    ![Data Lake Storage Gen1 explorador de dados](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 explorador de dados")

1.  Clique **em Nova Pasta** e, em seguida, introduza um nome para pasta onde pretende capturar os dados.

    ![Criar uma nova pasta no Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Criar uma nova pasta no Data Lake Storage Gen1")

1. Atribua permissões na raiz da Data Lake Storage Gen1. 

    a. Clique em **Data Explorer,** selecione a raiz da conta Dese de Armazenamento de Dados Gen1 e, em seguida, clique em **Access**.

    ![Screenshot do explorador de dados com a raiz da conta e a opção Access chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Atribuir permissões para a raiz gen1 de armazenamento de data lake")

    b. In **Access**, clique em **Adicionar**, clique em Selecionar Utilizador **ou Grupo** e, em seguida, procure `Microsoft.EventHubs` . 

    ![Screenshot da página Access com a opção Adicionar, Selecione User ou Group e a opção Microsoft Eventhubs chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a raiz gen1 de armazenamento de data lake")
    
    Clique em **Selecionar**.

    c. Sob **Permissões de Atribuição,** clique **em Selecionar Permissões**. Definir **permissões** para **executar**. Definir **Adicione a** esta pasta e a todas as **crianças**. De **configurar Adicionar como** uma entrada de **permissão de acesso e uma entrada de permissão por defeito**.

    > [!IMPORTANT]
    > Ao criar uma nova hierarquia de pastas para capturar dados recebidos pelo Azure Event Hubs, esta é uma forma fácil de garantir o acesso à pasta de destino.  No entanto, a adição de permissões a todas as crianças de uma pasta de nível superior com muitos ficheiros e pastas para crianças pode demorar muito tempo.  Se a sua pasta de raiz contiver um grande número de ficheiros e pastas, poderá ser mais rápido adicionar permissões **de execução** `Microsoft.EventHubs` individualmente a cada pasta no caminho para a sua pasta de destino final. 

    ![Screenshot da secção De permissões de atribuição com a opção 'Eliminar permissões' chamada. A secção 'Permissões Selecionar' está ao lado com a opção Executar, Adicionar à opção e Adicionar como opção chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Atribuir permissões para a raiz gen1 de armazenamento de data lake")

    Clique em **OK**.

1. Atribua permissões para a pasta na conta Desagéritão de Armazenamento de Dados Gen1 onde pretende capturar dados.

    a. Clique em **Data Explorer**, selecione a pasta na conta Desebônio de Armazenamento de Dados Gen1 e, em seguida, clique em **Access**.

    ![Screenshot do explorador de dados com uma pasta na conta e a opção Access chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Atribuir permissões para a pasta Gen1 de armazenamento de dados")

    b. In **Access**, clique em **Adicionar**, clique em Selecionar Utilizador **ou Grupo** e, em seguida, procure `Microsoft.EventHubs` . 

    ![Screenshot da página de Acesso ao explorador de dados com a opção Adicionar, Selecione User ou Group e a opção Microsoft Eventhubs chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a pasta Gen1 de armazenamento de dados")
    
    Clique em **Selecionar**.

    c. Sob **Permissões de Atribuição,** clique **em Selecionar Permissões**. Definir **permissões** para **ler, escrever** e **executar**. Definir **Adicione a** esta pasta e a todas as **crianças**. Por fim, desa um **ponto de fixação de** dados sobre uma entrada de **permissão de acesso e uma entrada de permissão por defeito**.

    ![Screenshot da secção De permissões de atribuição com a opção 'Eliminar permissões' chamada. A secção 'Permissões Selecionar' está ao lado com as opções de Ler, Escrever e Executar, a opção Adicionar à opção e o Add como opção chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Atribuir permissões para a pasta Gen1 de armazenamento de dados")
    
    Clique em **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configure os Centros de Eventos para capturar dados para data lake storage gen1

Nesta secção, você cria um Centro de Eventos dentro de um espaço de nomes de Event Hubs. Você também configura o Centro de Eventos para capturar dados para uma conta Azure Data Lake Storage Gen1. Esta secção pressupõe que já criou um espaço de nomes de Event Hubs.

1. A partir do painel de **visão** geral do espaço de nomes dos Centros de Eventos, clique **+ Centro de Eventos**.

    ![Screenshot do painel de visão geral com a opção Event Hub chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Criar Hub de Eventos")

1. Forneça os seguintes valores para configurar os Centros de Eventos para capturar dados para data lake storage gen1.

    ![Screenshot da caixa de diálogo Create Event Hub com a caixa de texto Name, a opção Captura, a opção Fornecedor de Captura, a opção Select Data Lake Store e a opção Data Lake Path chamada.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Criar Hub de Eventos")

    a. Forneça um nome para o Centro de Eventos.
    
    b. Para este tutorial, desajei a **contagem de partição** e **a retenção de mensagens** para os valores predefinidos.
    
    c. Definir **captura** para **on**. Desa ajuste a **janela de tempo** (com que frequência capturar) e Janela de **Tamanho** (tamanho de dados para capturar). 
    
    d. Para **Obter Provedor** de Captura , selecione **Azure Data Lake Store** e, em seguida, selecione a conta de Data Lake Storage Gen1 que criou anteriormente. Para **Data Lake Path,** insira o nome da pasta que criou na conta Desema de Armazenamento de Data Lake Gen1. Só precisa de fornecer o caminho relativo para a pasta.

    e. Deixe os **formatos de nome do ficheiro de captura de amostra** para o valor predefinido. Esta opção rege a estrutura da pasta que é criada sob a pasta de captura.

    f. Clique em **Criar**.

## <a name="test-the-setup"></a>Teste a configuração

Agora pode testar a solução enviando dados para o Azure Event Hub. Siga as instruções no [Enviar eventos para Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Assim que começar a enviar os dados, vê os dados refletidos na Data Lake Storage Gen1 utilizando a estrutura da pasta especificada. Por exemplo, vê uma estrutura de pasta, como mostra a seguinte imagem, na sua conta Deseguito de Armazenamento de Dados Gen1.

![Amostra de dados do EventHub na Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Amostra de dados do EventHub na Data Lake Storage Gen1")

> [!NOTE]
> Mesmo que não tenha mensagens a chegar aos Centros de Eventos, o Event Hubs escreve ficheiros vazios apenas com os cabeçalhos na conta da Data Lake Storage Gen1. Os ficheiros são escritos ao mesmo tempo que forneceu enquanto criava os Centros de Eventos.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analisar dados em Data Lake Storage Gen1

Uma vez que os dados estão na Data Lake Storage Gen1, você pode executar trabalhos analíticos para processar e recolher os dados. Consulte [o USQL Avro Exemplo](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) sobre como fazê-lo usando a Azure Data Lake Analytics.
  

## <a name="see-also"></a>Ver também
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Copiar dados de Azure Storage Blobs para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
