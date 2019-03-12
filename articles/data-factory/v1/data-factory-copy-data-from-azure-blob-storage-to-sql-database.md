---
title: Copiar dados de armazenamento de BLOBs para base de dados do SQL - Azure | Documentos da Microsoft
description: Este tutorial mostra-lhe como utilizar a atividade de cópia num pipeline do Azure Data Factory para copiar dados do armazenamento de BLOBs para base de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7accb8f2d97f5e4b4dc19ba93ab9edc1a4e6ce8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541338"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL com o Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste tutorial, vai criar uma fábrica de dados com um pipeline para copiar dados de armazenamento de BLOBs para base de dados SQL.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para uma visão geral detalhada do serviço Data Factory, consulte a [introdução ao Azure Data Factory](data-factory-introduction.md) artigo.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

* **Subscrição do Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte a [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) artigo para obter detalhes.
* **Conta de armazenamento do Azure**. Utilize o armazenamento de BLOBs como um **origem** do arquivo de dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* **Base de Dados SQL do Azure**. Utilizar uma base de dados SQL do Azure como um **destino** do arquivo de dados neste tutorial. Se não tiver uma base de dados SQL do Azure que pode utilizar o tutorial, veja [como criar e configurar uma base de dados do SQL Azure](../../sql-database/sql-database-get-started.md) para criar um.
* **SQL Server 2012 Maio de 2014 ou o Visual Studio 2013**. Utilize o SQL Server Management Studio ou o Visual Studio para criar uma base de dados de exemplo e ver os dados de resultado na base de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Recolher o nome de conta de armazenamento de BLOBs e a chave
Precisa do nome da conta e chave de conta da sua conta de armazenamento do Azure para fazer este tutorial. Tome nota **nome da conta** e **chave de conta** para a sua conta de armazenamento do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** no menu à esquerda e selecione **contas de armazenamento**.

    ![Procurar - contas de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Na **contas de armazenamento** painel, selecione a **conta de armazenamento do Azure** que pretende utilizar neste tutorial.
4. Selecione **chaves de acesso** ligação sob **definições**.
5. Clique em **cópia** botão (imagem), junto a **nome da conta de armazenamento** texto caixa e save/colá-lo em algum lugar (por exemplo: num ficheiro de texto).
6. Repita o passo anterior para copiar ou tome nota da **chave1**.

    ![Chave de acesso ao armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Fechar todos os painéis clicando **X**.

## <a name="collect-sql-server-database-user-names"></a>Recolher do SQL server, base de dados, os nomes de utilizador
Terá dos nomes de servidor SQL do Azure, base de dados e utilizador para fazer este tutorial. Anote os nomes dos **servidor**, **base de dados**, e **utilizador** da base de dados SQL do Azure.

1. Na **portal do Azure**, clique em **todos os serviços** à esquerda e selecione **bases de dados SQL**.
2. Na **painel de bases de dados SQL**, selecione a **base de dados** que pretende utilizar neste tutorial. Tome nota da **nome da base de dados**.  
3. Na **base de dados SQL** painel, clique em **propriedades** sob **definições**.
4. Anote os valores para **nome do servidor** e **início de sessão de administrador de servidor**.
5. Fechar todos os painéis clicando **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure aceder ao SQL server
Certifique-se de que **permitir o acesso aos serviços do Azure** definição folheada **ON** para o seu servidor SQL do Azure para que o serviço Data Factory possa acessar o servidor SQL do Azure. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique em **todos os serviços** hub à esquerda e clique em **servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
3. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
4. Fechar todos os painéis clicando **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o armazenamento de BLOBs e a base de dados SQL
Agora, prepare o armazenamento de Blobs do Azure e base de dados SQL do Azure para o tutorial, efetuando os seguintes passos:  

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como **Emp. txt** ao **C:\ADFGetStarted** pasta no disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Utilize ferramentas, como o [Explorador do Storage do Azure](https://storageexplorer.com/), para criar o contentor **adftutorial** e carregar o ficheiro **emp.txt** para o contentor.

    ![Explorador de armazenamento do Azure. Copiar dados de armazenamento de BLOBs para base de dados SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilize o seguinte script SQL para criar a tabela **emp** na SQL Database do Azure.  

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

    **Se tiver o SQL Server 2012 de Maio de 2014 instalado no seu computador:** siga as instruções [Managing Azure SQL Database using SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) para ligar ao seu servidor SQL do Azure e execute o script SQL. 

    Se o cliente não tiver permissão para aceder ao servidor SQL do Azure, terá de configurar a firewall do seu servidor SQL do Azure para permitir o acesso a partir do seu computador (Endereço IP). Veja [este artigo](../../sql-database/sql-database-configure-firewall-settings.md) para obter os passos para configurar a firewall para o seu servidor SQL do Azure.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Concluiu os pré-requisitos. Pode criar uma fábrica de dados através de uma das seguintes formas. Clique em uma das opções na lista pendente na parte superior ou as ligações seguintes para executar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Crie seu primeiro pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 
