---
title: Usar o R com Serviços de Machine Learning para consultar o banco de dados SQL do Azure
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra como usar um script R com o banco de dados SQL do Azure Serviços de Machine Learning para se conectar a um banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: ff38346a9b3bd14db51383c116240b030d3ee42a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514848"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Início rápido: Usar R com Serviços de Machine Learning para consultar um banco de dados SQL do Azure (versão prévia)

Este guia de início rápido demonstra como usar o [R](https://www.r-project.org/) com o serviços de Machine Learning para se conectar a um banco de dados SQL do Azure e usar instruções TRANSACT-SQL para consultar o dado. Serviços de Machine Learning é um recurso do banco de dados SQL do Azure, usado para executar scripts R no banco de dados. Para obter mais informações, consulte [serviços de Machine Learning do banco de dados SQL do Azure com R (versão prévia)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem o seguinte:

- Uma base de dados SQL do Azure. Você pode usar um desses guias de início rápido para criar e, em seguida, configurar um banco de dados no banco de dados SQL do Azure:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Base de dados individual |
  |:--- |:--- |
  | Criar| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Configurar | [Regra de firewall de IP de nível de servidor](sql-database-server-level-firewall-rule.md) |
  | Carregar dados | Adventure Works carregado por início rápido |
  |||

  > [!NOTE]
  > Durante a versão prévia do banco de dados SQL do Azure Serviços de Machine Learning com o R, não há suporte para a opção de implantação de instância gerenciada.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Serviços de Machine Learning (com R) habilitado. Durante a visualização pública, a Microsoft integrará você e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [inscrever-se para a versão prévia](sql-database-machine-learning-services-overview.md#signup).

- O SSMS ( [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) mais recente). Você pode executar scripts do R usando outras ferramentas de consulta e gerenciamento de banco de dados, mas neste guia de início rápido você usará o SSMS.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu banco de dados SQL

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda para se conectar [, consulte início rápido: Use SQL Server Management Studio para se conectar e consultar um banco de](sql-database-connect-query-ssms.md)dados SQL do Azure.

1. Passe o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   O script é passado pelo `@script` argumento. Tudo dentro do `@script` argumento deve ser um código R válido.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados AdventureWorksLT de exemplo, que você pode escolher como fonte ao criar o banco de dado. Se o seu banco de dados tiver um dado diferente, use tabelas do seu próprio banco na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Consulte [pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Verifique se as 20 principais linhas de categoria/produto são retornadas na janela **mensagens** .

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Serviços de Machine Learning do banco de dados SQL do Azure (com R)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples no Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia)](sql-database-quickstart-r-create-script.md)
- [Escrever funções de R avançadas no banco de dados SQL do Azure usando Serviços de Machine Learning (versão prévia)](sql-database-machine-learning-services-functions.md)
