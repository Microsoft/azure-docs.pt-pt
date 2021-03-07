---
title: Ligar e consultar
description: Links para Azure SQL Database quickstarts mostrando como conectar e consultar Azure SQL Database, e Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: a9f9e03227bfb75d94ed79cdf858278e2efe4f31
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440399"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL Database e Azure SQL Managed Instance connect and consultaer artigos
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O documento que se segue inclui links para exemplos Azure que mostram como conectar e consultar a Base de Dados Azure SQL e Azure SQL Managed Instance. Para algumas recomendações relacionadas para a Segurança do Nível de Transporte, consulte [considerações de TLS para a conectividade da base de dados.](#tls-considerations-for-database-connectivity)

## <a name="quickstarts"></a>Guias de Início Rápido

| Início Rápido | Descrição |
|---|---|
|[O SQL Server Management Studio](connect-query-ssms.md)|Este quickstart demonstra como utilizar o SSMS para ligar a uma base de dados e, em seguida, utilizar declarações Transact-SQL para consultar, inserir, atualizar e apagar dados na base de dados.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Este quickstart demonstra como usar o Azure Data Studio para se conectar a uma base de dados e, em seguida, usar declarações de Transact-SQL (T-SQL) para criar o TutorialDB usado nos tutoriais do Azure Data Studio.|
|[Portal do Azure](connect-query-portal.md)|Este quickstart demonstra como utilizar o editor de consulta para ligar a uma base de dados (apenas Azure SQL Database), e, em seguida, utilizar declarações Transact-SQL para consultar, inserir, atualizar e apagar dados na base de dados.|
|[Visual Studio Code](connect-query-vscode.md)|Este quickstart demonstra como usar o Código do Estúdio Visual para ligar a uma base de dados e, em seguida, usar declarações Transact-SQL para consultar, inserir, atualizar e apagar dados na base de dados.|
|[.NET com o Visual Studio](connect-query-dotnet-visual-studio.md)|Este quickstart demonstra como usar a estrutura .NET para criar um programa C# com o Visual Studio para ligar a uma base de dados e usar declarações Transact-SQL para consultar dados.|
|[.NET core](connect-query-dotnet-core.md)|Este quickstart demonstra como utilizar o Núcleo .NET no Windows/Linux/macOS para criar um programa C# para ligar a uma base de dados e utilizar declarações Transact-SQL para consultar dados.|
|[Ir](connect-query-go.md)|Este quickstart demonstra como usar o Go para ligar a uma base de dados. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Java](connect-query-java.md)|Este quickstart demonstra como usar Java para ligar a uma base de dados e, em seguida, usar declarações Transact-SQL para consultar dados.|
|[Node.js](connect-query-nodejs.md)|Este quickstart demonstra como usar Node.js para criar um programa para ligar a uma base de dados e usar declarações Transact-SQL para consultar dados.|
|[PHP](connect-query-php.md)|Este quickstart demonstra como usar PHP para criar um programa para ligar a uma base de dados e usar declarações Transact-SQL para consultar dados.|
|[Python](connect-query-python.md)|Este quickstart demonstra como usar python para ligar a uma base de dados e usar declarações Transact-SQL para consultar dados. |
|[Ruby](connect-query-ruby.md)|Este quickstart demonstra como usar a Ruby para criar um programa para ligar a uma base de dados e usar declarações Transact-SQL para consultar dados.|
|[R](connect-query-r.md)|Este quickstart demonstra como usar R com Azure SQL Database Machine Learning Services para criar um programa para ligar a uma base de dados na Base de Dados Azure SQL e usar declarações Transact-SQL para obter dados de consulta.|
|||

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a **página SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para a base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Obtenha informações de ligação ADO.NET (opcional - apenas base de dados SQL)

1. Navegue para a lâmina da base de dados no portal Azure e, em **Definições,** selecione **cadeias de ligação**.

