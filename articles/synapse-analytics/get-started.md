---
title: 'Tutorial: Começar com Azure Synapse Analytics'
description: Passos por passos para entender rapidamente conceitos básicos em Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 75c8d52a750567d3b34ad2aea236477ca8c97245
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171417"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Começando com Azure Synapse Analytics

Este tutorial irá guiá-lo através de todos os passos básicos necessários para configurar e usar a Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Prepare uma conta de armazenamento para uso com um espaço de trabalho Synapse

* Abra o [portal do Azure](https://portal.azure.com)
* Criar uma nova conta de armazenamento com as seguintes definições:
    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da conta de armazenamento**| Pode dar-lhe qualquer nome.|Neste documento, vamos chamar-lhe como `contosolake` .|
    |Noções básicas|**Tipo de conta**|Deve ser definido para`StorageV2`||
    |Noções básicas|**Localização**|Pode escolher qualquer local| Recomendamos que o seu espaço de trabalho Synapse e a conta Azure Data Lake Storage (ADLS) Gen2 estejam na mesma região.|
    |Avançado|**Armazenamento do Data Lake Ger2**|`Enabled`| A Azure Synapse só funciona com contas de armazenamento onde esta definição está ativada.|

1. Assim que a conta de armazenamento for criada, selecione **Access control (IAM)** a partir da navegação à esquerda. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas. 
    a. * Atribua-se à função **Proprietário** na conta de armazenamento b. * Atribua-se à função **de Proprietário de Dados** de Armazenamento na Conta de Armazenamento
1. A partir da navegação à esquerda, selecione **Recipientes** e crie um recipiente. Pode dar-lhe qualquer nome. Aceite o nível de **acesso público predefinido.** Neste documento, chamaremos o `users` contentor. Selecione **Criar**. 

## <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

* Abra o [portal Azure](https://portal.azure.com) e na procura superior por `Synapse` .
* Nos resultados de pesquisa em **Serviços**, selecione **Azure Synapse Analytics (pré-visualização de espaços de trabalho)**
* **Selecione + Adicionar** para criar um novo espaço de trabalho com estas definições

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da área de trabalho**|Pode chamar-lhe qualquer coisa.| Neste documento, vamos usar`myworkspace`|
    |Noções básicas|**Região**|Combine a região da conta de armazenamento|

1. Em **Select Data Lake Storage Gen 2,** selecione a conta e o recipiente que criou anteriormente.
    > [!NOTE]
    > Referimo-nos à conta de armazenamento escolhida aqui como a conta de armazenamento "primária" do espaço de trabalho da Sinapse. Esta conta é utilizada para armazenar dados em tabelas de faíscas Apache e para registos criados quando as piscinas Spark são criadas ou as aplicações Spark são executadas.

1. Selecione **Rever + criar**. Selecione **Criar**. O seu espaço de trabalho estará pronto em alguns minutos.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Verifique se o espaço de trabalho Synapse MSI tem acesso à conta de armazenamento

Isto pode já ter sido feito por si. De qualquer forma, deve verificar.

1. Abra o [portal Azure](https://portal.azure.com) e abra a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir da navegação à esquerda. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas. 
    a. Atribua a identidade do espaço de trabalho à função **de Contribuinte de Dados do Depósito** de Armazenamento na conta de armazenamento. A identidade do espaço de trabalho tem o mesmo nome que o espaço de trabalho. Neste documento, o nome do espaço de trabalho é `myworkspace` assim que a identidade do espaço de trabalho é`myworkspaced`
1. Selecione **Guardar**.
    
## <a name="launch-synapse-studio"></a>Estúdio De Lançamento Synapse

Uma vez criado o seu espaço de trabalho Synapse, tem duas formas de abrir o Synapse Studio:
* Abra o seu espaço de trabalho synapse no [portal Azure](https://portal.azure.com) e no topo da secção De **visão geral** selecione **Launch Synapse Studio**
* Vá diretamente e https://web.azuresynapse.net inscreva-se no seu espaço de trabalho.

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

1. No Synapse Studio, na navegação do lado esquerdo, **selecione Gerir > piscinas SQL**

    > [!NOTE] 
    > Todos os espaços de trabalho da Synapse vêm com uma piscina pré-criada chamada **SQL on-demand**.

1. Selecione **+Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|
    |**Nome da piscina SQL**| `SQLDB1`|
    |**Nível de desempenho**|`DW100C`|

1. Selecione **Review+create** e, em seguida, **selecione Criar**.
1. A sua piscina SQL estará pronta em poucos minutos.

    > [!NOTE]
    > Uma piscina Sinapse SQL corresponde ao que costumava ser chamado de "Azure SQL Data Warehouse"

Uma piscina SQL consome recursos faturados desde que esteja em funcionamento. Assim, você pode parar a piscina quando necessário para reduzir custos.

Quando a sua piscina SQL for criada, será associada a uma base de dados de piscinas SQL também chamada **SQLDB1**.

## <a name="create-an-apache-spark-pool"></a>Criar uma piscina Apache Spark

1. No Synapse Studio, no lado esquerdo **selecione Gerir > piscinas Apache Spark**
1. Selecione **+Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|
    |**Nome da piscina Apache Spark**|`Spark1`
    |**Tamanho do nó**| `Small`|
    |**Número de nós**| Definir o mínimo para 3 e o máximo para 3|

1. Selecione **Review+create** e, em seguida, **selecione Criar**.
1. A tua piscina Apache Spark estará pronta em alguns segundos.

> [!NOTE]
> Apesar do nome, uma piscina Apache Spark não é como uma piscina SQL. São apenas alguns metadados básicos que usa para informar o espaço de trabalho da Synapse como interagir com o Spark. 

Por serem metadados, as piscinas spark não podem ser iniciadas ou paradas. 

Quando fizer qualquer atividade de Faísca em Synapse, especifique uma piscina spark para usar. A piscina informa a Synapse quantos recursos de faíscas usar. Paga-se apenas pelos recursos que o Thar usa. Quando parar ativamente de usar a piscina, os recursos serão automaticamente eliminados e reciclados.

> [!NOTE]
> As bases de dados spark são criadas independentemente a partir de piscinas Spark. Um espaço de trabalho tem sempre um DB spark chamado **predefinido** e pode criar bases de dados de Spark adicionais.

## <a name="the-sql-on-demand-pool"></a>A piscina a pedido do SQL

Cada espaço de trabalho vem com uma piscina pré-construída e indelegável chamada **SQL on-demand**. O pool a pedido do SQL permite-lhe trabalhar com o SQL sem ter de criar ou pensar em gerir uma piscina Sinapse SQL. Ao contrário dos outros tipos de piscinas, a faturação para o SQL a pedido baseia-se na quantidade de dados digitalizados para executar a consulta - e não no número de recursos utilizados para executar a consulta.

* A SQL on demand também tem as suas próprias bases de dados SQL a pedido que existem independentemente de qualquer piscina a pedido do SQL.
* Atualmente um espaço de trabalho tem sempre exatamente um pool a pedido SQL chamado **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregue os dados da amostra de táxi de NYC na base de dados SQLDB1

1. No Synapse Studio, no menu mais azul, selecione o **?** .
1. **Selecione Começar > Começar a começar o hub**
1. Nos **dados**da amostra de consulta com rótulo do cartão, selecione a piscina SQL nomeada`SQLDB1`
1. Selecione **dados de consulta**. Verá uma notificação a dizer "Carregar dados de amostra" que aparecerão e depois desaparecerão.
1. Você verá uma barra de notificação azul-claro perto do topo do Synapse Studio indicando que os dados estão sendo carregados em SQLDB1. Espere até ficar verde e depois descartá-lo.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explore os dados do táxi de NYC na Piscina SQL

1. No Estúdio Synapse, navegue para o centro **de dados**
1. Navegue para **as tabelas de > SQLDB1**. Verá que várias mesas foram carregadas.
1. Clique à direita no **dbo. Tabela** de trip e selecione **Novo Script SQL > Selecione TOP 100 Linhas**
1. Um novo script SQL será criado e executado automaticamente.
1. Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada SQLDB1.
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

1. Esta consulta mostra como as distâncias totais de viagem e a distância média de viagem se relacionam com o número de passageiros
1. Na janela de resultados do script SQL, altere a **Vista** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Carregue os dados da amostra de táxi de NYC na base de dados Spark nyctaxi

Temos dados disponíveis numa tabela em `SQLDB1` . Agora colocamos numa base de dados spark chamada "nyctaxi".

1. No Estúdio Synapse, navegue para o centro **de Desenvolvimento**
1. Selecione **+** e selecione **Caderno**
1. No topo do caderno, definir o **Anexado para** valor`Spark1`
1. **Selecione Adicionar código** para adicionar uma célula de código de caderno e colar o texto abaixo:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Navegue no centro **de dados,** clique com o botão direito nas **bases de dados** e selecione **Refresh**.
1. Agora deve ver estas bases de dados:
    - SQLDB (piscina SQL)
    - nyctaxi (Faísca)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte ao seu caderno
1. Crie uma nova célula de código, introduza o texto abaixo e execute a célula para exemplo os dados de táxi de NYC que carregamos no `nyctaxi` Spark DB.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o seguinte código para realizar a mesma análise que fizemos anteriormente com a piscina `SQLDB1` SQL. Este código também guarda os resultados da análise numa tabela chamada `nyctaxi.passengercountstats` e visualiza os resultados.

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

1. Nos resultados da célula, selecione **Gráfico** para ver os dados visualizados
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personalizar dados de visualização de dados com Spark e cadernos

Com cadernos, pode controlar como renderizar gráficos. O seguinte código mostra um exemplo simples usando as bibliotecas populares `matplotlib` e `seaborn` . Irá renderizar o mesmo tipo de gráfico de linha que viu ao executar as consultas SQL mais cedo.

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

Anteriormente copiamos dados de uma mesa de bilhar SQL `SQLDB1.dbo.Trip` para uma mesa `nyctaxi.trip` spark. Depois, usando o Spark, agregamos os dados na tabela `nyctaxi.passengercountstats` Spark. Agora vamos copiar os dados de `nyctaxi.passengercountstats` uma mesa de bilhar SQL chamada `SQLDB1.dbo.PassengerCountStats` . 

Coloque a cela abaixo no seu caderno. Copiará a mesa de faíscas agregada de volta para a mesa de bilhar SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analise os dados dos táxis de NYC nas bases de dados spark usando SQL on demand 

As tabelas nas bases de dados Spark são automaticamente visíveis e consultadas pela SQL a pedido.

1. No Synapse Studio, navegue para o centro **de Desenvolvimento** e crie um novo script SQL
1. Definir **Ligar a** **SQL a pedido** 
1. Cole o texto seguinte no script e execute o script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > A primeira vez que fizer uma consulta que utilize o SQL a pedido, levará cerca de 10 segundos para o SQL a pedido para recolher os recursos SQL necessários para executar as suas consultas. As consultas subsequentes não exigirão desta vez e serão muito mais rápidas.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orquestrar atividades com oleodutos

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse. Nesta secção, verá como é fácil.

1. No Estúdio Synapse, navegue para o centro **de Orquestração.**
1. Selecione **+** então **o Pipeline**. Será criado um novo oleoduto.
1. Navegue até ao centro De desenvolvimento e encontre o caderno que criou anteriormente.
1. Arraste o caderno para o oleoduto.
1. No oleoduto, **selecione Adicionar o gatilho > Novo/editar**.
1. Em **Escolha o gatilho** selecione **Novo**e, em seguida, em recorrência, desacordo o gatilho a cada 1 hora.
1. Selecione **OK**.
1. **Selecione Publicar Tudo** e o oleoduto funcionará a cada hora.
1. Se pretender fazer o gasoduto funcionar agora sem esperar pela próxima hora, **selecione Adicione o gatilho > Novo/editar**.

## <a name="working-with-data-in-a-storage-account"></a>Trabalhar com dados numa conta de armazenamento

Até agora, cobrimos cenários que residiam em bases de dados no espaço de trabalho. Agora vamos mostrar como trabalhar com ficheiros em contas de armazenamento. Neste cenário, vamos usar a conta de armazenamento primário do espaço de trabalho e do contentor que especificamos ao criar o espaço de trabalho.

* O nome da conta de armazenamento:`contosolake`
* O nome do recipiente na conta de armazenamento:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Criar ficheiros CSV e Parquet na sua conta de Armazenamento

Execute o seguinte código num caderno. Cria um ficheiro CSV e um ficheiro parquet na conta de armazenamento

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

1. No Estúdio Synapse, navegue para o centro **de dados**
1. Selecione **Ligado**
1. Navegue para **as contas de armazenamento > myworkspace (Primário - contosolake)**
1. Selecione **utilizadores (Primário)"**
1. Devias ver uma pasta chamada "NYCTaxi". No interior deverá ver duas pastas 'PassengerCountStats.csv' e 'PassengerCountStats.parquet'.
1. Navegue na pasta 'PassengerCountStats.parquet'.
1. Clique com o botão direito no ficheiro parquet no interior e selecione **um novo caderno,** criará um caderno com uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Executar a cela.
1. Clique com o botão direito no ficheiro parquet no interior e selecione **o novo script SQL > SELECT TOP 100 linhas,** criará um script SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. No roteiro, o **conjunto ao** campo será definido para **SQL on demand**.
1. Execute o script.

## <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

A partir dos dados do táxi nyx, criamos conjuntos de dados agregados em duas tabelas:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Pode ligar um espaço de trabalho Power BI ao seu espaço de trabalho Synapse. Isto permite-lhe obter facilmente dados no seu espaço de trabalho Power BI e pode editar os seus relatórios Power BI diretamente no seu espaço de trabalho Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho Power BI

1. Inscreva-se [na powerbi.microsoft.com.](https://powerbi.microsoft.com/)
1. Criar um novo espaço de trabalho Power BI chamado `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Ligue o seu espaço de trabalho synapse ao seu novo espaço de trabalho Power BI

1. No Synapse Studio, navegue para os **Serviços Ligados > Gestão.**
1. Selecione **+ Novo** e selecione **Connect to Power BI** e definir estes campos:

    |Definição | Valor sugerido | 
    |---|---|
    |**Nome**|`NYCTaxiWorkspace1`|
    |**Nome da área de trabalho**|`NYCTaxiWorkspace1`|
        
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Synapse

1. No Synapse Studio, navegue para o **Develop > Power BI**.
1. Navegue para **NYCTaxiWorkspace1 > conjuntos de dados do Power BI** e selecione **new power bi dataset**.
1. Passe por cima da `SQLDB1` base de dados e selecione **Download .pbids file**.
1. Abra o `.pbids` ficheiro descarregado. 
1. Isto irá lançar o ambiente de trabalho Power BI e conectá-lo automaticamente `SQLDB1` no seu espaço de trabalho sinapse.
1. Se vir um diálogo, aparece uma base de dados de **servidor SQL:** a. Selecione **a conta Microsoft**. 
    b. Selecione **Iniciar sção** e iniciar sin.
    c. Selecione **Ligar**.
1. O diálogo **do Navegador** abrir-se-á. Quando o fizer, verifique a tabela **PassengerCountStats** e selecione **Load**.
1. Aparecerá um diálogo de definições de **ligação.** Selecione **DirectQuery** e selecione **OK**
1. Selecione o botão **Relatório** à esquerda.
1. Adicione **gráfico de linha** ao seu relatório.
    a. Arraste a coluna **PasssengerCount** para **visualizações > Eixo** b. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações > Valores**.
1. No **separador Casa,** **selecione Publicar**.
1. Perguntar-lhe-á se quer guardar as suas alterações. Selecione **Guardar**.
1. Vai pedir-lhe para escolher um nome de arquivo. Escolha `PassengerAnalysis.pbix` e **selecione Guardar.**
1. Pedir-lhe-á para **selecionar um destino** selecionado e `NYCTaxiWorkspace1` **selecionar Select**.
1. Espere que a publicação termine.

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **inscreva-se**
1. À esquerda, em **espaço de trabalho** seleciona o espaço de `NYCTaxiWorkspace1` trabalho.
1. Dentro desse espaço de trabalho deve ver um conjunto de dados chamado `Passenger Analysis` e um relatório chamado `Passenger Analysis` .
1. Passe sobre o `PassengerAnalysis` conjunto de dados e selecione o ícone com os três pontos e selecione **Definições**.
1. Nas **credenciais de origem de dados,** descreva o **método de autenticação** para **OAuth2** e selecione **Iniciar sposição em**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar uma reportagem no Synapse Studio

1. Volte para o Synapse Studio e selecione **Close and refresh** 
1. Navegue até ao centro **de desenvolvimento** 
1. Passe por cima **do Power BI** e clique na atualização do nó de **relatórios Power BI.**
1. Agora sob o **Power BI** você deve ver: a. * No âmbito **do NYCTaxiWorkspace1 > conjuntos de dados power bi**, um novo conjunto de dados chamado **PassengerAnalysis**.
    b. * Ao abrigo do **NYCTaxiWorkspace1 > Power BI reporte**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis.** 
1. O relatório será aberto e agora pode editar o relatório diretamente no Synapse Studio.

## <a name="monitor-activities"></a>Monitorizar atividades

1. No Synapse Studio, navegue para o monitor hub.
1. Neste local, você pode ver uma história de todas as atividades que estão acontecendo no espaço de trabalho e quais estão ativas agora.
1. Explore as **correções**do Pipeline, **aplicações Apache Spark**e **SQL** e poderá ver o que já fez no espaço de trabalho.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [a Azure Synapse Analytics (pré-visualização)](overview-what-is.md)

