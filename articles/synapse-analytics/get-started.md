---
title: 'Tutorial: Começa com o Azure Synapse Analytics'
description: Neste tutorial, você aprenderá os passos básicos para configurar e usar Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791865"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Começar com Azure Synapse Analytics

Este tutorial guia-o através dos passos básicos para configurar e utilizar o Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Preparar uma conta de armazenamento

1. Abra o [portal Azure](https://portal.azure.com).
1. Criar uma nova conta de armazenamento que tenha as seguintes definições:

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da conta de armazenamento**| Pode dar-lhe qualquer nome.|Neste documento, vamos chamar-lhe **contosolake.**|
    |Noções básicas|**Tipo de conta**|Deve ser definido para **StorageV2**.||
    |Noções básicas|**Localização**|Escolha qualquer local.| Recomendamos que o seu espaço de trabalho Azure Synapse Analytics e a conta Azure Data Lake Storage Gen2 estejam na mesma região.|
    |Avançado|**Data Lake Storage Gen2**|**Ativado**| A Azure Synapse só funciona com contas de armazenamento onde esta definição está ativada.|
    |||||

1. Depois de criar a conta de armazenamento, selecione **Access control (IAM)** no painel esquerdo. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas:
    1. Atribua-se ao papel **de Proprietário.**
    1. Atribua-se à função **de Proprietário de Dados blob de armazenamento.**
1. No painel esquerdo, selecione **Recipientes** e crie um recipiente.
1. Pode dar ao recipiente qualquer nome. Neste documento, vamos nomear os **utilizadores**do contentor.
1. Aceite a definição predefinitiva **do nível de acesso público**e, em seguida, selecione **Criar**.

No passo seguinte, irá configurar o seu espaço de trabalho Azure Synapse para utilizar esta conta de armazenamento como a conta de armazenamento "primária" e o recipiente para armazenar dados do espaço de trabalho. O espaço de trabalho armazena dados em tabelas Apache Spark. Armazena registos de aplicações Spark sob uma pasta chamada **/sinapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

1. Abra o [portal Azure](https://portal.azure.com)e na pesquisa superior para **Synapse**.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics (pré-visualização de espaços de trabalho)**.
1. **Selecione Adicionar** para criar um espaço de trabalho utilizando estas definições:

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da área de trabalho**|Pode chamar-lhe qualquer coisa.| Neste documento, usaremos **o meu espaço de trabalho.**|
    |Noções básicas|**Região**|Combine com a região da conta de armazenamento.|

1. Em **Select Data Lake Storage Gen 2,** selecione a conta e o recipiente que criou anteriormente.
1. **Selecione 'Rever +**  >  **criar' Criar**. O seu espaço de trabalho está pronto em poucos minutos.

## <a name="verify-access-to-the-storage-account"></a>Verifique o acesso à conta de armazenamento

As identidades geridas para o seu espaço de trabalho Azure Synapse podem já ter acesso à conta de armazenamento. Siga estes passos para se certificar de que:

1. Abra o [portal Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir do painel esquerdo.
1. Atribua as seguintes funções ou certifique-se de que já estão atribuídas. Usamos o mesmo nome para a identidade do espaço de trabalho e o nome do espaço de trabalho.
    1. Para a função **de Contribuinte de Dados blob de armazenamento** na conta de armazenamento, atribua o meu espaço de **trabalho** como identidade do espaço de trabalho.
    1. Atribua **o meu espaço de trabalho** como o nome do espaço de trabalho.

1. Selecione **Guardar**.

## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://portal.azure.com). No topo da secção **Overview,** selecione **Launch Synapse Studio**.
* Vá https://web.azuresynapse.net e inscreva-se no seu espaço de trabalho.

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas SQL**.
1. Selecione **Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina SQL**| **SQLDB1**|
    |**Nível de desempenho**|**DW100C**|
    |||

1. **Selecione 'Rever +**  >  **criar' Criar**. A sua piscina SQL estará pronta em poucos minutos. A sua piscina SQL está associada a uma base de dados de piscinas SQL que também se chama **SQLDB1**.

Uma piscina SQL consome recursos faturados desde que esteja ativo. Pode fazer uma pausa na piscina mais tarde para reduzir custos.

## <a name="create-an-apache-spark-pool"></a>Criar uma piscina Apache Spark

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas Apache Spark**.
1. Selecione **Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina Apache Spark**|**Faísca1**
    |**Tamanho do nó**| **Small**|
    |**Número de nós**| Definir o mínimo para 3 e o máximo para 3|

1. **Selecione 'Rever +**  >  **criar' Criar**. A tua piscina Apache Spark estará pronta em alguns segundos.

> [!NOTE]
> Apesar do nome, uma piscina Apache Spark não é como uma piscina SQL. São apenas alguns metadados básicos que usas para dizer ao espaço de trabalho do Azure Synapse como interagir com o Spark.

Por serem metadados, as piscinas de faíscas não podem ser iniciadas ou paradas.

Quando realizar atividade spark em Azure Synapse, especifique uma piscina spark para usar. A piscina diz ao Azure Synapse quantos recursos de faíscas usar. Paga apenas os recursos que utilizar. Quando deixa de utilizar ativamente a piscina, os recursos são automaticamente eliminados e são reciclados.

> [!NOTE]
> As bases de dados spark são criadas independentemente a partir de piscinas Spark. Um espaço de trabalho tem sempre uma base de dados Spark chamada **predefinição.** Pode criar bases de dados adicionais de Spark.

## <a name="the-sql-on-demand-pool"></a>A piscina a pedido do SQL

Cada espaço de trabalho vem com uma piscina pré-construída chamada **SQL on-demand**. Esta piscina não pode ser apagada. O pool a pedido do SQL permite-lhe trabalhar com o SQL sem ter de criar ou pensar em gerir uma piscina SQL em Azure Synapse.

Ao contrário dos outros tipos de piscinas, a faturação para o SQL a pedido baseia-se na quantidade de dados digitalizados para executar a consulta, e não no número de recursos utilizados para executar a consulta.

* A SQL on demand tem as suas próprias bases de dados SQL a pedido que existem independentemente de qualquer piscina a pedido do SQL.
* Um espaço de trabalho tem sempre exatamente uma piscina a pedido SQL chamada **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregue os dados da amostra do TÁXI NYC na base de dados SQLDB1

1. No Synapse Studio, no menu mais azul, selecione o **?** .
1. **Selecione Começar**  >  **A começar a começar o hub.**
1. Nos **dados**da amostra de consulta com rótulo do cartão, selecione a piscina SQL chamada **SQLDB1**.
1. Selecione **dados de consulta**. Aparece brevemente uma notificação de "carregar dados de amostra". Uma barra de estado azul-claro perto do topo do Synapse Studio indica que os dados estão a ser carregados em SQLDB1.
1. Depois que a barra de estado ficar verde, descarte-a.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explore os dados do Táxi nyc na piscina SQL

1. No Estúdio Synapse, vá ao centro **de dados.**
1. Vá às **tabelas SQLDB1**  >  **Tables**. Verá várias mesas carregadas.
1. Clique à direita no **dbo. Tabela** de trip e selecione **Novo Script SQL**  >  **Selecione TOP 100 Rows**.
1. Aguarde enquanto um novo script SQL é criado e executado.
1. Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada **SQLDB1**.
1. Substitua o texto do script SQL por este código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros.
1. Na janela de resultados do script SQL, altere a **Visualização** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregue os dados do táxi de NYC na base de dados Spark nyctaxi

Temos dados disponíveis numa tabela em **SQLDB1**. Coloque-o numa base de dados spark chamada **nyctaxi.**

1. No Estúdio Synapse, vá ao centro **de Desenvolvimento.**
1. Selecione **+**  >  **Caderno**.
1. Em cima do caderno, desaperte o **valor do Anexo** ao **Spark1**.
1. **Selecione Adicionar código** para adicionar uma célula de código de caderno e, em seguida, colar o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Aceda ao centro **de dados,** clique com o botão direito **bases de dados**e, em seguida, selecione **Refresh**. Devia ver estas bases de dados:

    - **SQLDB1** (piscina SQL)
    - **nyctaxi** (Faísca)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte para o seu caderno.
1. Crie uma nova célula de código e introduza o seguinte texto. Em seguida, executar o telemóvel para mostrar os dados do táxi nyc que carregamos na base de dados **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o seguinte código para realizar a mesma análise que fizemos anteriormente com a piscina **SQL SQLDB1**. Este código guarda os resultados da análise numa tabela chamada **nyctaxi.passengercountstats** e visualiza os resultados.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Chart** para ver os dados visualizados.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalize a visualização de dados com Spark e cadernos

Pode controlar como os gráficos são renderizados usando cadernos. O seguinte código mostra um exemplo simples. Utiliza as populares bibliotecas **matplotlib** e **seaborn.** O código torna o mesmo tipo de gráfico de linha que as consultas SQL que corremos anteriormente.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Carregue os dados de uma mesa spark em uma mesa de bilhar SQL

Anteriormente copiamos dados da mesa de bilhar SQL **SQLDB1.dbo.Trip** para a mesa Spark **nyctaxi.trip**. Depois, usando a Spark, agregamos os dados na tabela Spark **nyctaxi.passengercountstats.** Agora vamos copiar os dados de **nyctaxi.passengercountstats** em uma mesa de bilhar SQL chamada **SQLDB1.dbo.PassengerCountStats**.

Coloque a seguinte célula no seu caderno. Copia a mesa de faíscas agregada de volta à mesa de bilhar SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analise os dados do táxi da NYC em bases de dados spark usando SQL on demand

As tabelas nas bases de dados Spark são automaticamente visíveis e podem ser consultadas pela SQL a pedido.

1. No Synapse Studio, vá ao centro **De Desenvolvimento** e crie um novo script SQL.
1. Definir **Ligar a** **SQL a pedido**.
1. Cole o texto seguinte no script e execute o script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > A primeira vez que você faz uma consulta que usa SQL on-demand, leva cerca de 10 segundos para o SQL a pedido para recolher os recursos SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orquestrar atividades com oleodutos

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse.

1. No Estúdio Synapse, vá ao centro **de Orchestrate.**
1. Selecione **+**  >  **Pipeline** para criar um novo oleoduto.
1. Vá ao centro **De desenvolvimento** e encontre o caderno que criou anteriormente.
1. Arraste o caderno para o oleoduto.
1. No oleoduto, **selecione Adicionar gatilho**  >  **Novo/editar**.
1. No **Seletor**Trigger , selecione **Novo**e, em seguida, em **recorrência,** desacorda o gatilho a cada 1 hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo**. O oleoduto funciona a cada hora.
1. Para fazer o gasoduto funcionar agora, sem esperar pela próxima hora, **selecione Adicionar gatilho**  >  **Novo/editar**.

## <a name="work-with-data-in-a-storage-account"></a>Trabalhar com dados numa conta de armazenamento

Até agora, cobrimos cenários onde os dados residem em bases de dados no espaço de trabalho. Agora vamos mostrar-lhe como trabalhar com ficheiros em contas de armazenamento. Neste cenário, vamos usar a conta de armazenamento primário do espaço de trabalho e do contentor que especificamos ao criar o espaço de trabalho.

* O nome da conta de armazenamento: **contosolake**
* O nome do recipiente na conta de armazenamento: **utilizadores**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Crie ficheiros CSV e Parquet na sua conta de armazenamento

Execute o seguinte código num caderno. Cria um ficheiro CSV e um ficheiro parquet na conta de armazenamento.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

1. No Synapse Studio, vá ao centro **de dados** e, em seguida, selecione **Linked**.
1. Vá às **contas de armazenamento**  >  **myworkspace (Primário - contosolake)**.
1. Selecione **utilizadores (Primário)".** Devia ver a pasta **NYCTaxi.** No interior deverá ver duas pastas chamadas **PassengerCountStats.csv** e **PassengerCountStats.parquet**.
1. Abra a pasta **PassengerCountStats.parquet.** No interior você verá um arquivo de parquet com um nome como *parte-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*.
1. Clique à direita **em .parquet**e, em seguida, selecione **novo caderno**. Cria um caderno que tem uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Executar a cela.
1. Clique com o botão direito no ficheiro parquet no interior e, em seguida, selecione **Novo script SQL**  >  **SELECT TOP 100 linhas**. Cria um script SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     No roteiro, o **campo anexado ao** campo é definido para **SQL on demand**.

1. Execute o script.

## <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

A partir dos dados do Táxi de NYC, criámos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.contagem de passageiros**
- **SQLDB1.dbo.PassengerCountStats**

Pode ligar um espaço de trabalho Power BI ao seu espaço de trabalho Azure Synapse. Isto permite-lhe obter facilmente dados no seu espaço de trabalho Power BI. Pode editar os seus relatórios power bi diretamente no seu espaço de trabalho Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho Power BI

1. Inscreva-se na [powerbi.microsoft.com.](https://powerbi.microsoft.com/)
1. Criar um novo espaço de trabalho Power BI chamado **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Ligue o seu espaço de trabalho Azure Synapse ao seu novo espaço de trabalho Power BI

1. No Synapse Studio, vá a **Manage**  >  **Linked Services**.
1. Selecione **New**  >  **Connect to Power BI**e, em seguida, definir estes campos:

    |Definição | Valor sugerido | 
    |---|---|
    |**Nome**|**NYCTaxiWorkspace1**|
    |**Nome da área de trabalho**|**NYCTaxiWorkspace1**|

1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Azure Synapse

1. No Synapse Studio, vá ao **Develop**  >  **Power BI.**
1. Vá a conjuntos de dados do BI de energia **NYCTaxiWorkspace1**  >  e selecione o conjunto de**dados** do New Power **BI**.
1. Passe sobre a base de dados **SQLDB1** e selecione **Download .pbids file**.
1. Abra o ficheiro **.pbids** descarregado. O ambiente de trabalho power BI abre e liga-se automaticamente ao **SQLDB1** no seu espaço de trabalho Azure Synapse.
1. Se vir uma caixa de diálogo aparecer chamada **base de dados de servidor SQL:**
    1. Selecione **a conta Microsoft**.
    1. Selecione **Iniciar sôms** e inscreva-se na sua conta.
    1. Selecione **Ligar**.
1. Depois de abrir a caixa de diálogo **Navigator,** verifique a tabela **PassengerCountStats** e selecione **Load**.
1. Depois de aparecer a caixa de diálogo **de definições de ligação,** selecione **DirectQuery**  >  **OK**.
1. Selecione o botão **Relatório** no lado esquerdo.
1. Adicione **gráfico de linha** ao seu relatório.
    1. Arraste a coluna **PassengerCount** para o Eixo **visualizações.**  >  **Axis**
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Valores de Visualização**  >  **Values**.
1. No **separador Casa,** **selecione Publicar**.
1. Selecione **Guardar** para guardar as alterações.
1. Escolha o nome de ficheiro **PassengerAnalysis.pbix**e, em seguida, **selecione Save**.
1. Em **Selecionar um destino,** escolha **NYCTaxiWorkspace1**e, em seguida, clique em **Select**.
1. Espere que a publicação termine.

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **inscreva-se**.
1. No lado esquerdo, em **Workspaces,** selecione o espaço de trabalho **NYCTaxiWorkspace1.**
1. Dentro desse espaço de trabalho, localize um conjunto de dados chamado **Análise de Passageiros** e um relatório chamado **Análise de Passageiros.**
1. Passe sobre o conjunto de **dados PassengerAnalysis,** selecione o botão elipse (...) e, em seguida, selecione **Definições**.
1. Nas **credenciais de origem de dados**, descreva o **método de autenticação** para **OAuth2**e, em seguida, selecione **Iniciar sposição em**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar uma reportagem no Synapse Studio

1. Volte para o Synapse Studio e selecione **Close and refresh**.
1. Vá ao centro **de Desenvolvimento.**
1. Passe sobre **o Power BI** e selecione o nó de **relatórios Power BI.**
1. Sob **o Power BI** deve ver:
    1. Sob os conjuntos de dados **do NYCTaxiWorkspace1**Power BI , um novo conjunto de  >  **dados**chamado **PassengerAnalysis**.
    1. Ao abrigo dos relatórios **NYCTaxiWorkspace1**  >  **Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis.** O relatório abre e pode editá-lo diretamente no Synapse Studio.

## <a name="monitor-activities"></a>Monitorizar atividades

1. No Estúdio Synapse, vá ao **monitor.**
1. Neste local, você pode ver uma história de todas as atividades que estão acontecendo no espaço de trabalho e quais estão ativas agora.
1. Explore as **corridas**de Pipeline, **aplicações Apache Spark**e **SQL** para ver o que já fez no espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Azure Synapse Analytics (pré-visualização de espaços de trabalho)](overview-what-is.md).

