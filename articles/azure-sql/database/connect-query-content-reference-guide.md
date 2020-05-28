---
title: Referência de conteúdo de ligação e consulta
description: Uma referência da Base de Dados Azure SQL começa a mostrar-lhe como se conectar e consultar a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: ea4b201cd9ad6fa295bbccafe445733aadcf31c1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054318"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Quickstarts: Azure SQL Database connect and consulta
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O seguinte documento inclui links para exemplos azure que mostram como ligar e consultar a Base de Dados Azure SQL. Fornece também algumas recomendações para Segurança a Nível de Transporte.

## <a name="quickstarts"></a>Guias de Início Rápido

| |  |
|---|---|
|[Estúdio de Gestão de Servidores SQL](connect-query-ssms.md)|Este quickstart demonstra como usar sSMS para ligar à Base de Dados Azure SQL e, em seguida, usar declarações Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Este quickstart demonstra como usar o Azure Data Studio para se conectar à Base de Dados Azure SQL e, em seguida, usar declarações transact-SQL (T-SQL) para criar o TutorialDB usado nos tutoriais do Estúdio de Dados Azure.|
|[Portal do Azure](connect-query-portal.md)|Este início rápido demonstra como utilizar o Editor de consultas para ligar a uma base de dados SQL e, em seguida, utilizar as instruções de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Visual Studio Code](connect-query-vscode.md)|Este quickstart demonstra como usar o Código do Estúdio Visual para ligar à Base de Dados Azure SQL e, em seguida, usar declarações Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[.NET com o Visual Studio](connect-query-dotnet-visual-studio.md)|Este quickstart demonstra como usar a estrutura .NET para criar um programa C# com o Visual Studio para ligar à Base de Dados Azure SQL e usar declarações transact-SQL para consultar dados.|
|[núcleo .NET](connect-query-dotnet-core.md)|Este quickstart demonstra como usar o Núcleo .NET no Windows/Linux/macOS para criar um programa C# para ligar à Base de Dados Azure SQL e utilizar declarações Transact-SQL para consultar dados.|
|[Ir](connect-query-go.md)|Este quickstart demonstra como usar o Go para ligar à Base de Dados Azure SQL. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Java](connect-query-java.md)|Este quickstart demonstra como usar a Java para se ligar à Base de Dados Azure SQL e, em seguida, usar declarações Transact-SQL para consultar dados.|
|[Nó.js](connect-query-nodejs.md)|Este quickstart demonstra como usar o Node.js para criar um programa de ligação à Base de Dados Azure SQL e usar declarações Transact-SQL para consultar dados.|
|[PHP](connect-query-php.md)|Este quickstart demonstra como usar PHP para criar um programa de ligação à Base de Dados Azure SQL e usar declarações Transact-SQL para consultar dados.|
|[Python](connect-query-python.md)|Este quickstart demonstra como usar python para ligar à Base de Dados Azure SQL e usar declarações Transact-SQL para consultar dados. |
|[Ruby](connect-query-ruby.md)|Este quickstart demonstra como usar a Ruby para criar um programa de ligação à Base de Dados Azure SQL e usar declarações transact-SQL para consultar dados.|
|[R](connect-query-r.md)|Este quickstart demonstra como usar R com Serviços de Machine Learning de Base de Dados Azure SQL para criar um programa de ligação à Base de Dados Azure SQL e usar declarações Transact-SQL para consultar dados.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerações de TLS para conectividade de Base de Dados SQL

A Transport Layer Security (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para ligar à Base de Dados SQL do Azure. Não é necessária qualquer configuração especial. Para todas as ligações ao SQL Server, Azure SQL Database ou Azure SQL Managed Instance, recomendamos que todas as aplicações estabeleçam as seguintes configurações, ou os seus equivalentes:

- **Encriptar = Ativado**
- **TrustServerCertificate = Desativado**

Alguns sistemas utilizam palavras-chave diferentes mas equivalentes para as palavras-chave de configuração. Estas configurações garantem que o controlador do cliente verifica a identidade do certificado TLS recebido do servidor.

Recomendamos também que desative a TLS 1.1 e 1.0 no cliente, se tiver de estar em conformidade com a Data Security Standard (DSS) da Payment Card Industry (PCI).

Os controladores que não são da Microsoft podem não utilizar o TLS por predefinição. Isto poderá ser um fator ao ligar à Base de Dados SQL do Azure. As aplicações com controladores incorporados podem não permitir que controle estas definições de ligação. Recomendamos que analise a segurança de tais aplicações e controladores antes de os utilizar em sistemas que interajam com dados confidenciais.

## <a name="libraries"></a>Bibliotecas

Pode utilizar várias bibliotecas e estruturas para se ligar à Base de Dados Azure SQL ou à Instância Gerida Azure SQL. Confira os [nossos tutoriais Get started](https://aka.ms/sqldev) para começar rapidamente com linguagens de programação como C#, Java, Node.js, PHP e Python. Em seguida, construa uma aplicação utilizando o SQL Server no Linux ou Windows ou Docker no macOS.

A tabela seguinte lista bibliotecas de conectividade ou *controladores* que as aplicações do cliente podem usar a partir de uma variedade de idiomas para ligar e usar o SQL Server em execução no local ou na nuvem. Pode usá-los em Linux, Windows ou Docker e usá-los para ligar à Base de Dados Azure SQL e ao Armazém de Dados Azure SQL.

| Linguagem | Plataforma | Recursos adicionais | Download | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para O Servidor SQL](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Transferir](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Controlador Microsoft JDBC para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Transferir](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Condutor PHP SQL para Servidor SQL](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Transferir](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Condutor de Node.js para SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Condutor Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Instale escolhas: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Condutor de rubi para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Controlador Microsoft ODBC para SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Transferir](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

A tabela que se segue lista exemplos de quadros de mapeamento relacional de objetos (ORM) e quadros web que as aplicações do cliente podem usar com o SQL Server, Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse Analytics. Pode utilizar as estruturas em Linux, Windows ou Docker.

| Linguagem | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernar ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquente)](https://laravel.com/docs/eloquent)<br>[Doutrina](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Rubi nos trilhos](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a arquitetura de conectividade, veja [Arquitetura de Conectividade de Base de Dados SQL do Azure](connectivity-architecture.md).
- Encontre [controladores SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) que são usados para se conectarem a partir de aplicações de clientes
- Ligue-se à Base de Dados SQL:
  - [Ligar à Base de Dados SQL com o .NET (C#)](connect-query-dotnet-core.md)
  - [Ligar à Base de Dados SQL com PHP](connect-query-php.md)
  - [Ligar à Base de Dados SQL com o Node.js](connect-query-nodejs.md)
  - [Ligar à Base de Dados SQL com Java](connect-query-java.md)
  - [Ligar à Base de Dados SQL com o Python](connect-query-python.md)
  - [Ligar à Base de Dados SQL com Ruby](connect-query-ruby.md)
- Retry exemplos de código lógico:
  - [Ligue-se resilientemente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Ligue-se resilientemente à SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
