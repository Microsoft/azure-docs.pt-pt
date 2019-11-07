---
title: Copiar dados usando a ferramenta de Copiar Dados do Azure
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
ms.openlocfilehash: 4646d7429dc4b3286f6af8861eaf7f1e6e27a760
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683600"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copiar dados do Armazenamento de blobs do Azure para uma base de dados SQL com a ferramenta Copiar Dados
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-copy-data-tool.md)

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, use a ferramenta Copiar Dados para criar um pipeline que copia dados do armazenamento de BLOBs do Azure para um banco de dados SQL.

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:
> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: utilize o Armazenamento de blobs como arquivo de dados de _origem_. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
* **Base de Dados SQL do Azure**: utilize uma base de dados SQL como arquivo de dados _sink_. Se não tem uma base de dados SQL, veja as instruções apresentadas em [Criar uma base de dados SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Prepare o Armazenamento de blobs e a sua base de dados SQL para o tutorial ao executar os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o **Bloco de Notas**. Copie o seguinte texto e guarde-o num ficheiro com o nome **inputEmp.txt** no seu disco:

    ```
    John|Doe
    Jane|Doe
    ```

1. Crie um contentor com o nome **adfv2tutorial** e carregue o ficheiro inputEmp.txt para o contentor. Você pode usar o portal do Azure ou várias ferramentas como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para executar essas tarefas.

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

2. Permita o acesso dos serviços do Azure ao SQL Server. Verifique se a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor que tem a Base de Dados SQL em execução. Esta definição permite que o Data Factory escreva dados na instância da sua base de dados. Para verificar e ativar essa configuração, vá para Azure SQL Server > Visão geral > definir Firewall do servidor > defina a opção **permitir acesso aos serviços do Azure** como **ativado**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **Analytics** > **Data Factory**:
    
    ![Criação de nova fábrica de dados](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

    O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:
    
    ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

    Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
1. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:
    
    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Em **Versão**, selecione **V2** para indicar a versão.
1. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) utilizados pela fábrica de dados podem estar noutras localizações e regiões.
1. Selecione **Criar**.

1. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
    
    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
1. Para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o mosaico **Criar e Monitorizar**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Vamos começar**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados.

    ![Mosaico ferramenta Copiar Dados](./media/doc-common-process/get-started-page.png)
1. Na página **Propriedades**, em **Nome da tarefa**, introduza **CopyFromBlobToSqlPipeline**. Em seguida, selecione **Seguinte**. A IU do Data Factory cria um pipeline com o nome de tarefa especificado.

1. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **armazenamento de BLOBs do Azure** na galeria e selecione **continuar**.

    c. Na página **Novo Serviço Ligado**, selecione a sua conta de armazenamento da lista **Nome da conta de armazenamento** e, em seguida, selecione **Concluir**.

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de origem](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:
    
    a. Clique em **Navegar** para navegar para a pasta **adfv2tutorial/input**, selecione o ficheiro **inputEmp.txt** e clique em **Escolher**.

    b. Clique em **Seguinte** para mover para o passo seguinte.

1. Na página **Definições do formato do ficheiro**, repare que a ferramenta deteta automaticamente os delimitadores de coluna e linha. Selecione **Seguinte**. Também pode pré-visualizar os dados e ver o esquema dos dados de entrada nesta página.

    ![Definições do formato do ficheiro](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na página **Arquivo de dados de destino**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **banco de dados SQL do Azure** na galeria e selecione **continuar**.

    c. Na página **Novo Serviço Ligado**, selecione o nome do servidor e o nome da BD na lista pendente e especifique o nome de utilizador e a palavra-passe e, em seguida, selecione **Concluir**.

    ![Configurar BD SQL do Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de sink](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Na página **Mapeamento da tabela**, selecione a tabela **[dbo].[emp]** e, em seguida, selecione **Seguinte**.

1. Na página **Mapeamento de esquema**, repare que a primeira e segunda colunas no ficheiro de entrada estão mapeadas para as colunas **FirstName** e **LastName** da tabela **emp** . Selecione **Seguinte**.

    ![Página de mapeamento de esquema](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Na página **Definições**, selecione **Seguinte**.
1. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.
1. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).
1. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline. Selecione **Atualizar** para atualizar a lista.

1. Para ver as execuções da atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar à exibição de execuções de pipeline, selecione o link **execuções de pipeline** na parte superior. Para atualizar a vista, selecione **Atualizar**.

    ![Monitorização de execuções de atividade](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Certifique-se de que os dados estão inseridos na tabela **emp** na sua base de dados SQL.


1. Selecione o separador **Criar** à esquerda para mudar para o modo de edição. Pode atualizar os serviços ligados, os conjuntos de dados e os pipelines criados através da ferramenta com o editor. Para obter detalhes de edição sobre estas entidades na IU do Data Factory, veja [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Passos seguintes
O pipeline deste exemplo copia os dados do Armazenamento de blobs para uma base de dados SQL. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte, para saber como copiar dados do local para a cloud:

>[!div class="nextstepaction"]
>[Copiar dados do plano local para a cloud](tutorial-hybrid-copy-data-tool.md)
