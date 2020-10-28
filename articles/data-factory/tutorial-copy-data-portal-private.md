---
title: Utilize pontos finais privados para criar um oleoduto Azure Data Factory
description: Este tutorial disponibiliza instruções passo-a-passo para utilizar o portal do Azure para criar uma fábrica de dados com um pipeline. O pipeline utiliza a atividade de cópia para copiar dados do armazenamento Azure Blob para uma base de dados Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: c08dd1b5b2f90e874f36c6cf01c4cc5f5ae74d17
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636260"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copie os dados de forma segura do armazenamento do Azure Blob para uma base de dados SQL utilizando pontos finais privados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, vai criar uma fábrica de dados com a interface de utilizador (IU) do Azure Data Factory. *O oleoduto desta fábrica de dados copia os dados de forma segura do armazenamento do Azure Blob para uma base de dados Azure SQL (ambas permitindo o acesso a redes selecionadas apenas) utilizando pontos finais privados na Rede Virtual Gerida pela [Azure Data Factory](managed-virtual-network-private-endpoint.md).* O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista de lojas de dados suportadas como fontes e pias, consulte a [tabela de datas suportadas e formatos.](./copy-activity-overview.md)

> [!NOTE]
> Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](./introduction.md).

Neste tutorial, vai executar os seguintes passos:

* Criar uma fábrica de dados.
* Criar um pipeline com uma atividade de cópia.


## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure** . Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure** . Utilize o Armazenamento de blobs como um arquivo dos dados de *origem* . Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) para seguir os passos para criar uma. *Certifique-se de que a conta de armazenamento permite o acesso apenas a partir de redes selecionadas.* 
* **Base de Dados Azure SQL** . Pode utilizar a base de dados como um arquivo de dados *sink* . Se não tiver uma base de dados Azure SQL, consulte [criar uma base de dados SQL](../azure-sql/database/single-database-create-quickstart.md) para obter etapas para criar uma. *Certifique-se de que a conta SQL Database permite o acesso apenas a partir de redes selecionadas.* 

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare o seu armazenamento de bolhas e base de dados SQL para o tutorial realizando os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Abra o bloco de notas. Copie o texto seguinte e guarde-o como um ficheiro **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um recipiente nomeado **adftutorial** no seu armazenamento de bolhas. Crie uma pasta com o nome **input** neste contentor. Em seguida, carregue o ficheiro **emp.txt** para a pasta **input** . Utilize o portal do Azure ou ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) para realizar estas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

Utilize o seguinte script SQL para criar a tabela **dbo.emp** na sua base de dados SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados.

1. Abra o Microsoft Edge ou o Google Chrome. Atualmente, apenas os navegadores Web Microsoft Edge e Google Chrome suportam o UI da Data Factory.

1. No menu esquerdo, **selecione Criar uma** Fábrica de  >  **Analytics**  >  **Dados** de Análise de Recursos .

1. Na página **Nova fábrica de dados** , em **Nome** , introduza **ADFTutorialDataFactory** .

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para a fábrica de dados (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](./naming-rules.md).

1. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.

1. Em **Grupo de Recursos** , efetue um destes passos:

    - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
    - Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
     
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 

1. Em **Versão** , selecione **V2** .

1. Em **Localização** , selecione uma localização para a fábrica de dados. Apenas os locais suportados aparecem na lista de suspensos. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que a fábrica de dados utiliza podem estar noutras regiões.

1. Selecione **Criar** .

1. Depois de concluída a criação, vê o aviso no centro de Notificações. Selecione **Ir para o recurso** para ir à página data **Factory.**

1. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Criar um tempo de integração Azure na Rede Virtual Gerida pela Data Factory
Neste passo, cria-se um tempo de integração Azure e permite a Rede Virtual Gerida pela Data Factory.

