---
title: Use Python para consultar uma base de dados
description: Este tópico mostra-lhe como usar python para criar um programa que se conecta a uma base de dados Azure SQL e consulta-lo usando declarações Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 9929c483c2e27983254033e2e26b3b753699260b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758313"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o Python para consultar uma base de dados SQL do Azure

Neste arranque rápido, utiliza a Python para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Uma [base de dados Azure SQL](sql-database-single-database-get-started.md)
  
- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Para instalar Homebrew e Python, o controlador ODBC e o SQLCMD, e o condutor Python para o SQL Server, utilize os passos **1.2**, **1.3**e **2.1** na criação de [aplicações Python utilizando o SQL Server no macOS.](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)

  Para mais informações, consulte o [Condutor Microsoft ODBC no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar python e outros pacotes necessários, use `sudo apt-get install python python-pip gcc g++ build-essential`.

  Para instalar o controlador ODBC, O SQLCMD e o condutor python para o SQL Server, consulte um ambiente para o [desenvolvimento de Pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para mais informações, consulte [o Microsoft ODBC Driver no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Para instalar python, o controlador ODBC e OQLCMD, e o condutor Python para o SQL Server, consulte [um ambiente para o desenvolvimento pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para mais informações, consulte [o Microsoft ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma instância gerida pelo Azure SQL.
>
> Para criar e configurar, utilize o [Portal Azure,](sql-database-managed-instance-get-started.md) [PowerShell,](scripts/sql-database-create-configure-managed-instance-powershell.md)ou [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)em seguida, configurar [no local](sql-database-managed-instance-configure-p2s.md) ou [conectividade VM.](sql-database-managed-instance-configure-vm.md)
>
> Para carregar dados, consulte restaurar com o [FICHEIRO BACPAC](sql-database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base de dados dos [Importadores](sql-database-managed-instance-get-started-restore.md)do Mundo Wide .

Para explorar ainda mais a base de dados Python e a base de dados Azure SQL, consulte as bibliotecas de bases de [dados Azure SQL para Python](/python/api/overview/azure/sql), o [repositório pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e uma [amostra pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Vá às bases de **dados SQL** ou página de **instâncias geridas pela SQL.**

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** .

## <a name="create-code-to-query-your-sql-database"></a>Crie código para consultar a sua base de dados SQL 

1. Num editor de texto, crie um novo ficheiro chamado *sqltest.py*.  
   
1. Adicione o seguinte código. Substitua os seus próprios valores por \<servidor>, \<base de dados>, \<username>e \<password>.
   
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

1. Verifique se as 20 linhas de categoria/produto do top 20 são devolvidas e, em seguida, feche a janela de comando.

## <a name="next-steps"></a>Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controladores Microsoft Python para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro de desenvolvimento python](https://azure.microsoft.com/develop/python/?v=17.23h)

