---
title: Use Python para consultar uma base de dados
description: Este tópico mostra-lhe como usar python para criar um programa que se conecta a uma base de dados em Azure SQL Database e consulta-lo usando declarações Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 8286c304cc0ea557430fa3cf0240f1f33b92c838
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054414"
---
# <a name="quickstart-use-python-to-query-a-microsoft-azure-sql-database"></a>Quickstart: Use Python para consultar uma base de dados SQL microsoft Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste arranque rápido, utiliza a Python para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

  || Base de Dados SQL | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP ao nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade a partir de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir do local](../managed-instance/point-to-site-p2s-configure.md) | [Ligar ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macos"></a>[macOS](#tab/macos)

  Para instalar Homebrew e Python, o controlador ODBC e o SQLCMD, e o condutor Python para o SQL Server, utilize os passos **1.2**, **1.3**e **2.1** na criação de [aplicações Python utilizando o SQL Server no macOS.](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)

  Para mais informações, consulte o [Condutor Microsoft ODBC no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar python e outros pacotes necessários, `sudo apt-get install python python-pip gcc g++ build-essential` utilize.

  Para instalar o controlador ODBC, O SQLCMD e o condutor python para o SQL Server, consulte um ambiente para o [desenvolvimento de Pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para mais informações, consulte [o Microsoft ODBC Driver no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Para instalar python, o controlador ODBC e OQLCMD, e o condutor Python para o SQL Server, consulte [um ambiente para o desenvolvimento pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para mais informações, consulte [o Microsoft ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma Instância Gerida Azure SQL.
>
> Para criar e configurar, utilize o [portal Azure,](../managed-instance/instance-create-quickstart.md) [PowerShell,](../managed-instance/scripts/create-configure-managed-instance-powershell.md)ou [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)em seguida, configurar [no local](../managed-instance/point-to-site-p2s-configure.md) ou [conectividade VM.](../managed-instance/connect-vm-instance-configure.md)
>
> Para carregar dados, consulte restaurar com o [FICHEIRO BACPAC](database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base de dados dos [Importadores](../managed-instance/restore-sample-database-quickstart.md)do Mundo Wide .

Para explorar ainda mais a base de dados Python e a base de dados Azure SQL, consulte as bibliotecas de bases de [dados Azure SQL para Python](/python/api/overview/azure/sql), o [repositório pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)e uma [amostra pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Vá para as Bases de **Dados SQL** ou **página SQL Managed Instances.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma Base de Dados SQL Azure ou o nome de servidor totalmente qualificado (ou endereço IP) ao lado do **Host** para um Caso Gerido Azure SQL ou Servidor SQL num VM Azure. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

> [!NOTE]
> Para obter informações de ligação para o Servidor SQL num VM Azure, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-your-sql-database"></a>Crie código para consultar a sua base de dados SQL 

1. Num editor de texto, crie um novo ficheiro chamado *sqltest.py*.  
   
1. Adicione o seguinte código. Substitua os seus próprios valores \<server> por, \<database> e \<username> \<password> .
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados adventureWorksLT da amostra, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize as tabelas da sua própria base de dados na consulta SELECT. 
   
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

1. Num pedido de comando, execute o seguinte comando:

   ```cmd
   python sqltest.py
   ```

1. Verifique se as 20 linhas de categoria/produto do top 20 são devolvidas e, em seguida, feche a janela de comando.

## <a name="next-steps"></a>Próximos passos

- [Desenhe a sua primeira base de dados Azure SQL](design-first-database-tutorial.md)
- [Controladores Microsoft Python para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro de desenvolvimento python](https://azure.microsoft.com/develop/python/?v=17.23h)

