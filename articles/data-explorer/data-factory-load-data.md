---
title: Copiar dados de Azure Data Factory para o Azure Data Explorer
description: Neste artigo, você aprenderá a ingerir (carregar) dados no Azure Data Explorer usando a ferramenta de cópia Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300588"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados para o Azure Data Explorer usando Azure Data Factory 

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado. Ele oferece análise em tempo real sobre grandes volumes de dados que transmitem de várias fontes, como aplicativos, sites e dispositivos IoT. Com o Azure Data Explorer, você pode explorar de forma iterativa os dados e identificar padrões e anomalias para melhorar os produtos, aprimorar as experiências do cliente, monitorar dispositivos e impulsionar as operações. Ele ajuda você a explorar novas perguntas e obter respostas em minutos. 

Azure Data Factory é um serviço de integração de dados baseado em nuvem e totalmente gerenciado. Você pode usá-lo para preencher o banco de dados do Data Explorer do Azure com o seu sistema existente. Ele pode ajudá-lo a economizar tempo quando estiver criando soluções de análise.

Quando você carrega dados no Azure Data Explorer, o Data Factory oferece os seguintes benefícios:

* **Instalação fácil**: Obtenha um assistente intuitivo de cinco etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: Obtenha suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [armazenamentos de dados com suporte](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Seguro e em conformidade**: Os dados são transferidos por HTTPS ou Azure ExpressRoute. A presença do serviço global garante que seus dados nunca saiam do limite geográfico.
* **Alto desempenho**: A velocidade de carregamento de dados é de até 1 gigabyte por segundo (GBps) para o Azure Data Explorer. Para obter mais informações, consulte [desempenho da atividade de cópia](/azure/data-factory/copy-activity-performance).

Neste artigo, você usa a ferramenta Data Factory Copiar Dados para carregar dados do Amazon S3 (Simple Storage Service) no Azure Data Explorer. Você pode seguir um processo semelhante para copiar dados de outros armazenamentos de dados, como:
* [Armazenamento de Blobs do Azure](/azure/data-factory/connector-azure-blob-storage)
* [Base de Dados SQL do Azure](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Sistema de Ficheiros](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md).
* Uma fonte de dados.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso** > **análise** > **Data Factory**.

   ![Criar um data factory no portal do Azure](media/data-factory-load-data/create-adf.png)

1. No painel **novo data Factory** , forneça valores para os campos na tabela a seguir:

   ![O painel "New data factory"](media/data-factory-load-data/my-new-data-factory.png)  

   | Definição  | Valor a ser inserido  |
   |---|---|
   | **Name** | Na caixa, insira um nome globalmente exclusivo para sua data factory. Se você receber um erro, o *nome \"do data Factory LoadADXDemo\" não estará disponível*, insira um nome diferente para o data Factory. Para obter regras sobre como nomear artefatos Data Factory, consulte [Data Factory regras de nomenclatura](/azure/data-factory/naming-rules).|
   | **Subscrição** | Na lista suspensa, selecione a assinatura do Azure na qual criar o data factory. |
   | **Grupo de Recursos** | Selecione **criar novo**e digite o nome de um novo grupo de recursos. Se você já tiver um grupo de recursos, selecione **usar existente**. |
   | **Versão** | Na lista suspensa, selecione **v2**. |  
   | **Location** | Na lista suspensa, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista. Os armazenamentos de dados que são usados pelo data factory podem existir em outros locais ou regiões. |

1. Selecione **Criar**.

1. Para monitorar o processo de criação, selecione **notificações** na barra de ferramentas. Depois de criar o data factory, selecione-o.
   
   O painel de **Data Factory** é aberto.

   ![O painel de Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Para abrir o aplicativo em um painel separado, selecione o bloco **criar & monitor** .

## <a name="load-data-into-azure-data-explorer"></a>Carregar dados no Azure Data Explorer

Você pode carregar dados de vários tipos de [armazenamentos de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) no Azure data Explorer. Este artigo discute como carregar dados do Amazon S3.

Você pode carregar seus dados de uma das seguintes maneiras:

* Na interface do usuário do Azure Data Factory, no painel esquerdo, selecione o ícone **autor** , conforme mostrado na seção "criar uma data Factory" de [criar um data Factory usando a interface do usuário do Azure data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* Na ferramenta de Copiar Dados de Azure Data Factory, conforme mostrado em [usar a ferramenta de copiar dados para copiar dados](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados do Amazon S3 (origem)

1. No painel **vamos** começar, abra a ferramenta copiar dados selecionando **copiar dados**.

   ![O botão da ferramenta de Copiar Dados](media/data-factory-load-data/copy-data-tool-tile.png)

1. No painel **Propriedades** , na caixa **nome da tarefa** , insira um nome e, em seguida, selecione **Avançar**.

    ![O painel Propriedades de Copiar Dados](media/data-factory-load-data/copy-from-source.png)

1. No painel **armazenamento de dados de origem** , selecione **criar nova conexão**.

    ![O painel "armazenamento de dados de origem" Copiar Dados](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**e, em seguida, selecione **continuar**.

    ![O painel novo serviço vinculado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. No painel **novo serviço vinculado (Amazon S3)** , faça o seguinte:

    ![Especificar o serviço vinculado do Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Na caixa **nome** , digite o nome do seu novo serviço vinculado.

    b. Na lista suspensa **conectar via Integration Runtime** , selecione o valor.

    c. Na caixa **ID de chave de acesso** , insira o valor.
    
    > [!NOTE]
    > No Amazon S3, para localizar sua chave de acesso, selecione o nome de usuário do Amazon na barra de navegação e selecione **minhas credenciais de segurança**.
    
    d. Na caixa **chave de acesso de segredo** , insira um valor.

    e. Para testar a conexão de serviço vinculado que você criou, selecione **testar conexão**.

    f. Selecione **Concluir**.
    
      O painel **repositório de dados de origem** exibe a nova conexão AmazonS31. 

1. Selecione **Seguinte**.

   ![Conexão de armazenamento de dados de origem criada](media/data-factory-load-data/source-data-store-created-connection.png)

1. No painel **escolher o arquivo de entrada ou a pasta** , faça o seguinte:

    a. Navegue até o arquivo ou a pasta que você deseja copiar e, em seguida, selecione-o.

    b. Selecione o comportamento de cópia desejado. Certifique-se de que a caixa de seleção **cópia binária** esteja desmarcada.

    c. Selecione **Seguinte**.

    ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. No painel **configurações de formato de arquivo** , selecione as configurações relevantes para o arquivo. e, em seguida, selecione **Avançar**.

   ![O painel "configurações de formato de arquivo"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados para o Azure Data Explorer (destino)

O novo serviço vinculado do Azure Data Explorer é criado para copiar os dados para a tabela de destino Data Explorer do Azure (coletor) especificada nesta seção.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Criar o serviço vinculado do Data Explorer do Azure

Para criar o serviço vinculado do Azure Data Explorer, faça o seguinte:

1. Para usar uma conexão de repositório de dados existente ou especificar um novo armazenamento de dados, no painel **armazenamento de dados de destino** , selecione **criar nova conexão**.

    ![Painel armazenamento de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. No painel **novo serviço vinculado** , selecione **Azure data Explorer**e, em seguida, selecione **continuar**.

    ![O painel novo serviço vinculado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. No painel **novo serviço vinculado (data Explorer do Azure)** , faça o seguinte:

    ![O painel Azure Data Explorer novo serviço vinculado](media/data-factory-load-data/adx-new-linked-service.png)

   a. Na caixa **nome** , insira um nome para o serviço vinculado do Azure data Explorer.

   b. Em **método de seleção de conta**, siga um destes procedimentos: 

    * Selecione **da assinatura do Azure** e, em seguida, nas listas suspensas, selecione sua **assinatura do Azure** e seu **cluster**. 

        > [!NOTE]
        > O controle suspenso **cluster** lista somente os clusters associados à sua assinatura.

    * Selecione **inserir manualmente**e, em seguida, insira seu **ponto de extremidade**.

   c. Na caixa **locatário** , insira o nome do locatário.

   d. Na caixa **ID da entidade de serviço** , insira a ID da entidade de serviço.

   e. Selecione **chave da entidade de serviço** e, em seguida, na caixa **chave da entidade de serviço** , insira o valor para a chave.

   f. Na lista suspensa **banco de dados** , selecione o nome do banco de dados. Como alternativa, marque a caixa de seleção **Editar** e, em seguida, insira o nome do banco de dados.

   g. Para testar a conexão de serviço vinculado que você criou, selecione **testar conexão**. Se você puder se conectar ao seu serviço vinculado, o painel exibirá uma marca de seleção verde e uma mensagem de **conexão bem-sucedida** .

   h. Selecione **concluir** para concluir a criação do serviço vinculado.

    > [!NOTE]
    > A entidade de serviço é usada pelo Azure Data Factory para acessar o serviço de Data Explorer do Azure. Para criar uma entidade de serviço, vá para [criar uma entidade de serviço Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Não use o método Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurar a conexão de dados do Azure Data Explorer

Depois de criar a conexão de serviço vinculado, o painel **armazenamento de dados de destino** será aberto e a conexão que você criou estará disponível para uso. Para configurar a conexão, faça o seguinte:

1. Selecione **Seguinte**.

    ![O painel "armazenamento de dados de destino" do Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. No painel **mapeamento de tabela** , defina o nome da tabela de destino e, em seguida, selecione **Avançar**.

    ![O painel "mapeamento de tabela" do conjunto de tabelas de destino](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. No painel de **mapeamento de coluna** , ocorrem os seguintes mapeamentos:

    a. O primeiro mapeamento é executado por Azure Data Factory de acordo com o [mapeamento de esquema de Azure data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Faça o seguinte:

    * Defina os **mapeamentos de coluna** para a tabela de destino Azure data Factory. O mapeamento padrão é exibido da origem para a tabela de destino Azure Data Factory.

    * Cancele a seleção das colunas que você não precisa para definir o mapeamento de coluna.

    b. O segundo mapeamento ocorre quando esses dados de tabela são ingeridos no Azure Data Explorer. O mapeamento é executado de acordo com [as regras de mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping). Mesmo que os dados de origem não estejam no formato CSV, Azure Data Factory converte os dados em um formato tabular. Portanto, o mapeamento de CSV é o único mapeamento relevante neste estágio. Faça o seguinte:

    * Adicional Em **Propriedades do coletor do data Explorer do Azure (Kusto)** , adicione o **nome do mapeamento de ingestão** relevante para que o mapeamento de coluna possa ser usado.

    * Se o **nome do mapeamento de ingestão** não for especificado, a ordem de mapeamento *por nome* definida na seção **mapeamentos de coluna** será usada. Se o mapeamento *por nome* falhar, o Azure data Explorer tentará ingerir os dados em uma ordem de *posição por coluna* (ou seja, ele é mapeado por posição como o padrão).

    * Selecione **Seguinte**.

    ![O painel "mapeamento de coluna" do conjunto de associação de destino](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. No painel **configurações** , faça o seguinte:

    a. Em **configurações de tolerância a falhas**, insira as configurações relevantes.

    b. Em **configurações de desempenho**, **habilitar preparo** não se aplica e **as configurações avançadas** incluem considerações de custo. Se você não tiver requisitos específicos, deixe essas configurações como estão.

    c. Selecione **Seguinte**.

    ![O painel "configurações" de cópia de dados](media/data-factory-load-data/copy-data-settings.png)

1. No painel **Resumo** , examine as configurações e, em seguida, selecione **Avançar**.

    ![O painel "Resumo" de cópia de dados](media/data-factory-load-data/copy-data-summary.png)

1. No painel **implantação concluída** , faça o seguinte:

    a. Para alternar para a guia **Monitor** e exibir o status do pipeline (ou seja, o progresso, os erros e o fluxo de dados), selecione **Monitor**.

    b. Para editar serviços vinculados, conjuntos de itens e pipelines, selecione **Editar pipeline**.

    c. Selecione **concluir** para concluir a tarefa de cópia de dados.

    ![O painel "implantação concluída"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [conector de data Explorer do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure data Factory.
* Saiba mais sobre como editar serviços vinculados, conjuntos de informações e pipelines na [interface do usuário do data Factory](/azure/data-factory/quickstart-create-data-factory-portal).
* Saiba mais sobre as [consultas do Azure data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.
