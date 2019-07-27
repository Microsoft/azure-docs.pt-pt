---
title: Inícios rápidos de Ligação e Consulta da Base de Dados SQL do Azure | Microsoft Docs
description: Inícios rápidos da Base de Dados SQL do Azure que mostram como ligar e consultar uma base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 63ed2c5c334aef8f6281ee34ec4ed6e47ca8521a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569093"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Inícios rápidos: Conexão e consulta do banco de dados SQL do Azure

O seguinte documento inclui ligações para exemplos do Azure que mostram como ligar e consultar uma base de dados SQL do Azure. Fornece também algumas recomendações para Segurança a Nível de Transporte.

## <a name="quickstarts"></a>Guias de Introdução

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Este guia de introdução demonstra como utilizar o SSMS para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Este guia de início rápido demonstra como utilizar o Azure Data Studio para ligar a uma base de dados SQL do Azure e, em seguida, utilizar instruções Transact-SQL (T-SQL) para criar o TutorialDB utilizado nos tutoriais do Azure Data Studio.|
|[Azure portal](sql-database-connect-query-portal.md)|Este início rápido demonstra como utilizar o Editor de consultas para ligar a uma base de dados SQL e, em seguida, utilizar as instruções de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Este guia de introdução demonstra como utilizar o Visual Studio Code para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[.NET com o Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Este início rápido demonstra como utilizar o .NET Framework para criar um programa C# com o Visual Studio para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Este início rápido demonstra como utilizar o .NET Core em Windows/Linux/macOS para criar um programa C# para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[Go](sql-database-connect-query-go.md)|Este início rápido explica como utilizar o Go para ligar a uma base de dados SQL do Azure. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Java](sql-database-connect-query-java.md)|Este início rápido demonstra como utilizar o Java para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as instruções Transact-SQL para consultar dados.|
|[Node.js](sql-database-connect-query-nodejs.md)|Este início rápido demonstra como utilizar o Node.js para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[PHP](sql-database-connect-query-php.md)|Este início rápido demonstra como utilizar o PHP para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[Python](sql-database-connect-query-python.md)|Este início rápido demonstra como utilizar o Python para ligar a uma base de dados SQL do Azure e utilizar as instruções Transact-SQL para consultar dados. |
|[Ruby](sql-database-connect-query-ruby.md)|Este início rápido demonstra como utilizar o Ruby para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[R](sql-database-connect-query-r.md)|Este guia de início rápido demonstra como usar o R com o banco de dados SQL do Azure Serviços de Machine Learning para criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultas de consulta.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerações de TLS para conectividade de Base de Dados SQL
A Transport Layer Security (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para ligar à Base de Dados SQL do Azure. Não é necessária qualquer configuração especial. Para todas as ligações ao SQL Server ou à Base de Dados SQL do Azure, recomendamos que todas as aplicações definam as seguintes configurações ou os respetivos equivalentes:

 - **Encriptar = Ativado**
 - **TrustServerCertificate = Desativado**

Alguns sistemas utilizam palavras-chave diferentes mas equivalentes para as palavras-chave de configuração. Estas configurações garantem que o controlador do cliente verifica a identidade do certificado TLS recebido do servidor.

Recomendamos também que desative a TLS 1.1 e 1.0 no cliente, se tiver de estar em conformidade com a Data Security Standard (DSS) da Payment Card Industry (PCI).

Os controladores que não são da Microsoft podem não utilizar o TLS por predefinição. Isto poderá ser um fator ao ligar à Base de Dados SQL do Azure. As aplicações com controladores incorporados podem não permitir que controle estas definições de ligação. Recomendamos que analise a segurança de tais aplicações e controladores antes de os utilizar em sistemas que interajam com dados confidenciais.

## <a name="libraries"></a>Bibliotecas

Você pode usar várias bibliotecas e estruturas para se conectar ao banco de dados SQL do Azure. Confira nossos [tutoriais](https://aka.ms/sqldev) de introdução para começar rapidamente com linguagens C#de programação, como Java, Node. js, php e Python. Em seguida, crie um aplicativo usando o SQL Server em Linux ou o Windows ou o Docker no macOS.

A tabela a seguir lista as bibliotecas de conectividade ou os *drivers* que os aplicativos cliente podem usar de uma variedade de idiomas para se conectar e usar SQL Server em execução no local ou na nuvem. Você pode usá-los no Linux, Windows ou Docker e usá-los para se conectar ao banco de dados SQL do Azure e ao Azure SQL Data Warehouse. 

| Idioma | Plataforma | Recursos adicionais | Transferência | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Transferência](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Transferência](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver SQL do PHP para SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Transferência](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver node. js para SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver de SQL do Python](https://msdn.microsoft.com/library/mt652092.aspx) | Opções de instalação: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Driver Ruby para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Driver ODBC da Microsoft para SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Transferência](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

A tabela a seguir lista exemplos de estruturas de ORM (mapeamento relacional de objeto) e estruturas da Web que os aplicativos cliente podem usar com SQL Server em execução no local ou na nuvem. Você pode usar as estruturas no Linux, Windows ou Docker e usá-las para se conectar ao banco de dados SQL e SQL Data Warehouse. 

| Idioma | Plataforma | ORM (s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[ORM de hibernação](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (eloqüência)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [ORM Sequelize](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre a arquitetura de conectividade, veja [Arquitetura de Conectividade de Base de Dados SQL do Azure](sql-database-connectivity-architecture.md).
- Localizar [SQL Server drivers](https://msdn.microsoft.com/library/mt654049.aspx) que são usados para se conectar de aplicativos cliente
- Conectar ao banco de dados SQL:
  - [Ligar à Base de Dados SQL com o .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Ligar à Base de Dados SQL com PHP](sql-database-connect-query-php.md) 
  - [Ligar à Base de Dados SQL com o Node.js](sql-database-connect-query-nodejs.md) 
  - [Ligar à Base de Dados SQL com Java](sql-database-connect-query-java.md) 
  - [Ligar à Base de Dados SQL com o Python](sql-database-connect-query-python.md)
  - [Ligar à Base de Dados SQL com Ruby](sql-database-connect-query-ruby.md)
- Exemplos de código de lógica de repetição:
  - [Conectar-se de forma resiliente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Conectar-se de forma resiliente ao SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
