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
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491967"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Quickstart: Use Python para consultar uma base de dados

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste arranque rápido, você usa Python para ligar à Base de Dados Azure SQL, Azure SQL Gestd Instance, ou base de dados Sinaapse SQL e usar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Uma base de dados onde vai fazer uma consulta.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 e software relacionado
    

    |**Ação**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Instale o controlador ODBC, o SQLCMD e o controlador Python para o SQL Server|Utilize os passos **1.2**, **1.3** e **2.1** na [criação de aplicações Python utilizando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Isto também instalará a instalação Homebrew e Python.       |[Configure um ambiente para o desenvolvimento de pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Configure um ambiente para o desenvolvimento de pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Instalar Python e outros pacotes necessários|    |Utilize `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Informações adicionais|[Controlador ODBC da Microsoft no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Controlador ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Controlador ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Para explorar ainda mais a Python e a base de dados na Base de Dados Azure SQL, consulte [as bibliotecas de bases de dados Azure SQL para Python,](/python/api/overview/azure/sql)o [repositório de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e uma [amostra de pyodbc.](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)

## <a name="create-code-to-query-your-database"></a>Crie código para consultar a sua base de dados 

1. Num editor de texto, crie um novo ficheiro chamado *sqltest.py*.  
   
1. Adicione o seguinte código. Obtenha a informação de ligação da secção pré-requisitos e substitua os seus próprios valores \<server> \<database> \<username> por, e \<password> .
   
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

1. Verifique se as bases de dados e as suas colagens são devolvidas e feche a janela de comando.

## <a name="next-steps"></a>Passos seguintes

- [Desenhe a sua primeira base de dados na Base de Dados Azure SQL](design-first-database-tutorial.md)
- [Controladores Microsoft Python para SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centro de desenvolvimento python](https://azure.microsoft.com/develop/python/?v=17.23h)
