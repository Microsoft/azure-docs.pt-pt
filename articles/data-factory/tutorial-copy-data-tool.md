---
title: Copiar dados utilizando a ferramenta Copiar Dados do Azure | Microsoft Docs
description: Crie uma fábrica de dados do Azure e, em seguida, utilize a ferramenta Copiar Dados para copiar dados do Armazenamento de blobs do Azure para uma base de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: 553e87cacf407da2333da3105647719679feaabf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544210"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copiar dados do Armazenamento de blobs do Azure para uma base de dados SQL com a ferramenta Copiar Dados
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-copy-data-tool.md)

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, utiliza a ferramenta Copiar Dados para criar um pipeline que copia dados do Armazenamento de blobs do Azure para uma base de dados SQL.

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Utilizar o armazenamento de BLOBs como o _origem_ arquivo de dados. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
* **Base de dados SQL do Azure**: Utilizar uma base de dados SQL como o _sink_ arquivo de dados. Se não tem uma base de dados SQL, veja as instruções apresentadas em [Criar uma base de dados SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Prepare o Armazenamento de blobs e a sua base de dados SQL para o tutorial ao executar os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o **Bloco de Notas**. Copie o seguinte texto e guarde-o num ficheiro com o nome **inputEmp.txt** no seu disco:

    ```
    John|Doe
    Jane|Doe
    ```

1. Crie um contentor com o nome **adfv2tutorial** e carregue o ficheiro inputEmp.txt para o contentor. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

1. Utilize o seguinte script SQL para criar uma tabela com o nome **dbo.emp** na sua base de dados SQL:

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

2. Permita o acesso dos serviços do Azure ao SQL Server. Verifique se a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor que tem a Base de Dados SQL em execução. Esta definição permite que o Data Factory escreva dados na instância da sua base de dados. Para verificar e ativar esta definição, aceda ao seu servidor SQL do Azure > **Segurança** > **Firewalls e redes virtuais** > defina a opção **Permitir acesso aos serviços do Azure** para **ATIVADO**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **+ Novo** > **Dados + Análise** > **Fábrica de Dados**:
    
    ![Criação de nova fábrica de dados](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.
    
    ![Nova fábrica de dados](./media/tutorial-copy-data-tool/new-azure-data-factory.png)

    O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:
    
    ![Mensagem de erro de nova fábrica de dados](./media/tutorial-copy-data-tool/name-not-available-error.png)

    Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
1. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:
    
    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Em **Versão**, selecione **V2** para indicar a versão.
1. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) utilizados pela fábrica de dados podem estar noutras localizações e regiões.
1. Selecione **Afixar ao dashboard**.
1. Selecione **Criar**.
1. No dashboard, o mosaico **A Implementar a Fábrica de Dados** mostra o estado do processo.

    ![Mosaico A Implementar o Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
1. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
    
    ![Home page da fábrica de dados](./media/tutorial-copy-data-tool/data-factory-home-page.png)
1. Para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o mosaico **Criar e Monitorizar**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Vamos começar**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados.

    ![Mosaico ferramenta Copiar Dados](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
1. Na página **Propriedades**, em **Nome da tarefa**, introduza **CopyFromBlobToSqlPipeline**. Em seguida, selecione **Seguinte**. A IU do Data Factory cria um pipeline com o nome de tarefa especificado.

    ![Página Propriedades](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
1. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    ![Novo serviço ligado de origem](./media/tutorial-copy-data-tool/new-source-linked-service.png)

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Seguinte**.

    ![Selecionar origem do blob](./media/tutorial-copy-data-tool/select-blob-source.png)

    c. Na página **Novo Serviço Ligado**, selecione a sua conta de armazenamento da lista **Nome da conta de armazenamento** e, em seguida, selecione **Concluir**.

    ![Configurar armazenamento do azure](./media/tutorial-copy-data-tool/configure-azure-storage.png)

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de origem](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:
    
    a. Clique em **Navegar** para navegar para a pasta **adfv2tutorial/input**, selecione o ficheiro **inputEmp.txt** e clique em **Escolher**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-copy-data-tool/specify-source-path.png)

    b. Clique em **Seguinte** para mover para o passo seguinte.

1. Na página **Definições do formato do ficheiro**, repare que a ferramenta deteta automaticamente os delimitadores de coluna e linha. Selecione **Seguinte**. Também pode pré-visualizar os dados e ver o esquema dos dados de entrada nesta página.

    ![Definições do formato do ficheiro](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na página **Arquivo de dados de destino**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    ![Novo serviço ligado de sink](./media/tutorial-copy-data-tool/new-sink-linked-service.png)

    b. Selecione **Azure SQL Database** da Galeria e, em seguida, selecione **próxima**.

    ![Selecionar BD SQL do Azure](./media/tutorial-copy-data-tool/select-azure-sql-db.png)

    c. Na página **Novo Serviço Ligado**, selecione o nome do servidor e o nome da BD na lista pendente e especifique o nome de utilizador e a palavra-passe e, em seguida, selecione **Concluir**.

    ![Configurar BD SQL do Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de sink](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Na página **Mapeamento da tabela**, selecione a tabela **[dbo].[emp]** e, em seguida, selecione **Seguinte**.

    ![Mapeamento da tabela](./media/tutorial-copy-data-tool/table-mapping.png)
1. Na página **Mapeamento de esquema**, repare que a primeira e segunda colunas no ficheiro de entrada estão mapeadas para as colunas **FirstName** e **LastName** da tabela **emp** . Selecione **Seguinte**.

    ![Página de mapeamento de esquema](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Na página **Definições**, selecione **Seguinte**.
1. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-copy-data-tool/summary-page.png)
1. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-copy-data-tool/deployment-page.png)
1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline. Selecione **Atualizar** para atualizar a lista.

    ![Monitorizar execuções de pipeline](./media/tutorial-copy-data-tool/pipeline-monitoring.png)
1. Para ver as execuções da atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos) na coluna **Ações**. Para regressar à vista **Execuções do Pipeline**, selecione a ligação **Pipelines** na parte superior. Para atualizar a vista, selecione **Atualizar**.

    ![Monitorização de execuções de atividade](./media/tutorial-copy-data-tool/activity-monitoring.png)

    ![Copiar detalhes da atividade](./media/tutorial-copy-data-tool/copy-execution-details.png)

1. Certifique-se de que os dados estão inseridos na tabela **emp** na sua base de dados SQL.

    ![Verificar a saída do SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

1. Selecione o separador **Criar** à esquerda para mudar para o modo de edição. Pode atualizar os serviços ligados, os conjuntos de dados e os pipelines criados através da ferramenta com o editor. Para obter detalhes de edição sobre estas entidades na IU do Data Factory, veja [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Passos Seguintes
O pipeline deste exemplo copia os dados do Armazenamento de blobs para uma base de dados SQL. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados do plano local para a cloud:

> [!div class="nextstepaction"]
>[Copiar dados do plano local para a cloud](tutorial-hybrid-copy-data-tool.md)
