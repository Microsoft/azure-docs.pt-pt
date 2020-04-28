---
title: 'Ligue-se a Tijolos de Dados Azure de Excel, Python ou R '
description: Aprenda a usar o condutor da Simba para ligar os Tijolos de Dados Azure ao Excel, Python ou R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73601937"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ligue-se a Tijolos de Dados Azure de Excel, Python ou R

Neste artigo, aprende-se a utilizar o controlador Databricks ODBC para ligar os Databricks Azure com o microsoft Excel, python ou o idioma R. Assim que estabelecer a ligação, pode aceder aos dados em Tijolos de Dados Azure dos clientes Excel, Python ou R. Também pode utilizar os clientes para analisar mais aprofundadamente os dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks, um cluster Spark, e dados de amostra associados ao seu cluster. Se ainda não tiver estes pré-requisitos, complete o quickstart em [Run a Spark trabalho em Azure Databricks utilizando o portal Azure](quickstart-create-databricks-workspace-portal.md).

* Descarregue o controlador Databricks ODBC da página de [descarregamento do controlador Databricks](https://databricks.com/spark/odbc-driver-download). Instale a versão de 32 bits ou 64 bits dependendo da aplicação de onde pretende ligar-se aos Bricks Azure. Por exemplo, para ligar a partir do Excel, instale a versão de 32 bits do controlador. Para ligar a partir de R e Python, instale a versão de 64 bits do controlador.

* Configurar um sinal de acesso pessoal em Databricks. Para obter instruções, consulte a [gestão token](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Criar um DSN

Um nome de origem de dados (DSN) contém a informação sobre uma fonte específica de dados. Um condutor da ODBC precisa deste DSN para se ligar a uma fonte de dados. Nesta secção, configura um DSN que pode ser utilizado com o controlador Databricks ODBC para ligar a Azure Databricks de clientes como Microsoft Excel, Python ou R.

1. Do espaço de trabalho Azure Databricks, navegue até ao cluster Databricks.

    ![Cluster de Tijolos de Dados Abertos](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Cluster de Tijolos de Dados Abertos")

2. No separador **Configuração,** clique no separador **JDBC/ODBC** e copie os valores para o nome de **anfitrião do servidor** e o **caminho http**. Precisa destes valores para completar os passos deste artigo.

    ![Obter configuração de Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obter configuração de Databricks")

3. No seu computador, inicie a aplicação **ODBC Data Sources** (32 bits ou 64 bits) dependendo da aplicação. Para ligar o Excel, utilize a versão de 32 bits. Para ligar a partir de R e Python, use a versão de 64 bits.

    ![Lançamento ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Lançar app ODBC")

4. No separador **DSN** do utilizador, clique em **Adicionar**. Na caixa de diálogo **Create New Data Source,** selecione o **controlador Simba Spark ODBC**, e, em seguida, clique em **Terminar**.

    ![Lançamento ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Lançar app ODBC")

5. Na caixa de diálogo **simba Spark ODBC Driver,** forneça os seguintes valores:

    ![Configurar DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurar DSN")

    A tabela seguinte fornece informações sobre os valores a fornecer na caixa de diálogo.
    
    |Campo  | Valor  |
    |---------|---------|
    |**Nome da Origem de Dados**     | Forneça um nome para a fonte de dados.        |
    |**Hospedeiro(s)**     | Forneça o valor que copiou do espaço de trabalho databricks para *o nome de anfitrião do Servidor*.        |
    |**Porto**     | *Insira 443*.        |
    |**Authentication** > **Mecanismo de** autenticação     | Selecione *o nome do utilizador e a palavra-passe*.        |
    |**Nome de utilizador**     | Insira *o símbolo.*        |
    |**Palavra-passe**     | Introduza o valor simbólico que copiou do espaço de trabalho databricks. |
    
    Efetue os seguintes passos adicionais na caixa de diálogo de configuração DSN.
    
    * Clique em **OPÇões HTTP**. Na caixa de diálogo que se abre, colhe o valor para *http Path* que copiou do espaço de trabalho databricks. Clique em **OK**.
    * Clique em **opções SSL**. Na caixa de diálogo que se abre, selecione a caixa de verificação **Enable SSL.** Clique em **OK**.
    * Clique em **Testar** para testar a ligação aos Tijolos de Dados Azure. Clique **em OK** para salvar a configuração.
    * Na caixa de diálogo do Administrador de Origem de **Dados da ODBC,** clique em **OK**.

Agora tens o teu DSN preparado. Nas secções seguintes, utiliza este DSN para ligar aos Tijolos de Dados Azure do Excel, Python ou R.

## <a name="connect-from-microsoft-excel"></a>Ligue-se a partir do Microsoft Excel

Nesta secção, retira dados dos Ficheiros Azure para o Microsoft Excel utilizando o DSN que criou anteriormente. Antes de começar, certifique-se de que tem o Microsoft Excel instalado no seu computador. Pode utilizar uma versão experimental do Excel a partir do [link de teste Microsoft Excel](https://products.office.com/excel).

1. Abra um livro em branco no Microsoft Excel. A partir da fita **Data,** clique em **Obter Dados**. Clique **em Outras Fontes** e, em seguida, clique em **ODBC**.

    ![Lançamento da ODBC do Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Lançamento da ODBC do Excel")

2. Na caixa de diálogo **From ODBC,** selecione o DSN que criou anteriormente e, em seguida, clique em **OK**.

    ![Selecione DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Selecione DSN")

3. Se for solicitado credenciais, para **token**o nome do utilizador insira . Para obter uma palavra-passe, forneça o valor simbólico que recuperou do espaço de trabalho databricks.

    ![Fornecer credenciais para Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Selecione DSN")

4. A partir da janela do navegador, selecione a tabela em Databricks que pretende carregar para Excel e, em seguida, clique em **Carregar**. 

    ![Carregue dta em Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Carregue dta em Excel")

Assim que tiver os dados no seu livro Excel, pode efetuar operações analíticas.

## <a name="connect-from-r"></a>Ligar a partir de R

> [!NOTE]
> Esta secção fornece informações sobre como integrar um cliente R Studio a correr no seu ambiente de trabalho com os Azure Databricks. Para obter instruções sobre como utilizar o Estúdio R no próprio cluster Azure Databricks, consulte [o Estúdio R em Tijolos de Dados Azure](/azure/databricks/spark/latest/sparkr/rstudio).

Nesta secção, utiliza um IDE de linguagem R para os dados de referência disponíveis nos Databricks do Azure. Antes de começar, deve ter o seguinte instalado no computador.

* Um IDE para a linguagem R. Este artigo usa o RStudio para desktop. Pode instalá-lo a partir do download do [R Studio.](https://www.rstudio.com/products/rstudio/download/)
* Se utilizar o RStudio para desktop como iDE, [https://aka.ms/rclient/](https://aka.ms/rclient/)instale também o Cliente Microsoft R a partir de . 

Abra o RStudio e faça os seguintes passos:

- Referenciar `RODBC` o pacote. Isto permite-lhe ligar-se aos Tecidos Azure utilizando o DSN que criou anteriormente.
- Estabelecer uma ligação utilizando o DSN.
- Ecorra uma consulta SQL sobre os dados em Azure Databricks. No seguinte corte, *radio_sample_data* é uma tabela que já existe em Azure Databricks.
- Efetue algumas operações na consulta para verificar a saída. 

O seguinte código executa estas tarefas:

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

## <a name="connect-from-python"></a>Conecte-se a partir de Python

Nesta secção, utiliza um Python IDE (como o IDLE) para os dados de referência disponíveis nos Tijolos de Dados Do Azure. Antes de começar, preencha os seguintes pré-requisitos:

* Instale Python [daqui.](https://www.python.org/downloads/) Instalar python a partir deste link também instala idle.

* A partir de um pedido `pyodbc` de comando no computador, instale a embalagem. Execute o seguinte comando:

      pip install pyodbc

Abra o IDLE e faça os seguintes passos:

- Importar `pyodbc` o pacote. Isto permite-lhe ligar-se aos Tecidos Azure utilizando o DSN que criou anteriormente.
- Estabeleça uma ligação utilizando o DSN que criou anteriormente.
-  Faça uma consulta SQL utilizando a ligação que criou. No seguinte corte, *radio_sample_data* é uma tabela que já existe em Azure Databricks.
- Efetuar operações na consulta para verificar a saída.

O seguinte código executa estas tarefas:

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

## <a name="next-steps"></a>Passos seguintes

* Para saber sobre fontes de onde pode importar dados para Os Databricks Do Azure, consulte fontes de [dados de Tijolos de Dados Azure](/azure/databricks/data/data-sources/index)


