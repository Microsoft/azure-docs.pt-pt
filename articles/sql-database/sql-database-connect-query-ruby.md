---
title: Use Ruby para consultar
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
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73826931"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o Ruby para consultar uma base de dados SQL do Azure

Este quickstart demonstra como usar a [Ruby](https://www.ruby-lang.org) para se ligar a uma base de dados Azure SQL e dados de consulta com declarações Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- Uma base de dados SQL do Azure. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de um VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade a partir do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com um caso gerido, deve importar a base de dados da Adventure Works numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo.
  
- Ruby e software relacionado para o seu sistema operativo:
  
  - **MacOS**: Instale homebrew, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Consulte os passos 1.2, 1.3, 1.4, 1.5 e 2.1 em Create Ruby aplicações utilizando o [Servidor SQL no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale pré-requisitos para ruby, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Consulte os passos 1.2, 1.3, 1.4, 1.5 e 2.1 em [Create Ruby aplicações usando o SQL Server em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Janelas**: Instale Ruby, Ruby Devkit e TinyTDS. Consulte o ambiente de [desenvolvimento da Configuração para o desenvolvimento da Ruby.](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para as bases de **dados SQL** ou página de **instâncias geridas pela SQL.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma única base de dados ou o nome de servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.** 

## <a name="create-code-to-query-your-sql-database"></a>Crie código para consultar a sua base de dados SQL

1. Num texto ou editor de código, crie um novo ficheiro chamado *sqltest.rb*.
   
1. Adicione o seguinte código. Substitua os valores da sua `<server>`base `<database>` `<username>`de `<password>`dados Azure SQL por, , e .
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados adventureWorksLT da amostra, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize as tabelas da sua própria base de dados na consulta SELECT. 
   
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

1. Num pedido de comando, execute o seguinte comando:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verifique se as 20 melhores linhas de categoria/produto da sua base de dados são devolvidas. 

## <a name="next-steps"></a>Passos seguintes
- [Desenhe a sua primeira base de dados Azure SQL](sql-database-design-first-database.md).
- [Repositório GitHub para TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Reportar questões ou fazer perguntas sobre tinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Condutor de rubi para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
