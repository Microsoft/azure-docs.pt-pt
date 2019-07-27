---
title: Utilizar Ruby para consultar a Base de Dados SQL do Azure | Microsoft Docs
description: Este tópico mostra-lhe como utilizar Ruby para criar um programa que se liga a uma base de dados SQL do Azure e a consulta com instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 5b47ddc2d865108e03b3c649536bfaa700e4a59d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569116"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Início rápido: Utilizar Ruby para consultar uma base de dados SQL do Azure

Este guia de início rápido demonstra como usar o [Ruby](https://www.ruby-lang.org) para conectar-se a um banco de dados SQL do Azure e consultar os dados com instruções TRANSACT-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa dos seguintes pré-requisitos:

- Uma base de dados SQL do Azure. Você pode usar um desses guias de início rápido para criar e, em seguida, configurar um banco de dados no banco de dados SQL do Azure:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall de IP de nível de servidor](sql-database-server-level-firewall-rule.md)| [Conectividade de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Adventure Works carregado por início rápido|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar o Adventure Works do arquivo [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para usar o banco de dados do Adventure Works. Com uma instância gerenciada, você deve importar o banco de dados do Adventure Works para um banco de dados de instância ou modificar os scripts deste artigo para usar o banco de dados de importadores mundiais.
  
- Ruby e software relacionado para seu sistema operacional:
  
  - **MacOS**: Instale o Homebrew, o rbenv e o Ruby-Build, Ruby, FreeTDS e função tinytds. Consulte as etapas 1,2, 1,3, 1,4, 1,5 e 2,1 em [criar aplicativos Ruby usando o SQL Server no MacOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale os pré-requisitos para Ruby, rbenv e Ruby-Build, Ruby, FreeTDS e função tinytds. Consulte as etapas 1,2, 1,3, 1,4, 1,5 e 2,1 em [criar aplicativos Ruby usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Instale o Ruby, o Ruby devkit e o função tinytds. Consulte [Configurar o ambiente de desenvolvimento para desenvolvimento Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** . 

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu banco de dados SQL

1. Em um editor de texto ou de código, crie um novo arquivo chamado *sqltest. rb*.
   
1. Adicione o seguinte código. Substitua os valores do banco de dados SQL do `<server>`Azure `<database>`para `<username>`,, `<password>`e.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados AdventureWorksLT de exemplo, que você pode escolher como fonte ao criar o banco de dado. Se o seu banco de dados tiver um dado diferente, use tabelas do seu próprio banco na consulta SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Executar o código

1. Em um prompt de comando, execute o seguinte comando:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verifique se as 20 principais linhas de categoria/produto do banco de dados são retornadas. 

## <a name="next-steps"></a>Passos Seguintes
- [Projete seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md).
- [Repositório GitHub para função tinytds](https://github.com/rails-sqlserver/tiny_tds).
- [Relate problemas ou faça perguntas sobre o função tinytds](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Driver Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
