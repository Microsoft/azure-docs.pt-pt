---
title: 'Ligar ao Azure Databricks a partir do Excel, Python ou R '
description: Saiba como utilizar o controlador de Simba para ligar o Azure Databricks para o Excel, Python ou R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: 1805f04d7833dea180847defadd865cb23e9df62
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340861"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ligar ao Azure Databricks a partir do Excel, Python ou R

Neste artigo, irá aprender a utilizar o controlador ODBC do Databricks para ligar o Azure Databricks com a linguagem R, Python ou Microsoft Excel. Depois de estabelecer a ligação, pode acessar os dados no Azure Databricks dos clientes Excel, Python ou R. Também pode utilizar os clientes para analisar mais aprofundadamente os dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter uma área de trabalho do Azure Databricks, um cluster do Spark e os dados de exemplo associados com o seu cluster. Se ainda não tiver estes pré-requisitos, conclua o guia de introdução ao [executar uma tarefa do Spark no Azure Databricks com o portal do Azure](quickstart-create-databricks-workspace-portal.md).

* Transferir o controlador ODBC do Databricks partir [página de transferência de driver do Databricks](https://databricks.com/spark/odbc-driver-download). Instale a versão de 32 bits ou 64 bits, dependendo da aplicação a partir do qual pretende ligar ao Azure Databricks. Por exemplo, para ligar a partir do Excel, instale a versão de 32 bits do driver. Para ligar a partir de R e Python, instale a versão de 64 bits do driver.

* Configure um token de acesso pessoal no Databricks. Para obter instruções, consulte [Token gestão](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Configurar um DSN

Um nome de origem de dados (DSN) contém as informações sobre uma origem de dados específica. Um controlador ODBC tem este DSN para ligar a uma origem de dados. Nesta secção, configurou um DSN que pode ser utilizado com o controlador ODBC do Databricks, para ligar ao Azure Databricks a partir de clientes, como o Microsoft Excel, Python ou R.

1. A área de trabalho do Azure Databricks, navegue para o cluster do Databricks.

    ![Cluster do Databricks Open](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "cluster do Databricks aberto")

2. Sob o **Configuration** separador, clique no **JDBC/ODBC** separador e copie os valores para **nome de anfitrião do servidor** e **caminho HTTP**. Vai precisar destes valores para concluir os passos neste artigo.

    ![Obter configuração de Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks obter configuração")

3. No seu computador, inicie **origens de dados de ODBC** aplicação (32 bits ou 64 bits), dependendo do aplicativo. Para ligar a partir do Excel, utilize a versão de 32 bits. Para ligar a partir de R e Python, utilize a versão de 64 bits.

    ![Inicie o ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "iniciar a aplicação ODBC")

4. Sob o **utilizador DSN** separador, clique em **Add**. Na **criar nova origem de dados** caixa de diálogo, selecione a **Simba Spark ODBC Driver**e, em seguida, clique em **concluir**.

    ![Inicie o ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "iniciar a aplicação ODBC")

5. Na **Simba Spark ODBC Driver** diálogo caixa, forneça os valores seguintes:

    ![Configurar o DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "configurar DSN")

    A tabela seguinte fornece informações sobre os valores a fornecer na caixa de diálogo.
    
    |Campo  | Value  |
    |---------|---------|
    |**Nome da origem de dados**     | Forneça um nome para a origem de dados.        |
    |**Host(s)**     | Forneça o valor que copiou a partir da área de trabalho do Databricks para *nome de anfitrião do servidor*.        |
    |**Porta**     | Enter *443*.        |
    |**Autenticação** > **mecanismo**     | Selecione *nome de utilizador e palavra-passe*.        |
    |**Nome de utilizador**     | Enter *token*.        |
    |**Palavra-passe**     | Introduza o valor do token que copiou a partir da área de trabalho do Databricks. |
    
    Execute os seguintes passos adicionais na caixa de diálogo de configuração de DSN.
    
    * Clique em **opções de HTTP**. Na caixa de diálogo que se abre, cole o valor para *caminho HTTP* que copiou da área de trabalho do Databricks. Clique em **OK**.
    * Clique em **as opções de SSL**. Na caixa de diálogo que se abre, selecione o **ativar SSL** caixa de verificação. Clique em **OK**.
    * Clique em **testar** para testar a ligação ao Azure Databricks. Clique em **OK** para guardar a configuração.
    * Na **administrador de fonte de dados de ODBC** caixa de diálogo, clique em **OK**.

Tem agora o DSN configurar. As secções seguintes, vai utilizar este DSN para ligar ao Azure Databricks a partir do Excel, Python ou R.

## <a name="connect-from-microsoft-excel"></a>Ligar a partir do Microsoft Excel

Nesta seção, extrair dados do Azure Databricks para o Microsoft Excel usando o DSN que criou anteriormente. Antes de começar, certifique-se de que tem o Microsoft Excel instalado no seu computador. Pode utilizar uma versão de avaliação do Excel a partir do [link de avaliação do Microsoft Excel](https://products.office.com/excel).

1. Abra um livro em branco no Microsoft Excel. Partir do **dados** Friso, clique em **obter dados**. Clique em **de outras origens** e, em seguida, clique em **do ODBC**.

    ![Inicie o ODBC do Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "iniciar ODBC do Excel")

2. Na **de ODBC** caixa de diálogo, selecione o DSN que criou anteriormente e clique em **OK**.

    ![Select DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Select DSN")

3. Se lhe forem pedidas as credenciais, no nome de utilizador introduza **token**. Palavra-passe, forneça o valor do token obtidos a partir da área de trabalho do Databricks.

    ![Forneça credenciais para o Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "selecione DSN")

4. Na janela do navegador, selecione a tabela no Databricks que pretende carregar para o Excel e, em seguida, clique em **carregar**. 

    ![Carregar o dta para Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "dta de carga para o Excel")

Depois de ter os dados no seu livro do Excel, pode efetuar operações de análises no mesmo.

## <a name="connect-from-r"></a>Ligar a partir de R

> [!NOTE]
> Esta seção fornece informações sobre como integrar um cliente de R Studio em execução no seu ambiente de trabalho com o Azure Databricks. Para obter instruções sobre como utilizar o R Studio o próprio cluster do Azure Databricks, veja [R Studio no Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

Nesta secção, vai utilizar uma linguagem de R IDE para dados de referência disponíveis no Azure Databricks. Antes de começar, tem de ter o seguinte instalado no computador.

* Um IDE para a linguagem R. Este artigo utiliza o RStudio para ambiente de trabalho. Pode instalá-lo partir [download do R Studio](https://www.rstudio.com/products/rstudio/download/).
* Se utilizar o r Studio para a área de trabalho como seu IDE, também instalar a partir do Microsoft R Client [ https://aka.ms/rclient/ ](https://aka.ms/rclient/). 

Abra o RStudio e siga os passos abaixo:

- Referência a `RODBC` pacote. Isto permite-lhe ligar ao Azure Databricks com o DSN que criou anteriormente.
- Estabelece uma ligação com o DSN.
- Execute uma consulta SQL em dados no Azure Databricks. No seguinte fragmento *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Realizar algumas operações na consulta para verificar a saída. 

O fragmento de código seguinte executa estas tarefas:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Ligar a partir de Python

Nesta secção, vai utilizar um IDE de Python (por exemplo, o IDLE) para dados de referência disponíveis no Azure Databricks. Antes de começar, conclua os seguintes pré-requisitos:

* Instalar o Python a partir [aqui](https://www.python.org/downloads/). Instalar o Python a partir desta ligação também instala a INATIVA.

* Num prompt de comando no computador, instalar o `pyodbc` pacote. Execute o seguinte comando:

      pip install pyodbc

Abra INATIVA e siga os passos abaixo:

- Importar o `pyodbc` pacote. Isto permite-lhe ligar ao Azure Databricks com o DSN que criou anteriormente.
- Estabelece uma ligação com o DSN que criou anteriormente.
-  Execute uma consulta SQL com a ligação que criou. No seguinte fragmento *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Realizar operações na consulta para verificar a saída.

O fragmento de código seguinte executa estas tarefas:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre as origens de onde pode importar dados para o Azure Databricks, veja [origens de dados para o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


