---
title: Use Python para consultar uma base de dados
description: Este tópico mostra-lhe como usar python para criar um programa que se conecta a uma base de dados na Base de Dados Azure SQL e questioná-lo usando declarações Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5bad2293078711456ff06d998c43b87ecc1c9b76
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674276"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Use Python para consultar uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste arranque rápido, você usa Python para ligar à Base de Dados Azure SQL ou Azure SQL Managed Instance, e usar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macos"></a>[macOS](#tab/macos)

  Para instalar o Homebrew e o Python, o controlador ODBC e o SQLCMD, e o controlador Python para o SQL Server, utilize os passos **1.2**, **1.3** e **2.1** na [criação de aplicações Python utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Para mais informações, consulte [o controlador microsoft ODBC no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar python e outros pacotes necessários, utilize `sudo apt-get install python python-pip gcc g++ build-essential` .

  Para instalar o controlador ODBC, SQLCMD, e o controlador Python para o SQL Server, consulte [configurar um ambiente para o desenvolvimento de pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para mais informações, consulte [o controlador da Microsoft ODBC no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Para instalar python, o controlador ODBC e SQLCMD, e o controlador Python para o SQL Server, consulte [configurar um ambiente para o desenvolvimento de pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para mais informações, consulte [o controlador microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
Para explorar ainda mais a Python e a base de dados na Base de Dados Azure SQL, consulte [as bibliotecas de bases de dados Azure SQL para Python,](/python/api/overview/azure/sql)o [repositório de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e uma [amostra de pyodbc.](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Aceda à página **SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para a base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Crie código para consultar a sua base de dados 

1. Num editor de texto, crie um novo ficheiro chamado *sqltest.py*.  
   
1. Adicione o seguinte código. Substitua os seus próprios valores \<server> \<database> \<username> por, e \<password> .
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza a amostra de dados AdventureWorksLT, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize tabelas da sua própria base de dados na consulta SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Executar o código

1. Com um pedido de comando, executar o seguinte comando:

   ```cmd
   python sqltest.py
   ```

1. Verifique se as linhas top 20 categoria/produto são devolvidas e, em seguida, feche a janela de comando.

## <a name="next-steps"></a>Passos seguintes

- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Controladores Microsoft Python para SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centro de desenvolvimento python](https://azure.microsoft.com/develop/python/?v=17.23h)
