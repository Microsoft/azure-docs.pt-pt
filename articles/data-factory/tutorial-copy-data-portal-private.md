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
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537654"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copie os dados de forma segura do armazenamento do Azure Blob para uma base de dados SQL utilizando pontos finais privados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, vai criar uma fábrica de dados com a interface de utilizador (IU) do Azure Data Factory. **O oleoduto desta fábrica de dados copia os dados de forma segura do armazenamento do Azure Blob para uma base de dados Azure SQL (ambas permitindo o acesso a apenas redes selecionadas) utilizando pontos finais privados na [Azure Data Factory gerida rede virtual.](managed-virtual-network-private-endpoint.md)** O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista dos arquivos de dados suportados como origens e sinks, consulte a tabela de [arquivos de dados suportados](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Neste tutorial, vai executar os seguintes passos:

> * Criar uma fábrica de dados
> * Criar um oleoduto com uma atividade de cópia


## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utilize o Armazenamento de blobs como um arquivo dos dados de *origem*. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) para seguir os passos para criar uma. **Certifique-se de que a conta de Armazenamento permite o acesso apenas a partir de 'Redes Selecionadas'.** 
* **Base de Dados Azure SQL**. Pode utilizar a base de dados como um arquivo de dados *sink*. Se não tiver uma base de dados Azure SQL, consulte [criar uma base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) para obter etapas para criar uma. **Certifique-se de que a conta Azure SQL Database permite o acesso apenas a partir de 'Redes Selecionadas'.** 

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare o Armazenamento de blobs e a Base de Dados SQL para o tutorial, ao efetuar os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como um ficheiro **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um contentor com o nome **adftutorial** no Armazenamento de blobs. Crie uma pasta com o nome **input** neste contentor. Em seguida, carregue o ficheiro **emp.txt** para a pasta **input**. Utilize o portal do Azure ou ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) para realizar estas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

1. Utilize o seguinte script SQL para criar a tabela **dbo.emp** na sua base de dados SQL:

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

1. Permita que os serviços do Azure acedam ao SQL Server. Certifique-se de que **Permitir acesso aos serviços do Azure** está **ATIVADO** para o SQL Server, para que o Data Factory possa escrever dados no SQL Server. Para verificar e ligar esta definição, vá ao servidor Azure SQL > visão geral > Definir firewall do servidor> definir a opção **de acesso a serviços Azure** para **ON**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.


2. No menu esquerdo, **selecione Criar uma**Fábrica de  >  **Analytics**  >  **Dados**de Análise de Recursos .

