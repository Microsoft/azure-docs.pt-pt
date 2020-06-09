---
title: 'Ligue-se a Azure Databricks de Excel, Python ou R '
description: Saiba como utilizar o controlador Simba para ligar a Azure Databricks ao Excel, Python ou R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.custom: tracking-python
ms.openlocfilehash: 77b9bcf0dad8fa11fad4828649cd75d7df7806d5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561302"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Ligue-se a Azure Databricks de Excel, Python ou R

Neste artigo, aprende a usar o controlador Databricks ODBC para ligar Azure Databricks com o Microsoft Excel, Python ou R. Assim que estabelecer a ligação, pode aceder aos dados em Azure Databricks dos clientes Excel, Python ou R. Também pode utilizar os clientes para analisar os dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks, um cluster Spark, e dados de amostra associados ao seu cluster. Se ainda não tiver estes pré-requisitos, complete o quickstart at [Run a Spark job on Azure Databricks utilizando o portal Azure](quickstart-create-databricks-workspace-portal.md).

* Descarregue o controlador Databricks ODBC da página de descarregamento do [controlador Databricks](https://databricks.com/spark/odbc-driver-download). Instale a versão de 32 bits ou 64 bits dependendo da aplicação de onde pretende ligar-se aos Dados Azure. Por exemplo, para ligar a partir do Excel, instale a versão de 32 bits do controlador. Para ligar a partir de R e Python, instale a versão de 64 bits do controlador.

* Crie um símbolo de acesso pessoal em Databricks. Para obter instruções, consulte [a gestão da Token.](/azure/databricks/dev-tools/api/latest/authentication)

## <a name="set-up-a-dsn"></a>Criar um DSN

Um nome de fonte de dados (DSN) contém a informação sobre uma fonte de dados específica. Um controlador ODBC precisa deste DSN para se ligar a uma fonte de dados. Nesta secção, criou um DSN que pode ser usado com o controlador Databricks ODBC para ligar a Azure Databricks de clientes como Microsoft Excel, Python ou R.

1. Do espaço de trabalho Azure Databricks, navegue até ao cluster Databricks.

    ![Conjunto de databricks aberto](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Conjunto de databricks aberto")

2. No separador **Configuração,** clique no separador **JDBC/ODBC** e copie os valores para **o nome anfitrião do servidor** e caminho **HTTP**. Precisa destes valores para completar os passos deste artigo.

    ![Obtenha a configuração databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obtenha a configuração databricks")

3. No seu computador, inicie a aplicação **ODBC Data Sources** (32-bit ou 64-bit) dependendo da aplicação. Para ligar a partir do Excel, utilize a versão de 32 bits. Para ligar a partir de R e Python, utilize a versão de 64 bits.

    ![Lançar ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Lançar app ODBC")

4. No **separador DSN do utilizador,** clique em **Adicionar**. Na caixa de diálogo **Create New Data Source,** selecione o **Controlador ODBC Simba Spark**e, em seguida, clique em **Terminar**.

    ![Lançar ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Lançar app ODBC")

5. Na caixa de diálogo **do condutor Simba Spark ODBC,** forneça os seguintes valores:

    ![Configurar DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurar DSN")

    A tabela seguinte fornece informações sobre os valores a fornecer na caixa de diálogo.
    
    |Campo  | Valor  |
    |---------|---------|
    |**Nome da Origem de Dados**     | Forneça um nome para a fonte de dados.        |
    |**Anfitriões ou anfitriões**     | Forneça o valor que copiou do espaço de trabalho databricks para *o nome de anfitrião do Servidor*.        |
    |**Porto**     | Insira *443*.        |
    |**Autenticação**  >  **Mecanismo**     | Selecione *o nome de utilizador e a palavra-passe.*        |
    |**Nome de utilizador**     | *Insira o símbolo*.        |
    |**Palavra-passe**     | Introduza o valor simbólico que copiou do espaço de trabalho databricks. |
    
    Execute os seguintes passos adicionais na caixa de diálogo de configuração DSN.
    
    * Clique **em opções HTTP**. Na caixa de diálogo que se abre, cole o valor para *O Caminho HTTP* que copiou do espaço de trabalho databricks. Clique em **OK**.
    * Clique em **opções SSL**. Na caixa de diálogo que se abre, selecione a caixa de verificação **Enable SSL.** Clique em **OK**.
    * Clique **em Teste** para testar a ligação a Azure Databricks. Clique **em OK** para guardar a configuração.
    * Na caixa de diálogo **do administrador de fonte de dados ODBC,** clique em **OK**.

Agora tem o seu DSN preparado. Nas secções seguintes, você usa este DSN para ligar a Azure Databricks de Excel, Python ou R.

## <a name="connect-from-microsoft-excel"></a>Conecte-se a partir do Microsoft Excel

Nesta secção, você retira dados do Azure Databricks para o Microsoft Excel usando o DSN que criou anteriormente. Antes de começar, certifique-se de que tem o Microsoft Excel instalado no seu computador. Pode utilizar uma versão experimental do Excel a partir do link de teste do [Microsoft Excel](https://products.office.com/excel).

1. Abra um livro em branco no Microsoft Excel. A partir da fita **Data,** clique em **Obter Dados.** Clique **em Outras Fontes** e clique em **ODBC**.

    ![Lançamento ODBC do Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Lançamento ODBC do Excel")

2. Na caixa de diálogo **From ODBC,** selecione o DSN que criou anteriormente e, em seguida, clique **em OK**.

    ![Selecione DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Selecione DSN")

3. Se for solicitado para obter credenciais, para que o nome de utilizador **introduza o token**. Para obter senha, forneça o valor simbólico que recuperou do espaço de trabalho databricks.

    ![Fornecer credenciais para Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Selecione DSN")

4. A partir da janela do navegador, selecione a tabela em Databricks que pretende carregar para o Excel e, em seguida, clique em **Carregar**. 

    ![Carregue dta no Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Carregue dta no Excel")

Assim que tiver os dados no seu livro do Excel, poderá realizar operações analíticas sobre o mesmo.

## <a name="connect-from-r"></a>Ligar a partir de R

> [!NOTE]
> Esta secção fornece informações sobre como integrar um cliente R Studio em execução no seu ambiente de trabalho com a Azure Databricks. Para obter instruções sobre como usar o R Studio no cluster Azure Databricks em si, consulte [r studio em Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

Nesta secção, utiliza-se um IDE de idioma R para fazer referência aos dados disponíveis em Azure Databricks. Antes de começar, deve ter o seguinte instalado no computador.

* Um IDE para linguagem R. Este artigo utiliza RStudio para Desktop. Pode instalá-lo a partir do download do [R Studio.](https://www.rstudio.com/products/rstudio/download/)
* Se utilizar o RStudio para desktop como o seu IDE, instale também o Cliente Microsoft R a partir de [https://aka.ms/rclient/](https://aka.ms/rclient/) . 

Abra o RStudio e faça os seguintes passos:

- Referenciar o `RODBC` pacote. Isto permite-lhe ligar-se ao Azure Databricks utilizando o DSN que criou anteriormente.
- Estabelecer uma ligação utilizando o DSN.
- Executar uma consulta SQL sobre os dados em Azure Databricks. No seguinte corte, *radio_sample_data* é uma tabela que já existe em Azure Databricks.
- Efetue algumas operações na consulta para verificar a saída. 

O seguinte código corta-códigos executa estas tarefas:

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

Nesta secção, utiliza-se um Python IDE (como o IDLE) para fazer referência aos dados disponíveis em Azure Databricks. Antes de começar, complete os seguintes pré-requisitos:

* Instale python a partir [daqui.](https://www.python.org/downloads/) A instalação do Python a partir deste link também instala o IDLE.

* A partir de um pedido de comando no computador, instale o `pyodbc` pacote. Execute o seguinte comando:

      pip install pyodbc

Abra o CIO e faça os seguintes passos:

- Importe o `pyodbc` pacote. Isto permite-lhe ligar-se ao Azure Databricks utilizando o DSN que criou anteriormente.
- Estabeleça uma ligação utilizando o DSN que criou anteriormente.
-  Executar uma consulta SQL utilizando a ligação que criou. No seguinte corte, *radio_sample_data* é uma tabela que já existe em Azure Databricks.
- Efetuar operações na consulta para verificar a saída.

O seguinte código corta-códigos executa estas tarefas:

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

* Para saber mais sobre as fontes de onde pode importar dados em Azure Databricks, consulte [fontes de dados da Azure Databricks](/azure/databricks/data/data-sources/index)


