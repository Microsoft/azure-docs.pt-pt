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
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: d674928bbe585174db897b2a052a5fd09bcee329
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65753876"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Início rápido: Utilizar Ruby para consultar uma base de dados SQL do Azure

Este início rápido demonstra como usar [Ruby](https://www.ruby-lang.org) para ligar a uma data de base de dados e consulta de SQL do Azure com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, terá dos seguintes pré-requisitos:

- Uma base de dados SQL do Azure. Pode utilizar um dos seguintes inícios rápidos para criar e, em seguida, configurar uma base de dados na base de dados do Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [regra de firewall do IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade no local do](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|A Adventure Works carregados por início rápido|[Restaurar a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaure ou importar Adventure Works no [BACPAC](sql-database-import.md) ficheiro [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para utilizar a base de dados do Adventure Works. Com uma instância gerida, tem de importar a base de dados do Adventure Works para uma base de dados de instância ou modificar os scripts neste artigo para utilizar a base de dados do Wide World Importers.
  
- Software de Ruby e relacionado para o seu sistema operativo:
  
  - **MacOS**: Instale o Homebrew, o rbenv e o ruby-build, o Ruby, o FreeTDS e o TinyTDS. Veja os passos 1.2, 1.3, 1.4, 1,5 e 2.1 no [aplicações Ruby criar usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale os pré-requisitos para Ruby, o rbenv e o ruby-build, o Ruby, o FreeTDS e o TinyTDS. Veja os passos 1.2, 1.3, 1.4, 1,5 e 2.1 no [aplicações Ruby criar usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Instale Ruby, Ruby Devkit e TinyTDS. Ver [configurar o ambiente de desenvolvimento para o desenvolvimento de Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone. 

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar a base de dados SQL

1. Num editor de texto ou de código, crie um novo ficheiro designado *sqltest.rb*.
   
1. Adicione o seguinte código. Substitua os valores da sua base de dados SQL do Azure para `<server>`, `<database>`, `<username>`, e `<password>`.
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados de AdventureWorksLT de exemplo, o que pode escolher como origem ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize a consulta SELECT tabelas da sua própria base de dados. 
   
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

1. No prompt de comando, execute o seguinte comando:

   ```bash
   ruby sqltest.rb
   ```
   
1. Certifique-se de que as linhas de categoria/produto de 20 melhores da sua base de dados são devolvidas. 

## <a name="next-steps"></a>Passos Seguintes
- [Conceber a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md).
- [Repositório do GitHub para TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Comunicar problemas ou fazer perguntas sobre o TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Controladores Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