1. No portal Data Factory, vá a **Manage** e selecione **New** para criar um novo tempo de integração do Azure.

   ![Screenshot que mostra a criação de um novo tempo de integração Azure.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Opte por criar um tempo de integração **Azure.**

   ![Screenshot que mostra um novo tempo de integração Azure.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. Na **configuração da rede virtual (Pré-visualização)** , selecione **Enable** .

   ![Screenshot que mostra permitir um novo tempo de integração Azure.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Selecione **Criar** .

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma atividade de cópia na fábrica de dados. A atividade de cópia copia os dados do Armazenamento de blobs para a Base de Dados SQL. No [Tutorial de início rápido](./quickstart-create-data-factory-portal.md), seguiu os passos abaixo para criar um pipeline:

1. Criar o serviço ligado.
1. Criar os conjuntos de dados de entrada e saída.
1. Criar um pipeline.

Neste tutorial, começa-se por criar um oleoduto. Em seguida, vai criar serviços ligados e conjuntos de dados quando forem necessários para configurar o pipeline.

1. Na página **Vamos começar** , selecione **Criar pipeline** .

   ![Screenshot que mostra a criação de um oleoduto.](./media/doc-common-process/get-started-page.png)
1. No painel de propriedades para o oleoduto, **insira copyPipeline** para o nome do gasoduto.

1. Na caixa de **ferramentas Atividades,** expanda a categoria **Move and Transform** e arraste a atividade de **dados copy** da caixa de ferramentas para a superfície do designer de gasodutos. Introduza **copyFromBlobToSql** para obter o nome.

    ![Screenshot que mostra a atividade da cópia.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Configure uma fonte

>[!TIP]
>Neste tutorial, utiliza a **chave Conta** como tipo de autenticação para a sua loja de dados de origem. Pode também escolher outros métodos de autenticação suportados, como **SAS URI,****Service Principal** e **Managed Identity,** se necessário. Para obter mais informações, consulte as secções correspondentes em [Copy e transforme os dados no armazenamento da Azure Blob utilizando a Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Para armazenar segredos para lojas de dados de forma segura, recomendamos também que utilize o Cofre da Chave Azure. Para obter mais informações e ilustrações, consulte [as credenciais da Loja no Cofre da Chave Azure.](./store-credentials-in-key-vault.md)

#### <a name="create-a-source-dataset-and-linked-service"></a>Criar um conjunto de dados de origem e serviço ligado

1. Vá ao separador **Fonte.** Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **new dataset,** selecione **Azure Blob Storage** e, em seguida, selecione **Continue** . A origem de dados está num Armazenamento de blobs, pelo que vai selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Select Format,** selecione o tipo de formato dos seus dados e, em seguida, selecione **Continue** .

1. Na caixa de diálogo **set Properties,** insira **o SourceBlobDataset** para **nome** . Selecione a caixa de verificação para **primeira linha como cabeçalho** . Na caixa de texto **de serviço Linked,** selecione **+ Novo** .

1. Na caixa de diálogo do **novo serviço ligado (Azure Blob Storage),** insira **o AzureStorageLinkedService** como **Nome** e selecione a sua conta de armazenamento na lista de nomes da conta **de armazenamento.** 

1. Certifique-se de que ativa **a autoria interativa.** Pode levar cerca de um minuto para ser ativado.

    ![Screenshot que mostra a autoria interativa.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selecione **Testar ligação** . Deverá falhar quando a conta de armazenamento permite o acesso apenas a partir de **redes Selecionadas** e exige que a Data Factory crie um ponto final privado que deve ser aprovado antes da sua utilização. Na mensagem de erro, deverá ver um link para criar um ponto final privado que pode seguir para criar um ponto final privado gerido. Uma alternativa é ir diretamente ao **separador Gerir** e seguir as instruções na [secção seguinte](#create-a-managed-private-endpoint) para criar um ponto final privado gerido.

   > [!NOTE]
   > O **separador Manage** pode não estar disponível para todas as instâncias da fábrica de dados. Se não o vir, pode aceder a pontos finais privados selecionando **o Ponto**  >  De Terminante Privado de **Ligações**  >  **de** Autor .
1. Mantenha a caixa de diálogo aberta e, em seguida, vá para a sua conta de armazenamento.

1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-a-storage-account) para aprovar o link privado.

1. Volta para a caixa de diálogo. Selecione a **ligação de teste** novamente e selecione **Criar** para implementar o serviço ligado.

1. Após a criação do serviço ligado, volta para a página **De Propriedades Definidas.** Junto a **Caminho do ficheiro** , selecione **Procurar** .

1. Vá à pasta **adftutorial/entrada,** selecione o ficheiro **emp.txt** e, em seguida, selecione **OK** .

1. Selecione **OK** . Vai automaticamente para a página do pipeline. No **separador 'Fonte',** confirme se **o SourceBlobDataset** está selecionado. Para pré-visualizar os dados nesta página, selecione **Pré-visualizar dados** .

    ![Screenshot que mostra o conjunto de dados de origem.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Se não selecionou a hiperligação quando testou a ligação, siga o caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado que criou.

1. Vá ao **separador Gerir.**

   > [!NOTE]
   > O **separador Manage** pode não estar disponível para todas as instâncias da Data Factory. Se não o vir, pode aceder a pontos finais privados selecionando **o Ponto**  >  De Terminante Privado de **Ligações**  >  **de** Autor .

1. Aceda à secção **de pontos finais privados geridos.**

1. Selecione **+ Novos** **pontos finais privados geridos** .

    ![Screenshot que mostra o botão novo dos pontos finais privados geridos.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo **de armazenamento Azure Blob** da lista e selecione **Continue** .

1. Insira o nome da conta de armazenamento que criou.

1. Selecione **Criar** .

1. Após alguns segundos, deve ver que o link privado criado precisa de uma aprovação.

1. Selecione o ponto final privado que criou. Pode ver uma hiperligação que o levará a aprovar o ponto final privado ao nível da conta de armazenamento.

    ![Screenshot que mostra o painel de pontos finais privados gerido.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Aprovação de um link privado numa conta de armazenamento
1. Na conta de armazenamento, aceda às **ligações de ponto final privados** na secção **Definições.**

1. Selecione a caixa de verificação para o ponto final privado que criou e **selecione Aprovar** .

    ![Screenshot que mostra o botão Aprovar para o ponto final privado.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adicione uma descrição e selecione **sim** .
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Data Factory.
1. Após cerca de um ou dois minutos, deverá ver a aprovação do seu ponto final privado aparecer na UI da Data Factory.


### <a name="configure-a-sink"></a>Configure uma pia
>[!TIP]
>Neste tutorial, utiliza a **autenticação SQL** como tipo de autenticação para a sua loja de dados de pia. Também pode escolher outros métodos de autenticação suportados, como **o Principal de Serviço** e a Identidade **Gerida,** se necessário. Para obter mais informações, consulte as secções correspondentes em [Copiar e transforme os dados na Base de Dados Azure SQL utilizando a Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Para armazenar segredos para lojas de dados de forma segura, recomendamos também que utilize o Cofre da Chave Azure. Para obter mais informações e ilustrações, consulte [as credenciais da Loja no Cofre da Chave Azure.](./store-credentials-in-key-vault.md)

#### <a name="create-a-sink-dataset-and-linked-service"></a>Criar um conjunto de dados de lavatório e serviço ligado
1. Vá para o separador **Sink** e selecione **+ Novo** para criar um conjunto de dados sink.

1. Na caixa de diálogo **New Dataset,** introduza **o SQL** na caixa de pesquisa para filtrar os conectores. Selecione **Azure SQL Database** e, em seguida, selecione **Continue** . Neste tutorial, vai copiar dados para uma base de dados SQL.

1. Na caixa de diálogo **set Properties,** **introduza o Número de Dados de Resultados** para **o nome** . A partir da lista de drop-down do **serviço Linked,** selecione **+ Novo** . Os conjuntos de dados têm de estar associados a um serviço ligado. O serviço ligado tem a cadeia de ligação que o Data Factory utiliza para ligar à base de dados SQL no runtime. O conjunto de dados especifica o contentor, a pasta e o ficheiro (opcional) para os quais os dados são copiados.

1. Na caixa de diálogo de **serviço novo (Azure SQL Database),** tome os seguintes passos:

    1. Em **Name** , introduza **AzureSqlDatabaseLinkedService** .
    1. Em **Nome do servidor** , selecione a sua instância do SQL Server.
    1. Certifique-se de que ativa **a autoria interativa.**
    1. Em **Nome da base de dados** , selecione a sua base de dados SQL.
    1. Em **Nome de utilizador** , introduza o nome do utilizador.
    1. Em **Palavra-passe** , introduza a palavra-passe do utilizador.
    1. Selecione **Testar ligação** . Deve falhar porque o servidor SQL permite o acesso apenas a partir de **redes Selecionadas** e exige que a Data Factory crie um ponto final privado para o mesmo, que deve ser aprovado antes da sua utilização. Na mensagem de erro, deverá ver um link para criar um ponto final privado que pode seguir para criar um ponto final privado gerido. Uma alternativa é ir diretamente ao **separador Gerir** e seguir as instruções na secção seguinte para criar um ponto final privado gerido.
    1. Mantenha a caixa de diálogo aberta e, em seguida, vá para o servidor SQL selecionado.
    1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-sql-server) para aprovar o link privado.
    1. Volta para a caixa de diálogo. Selecione a **ligação de teste** novamente e selecione **Criar** para implementar o serviço ligado.

1. Vai automaticamente para a caixa de diálogo **set Properties.** Em **Tabela** , selecione **[dbo].[emp]** . Em seguida, selecione **OK** .

1. Vá ao separador com o pipeline e no **conjunto de dados do Sink,** confirme que **o OutputSqlDataset** está selecionado.

    ![Screenshot que mostra o separador Pipeline.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Pode mapear opcionalmente o esquema da fonte para o esquema correspondente do destino, seguindo o [mapeamento de Schema na atividade de cópia.](./copy-activity-schema-and-type-mapping.md)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Se não selecionou a hiperligação quando testou a ligação, siga o caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado que criou.

1. Vá ao **separador Gerir.**
1. Aceda à secção **de pontos finais privados geridos.**
1. Selecione **+ Novos** **pontos finais privados geridos** .

    ![Screenshot que mostra o botão novo dos pontos finais privados geridos.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo da **Base de Dados Azure SQL** da lista e selecione **Continue** .
1. Insira o nome do servidor SQL selecionado.
1. Selecione **Criar** .
1. Após alguns segundos, deve ver que o link privado criado precisa de uma aprovação.
1. Selecione o ponto final privado que criou. Pode ver uma hiperligação que o levará a aprovar o ponto final privado ao nível do servidor SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Aprovação de um link privado no SQL Server
1. No servidor SQL, aceda às **ligações de ponto final privados** na secção **Definições.**
1. Selecione a caixa de verificação para o ponto final privado que criou e **selecione Aprovar** .
1. Adicione uma descrição e selecione **sim** .
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Data Factory.
1. Deve levar um ou dois minutos para que a aprovação apareça para o seu ponto final privado.

#### <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline

Pode depurar um pipeline antes de publicar artefactos (serviços ligados, conjuntos de dados e pipeline) no Data Factory ou no seu próprio repositório Git do Azure.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída** , na parte inferior da janela.
1. Depois de o gasoduto poder funcionar com sucesso, na barra de ferramentas superior, **selecione Publicar tudo** . Esta ação publica entidades (conjuntos de dados e oleodutos) que criou para a Data Factory.
1. Aguarde até ver a mensagem **Publicação com êxito** . Para ver mensagens de notificação, selecione **'Mostrar Notificações'** no canto superior direito (botão de campainha).


#### <a name="summary"></a>Resumo
O pipeline desta amostra copia dados do armazenamento blob para a Base de Dados SQL utilizando pontos finais privados na Rede Virtual Gerida pela Data Factory. Aprendeu a:

* Criar uma fábrica de dados.
* Criar um pipeline com uma atividade de cópia.