---
title: Utilizar R para consultar a base de dados do Azure SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra-lhe como utilizar um script R para ligar a uma base de dados SQL do Azure e a consulta com instruções Transact-SQL.
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
ms.openlocfilehash: 796e9c17a457bfb1a79b600b178d2c733e10f91e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382415"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Início rápido: Utilizar R para consultar uma base de dados SQL do Azure (pré-visualização)

 Este início rápido demonstra como usar [R](https://www.r-project.org/) com Machine Learning Services para ligar a uma base de dados SQL do Azure e utilizar instruções Transact-SQL para consultar dados. Serviços de Machine Learning é um recurso do Azure SQL Database, utilizado para a execução de scripts do R na base de dados. Para obter mais informações, consulte [serviços do Azure SQL Database Machine Learning com R (pré-visualização)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem o seguinte:

- Uma base de dados SQL do Azure. Pode utilizar um dos seguintes inícios rápidos para criar e, em seguida, configurar uma base de dados na base de dados do Azure SQL:

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
  | Configurar | [regra de firewall do IP ao nível do servidor](sql-database-server-level-firewall-rule.md) |
  | Carregar dados | A Adventure Works carregados por início rápido |
  |||

  > [!NOTE]
  > Durante a pré-visualização do Azure SQL da base de dados dos serviços Machine Learning com R, a opção de implementação de instância gerida não é suportada.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Serviços de Machine Learning (com R) ativado. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- A versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este início rápido irá utilizar o SSMS.

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone.

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar a base de dados SQL

1. Abra o **SQL Server Management Studio** e ligue à sua base de dados SQL.

   Se precisar de ajuda com a ligação, veja [início rápido: Utilizar o SQL Server Management Studio para se ligar e consultar uma base de dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedimento armazenado.

   O script é transmitido a `@script` argumento. Tudo dentro do `@script` argumento tem de ser um código de R válido.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se receber um erro, poderá ser porque a pré-visualização pública do Machine Learning Services (com R) não está ativada para a sua Base de Dados SQL. Ver [pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Executar o [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedimento armazenado.

1. Certifique-se de que as linhas de categoria/produto 20 melhores são retornadas no **mensagens** janela.

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [SQL do Azure da base de dados serviços de Machine Learning (com R)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts simples do R no SQL da base de dados serviços Azure Machine Learning (pré-visualização)](sql-database-quickstart-r-create-script.md)
- [Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)](sql-database-machine-learning-services-functions.md)