3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](https://docs.microsoft.com/azure/data-factory/naming-rules).

4. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.

5. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. Em **Versão**, selecione **V2**.

7. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que a fábrica de dados utiliza podem estar noutras regiões.


8. Selecione **Criar**.


9. Depois de concluída a criação, vê o aviso no Centro de Notificações. Selecione **Ir para o recurso** para navegar para a página de fábrica de dados.

10. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Criar um tempo de execução de integração Azure na Rede Virtual Gerida ADF
Neste passo, cria-se um Tempo de Execução de Integração Azure e ativar a Rede Virtual Gerida.

1. No portal ADF, vá ao **Manage Hub** e clique em **New** para criar um novo Tempo de Execução de Integração Azure.
   ![Criar novo tempo de execução de integração Azure](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Opte por criar um Tempo de Integração Azure**.
   ![Novo tempo de integração da Azure](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Ativar **a Rede Virtual**.
   ![Novo tempo de integração da Azure](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selecione **Criar**.

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma atividade de cópia na fábrica de dados. A atividade de cópia copia os dados do Armazenamento de blobs para a Base de Dados SQL. No [Tutorial de início rápido](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal), seguiu os passos abaixo para criar um pipeline:

1. Criar o serviço ligado.
1. Criar os conjuntos de dados de entrada e saída.
1. Criar um pipeline.

Neste tutorial, vai começar pela criação do pipeline. Em seguida, vai criar serviços ligados e conjuntos de dados quando forem necessários para configurar o pipeline.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)
1. No painel **de propriedades** para o gasoduto, introduza **copyPipeline** para **nome** do oleoduto.

1. Na caixa de **ferramentas Atividades,** expanda a categoria **Move and Transform** e arraste e deixe cair a atividade de **Dados** de Cópia da caixa de ferramentas para a superfície do designer de gasodutos. Especifique **CopyFromBlobToSql** em **Nome**.

    ![Atividade Copiar](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar origem

>[!TIP]
>Neste tutorial, utiliza a *tecla Conta* como tipo de autenticação para a sua loja de dados de origem, mas pode escolher outros métodos de autenticação suportados: *SAS URI,**Diretor de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) para obter mais detalhes.
>Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre de Chaves Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

#### <a name="create-source-dataset-and-linked-service"></a>Criar conjunto de dados de origem e serviço ligado

1. Ir ao separador **'Fonte.Selecione** **+ Novo** para criar um conjunto de dados de origem.'

1. Na caixa de diálogo **new dataset,** selecione **Azure Blob Storage**e, em seguida, selecione **Continue**. A origem de dados está num Armazenamento de blobs, pelo que vai selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Select Format,** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continue**.

1. Na caixa de diálogo **set Properties,** insira **o SourceBlobDataset** para nome. Selecione a caixa de verificação para **primeira linha como cabeçalho**. Na caixa de texto **de serviço Linked,** selecione **+ Novo**.

1. Na caixa de diálogo **New Linked Service (Azure Blob Storage),** insira **o AzureStorageLinkedService** como nome, selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** 

1. Certifique-se de que ativa **a Autoria Interativa.** Pode levar cerca de 1 minuto para ser ativado.

    ![Autoria interativa](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selecione **a ligação de teste**, deve falhar quando a Conta de Armazenamento permite o acesso apenas a partir de 'Rede Seleccionada' e exige que a Azure Data Factory crie um ponto final privado para o mesmo que deve ser aprovado antes da sua utilização. Na mensagem de erro, deverá ver um link para criar um **ponto final privado** que pode seguir para criar um ponto final privado gerido. *Uma alternativa é ir diretamente ao separador Gerir e seguir as instruções na [secção seguinte](#create-a-managed-private-endpoint) para criar um ponto final privado gerido*
> [!NOTE]
> O separador de gestão pode não estar disponível para todas as instâncias da fábrica de dados. Se não o vir, ainda pode aceder a Private Endpoints através do separador '**Author**- > '**Connections**' -- > '**Private Endpoint**'
1. Mantenha a caixa de diálogo aberta e, em seguida, vá à sua Conta de Armazenamento selecionada acima.

1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-storage-account) para aprovar o link privado.

1. Volta para a caixa de diálogo. **Volte a testar a ligação** e selecione **Criar** para implementar o serviço ligado.

1. Após a criação do serviço ligado, é navegado de volta para a página **de propriedades set.** Junto a **Caminho do ficheiro**, selecione **Procurar**.

1. Navegue para a pasta **adftutorial/entrada,** selecione o ficheiro **emp.txt** e, em seguida, selecione **OK**.

1. Selecione **OK**. Navega automaticamente para a página do pipeline. No separador **Fonte,** confirme se **o SourceBlobDataset** está selecionado. Para pré-visualizar os dados nesta página, selecione **Pré-visualizar dados**.

    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Caso não tenha clicando na hiperligação ao testar a ligação acima, siga o seguinte caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado acima.

1. Vá ao separador Gerir.
> [!NOTE]
> O separador de gestão pode não estar disponível para todas as instâncias da fábrica de dados. Se não o vir, ainda pode aceder a Private Endpoints através do separador '**Author**- > '**Connections**' -- > '**Private Endpoint**'

1. Aceda à secção de pontos finais privados geridos.

1. Selecione **+ Novos** em pontos finais privados geridos.

    ![Novo ponto final privado gerido](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo de armazenamento Azure Blob da lista e selecione **Continue**.

1. Insira o nome da Conta de Armazenamento que criou acima.

1. Selecione **Criar**.

1. Você deve ver depois de esperar alguns segundos que o link privado criado precisa de uma aprovação.

1. Selecione o Ponto Final Privado que criou acima. Pode ver uma hiperligação que o levará a aprovar o Ponto Final Privado ao nível da Conta de Armazenamento.

    ![Gerir o ponto final privado](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Aprovação de um link privado na conta de armazenamento
1. Na Conta de Armazenamento, aceda a **ligações de ponto final privado na** secção Definições.

1. Marque o ponto final privado que criou acima e selecione **Aprovar**.

    ![Aprovar ponto final privado](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adicione uma descrição e clique **em sim**
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Azure Data Factory.
1. Deve levar cerca de 1 a 2 minutos para obter a aprovação para o seu ponto final privado refletir na UI da Fábrica de Dados Azure.


### <a name="configure-sink"></a>Configurar sink
>[!TIP]
>Neste tutorial, utiliza *a autenticação SQL* como tipo de autenticação para a sua loja de dados de lavatórios, mas pode escolher outros métodos de autenticação suportados: *Principal de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) para obter mais detalhes.
>Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre de Chaves Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para obter ilustrações detalhadas.

#### <a name="create-sink-dataset-and-linked-service"></a>Criar conjunto de dados de pia e serviço ligado
1. Vá para o separador **Sink** e selecione **+ Novo** para criar um conjunto de dados sink.

1. Na caixa de diálogo **New Dataset,** insira "SQL" na caixa de pesquisa para filtrar os conectores, selecione **Azure SQL Database**e, em seguida, selecione **Continue**. Neste tutorial, vai copiar dados para uma base de dados SQL.

1. Na caixa de diálogo **set Properties,** **introduza o Número de Dados de Resultados** para nome. A partir da lista de dropdown **de serviço Linked,** selecione **+ Novo**. Os conjuntos de dados têm de estar associados a um serviço ligado. O serviço ligado tem a cadeia de ligação que o Data Factory utiliza para ligar à base de dados SQL no runtime. O conjunto de dados especifica o contentor, a pasta e o ficheiro (opcional) para os quais os dados são copiados.

1. Na caixa de diálogo **New Linked Service (Azure SQL Database),** tome os seguintes passos:

    1. Em **Name**, introduza **AzureSqlDatabaseLinkedService**.
    1. Em **Nome do servidor**, selecione a sua instância do SQL Server.
    1. Certifique-se de que ativa **a Autoria Interativa.**
    1. Em **Nome da base de dados**, selecione a sua base de dados SQL.
    1. Em **Nome de utilizador**, introduza o nome do utilizador.
    1. Em **Palavra-passe**, introduza a palavra-passe do utilizador.
    1. Selecione **Testar ligação**. Deve falhar porque o servidor SQL permite o acesso apenas a partir de "redes selecionadas" e exige que a Azure Data Factory crie um Ponto Final Privado para o mesmo, que deve ser aprovado antes da sua utilização. Na mensagem de erro, deverá ver um link para criar um **ponto final privado** que pode seguir para criar um ponto final privado gerido. *Uma alternativa é ir diretamente ao separador Gerir e seguir as instruções na secção seguinte para criar um ponto final privado gerido*
    1. Mantenha a caixa de diálogo aberta e, em seguida, vá para o seu servidor SQL selecionado acima.    
    1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-sql-server) para aprovar o link privado.
    1. Volta para a caixa de diálogo. **Volte a testar a ligação** e selecione **Criar** para implementar o serviço ligado.

1. Navega automaticamente para a caixa de diálogo **set Properties.** Em **Tabela**, selecione **[dbo].[emp]**. Em seguida, selecione **OK**.

1. Vá para o separador com o pipeline e, em **Conjunto de Dados Sink**, confirme que **OutputSqlDataset** está selecionado.

    ![Separador do pipeline](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Pode mapear opcionalmente o esquema da fonte para o esquema de destino correspondente, seguindo [o mapeamento de Schema na atividade de cópia.](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Caso não tenha clicando na hiperligação ao testar a ligação acima, siga o seguinte caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado acima.

1. Vá ao separador Gerir.
1. Aceda à secção de pontos finais privados geridos.
1. Selecione **+ Novos** em pontos finais privados geridos.

    ![Novo ponto final privado gerido](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo da Base de Dados Azure SQL da lista e selecione **Continue**.
1. Insira o nome do servidor SQL que selecionou acima.
1. Selecione **Criar**.
1. Você deve ver depois de esperar alguns segundos que o link privado criado precisa de uma aprovação.
1. Selecione o Ponto Final Privado que criou acima. Pode ver uma hiperligação que o levará a aprovar o Ponto Final Privado ao nível do servidor SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Aprovação de um link privado no servidor SQL
1. No servidor SQL, aceda às **ligações de ponto final privado na** secção Definições.
1. Marque o ponto final privado que criou acima e selecione **Aprovar**.
1. Adicione uma descrição e clique **em sim**.
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Azure Data Factory.
1. Deve levar cerca de 1 a 2 minutos para que a aprovação seja refletida para o seu ponto final privado.

#### <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline

Pode depurar um pipeline antes de publicar artefactos (serviços ligados, conjuntos de dados e pipeline) no Data Factory ou no seu próprio repositório Git do Azure.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela.
2. Uma vez que o gasoduto possa funcionar com sucesso, na barra de ferramentas superior, **selecione Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.
3. Aguarde até ver a mensagem **Publicação com êxito**. Para ver as mensagens de notificação, clique nas **Notificações de Exibição** no botão superior direito (botão de campainha).


#### <a name="summary"></a>Resumo
O pipeline desta amostra copia dados do armazenamento blob para Azure SQL DB usando o ponto final privado na Rede Virtual gerida. Aprendeu a:

> * Criar uma fábrica de dados
> * Criar um oleoduto com uma atividade de cópia

