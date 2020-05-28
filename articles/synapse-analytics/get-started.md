---
title: 'Tutorial: Começar com azure Synapse Analytics'
description: Passos a passos para entender rapidamente conceitos básicos em Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: d5484f5725047201770e5b3cbab89847b27117f9
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116931"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Começando com Azure Synapse Analytics

Este tutorial irá guiá-lo através de todos os passos básicos necessários para configurar e utilizar o Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Prepare uma conta de armazenamento para uso com um espaço de trabalho Synapse

* Abra o [portal do Azure](https://portal.azure.com)
* Criar uma nova conta de armazenamento com as seguintes definições:
    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da conta de armazenamento**| Pode dar-lhe qualquer nome.|Neste documento, vamos referir-nos a ele como `contosolake` .|
    |Noções básicas|**Tipo de conta**|Deve ser definido para`StorageV2`||
    |Noções básicas|**Localização**|Pode escolher qualquer local| Recomendamos que o seu espaço de trabalho Synapse e a conta de Gen2 do Lago De Dados Azure (ADLS) estejam na mesma região.|
    |Avançado|**Armazenamento do Data Lake Ger2**|`Enabled`| A Azure Synapse só trabalha com contas de armazenamento onde esta definição está ativada.|
    |||||

1. Assim que a conta de armazenamento for criada, selecione o controlo de **acesso (IAM)** a partir da navegação esquerda. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas. 
    a. * Atribuir-se ao papel **proprietário** na conta de armazenamento b. * Atribuir-se ao papel do Proprietário de **Dados blob** de armazenamento na Conta de Armazenamento
1. A partir da navegação à esquerda, selecione **Recipientes** e crie um recipiente. Pode dar-lhe qualquer nome. Aceite o **nível**de acesso público padrão . Neste documento, chamaremos o recipiente `users` . Selecione **Criar**. 

## <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

* Abra o [portal Azure](https://portal.azure.com) e na procura de topo `Synapse` por .
* Nos resultados da pesquisa em **Serviços,** **selecione Azure Synapse Analytics (pré-visualização** dos espaços de trabalho)
* Selecione **+ Adicione** para criar um novo espaço de trabalho com estas definições

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da área de trabalho**|Pode chamar-lhe qualquer coisa.| Neste documento, usaremos`myworkspace`|
    |Noções básicas|**Região**|Corresponder à região da conta de armazenamento|
    ||||

1. Em **Select Data Lake Storage Gen 2,** selecione a conta e o recipiente que criou anteriormente.
    > [!NOTE]
    > Referimo-nos à conta de armazenamento escolhida aqui como a conta de armazenamento "primária" do espaço de trabalho synapse. Esta conta é usada para armazenar dados em mesas de faíscas Apache e para registos criados quando as piscinas Spark são criadas ou as aplicações Spark funcionam.

1. Selecione **Rever + criar**. Selecione **Criar**. O seu espaço de trabalho estará pronto em poucos minutos.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Verifique se o espaço de trabalho Synapse MSI tem acesso à conta de armazenamento

Isto pode já ter sido feito por si. De qualquer forma, deve verificar.

1. Abra o [portal Azure](https://portal.azure.com) e abra a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir da navegação esquerda. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas. 
    a. Atribuir a identidade do espaço de trabalho à função de Colaborador de **Dados blob** de armazenamento na conta de armazenamento. A identidade do espaço de trabalho tem o mesmo nome que o espaço de trabalho. Neste documento, o nome do espaço de trabalho é `myworkspace` assim que a identidade do espaço de trabalho é`myworkspaced`
1. Selecione **Guardar**.
    
## <a name="launch-synapse-studio"></a>Lançar Estúdio Synapse

Assim que o seu espaço de trabalho Synapse for criado, você tem duas maneiras de abrir o Estúdio Synapse:
* Abra o seu espaço de trabalho Synapse no [portal Azure](https://portal.azure.com) e no topo da secção **Overview** selecione **Launch Synapse Studio**
* Vá diretamente https://web.azuresynapse.net e inscreva-se no seu espaço de trabalho.

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

1. No Estúdio Synapse, na navegação lateral esquerda, selecione **Gerir > piscinas SQL**

    > [!NOTE] 
    > Todos os espaços de trabalho synapse vêm com uma piscina pré-criada chamada **SQL on-demand**.

1. **Selecione +Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina SQL**| `SQLDB1`|
    |**Nível de desempenho**|`DW100C`|
    |||

1. Selecione **Review+criar** e, em seguida, **selecione Criar**.
1. Sua piscina SQL estará pronta em alguns minutos.

    > [!NOTE]
    > Uma piscina Synapse SQL corresponde ao que costumava ser chamado de "Armazém de Dados Azure SQL"

Uma piscina SQL consome recursos faturados desde que esteja a funcionar. Então, você pode parar a piscina quando necessário para reduzir custos.

Quando o seu pool SQL for criado, será associado a uma base de dados de piscina SQL também chamada **SQLDB1**.

## <a name="create-an-apache-spark-pool"></a>Crie uma piscina apache spark

1. No Estúdio Synapse, no lado esquerdo selecione **Manage > Apache Spark pools**
1. **Selecione +Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina Apache Spark**|`Spark1`
    |**Tamanho do nó**| `Small`|
    |**Número de nós**| Desmarcar o mínimo para 3 e o máximo para 3|
    |||

1. Selecione **Review+criar** e, em seguida, **selecione Criar**.
1. Sua piscina Apache Spark estará pronta em alguns segundos.

> [!NOTE]
> Apesar do nome, uma piscina Apache Spark não é como uma piscina SQL. São apenas alguns metadados básicos que usas para informar o espaço de trabalho synapse como interagir com o Spark. 

Por serem metadados, as piscinas spark não podem ser iniciadas ou paradas. 

Quando você faz qualquer atividade spark em Synapse, você especifica uma piscina Spark para usar. A piscina informa a Synapse quantos recursos da Spark usar. Só pagas pelos recursos que a Thar é usada. Quando deixar ativamente de utilizar a piscina, os recursos irão automaticamente esgotar-se e ser reciclados.

> [!NOTE]
> As bases de dados de faíscas são criadas independentemente a partir de piscinas Spark. Um espaço de trabalho tem sempre um Spark DB chamado **padrão** e pode criar bases de dados adicionais spark.

## <a name="the-sql-on-demand-pool"></a>O pool on-demand SQL

Cada espaço de trabalho vem com uma piscina pré-construída e imbatível chamada **SQL on-demand**. O pool on-demand SQL permite-lhe trabalhar com a SQL sem ter de criar ou pensar em gerir uma piscina SQL Synapse. Ao contrário dos outros tipos de piscinas, a faturação para a Procura sQL baseia-se na quantidade de dados digitalizados para executar a consulta - e não no número de recursos utilizados para executar a consulta.

* A SQL on-demand também tem as suas próprias bases de dados SQL on-demand que existem independentemente de qualquer pool on-demand SQL.
* Atualmente, um espaço de trabalho tem sempre exatamente um pool SQL on-demand chamado **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregue os dados da amostra de táxi de NYC na base de dados SQLDB1

1. No Estúdio Synapse, no menu mais azul, selecione o **?** Afixar.
1. Select **Getting started > Getting started hub**
1. Nos dados da amostra de **consulta**com o carimbo, selecione a piscina SQL nomeada`SQLDB1`
1. Selecione **dados de consulta**. Verá uma notificação a dizer "Carregar dados da amostra" que aparecerão e depois desaparecerão.
1. Verá uma barra de notificação azul-claro perto do topo do Estúdio Synapse indicando que os dados estão a ser carregados no SQLDB1. Espere até ficar verde e depois dispense-o.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explore os dados do táxi de NYC no SQL Pool

1. No Estúdio Synapse, navegue para o centro de **dados**
1. Navegue para **tabelas de > SQLDB1**. Verá que várias mesas foram carregadas.
1. Clique na direita no **dbo. Mesa de viagem** e selecione **Novo Script SQL > Selecione TOP 100 Rows**
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

1. Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros
1. Na janela de resultados do script SQL, altere a **Vista** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Carregue os dados da amostra de táxi de NYC na base de dados Spark nyctaxi

Temos dados disponíveis numa tabela em `SQLDB1` . Agora colocamos numa base de dados de Spark chamada "nyctaxi".

1. No Estúdio Synapse, navegue para o centro **de Desenvolvimento**
1. **+** Selecione e selecione **Notebook**
1. No topo do caderno, definir o **Anexo ao** valor para`Spark1`
1. **Selecione Adicionar código** para adicionar uma célula de código portátil e colar o texto abaixo:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Navegue para o centro de **Dados,** clique à direita nas Bases de **Dados** e selecione **Refresh**.
1. Agora deve ver estas bases de dados:
    - SQLDB (piscina SQL)
    - nyctaxi (Faísca)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do táxi de NYC usando faíscas e cadernos

1. Volte ao seu caderno
1. Crie uma nova célula de código, introduza o texto abaixo e passe a célula para dar o exemplo dos dados de táxi de NYC que carregamos no `nyctaxi` Spark DB.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o seguinte código para realizar a mesma análise que fizemos anteriormente com a piscina SQL `SQLDB1` . Este código também guarda os resultados da análise numa tabela chamada `nyctaxi.passengercountstats` e visualiza os resultados.

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
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personalize dados de visualização de dados com Faísca seleções e cadernos

Com cadernos, pode controlar como renderiza gráficos. O código que se segue mostra um exemplo simples utilizando as bibliotecas populares `matplotlib` e `seaborn` . Vai render o mesmo tipo de gráfico de linha que viu ao executar as consultas SQL mais cedo.

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
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Carregue os dados de uma mesa de faísca sql em uma mesa de bilhar SQL

Anteriormente copiamos dados de uma mesa de bilhar SQL `SQLDB1.dbo.Trip` para uma mesa de `nyctaxi.trip` faíscas. Depois, usando o Spark, agregamos os dados na tabela Spark `nyctaxi.passengercountstats` . Agora vamos copiar os dados de uma mesa de `nyctaxi.passengercountstats` bilhar SQL chamada `SQLDB1.dbo.PassengerCountStats` . 

Coloque a cela abaixo no seu caderno. Copiará a mesa de faíscas agregada de volta para a mesa de bilhar SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analise os dados de táxi de NYC nas bases de dados spark usando sQL on-demand 

As tabelas nas bases de dados Spark são automaticamente visíveis e consultadas pela SQL a pedido.

1. No Estúdio Synapse, navegue para o hub **De desenvolvimento** e crie um novo script SQL
1. Definir **Ligar para** **sql a pedido** 
1. Colhe o seguinte texto no script e execute o script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > A primeira vez que executa uma consulta que usa sQL a pedido, levará cerca de 10 segundos para a SQL a pedido para recolher os recursos SQL necessários para executar as suas consultas. As consultas subsequentes não exigirão este tempo e serão muito mais rápidas.
  
## <a name="orchestrate-activities-with-pipelines"></a>Atividades orquestradas com oleodutos

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse. Nesta secção, verá como é fácil.

1. No Estúdio Synapse, navegue até ao centro **de Orchestrate.**
1. **+** Selecione e, em seguida, selecione **Pipeline**. Será criado um novo oleoduto.
1. Navegue até ao centro de Desenvolvimento e encontre o caderno que criou anteriormente.
1. Arraste o caderno para o oleoduto.
1. No gasoduto, selecione **Adicionar gatilho > Nova/edição**.
1. Em **Escolha selecione** **Novo**, e, em seguida, em recorrência, despolete o gatilho a funcionar a cada 1 hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo** e o gasoduto será executado a cada hora.
1. Se quiser fazer o gasoduto funcionar agora sem esperar pela próxima hora, selecione **Adicionar gatilho > Novo/editar**.

## <a name="working-with-data-in-a-storage-account"></a>Trabalhar com dados numa conta de armazenamento

Até agora, cobrimos cenários que residem em bases de dados no espaço de trabalho. Agora vamos mostrar como trabalhar com ficheiros em contas de armazenamento. Neste cenário, usaremos a conta de armazenamento primária do espaço de trabalho e do contentor que especificámos na criação do espaço de trabalho.

* O nome da conta de armazenamento:`contosolake`
* O nome do recipiente na conta de armazenamento:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Criação de ficheiros CSV e Parquet na sua conta de Armazenamento

Execute o seguinte código num caderno. Cria um ficheiro CSV e um ficheiro parquet na conta de armazenamento

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

1. No Estúdio Synapse, navegue para o centro de **dados**
1. Selecione **Linked**
1. Navegue para contas de **armazenamento > myworkspace (Primary - contosolake)**
1. Selecione **utilizadores (Primário)"**
1. Devia ver uma pasta chamada "NYCTaxi". No seu interior deverá ver duas pastas 'PassengerCountStats.csv' e 'PassengerCountStats.parquet'.
1. Navegue na pasta 'PassengerCountStats.parquet'.
1. Clique à direita no ficheiro parquet no interior e selecione **um novo caderno,** criará um caderno com uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Dirija a cela.
1. Clique à direita no ficheiro parquet no interior e selecione **o novo script SQL > select TOP 100 linhas,** criará um script SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. No script, o **attach to** field será definido para **SQL on-demand**.
1. Execute o script.

## <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

A partir dos dados do táxi NYX, criamos conjuntos de dados agregados em duas tabelas:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Pode ligar um espaço de trabalho power BI ao seu espaço de trabalho Synapse. Isto permite-lhe obter facilmente dados no seu espaço de trabalho Power BI e pode editar os seus relatórios Power BI diretamente no seu espaço de trabalho Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho power BI

1. Assine [powerbi.microsoft.com.](https://powerbi.microsoft.com/)
1. Crie um novo espaço de trabalho Power BI chamado `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Ligue o seu espaço de trabalho Synapse ao seu novo espaço de trabalho Power BI

1. No Estúdio Synapse, navegue até ao **Manage > Linked Services.**
1. Selecione **+ Novo** e selecione **Ligar ao Power BI** e definir estes campos:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome**|`NYCTaxiWorkspace1`|
    |**Nome da área de trabalho**|`NYCTaxiWorkspace1`|
    |||
    
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Synapse

1. No Estúdio Synapse, navegue para o **Develop > Power BI**.
1. Navegue para **NYCTaxiWorkspace1 > conjuntos** de dados power BI e selecione o conjunto de **dados New Power BI**.
1. Passe por cima da `SQLDB1` base de dados e selecione download **.pbids file**.
1. Abra o `.pbids` ficheiro descarregado. 
1. Isto lançará o ambiente de trabalho Power BI e ligá-lo-á automaticamente no seu espaço de `SQLDB1` trabalho sinapse.
1. Se vir um dialog aparece chamado base de dados do **servidor SQL**: a. Selecione **a conta microsoft**. 
    b. Selecione **Iniciar sessão** e iniciar sessão.
    c. Selecione **Ligar**.
1. O diálogo **do Navigator** abrirá. Quando isso acontecer, verifique a tabela **PassengerCountStats** e selecione **Load**.
1. Aparecerá um diálogo de definições de **ligação.** Selecione **DirectQuery** e selecione **OK**
1. Selecione o botão **Relatório** à esquerda.
1. Adicione **o gráfico** de linha ao seu relatório.
    a. Arraste a coluna **PasssengerCount** para **Visualizações > Eixo** B. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações > Valores**.
1. No separador **Home,** **selecione Publicar**.
1. Perguntar-lhe-á se quer poupar as suas alterações. Selecione **Guardar**.
1. Vai pedir-lhe para escolher um nome de arquivo. Escolha `PassengerAnalysis.pbix` e selecione **Guardar**.
1. Pedirá-lhe que **selecione um destino** `NYCTaxiWorkspace1` e selecione **.**
1. Espere que a publicação termine.

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

1. Abrir [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **iniciar sessão**
1. À esquerda, sob espaços de **trabalho** selecione o `NYCTaxiWorkspace1` espaço de trabalho.
1. Dentro desse espaço de trabalho deve-se ver um conjunto de dados chamado `Passenger Analysis` e um relatório chamado `Passenger Analysis` .
1. Passe sobre o conjunto de `PassengerAnalysis` dados e selecione o ícone com os três pontos e selecione **Definições**.
1. Nas **credenciais de origem de dados,** delineie o método de **Autenticação** para **OAuth2** e selecione **Iniciar sessão**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar uma reportagem no Estúdio Synapse

1. Volte ao Estúdio Synapse e selecione **Close e refresh** 
1. Navegue para o centro **de Desenvolvimento** 
1. Passe sobre **power BI** e clique no refresco do nó de **relatórios Power BI.**
1. Agora, sob o **Power BI,** deve ver: a. * Em **NYCTaxiWorkspace1 > conjuntos**de dados power BI, um novo conjunto de dados chamado **PassengerAnalysis**.
    b. * Sob **os relatórios NYCTaxiWorkspace1 > Power BI,** um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis.** 
1. O relatório será aberto e agora pode editar o relatório diretamente no Estúdio Synapse.

## <a name="monitor-activities"></a>Monitorizar atividades

1. No Estúdio Synapse, navegue até ao centro de monitores.
1. Neste local, pode ver-se um histórico de todas as atividades que estão a decorrer no espaço de trabalho e quais as que estão ativas agora.
1. Explore as execuções do **Pipeline,** **aplicações Apache Spark**e pedidos **SQL** e poderá ver o que já fez no espaço de trabalho.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [o Azure Synapse Analytics (pré-visualização)](overview-what-is.md)

