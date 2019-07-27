---
title: Utilizar o Python para consultar a Base de Dados SQL do Azure | Microsoft Docs
description: Este tópico mostra como usar o Python para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 89e67fd58f6cfc54e21406e9385e7ca5e5bc1d17
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569153"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Início rápido: Utilizar o Python para consultar uma base de dados SQL do Azure

 Este guia de introdução demonstra como utilizar o [Python](https://python.org) para ligar a uma base de dados SQL do Azure e utilizar as instruções Transact-SQL para consultar dados. Para obter mais detalhes do SDK, Confira nossa documentação de [referência](https://docs.microsoft.com/python/api/overview/azure/sql) , o [repositório GitHub do pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e um [exemplo de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem o seguinte:

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
  
- Python e software relacionado para seu sistema operacional:
  
  - **MacOS**: Instale o homebrew e o Python, instale o driver ODBC e o SQLCMD e, em seguida, instale o driver Python para SQL Server. Consulte as etapas 1,2, 1,3 e 2,1 em [criar aplicativos Python usando o SQL Server no MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Para obter mais informações, consulte [instalar o Microsoft ODBC driver no Linux e no MacOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Instale o Python e outros pacotes necessários `sudo apt-get install python python-pip gcc g++ build-essential`com o. Baixe e instale o driver ODBC, SQLCMD e o driver Python para SQL Server. Para obter instruções, consulte [configurar um ambiente de desenvolvimento para o desenvolvimento de Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Instale o Python, o driver ODBC e o SQLCMD e o driver Python para SQL Server. Para obter instruções, consulte [configurar um ambiente de desenvolvimento para o desenvolvimento de Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu banco de dados SQL 

1. Em um editor de texto, crie um novo arquivo chamado *sqltest.py*.  
   
1. Adicione o seguinte código. Substitua seus próprios valores para \<Server >, \<Database >, \<username > e \<password >.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados AdventureWorksLT de exemplo, que você pode escolher como fonte ao criar o banco de dado. Se o seu banco de dados tiver um dado diferente, use tabelas do seu próprio banco na consulta SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Executar o código

1. Em um prompt de comando, execute o seguinte comando:

   ```cmd
   python sqltest.py
   ```

1. Verifique se as 20 principais linhas de categoria/produto são retornadas e feche a janela de comando.

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Drivers Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)

