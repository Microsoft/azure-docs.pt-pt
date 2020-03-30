---
title: Connect e Query quickstarts
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
ms.openlocfilehash: 647bdcf5c8c49b5c942419c78155ed4f61c848bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240563"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Quickstarts: Azure SQL Database connect and consulta

O seguinte documento inclui ligações para exemplos do Azure que mostram como ligar e consultar uma base de dados SQL do Azure. Fornece também algumas recomendações para Segurança a Nível de Transporte.

## <a name="quickstarts"></a>Guias de Início Rápido

| |  |
|---|---|
|[Estúdio de Gestão de Servidores SQL](sql-database-connect-query-ssms.md)|Este guia de introdução demonstra como utilizar o SSMS para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Este guia de início rápido demonstra como utilizar o Azure Data Studio para ligar a uma base de dados SQL do Azure e, em seguida, utilizar instruções Transact-SQL (T-SQL) para criar o TutorialDB utilizado nos tutoriais do Azure Data Studio.|
|[Portal Azure](sql-database-connect-query-portal.md)|Este início rápido demonstra como utilizar o Editor de consultas para ligar a uma base de dados SQL e, em seguida, utilizar as instruções de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Código de estúdio visual](sql-database-connect-query-vscode.md)|Este guia de introdução demonstra como utilizar o Visual Studio Code para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[.NET com o Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Este início rápido demonstra como utilizar o .NET Framework para criar um programa C# com o Visual Studio para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[núcleo .NET](sql-database-connect-query-dotnet-core.md)|Este início rápido demonstra como utilizar o .NET Core em Windows/Linux/macOS para criar um programa C# para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[Ir](sql-database-connect-query-go.md)|Este início rápido explica como utilizar o Go para ligar a uma base de dados SQL do Azure. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Java](sql-database-connect-query-java.md)|Este início rápido demonstra como utilizar o Java para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as instruções Transact-SQL para consultar dados.|
|[Nó.js](sql-database-connect-query-nodejs.md)|Este início rápido demonstra como utilizar o Node.js para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[PHP](sql-database-connect-query-php.md)|Este início rápido demonstra como utilizar o PHP para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[Pitão](sql-database-connect-query-python.md)|Este início rápido demonstra como utilizar o Python para ligar a uma base de dados SQL do Azure e utilizar as instruções Transact-SQL para consultar dados. |
|[Ruby](sql-database-connect-query-ruby.md)|Este início rápido demonstra como utilizar o Ruby para criar um programa para ligar a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.|
|[R](sql-database-connect-query-r.md)|Este quickstart demonstra como usar R com Serviços de Machine Learning de Base de Dados Azure SQL para criar um programa de ligação a uma base de dados Azure SQL e usar declarações Transact-SQL para consultar dados.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerações de TLS para conectividade de Base de Dados SQL
A Transport Layer Security (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para ligar à Base de Dados SQL do Azure. Não é necessária qualquer configuração especial. Para todas as ligações ao SQL Server ou à Base de Dados SQL do Azure, recomendamos que todas as aplicações definam as seguintes configurações ou os respetivos equivalentes:

 - **Encriptar = Ativado**
 - **TrustServerCertificate = Desativado**

Alguns sistemas utilizam palavras-chave diferentes mas equivalentes para as palavras-chave de configuração. Estas configurações garantem que o controlador do cliente verifica a identidade do certificado TLS recebido do servidor.

Recomendamos também que desative a TLS 1.1 e 1.0 no cliente, se tiver de estar em conformidade com a Data Security Standard (DSS) da Payment Card Industry (PCI).

Os controladores que não são da Microsoft podem não utilizar o TLS por predefinição. Isto poderá ser um fator ao ligar à Base de Dados SQL do Azure. As aplicações com controladores incorporados podem não permitir que controle estas definições de ligação. Recomendamos que analise a segurança de tais aplicações e controladores antes de os utilizar em sistemas que interajam com dados confidenciais.

## <a name="libraries"></a>Bibliotecas

Pode utilizar várias bibliotecas e estruturas para se ligar à Base de Dados Azure SQL. Confira os [nossos tutoriais Get started](https://aka.ms/sqldev) para começar rapidamente com linguagens de programação como C#, Java, Node.js, PHP e Python. Em seguida, construa uma aplicação utilizando o SQL Server no Linux ou Windows ou Docker no macOS.

A tabela seguinte lista bibliotecas de conectividade ou *controladores* que as aplicações do cliente podem usar a partir de uma variedade de idiomas para ligar e usar o SQL Server em execução no local ou na nuvem. Pode usá-los em Linux, Windows ou Docker e usá-los para ligar à Base de Dados Azure SQL e ao Armazém de Dados Azure SQL. 

| Idioma | Plataforma | Recursos adicionais | Transferência | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para O Servidor SQL](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Transferir](https://www.microsoft.com/net/download/) | [Começar](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Controlador Microsoft JDBC para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Transferir](https://go.microsoft.com/fwlink/?linkid=852460) |  [Começar](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Condutor PHP SQL para Servidor SQL](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Transferir](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Começar](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Condutor de Node.js para SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Começar](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Condutor Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Instale escolhas: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Começar](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Condutor de rubi para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Começar](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Controlador Microsoft ODBC para SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Transferir](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

A tabela seguinte enumera exemplos de quadros de mapeamento relacional de objetos (ORM) e quadros web que as aplicações do cliente podem usar com o Servidor SQL a funcionar no local ou na nuvem. Pode utilizar as estruturas em Linux, Windows ou Docker e usá-las para se ligar à Base de Dados SQL e ao Armazém de Dados SQL. 

| Idioma | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernar ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquente)](https://laravel.com/docs/eloquent)<br>[Doutrina](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Rubi nos trilhos](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a arquitetura de conectividade, veja [Arquitetura de Conectividade de Base de Dados SQL do Azure](sql-database-connectivity-architecture.md).
- Encontre [controladores SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) que são usados para se conectarem a partir de aplicações de clientes
- Ligue-se à Base de Dados SQL:
  - [Ligar à Base de Dados SQL com o .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Ligar à Base de Dados SQL com PHP](sql-database-connect-query-php.md) 
  - [Ligar à Base de Dados SQL com o Node.js](sql-database-connect-query-nodejs.md) 
  - [Ligar à Base de Dados SQL com Java](sql-database-connect-query-java.md) 
  - [Ligar à Base de Dados SQL com o Python](sql-database-connect-query-python.md)
  - [Ligar à Base de Dados SQL com Ruby](sql-database-connect-query-ruby.md)
- Retry exemplos de código lógico:
  - [Ligue-se resilientemente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Ligue-se resilientemente à SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
