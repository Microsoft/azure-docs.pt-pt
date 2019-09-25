---
title: Copiar dados de Azure Data Factory para o Azure Data Explorer
description: Neste tópico, você aprende a ingerir (carregar) dados no Azure Data Explorer usando Azure Data Factory ferramenta de cópia
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb05df7ed97839ef80798a752565234d180f0e2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268827"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copiar dados para o Azure Data Explorer usando Azure Data Factory 

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real em grandes volumes de streaming de dados de várias fontes, como aplicativos, sites e dispositivos IoT. Explore iterativamente os dados e identifique padrões e anomalias para melhorar os produtos, melhorar as experiências do cliente, monitorar dispositivos e impulsionar as operações. Explore novas questões e obtenha respostas em poucos minutos. Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço para popular o banco de dados do Data Explorer do Azure com o seu sistema existente e economizar tempo ao criar soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure Data Explorer:

* **Fácil de configurar**: Um assistente de cinco etapas intuitivo sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: Suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [armazenamentos de dados com suporte](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Seguro e em conformidade**: Os dados são transferidos por HTTPS ou ExpressRoute. A presença do serviço global garante que seus dados nunca saiam do limite geográfico.
* **Alto desempenho**: Velocidade de carregamento de dados de até 1 GB/s no Azure Data Explorer. Para obter detalhes, consulte [desempenho da atividade de cópia](/azure/data-factory/copy-activity-performance).

Este artigo mostra como usar a ferramenta de Copiar Dados de Data Factory para carregar dados do Amazon S3 para o Azure Data Explorer. Você pode seguir etapas semelhantes para copiar dados de outros armazenamentos de dados, como o [armazenamento de BLOBs do Azure](/azure/data-factory/connector-azure-blob-storage), o Azure [SQL Database](/azure/data-factory/connector-azure-sql-database), o [SQL data warehouse do](/azure/data-factory/connector-azure-sql-data-warehouse)Google, o [BigQuery](/azure/data-factory/connector-google-bigquery), o[Oracle](/azure/data-factory/connector-oracle)e o [sistema de arquivos](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de Data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)
* Fonte de dados.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Selecione o botão **criar um recurso** (+) no canto superior esquerdo do portal > **Analytics** > **Data Factory**.

   ![Criar uma nova fábrica de dados](media/data-factory-load-data/create-adf.png)

1. Na página **novo data Factory** , forneça valores para os campos a seguir e, em seguida, selecione **criar**.

    ![Página Nova fábrica de dados](media/data-factory-load-data/my-new-data-factory.png)

    **Definição**  | **Descrição do campo**
    |---|---|
    | **Name** | Insira um nome globalmente exclusivo para sua data factory. Se você receber o erro *"o nome \"do data\" Factory LoadADXDemo não está disponível"* , insira um nome diferente para o data Factory. Para obter regras de nomenclatura de artefatos de Data Factory, consulte [Data Factory regras de nomenclatura](/azure/data-factory/naming-rules).|
    | **Subscrição** | Selecione sua assinatura do Azure na qual criar o data factory. |
    | **Grupo de Recursos** | Selecione **criar novo** e insira o nome de um novo grupo de recursos. Selecione **usar existente**se você tiver um grupo de recursos existente. |
    | **Versão** | Selecione **v2** |
    | **Location** | Selecione o local para o data factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados usados pelo data factory podem estar em outros locais ou regiões. |
    | | |

1. Selecione notificações na barra de ferramentas para monitorar o processo de criação. Após a conclusão da criação, vá para o data factory que você criou. O **Data Factory** Home Page é aberto.

   ![Home page da fábrica de dados](media/data-factory-load-data/data-factory-home-page.png)

1. Selecione o bloco **criar & monitor** para iniciar o aplicativo em uma guia separada.

## <a name="load-data-into-azure-data-explorer"></a>Carregar dados no Azure Data Explorer

Os dados podem ser carregados de vários tipos de [armazenamentos de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) no Azure data Explorer. Este tópico detalha o carregamento de dados do Amazon S3.

Há duas maneiras de carregar dados no Azure Data Explorer usando Azure Data Factory:

* Interface do usuário do Azure Data Factory – [guia **autor**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **copiar dados** ferramenta](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) usada neste artigo.

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados do Amazon S3 (origem)

1. Na página **vamos** começar, selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados.

   ![Bloco da ferramenta copiar dados](media/data-factory-load-data/copy-data-tool-tile.png)

1. Na página **Propriedades** , especifique **nome da tarefa** e selecione **Avançar**.

    ![Copiar das propriedades de origem](media/data-factory-load-data/copy-from-source.png)

1. Na página **armazenamento de dados de origem** , clique em **+ criar nova conexão**.

    ![Dados de origem criar conexão](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**e, em seguida, selecione **continuar**

    ![Novo serviço ligado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Na página **novo serviço vinculado (Amazon S3)** , execute as seguintes etapas:

    ![Especificar o serviço vinculado do Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Especifique o **nome** do seu novo serviço vinculado.
    * Selecione **conectar por meio** do valor do Integration Runtime na lista suspensa.
    * Especifique o valor da **ID de chave de acesso** .
    * Especifique o valor da **chave de acesso de segredo** .
    * Selecione **testar conexão** para testar a conexão de serviço vinculado que você criou.
    * Selecione **Concluir**.
    
    > [!NOTE]
    > No Amazon S3, selecione seu nome de usuário do Amazon na barra de navegação e selecione **minhas credenciais de segurança** para localizar sua **chave de acesso**. 

1. Na página **armazenamento de dados de origem** , você verá sua nova conexão AmazonS31. Selecione **Seguinte**.

   ![Conexão de armazenamento de dados de origem criada](media/data-factory-load-data/source-data-store-created-connection.png)

1. Na página **escolha o arquivo ou pasta de entrada** :

    1. Navegue até a pasta/arquivo que você deseja copiar. Selecione a pasta/arquivo.
    1. Selecione o comportamento de cópia conforme necessário. Mantenha a **cópia binária** desmarcada.
    1. Selecione **Seguinte**.

    ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. Na página **configurações de formatos de arquivo** , selecione as configurações relevantes para o arquivo e clique em **Avançar**.

   ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados para o Azure Data Explorer (destino)

O Azure Data Explorer novo serviço vinculado é criado para copiar os dados para a tabela de destino do Azure Data Explorer (coletor) especificada abaixo.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Criar o serviço vinculado do Data Explorer do Azure

1. Na página **armazenamento de dados de destino** , você pode usar uma conexão de repositório de dados existente ou especificar um novo armazenamento de dados clicando em **+ criar nova conexão**.

    ![Página arquivo de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. Na página **novo serviço vinculado** , selecione **Azure data Explorer**e, em seguida, selecione **continuar**

    ![Selecione Azure Data Explorer-novo serviço vinculado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Na página **novo serviço vinculado (Azure data Explorer)** , execute as seguintes etapas:

    ![ADX novo serviço vinculado](media/data-factory-load-data/adx-new-linked-service.png)

   * Selecione o **nome** do serviço vinculado do Azure data Explorer.
   * Em **método de seleção de conta**: 
        * Selecione o botão **de opção assinatura do Azure** e selecione sua conta de **assinatura do Azure** . Em seguida, selecione o **cluster**. Observe que o menu suspenso listará apenas os clusters que pertencem ao usuário.
        * Como alternativa, selecione o botão de opção **inserir manualmente** e insira seu **ponto de extremidade**.
    * Especifique o **locatário**.
    * Insira a **ID da entidade de serviço**.
    * Selecione o botão **chave da entidade de serviço** e insira a chave da entidade de **serviço**.
    * Selecione seu **banco de dados** no menu suspenso. Como alternativa, marque a caixa de seleção **Editar** e insira o nome do banco de dados.
    * Selecione **testar conexão** para testar a conexão de serviço vinculado que você criou. Se você puder se conectar à sua configuração, uma marca de seleção verde **com êxito** será exibida.
    * Selecione **concluir** para concluir a criação do serviço vinculado.

    > [!NOTE]
    > A entidade de serviço é usada pelo Azure Data Factory para acessar o serviço de Data Explorer do Azure. Para a entidade de serviço, [crie uma entidade de serviço Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Não use o método **Azure Key Vault** .

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurar a conexão de dados do Azure Data Explorer

1. O **armazenamento de dados de destino** é aberto. A conexão de dados do Azure Data Explorer que você criou está disponível para uso. Selecione **Avançar** para configurar a conexão.

    ![Armazenamento de dados de destino ADX](media/data-factory-load-data/destination-data-store.png)

1. Em **mapeamento de tabela**, defina o nome da tabela de destino e selecione **Avançar**.

    ![Mapeamento de tabela do conjunto de tabelas de destino](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Na página **mapeamento de coluna** :
    * O primeiro mapeamento é executado pelo ADF de acordo com o [mapeamento de esquema do ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Defina os **mapeamentos de coluna** para a tabela de destino Azure data Factory. O mapeamento padrão é exibido da tabela de destino de origem para ADF.
        * Desmarque as colunas que você não precisa para definir o mapeamento de coluna.
    * O segundo mapeamento ocorre quando esses dados de tabela são ingeridos no Azure Data Explorer. O mapeamento é executado de acordo com [as regras de mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping). Observe que, mesmo que os dados de origem não estavam no formato CSV, o ADF converteu os dados em um formato tabular, portanto, o mapeamento de CSV é o único mapeamento relevante neste estágio.
        * Em **Propriedades do coletor de data Explorer do Azure (Kusto)** , adicione o **nome do mapeamento de ingestão** relevante (opcional) para que o mapeamento de coluna possa ser usado.
        * Se o **nome do mapeamento de ingestão** não for especificado, a ordem de mapeamento "por nome" definida na seção **mapeamentos de coluna** ocorrerá. Se o mapeamento "por nome" falhar, o Azure Data Explorer tentará ingerir os dados em uma ordem de "posição por coluna" (mapas por posição como padrão).
    * Selecione **Seguinte**

    ![Mapeamento de coluna do conjunto de destino](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na página **Settings** (Definições):
    * Defina as **configurações de tolerância a falhas**relevantes.
    * **Configurações de desempenho**: Habilitar o preparo não é aplicável. **As configurações avançadas** incluem considerações de custo. Deixe como está se não houver necessidade específica.
    * Selecione **Seguinte**.

    ![Copiar configurações de dados](media/data-factory-load-data/copy-data-settings.png)

1. Em **Resumo**, examine as configurações e selecione **Avançar**.

    ![Copiar Resumo de dados](media/data-factory-load-data/copy-data-summary.png)

1. Na **página implantação**:
    * Selecione **Monitor** para alternar para a guia **Monitor** e ver o status do pipeline (progresso, erros e fluxo de dados).
    * Selecione **Editar pipeline** para editar serviços vinculados, conjuntos de itens e pipelines.
    * Selecione **concluir** para concluir a tarefa de cópia de dados

    ![Página de implementação](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [conector de data Explorer do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure data Factory.

* Saiba mais sobre como editar serviços vinculados, conjuntos de informações e pipelines na [interface do usuário do data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Saiba mais sobre as [consultas do Azure data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.
