---
title: Utilizar o Python para consultar a Base de Dados SQL do Azure | Microsoft Docs
description: Este tópico mostra-lhe como utilizar Python para criar um programa que se liga a uma base de dados SQL do Azure e a consulta com instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7fbb9265ac12126fb13a26650fbb5d65f3d39260
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65753903"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Início rápido: Utilizar o Python para consultar uma base de dados SQL do Azure

 Este guia de introdução demonstra como utilizar o [Python](https://python.org) para ligar a uma base de dados SQL do Azure e utilizar as instruções Transact-SQL para consultar dados. Para obter mais detalhes sobre o SDK, confira nossos [referência](https://docs.microsoft.com/python/api/overview/azure/sql) documentação, o [repositório do GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e um [pyodbc exemplo](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem o seguinte:

- Uma base de dados SQL do Azure. Pode utilizar um dos seguintes inícios rápidos para criar e, em seguida, configurar uma base de dados na base de dados do Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [regra de firewall do IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade no local do](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|A Adventure Works carregados por início rápido|[Restaurar a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaure ou importar Adventure Works no [BACPAC](sql-database-import.md) ficheiro [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para utilizar a base de dados do Adventure Works. Com uma instância gerida, tem de importar a base de dados do Adventure Works para uma base de dados de instância ou modificar os scripts neste artigo para utilizar a base de dados do Wide World Importers.
  
- Python e software relacionado para o seu sistema operativo:
  
  - **MacOS**: Instale o Homebrew e o Python, instale o controlador ODBC e o SQLCMD e, em seguida, instale o controlador Python para SQL Server. Veja os passos 1.2, 1.3 e 2.1 no [aplicações de Python de criar através do SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Para obter mais informações, consulte [instalar o Microsoft ODBC Driver no Linux e macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Instale o Python e outros pacotes necessários com `sudo apt-get install python python-pip gcc g++ build-essential`. Baixe e instale o controlador ODBC, SQLCMD e o controlador Python para SQL Server. Para obter instruções, consulte [configurar um ambiente de desenvolvimento para pyodbc desenvolvimento Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Instale o Python, o controlador ODBC e o SQLCMD e o controlador Python para SQL Server. Para obter instruções, consulte [configurar um ambiente de desenvolvimento para pyodbc desenvolvimento Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone.

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar a base de dados SQL 

1. Num editor de texto, crie um novo ficheiro designado *sqltest.py*.  
   
1. Adicione o seguinte código. Substitua os seus valores para \<servidor >, \<base de dados >, \<username >, e \<palavra-passe >.
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados de AdventureWorksLT de exemplo, o que pode escolher como origem ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize a consulta SELECT tabelas da sua própria base de dados. 
   
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

1. No prompt de comando, execute o seguinte comando:

   ```cmd
   python sqltest.py
   ```

1. Certifique-se de que as primeiras linhas de categoria/produto 20 são devolvidas, em seguida, feche a janela de comando.

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controladores Python da Microsoft para o SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro de programadores Python](https://azure.microsoft.com/develop/python/?v=17.23h)

