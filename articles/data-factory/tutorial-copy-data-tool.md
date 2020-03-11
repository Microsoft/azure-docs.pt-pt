---
title: Copiar dados do armazenamento do Azure Blob para o SQL utilizando a ferramenta Copy Data
description: Crie uma fábrica de dados Azure e, em seguida, utilize a ferramenta Copy Data para copiar dados do armazenamento do Azure Blob para uma Base de Dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 52ed43277eef84de826d2f4fa41ba860211a1531
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969932"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copiar dados do armazenamento do Azure Blob para uma base de dados SQL utilizando a ferramenta Copy Data

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-copy-data-tool.md)

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, utiliza a ferramenta Copy Data para criar um pipeline que copia dados do armazenamento do Azure Blob para uma Base de Dados SQL.

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:
> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento Azure**: Utilize o armazenamento blob como loja de dados _de origem._ Se não tiver uma conta de Armazenamento Azure, consulte as instruções em [Criar uma conta](../storage/common/storage-account-create.md)de armazenamento .
* **Base de dados Azure SQL**: Utilize uma base de dados SQL como depósito de dados do _lavatório._ Se não tiver uma Base de Dados SQL, consulte as instruções em Criar uma Base de [Dados SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Prepare o seu armazenamento Blob e a sua Base de Dados SQL para o tutorial executando estes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o **Bloco de Notas**. Copie o seguinte texto e guarde-o num ficheiro com o nome **inputEmp.txt** no seu disco:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Crie um contentor com o nome **adfv2tutorial** e carregue o ficheiro inputEmp.txt para o contentor. Pode utilizar o portal Azure ou várias ferramentas como o [Azure Storage Explorer](https://storageexplorer.com/) para executar estas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

1. Utilize o seguinte script SQL para criar uma tabela chamada **dbo.emp** na sua Base de Dados SQL:

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

2. Permita o acesso dos serviços do Azure ao SQL Server. Verifique se a definição **permite que os serviços e recursos do Azure acedam a este servidor** para o seu servidor que está a executar a Base de Dados SQL. Esta definição permite que o Data Factory escreva dados na instância da sua base de dados. Para verificar e ativar esta definição, aceda ao servidor Azure SQL > Security > Firewalls e redes virtuais > defina os **serviços e recursos do Allow Azure para aceder a esta** opção de servidor para **ON**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory:**

    ![Criação de nova fábrica de dados](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

    O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:

    ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

    Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
1. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

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
    ![Criar um pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **Armazenamento Azure Blob** na galeria e, em seguida, selecione **Continuar**.

    c. Na página **new Linked Service,** selecione a sua subscrição Azure e selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, **selecione Criar**.

    d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

    ![Selecionar serviço ligado de origem](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

    a. Clique em **Navegar** para navegar para a pasta **adfv2tutorial/input**, selecione o ficheiro **inputEmp.txt** e clique em **Escolher**.

    b. Clique em **Seguinte** para mover para o passo seguinte.

1. Na página de definições do **formato 'Ficheiro',** ative a caixa de verificação para *a primeira linha como cabeçalho*. Note que a ferramenta deteta automaticamente os delimitadores da coluna e da linha. Selecione **Seguinte**. Também pode pré-visualizar dados e ver o esquema dos dados de entrada nesta página.

    ![Definições do formato do ficheiro](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na página **Arquivo de dados de destino**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação

    b. Selecione **Base de Dados Azure SQL** na galeria e, em seguida, selecione **Continuar**.

    c. Na página **New Linked Service,** selecione o nome do seu servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe e, em seguida, selecione **Criar**.

    ![Configurar BD SQL do Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecione o serviço ligado criado recentemente como sink e, em seguida, clique em **Seguinte**.

1. Na página **Mapeamento da tabela**, selecione a tabela **[dbo].[emp]** e, em seguida, selecione **Seguinte**.

1. Na página de mapeamento da **Coluna,** note que a segunda e a terceira colunas no ficheiro de entrada estão mapeadas para as colunas **FirstName** e **LastName** da tabela **emp.** Ajuste o mapeamento para se certificar de que não há erro e, em seguida, selecione **Seguinte**.

    ![Página de mapeamento de colunas](./media/tutorial-copy-data-tool/column-mapping.png)

1. Na página **Definições**, selecione **Seguinte**.
1. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.
1. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).
 
    ![Monitorizar o pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Na página pipeline executa, selecione **Refresh** para refrescar a lista. Clique no link em **PIPELINE NAME** para ver os detalhes de execução da atividade ou reexecutar o gasoduto. 
    ![Pipeline executar](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Na página 'Atividade', selecione o link **Detalhes** (ícone de óculos) na coluna **'NAME'** para obter mais detalhes sobre o funcionamento da cópia. Para voltar à vista Pipeline Runs, selecione o **link ALL pipeline executa** o menu de migalhas de pão. Para atualizar a vista, selecione **Atualizar**.

    ![Monitorização de execuções de atividade](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Verifique se os dados estão inseridos na tabela **dbo.emp** na sua Base de Dados SQL.


1. Selecione o separador **Criar** à esquerda para mudar para o modo de edição. Pode atualizar os serviços ligados, os conjuntos de dados e os pipelines criados através da ferramenta com o editor. Para obter detalhes de edição sobre estas entidades na IU do Data Factory, veja [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

    ![Selecione separador Autor](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Passos seguintes
O gasoduto desta amostra copia dados do armazenamento blob para uma base de dados SQL. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados do plano local para a cloud:

>[!div class="nextstepaction"]
>[Copiar dados do plano local para a cloud](tutorial-hybrid-copy-data-tool.md)
