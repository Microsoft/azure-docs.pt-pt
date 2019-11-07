---
title: 'Conectar-se a Azure Databricks do Excel, Python ou R '
description: Saiba como usar o driver Simba para se conectar Azure Databricks ao Excel, Python ou R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601937"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Conectar-se a Azure Databricks do Excel, Python ou R

Neste artigo, você aprenderá a usar o driver ODBC do databricks para se conectar Azure Databricks com o Microsoft Excel, Python ou linguagem R. Depois de estabelecer a conexão, você pode acessar os dados em Azure Databricks dos clientes Excel, Python ou R. Você também pode usar os clientes para analisar ainda mais os dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks, um cluster Spark e dados de exemplo associados ao cluster. Se você ainda não tiver esses pré-requisitos, conclua o guia de início rápido em [executar um trabalho do Spark em Azure Databricks usando o portal do Azure](quickstart-create-databricks-workspace-portal.md).

* Baixe o driver ODBC do databricks da [página de download do driver do databricks](https://databricks.com/spark/odbc-driver-download). Instale a versão de 32 bits ou 64 bits, dependendo do aplicativo do qual você deseja se conectar ao Azure Databricks. Por exemplo, para se conectar do Excel, instale a versão de 32 bits do driver. Para se conectar de R e Python, instale a versão de 64 bits do driver.

* Configure um token de acesso pessoal no databricks. Para obter instruções, consulte [Gerenciamento de token](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Configurar um DSN

Um DSN (nome da fonte de dados) contém as informações sobre uma fonte de dados específica. Um driver ODBC precisa desse DSN para se conectar a uma fonte de dados. Nesta seção, você configura um DSN que pode ser usado com o driver ODBC do databricks para se conectar a Azure Databricks de clientes como Microsoft Excel, Python ou R.

1. No espaço de trabalho Azure Databricks, navegue até o cluster do databricks.

    ![Abrir cluster do databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Abrir cluster do databricks")

2. Na guia **configuração** , clique na guia **JDBC/ODBC** e copie os valores para o **nome de host do servidor** e o **caminho http**. Você precisa desses valores para concluir as etapas neste artigo.

    ![Obter configuração do databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obter configuração do databricks")

3. No computador, inicie o aplicativo de **fontes de dados ODBC** (32 bits ou 64 bits), dependendo do aplicativo. Para se conectar do Excel, use a versão de 32 bits. Para se conectar de R e Python, use a versão de 64 bits.

    ![Iniciar ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Iniciar aplicativo ODBC")

4. Na guia **DSN de usuário** , clique em **Adicionar**. Na caixa de diálogo **criar nova fonte de dados** , selecione o **driver ODBC do Simba Spark**e clique em **concluir**.

    ![Iniciar ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Iniciar aplicativo ODBC")

5. Na caixa de diálogo **driver ODBC do Simba Spark** , forneça os seguintes valores:

    ![Configurar o DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurar o DSN")

    A tabela a seguir fornece informações sobre os valores a serem fornecidos na caixa de diálogo.
    
    |Campo  | Valor  |
    |---------|---------|
    |**Nome da fonte de dados**     | Forneça um nome para a fonte de dados.        |
    |**Host (s)**     | Forneça o valor que você copiou do espaço de trabalho do databricks para o *nome de host do servidor*.        |
    |**Porta**     | Insira *443*.        |
    |**Mecanismo** de > de **autenticação**     | Selecione *nome de usuário e senha*.        |
    |**Nome de utilizador**     | Insira o *token*.        |
    |**Palavra-passe**     | Insira o valor do token que você copiou do espaço de trabalho do databricks. |
    
    Execute as etapas adicionais a seguir na caixa de diálogo configuração de DSN.
    
    * Clique em **Opções http**. Na caixa de diálogo que é aberta, Cole o valor do *caminho http* que você copiou do espaço de trabalho do databricks. Clique em **OK**.
    * Clique em **Opções de SSL**. Na caixa de diálogo que é aberta, marque a caixa de seleção **habilitar SSL** . Clique em **OK**.
    * Clique em **testar** para testar a conexão com Azure Databricks. Clique em **OK** para salvar a configuração.
    * Na caixa de diálogo **administrador de fonte de dados ODBC** , clique em **OK**.

Agora você tem seu DSN configurado. Nas próximas seções, você usará esse DSN para se conectar ao Azure Databricks do Excel, Python ou R.

## <a name="connect-from-microsoft-excel"></a>Conectar do Microsoft Excel

Nesta seção, você efetua pull de dados de Azure Databricks para o Microsoft Excel usando o DSN que você criou anteriormente. Antes de começar, verifique se você tem o Microsoft Excel instalado em seu computador. Você pode usar uma versão de avaliação do Excel a partir do [link de avaliação do Microsoft Excel](https://products.office.com/excel).

1. Abra uma pasta de trabalho em branco no Microsoft Excel. Na faixa de de **dados** , clique em **obter dados**. Clique em **de outras fontes** e, em seguida, clique em **do ODBC**.

    ![Iniciar ODBC do Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Iniciar ODBC do Excel")

2. Na caixa **de diálogo do ODBC** , selecione o DSN que você criou anteriormente e clique em **OK**.

    ![Selecionar DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Selecionar DSN")

3. Se você for solicitado a fornecer credenciais, para nome de usuário, insira **token**. Para senha, forneça o valor do token que você recuperou do espaço de trabalho do databricks.

    ![Fornecer credenciais para databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Selecionar DSN")

4. Na janela navegador, selecione a tabela no databricks que você deseja carregar para o Excel e clique em **carregar**. 

    ![Carregar o dta no Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Carregar o dta no Excel")

Depois de ter os dados em sua pasta de trabalho do Excel, você pode executar operações analíticas nele.

## <a name="connect-from-r"></a>Conectar do R

> [!NOTE]
> Esta seção fornece informações sobre como integrar um cliente do R Studio em execução na sua área de trabalho com Azure Databricks. Para obter instruções sobre como usar o R Studio no próprio cluster Azure Databricks, consulte [R Studio em Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

Nesta seção, você usa um IDE de linguagem R para dados de referência disponíveis no Azure Databricks. Antes de começar, você deve ter o seguinte instalado no computador.

* Um IDE para a linguagem R. Este artigo usa o RStudio para área de trabalho. Você pode instalá-lo do [download do R Studio](https://www.rstudio.com/products/rstudio/download/).
* Se você usar o RStudio para área de trabalho como seu IDE, instale também Microsoft R Client do [https://aka.ms/rclient/](https://aka.ms/rclient/). 

Abra RStudio e execute as seguintes etapas:

- Referencie o pacote de `RODBC`. Isso permite que você se conecte a Azure Databricks usando o DSN que você criou anteriormente.
- Estabeleça uma conexão usando o DSN.
- Execute uma consulta SQL nos dados em Azure Databricks. No trecho a seguir, *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Execute algumas operações na consulta para verificar a saída. 

O trecho de código a seguir executa estas tarefas:

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

## <a name="connect-from-python"></a>Conectar do Python

Nesta seção, você usará um IDE do Python (como ocioso) para dados de referência disponíveis em Azure Databricks. Antes de começar, conclua os seguintes pré-requisitos:

* Instale o Python [aqui](https://www.python.org/downloads/). A instalação do Python a partir deste link também instala o IDLE.

* Em um prompt de comando no computador, instale o pacote de `pyodbc`. Execute o seguinte comando:

      pip install pyodbc

Abra o IDLE e execute as seguintes etapas:

- Importe o pacote de `pyodbc`. Isso permite que você se conecte a Azure Databricks usando o DSN que você criou anteriormente.
- Estabeleça uma conexão usando o DSN que você criou anteriormente.
-  Execute uma consulta SQL usando a conexão que você criou. No trecho a seguir, *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Execute operações na consulta para verificar a saída.

O trecho de código a seguir executa estas tarefas:

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

* Para saber mais sobre fontes de onde você pode importar dados para Azure Databricks, consulte [fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index)


