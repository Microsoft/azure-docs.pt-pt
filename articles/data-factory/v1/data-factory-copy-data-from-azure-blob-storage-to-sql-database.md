---
title: Copiar dados do Blob Storage para a BASE de Dados SQL - Azure
description: Este tutorial mostra-lhe como utilizar a Copy Activity num oleoduto Azure Data Factory para copiar dados do armazenamento blob para a base de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 15bce219b96268124729de2f475e33fc386348a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021219"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Copiar dados do Blob Storage para a Base de Dados SQL utilizando a Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, cria-se uma fábrica de dados com um pipeline para copiar dados do armazenamento blob para a Base de Dados SQL.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para obter uma visão geral detalhada do serviço data factory, consulte o artigo [Introdução à Fábrica de Dados Azure.](data-factory-introduction.md)
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

* **Assinatura Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o artigo [Free Trial](https://azure.microsoft.com/pricing/free-trial/) para mais detalhes.
* **Conta de Armazenamento Azure**. Você usa o armazenamento de bolhas como uma loja de dados **de origem** neste tutorial. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar um](../../storage/common/storage-account-create.md) artigo de conta de armazenamento para etapas para criar uma.
* **Base de Dados Azure SQL**. Utiliza a Base de Dados Azure SQL como uma loja de dados de **destino** neste tutorial. Se não tiver uma base de dados na Base de Dados Azure SQL que pode utilizar no tutorial, consulte [como criar e configurar uma base de dados na Base de Dados Azure SQL](../../azure-sql/database/single-database-create-quickstart.md) para criar uma.
* **SQL Server 2012/2014 ou Visual Studio 2013**. Utiliza o SQL Server Management Studio ou o Visual Studio para criar uma base de dados de amostras e para visualizar os dados dos resultados na base de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Colete o nome e a chave da conta de armazenamento blob
Precisa do nome da conta e da chave da conta da sua conta de armazenamento Azure para fazer este tutorial. Note o **nome da conta** e a chave de **conta** para a sua conta de armazenamento Azure.

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** no menu esquerdo e selecione **Contas de Armazenamento.**

    ![Procurar - Contas de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Na lâmina **'Contas de Armazenamento',** selecione a **conta de armazenamento Azure** que pretende utilizar neste tutorial.
4. Selecione o link **de teclas** de acesso **em DEFINIÇÕES**.
5. Clique no botão **de cópia** (imagem) ao lado da caixa de texto do nome da conta **de armazenamento** e guarde-o/cole-o em algum lugar (por exemplo: num ficheiro de texto).
6. Repita o passo anterior para copiar ou anotar a **tecla1**.

    ![Chave de acesso ao armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Feche todas as lâminas clicando **em X**.

## <a name="collect-sql-server-database-user-names"></a>Recolher servidor SQL, base de dados, nomes de utilizadores
Precisa dos nomes do servidor, base de dados e utilizador lógicos do SQL para fazer este tutorial. Note os nomes do **servidor,** **base de dados** e **utilizador** da Base de Dados Azure SQL.

1. No **portal Azure,** clique em **Todos os serviços** à esquerda e selecione **bases de dados SQL**.
2. Na lâmina de **bases de dados SQL,** selecione a **base de dados** que pretende utilizar neste tutorial. Note o nome da **base de dados.**  
3. Na lâmina de **base de dados SQL,** clique em **Propriedades** em **DEFINIÇÕES**.
4. Note os valores para **O NOME DO SERVIDOR** e O INÍCIO DE LOGIN DO SERVIDOR **ADMIN**.
5. Feche todas as lâminas clicando **em X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços da Azure acedam ao servidor SQL
Certifique-se de que permitir o acesso à definição **de serviços Azure** **foi** ligado para o seu servidor para que o serviço Data Factory possa aceder ao seu servidor. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique em **Todos os centros de serviços** à esquerda e clique nos **servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
3. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
4. Feche todas as lâminas clicando **em X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar armazenamento blob e base de dados SQL
Agora, prepare o seu armazenamento de bolhas Azure e base de dados Azure SQL para o tutorial realizando os seguintes passos:  

1. Inicie o Bloco de notas. Copie o texto que se segue e guarde-o como **emp.txt** para a pasta **C:\ADFGetStarted** no seu disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Utilize ferramentas como [o Azure Storage Explorer](https://storageexplorer.com/) para criar o recipiente **adftutorial** e para carregar o ficheiro **emp.txt** para o recipiente.

3. Utilize o seguinte script SQL para criar a tabela **emp** na Base de Dados SQL do Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Se tiver o SQL Server 2012/2014 instalado no seu computador:** siga as instruções da [Managing Azure SQL Database utilizando](../../azure-sql/database/single-database-manage.md) o SQL Server Management Studio para ligar ao seu servidor e executar o script SQL.

    Se o seu cliente não estiver autorizado a aceder ao servidor lógico SQL, tem de configurar firewall para o seu servidor para permitir o acesso a partir da sua máquina (Endereço IP). Consulte [este artigo](../../azure-sql/database/firewall-configure.md) para obter etapas para configurar a firewall do seu servidor.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Completou os pré-requisitos. Pode criar uma fábrica de dados utilizando uma das seguintes formas. Clique numa das opções na lista de drop-down no topo ou nos seguintes links para realizar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build your first pipeline to transform data using Hadoop cluster (Tutorial: Criar o seu primeiro pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).
>
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas.