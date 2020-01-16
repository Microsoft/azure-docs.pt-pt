---
title: Copiar dados do armazenamento de BLOBs para o banco de dados SQL – Azure
description: Este tutorial mostra como usar a atividade de cópia em um pipeline de Azure Data Factory para copiar dados do armazenamento de BLOBs para o banco de dados SQL.
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
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979732"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: copiar dados do armazenamento de BLOBs para o banco de dado SQL usando Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, você cria um data factory com um pipeline para copiar dados do armazenamento de BLOBs para o banco de dados SQL.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para obter uma visão geral detalhada do serviço de Data Factory, consulte o artigo [introdução ao Azure data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

* **Subscrição do Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o artigo [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
* **Conta de armazenamento do Azure**. Você usa o armazenamento de BLOBs como um armazenamento de dados de **origem** neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para obter os passos para criar uma.
* **Base de Dados SQL do Azure**. Você usa um banco de dados SQL do Azure como um armazenamento de dado de **destino** neste tutorial. Se você não tiver um banco de dados SQL do Azure que possa ser usado no tutorial, consulte [como criar e configurar um banco de dados SQL do Azure](../../sql-database/sql-database-get-started.md) para criar um.
* **SQL Server 2012/2014 ou Visual Studio 2013**. Você usa o SQL Server Management Studio ou o Visual Studio para criar um banco de dados de exemplo e para exibir o resultado no banco de dado.  

## <a name="collect-blob-storage-account-name-and-key"></a>Coletar nome e chave da conta de armazenamento de BLOBs
Você precisa do nome da conta e da chave de conta da sua conta de armazenamento do Azure para fazer este tutorial. Anote o **nome da conta** e a **chave de conta** da sua conta de armazenamento do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** no menu à esquerda e selecione **contas de armazenamento**.

    ![Procurar-contas de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Na folha **contas de armazenamento** , selecione a **conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Selecione o link **chaves de acesso** em **configurações**.
5. Clique no botão **copiar** (imagem) ao lado da caixa de texto **nome da conta de armazenamento** e salve/Cole em algum lugar (por exemplo: em um arquivo de texto).
6. Repita a etapa anterior para copiar ou anotar a **key1**.

    ![Chave de acesso ao armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Feche todas as folhas clicando em **X**.

## <a name="collect-sql-server-database-user-names"></a>Coletar nomes de usuário, banco de dados e SQL Server
Você precisa dos nomes do Azure SQL Server, do banco de dados e do usuário para fazer este tutorial. Anote os nomes do **servidor, do** **banco de dados**e do **usuário** para o banco de dados SQL do Azure.

1. Na **portal do Azure**, clique em **todos os serviços** à esquerda e selecione **bancos de dados SQL**.
2. Na **folha bancos**de dados SQL, selecione o **banco** que você deseja usar neste tutorial. Anote o **nome do banco de dados**.  
3. Na folha **banco de dados SQL** , clique em **Propriedades** em **configurações**.
4. Anote os valores para **nome do servidor** e **logon de administrador do servidor**.
5. Feche todas as folhas clicando em **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o SQL Server
Verifique se a configuração **permitir acesso aos serviços do Azure** está ativada para o servidor SQL do **Azure para que** o serviço de data Factory possa acessar o SQL Server do Azure. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique em **todos os serviços** Hub à esquerda e clique em **servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
3. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
4. Feche todas as folhas clicando em **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o armazenamento de BLOBs e o banco de dados SQL
Agora, prepare o armazenamento de BLOBs do Azure e o banco de dados SQL do Azure para o tutorial executando as seguintes etapas:  

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como **EMP. txt** na pasta **C:\ADFGetStarted** no seu disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Utilize ferramentas, como o [Explorador do Storage do Azure](https://storageexplorer.com/), para criar o contentor **adftutorial** e carregar o ficheiro **emp.txt** para o contentor.

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

    **Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções em [Gerenciando o banco de dados SQL do Azure usando SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) para se conectar ao servidor SQL do Azure e executar o script SQL.

    Se o cliente não tiver permissão para aceder ao servidor SQL do Azure, terá de configurar a firewall do seu servidor SQL do Azure para permitir o acesso a partir do seu computador (Endereço IP). Veja [este artigo](../../sql-database/sql-database-configure-firewall-settings.md) para obter os passos para configurar a firewall para o seu servidor SQL do Azure.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Você concluiu os pré-requisitos. Você pode criar um data factory usando uma das seguintes maneiras. Clique em uma das opções na lista suspensa na parte superior ou nos links a seguir para executar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build your first pipeline to transform data using Hadoop cluster (Tutorial: Criar o seu primeiro pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).
>
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas.
