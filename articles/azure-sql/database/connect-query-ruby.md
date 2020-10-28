---
title: Use o Ruby para consultar
description: Este tópico mostra-lhe como usar a Ruby para criar um programa que se conecta a uma base de dados e o consulta usando declarações Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 0434a99d28b4f71594e0ca9ce312087dee5b0ee2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672534"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Use o Ruby para consultar uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este quickstart demonstra como usar a [Ruby](https://www.ruby-lang.org) para ligar a uma base de dados e consultar dados com declarações Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- Uma base de dados. Pode utilizar um destes quickstarts para criar e, em seguida, configurar a base de dados:

  | Ação | SQL Database | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP de nível de servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir de instalações](../managed-instance/point-to-site-p2s-configure.md) | [Ligue-se a uma instância do Servidor SQL](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma SQL Managed Instance, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do mundo amplo.
  
- Ruby e software relacionado para o seu sistema operativo:
  
  - **macOS** : Instale homebrew, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Consulte os passos 1.2, 1.3, 1.4, 1.5 e 2.1 em [apps Create Ruby utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu** : Instale pré-requisitos para Ruby, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Consulte os passos 1.2, 1.3, 1.4, 1.5 e 2.1 em [aplicações Create Ruby utilizando o SQL Server em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Janelas** : Instalar Ruby, Ruby Devkit e TinyTDS. Consulte [o ambiente de desenvolvimento configurar para o desenvolvimento da Ruby.](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar a uma base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue para as **bases de dados SQL** ou página **sql Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para uma base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>Crie código para consultar a sua base de dados na Base de Dados Azure SQL

1. Num editor de texto ou código, crie um novo ficheiro chamado *sqltest.rb* .

1. Adicione o seguinte código. Substitua os valores da sua base de dados na Base de Dados Azure SQL `<server>` `<database>` por, `<username>` e `<password>` .

   >[!IMPORTANT]
   >O código neste exemplo utiliza a amostra de dados AdventureWorksLT, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize tabelas da sua própria base de dados na consulta SELECT. 

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

1. Com um pedido de comando, executar o seguinte comando:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verifique se as 20 melhores linhas de categoria/produto da sua base de dados são devolvidas. 

## <a name="next-steps"></a>Passos seguintes
- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Repositório do GitHub para TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Report issues or ask questions about TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues) (Comunicar problemas ou fazer perguntas sobre o TinyTDS)
- [Controlador de rubi para SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/)