2. Reveja a cadeia de ligação **ADO.NET** completa.

    ![Cadeia de ligação de ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Copie o **fio de** ligação ADO.NET se pretender usá-lo.

## <a name="tls-considerations-for-database-connectivity"></a>Considerações TLS para conectividade de base de dados

A Segurança da Camada de Transporte (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para se ligar a bases de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance. Não é necessária qualquer configuração especial. Para todas as ligações a uma instância do SQL Server, uma base de dados na Base de Dados Azure SQL ou uma instância de Azure SQL Managed Instance, recomendamos que todas as aplicações desafaçam as seguintes configurações, ou os seus equivalentes:

- **Encriptar = Ativado**
- **TrustServerCertificate = Desativado**

Alguns sistemas utilizam palavras-chave diferentes mas equivalentes para as palavras-chave de configuração. Estas configurações garantem que o controlador do cliente verifica a identidade do certificado TLS recebido do servidor.

Recomendamos também que desative a TLS 1.1 e 1.0 no cliente, se tiver de estar em conformidade com a Data Security Standard (DSS) da Payment Card Industry (PCI).

Os controladores que não são da Microsoft podem não utilizar o TLS por predefinição. Isto pode ser um fator ao ligar-se à Base de Dados Azure SQL ou à Instância Gerida Azure SQL. As aplicações com controladores incorporados podem não permitir que controle estas definições de ligação. Recomendamos que analise a segurança de tais aplicações e controladores antes de os utilizar em sistemas que interajam com dados confidenciais.

## <a name="libraries"></a>Bibliotecas

Pode utilizar várias bibliotecas e estruturas para ligar à Base de Dados Azure SQL ou à Azure SQL Managed Instance. Confira os [nossos tutoriais Get iniciou-se](https://aka.ms/sqldev) rapidamente com linguagens de programação como C#, Java, Node.js, PHP e Python. Em seguida, construa uma aplicação utilizando o SQL Server no Linux ou Windows ou Docker no macOS.

A tabela que se segue lista bibliotecas de conectividade ou *controladores* que as aplicações do cliente podem usar a partir de uma variedade de idiomas para se conectar e usar o SQL Server em funcionamento no local ou na nuvem. Pode usá-los em Linux, Windows ou Docker e usá-los para ligar à Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics.

| Linguagem | Plataforma | Recursos adicionais | Download | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Transferência](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Controlador JDBC da Microsoft para SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Transferência](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Controlador PHP SQL para SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Transferência](/sql/connect/php/download-drivers-php-sql-server) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js controlador para o SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Instalar](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Condutor python SQL](/sql/connect/python/python-driver-for-sql-server/) | Instalar escolhas: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Controlador de rubi para SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Instalar](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Controlador ODBC da Microsoft para SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Transferência](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

A tabela que se segue lista exemplos de quadros de mapeamento relacional de objetos (ORM) e quadros web que as aplicações do cliente podem usar com SQL Server, Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse Analytics. Pode utilizar as estruturas em Linux, Windows ou Docker.

| Linguagem | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Operação de hibernação](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquente)](https://laravel.com/docs/eloquent)<br>[Doutrina](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Orm de sequelaização](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Rubi nos trilhos](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a arquitetura de conectividade, veja [Arquitetura de Conectividade de Base de Dados SQL do Azure](connectivity-architecture.md).
- Encontre [controladores SQL Server](/sql/connect/sql-connection-libraries/) que são usados para se conectarem a partir de aplicações do cliente.
- Ligue à Base de Dados Azure SQL ou à Instância Gerida Azure SQL:
  - [Conecte-se e consulta usando .NET (C#)](connect-query-dotnet-core.md)
  - [Conecte-se e consultar PHP](connect-query-php.md)
  - [Conecte-se e consultar Node.js](connect-query-nodejs.md)
  - [Conecte-se e consultar o Java](connect-query-java.md)
  - [Conecte-se e consultar o Python](connect-query-python.md)
  - [Conecte-se e consultar o Ruby](connect-query-ruby.md)
- Relem os exemplos de código lógico:
  - [Conecte-se resilientemente com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Conecte-se resilientemente com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
