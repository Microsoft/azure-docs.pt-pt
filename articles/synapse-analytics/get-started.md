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
ms.openlocfilehash: e826075d2f0032f796ebe6d2c8648130e5b453b7
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800896"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Começando com Azure Synapse Analytics

Este tutorial irá guiá-lo através de todos os passos básicos necessários para configurar e utilizar o Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Prepare uma conta de armazenamento para uso com um espaço de trabalho Synapse

* Abra o [portal do Azure](https://portal.azure.com)
* Criar uma nova conta de armazenamento com as seguintes definições:
    * No separador **Basics**

    |Definição | Valor sugerido | Descrição |
    |---|---|---|
    |**Nome da conta de armazenamento**| Pode dar-lhe qualquer nome.|Neste documento, vamos referir-nos a ele como `contosolake` .
    |**Tipo de conta**|Deve ser definido para`StorageV2`||
    |**Localização**|Pode escolher qualquer local| Recomendamos que o seu espaço de trabalho Synapse e a conta de Gen2 do Lago De Dados Azure (ADLS) estejam na mesma região.|
    ||||

    * No separador **Avançado**

    |Definição | Valor sugerido | Descrição |
    |---|---|---|
    |**Armazenamento do Data Lake Ger2**|`Enabled`| A Azure Synapse só trabalha com contas de armazenamento onde esta definição está ativada.|
    ||||

* Uma vez criada a conta de armazenamento, faça estas atribuições de funções ou certifique-se de que já estão atribuídas. Na conta de armazenamento, selecione o controlo de **acesso (IAM)** a partir da navegação esquerda.
    * Atribua-se ao papel **proprietário** na conta de armazenamento
    * Atribua-se ao papel do Proprietário de **Dados blob** de armazenamento na Conta de Armazenamento
* A partir da navegação à esquerda, selecione **Recipientes** e crie um recipiente. Pode dar-lhe qualquer nome. Aceite o **nível**de acesso público padrão . Neste documento, chamaremos o recipiente `users` . Selecione **Criar**. 

## <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

* Abra o [portal Azure](https://portal.azure.com) e na procura de topo `Synapse` por .
* Nos resultados da pesquisa em **Serviços,** **selecione Azure Synapse Analytics (pré-visualização** dos espaços de trabalho)
* Selecione **+ Adicionar**
* **Separador básico:**

    |Definição | Valor sugerido | Descrição |
    |---|---|---|
    |**Nome da área de trabalho**|Pode chamar-lhe qualquer coisa.| Neste documento, usaremos`myworkspace`
    |**Região**|Corresponder à região da conta de armazenamento||
    |||

* Em **Select Data Lake Storage Gen 2** selecione a conta e o recipiente que criou anteriormente

> [!NOTE]
> A conta de armazenamento escolhida aqui será referida como a conta de armazenamento "primária" do espaço de trabalho Synapse

* Selecione **Rever + criar**. Selecione **Criar**. O seu espaço de trabalho estará pronto em poucos minutos.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Verifique se o espaço de trabalho Synapse MSI tem acesso à conta de armazenamento

Isto pode já ter sido feito por si. De qualquer forma, deve verificar.

* Abra o [portal Azure](https://portal.azure.com) abra a conta de armazenamento primária escolhida para o seu espaço de trabalho.
* Certifique-se de que a seguinte atribuição existe ou crie-a se não existir
    * Papel de Colaborador de Dados blob de armazenamento na conta de armazenamento para o seu espaço de trabalho.
    * Para atribuir esta função ao espaço de trabalho, selecione a função De contribuinte de dados do Depósito Blob, deixe o acesso padrão atribuir **e** no tipo de caixa **Select** o nome do seu espaço de trabalho. Selecione **Guardar**.
    
## <a name="launch-synapse-studio"></a>Lançar Estúdio Synapse

Assim que o seu espaço de trabalho Synapse for criado, você tem duas maneiras de abrir o Estúdio Synapse:
* Abra o seu espaço de trabalho Synapse no [portal Azure](https://portal.azure.com) e no topo da secção **Overview** selecione **Launch Synapse Studio**
* Vá diretamente https://web.azuresynapse.net e faça login no seu espaço de trabalho.

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

* No Estúdio Synapse, no lado esquerdo navega para **gerir piscinas SQL >**
* NOTA: Todos os espaços de trabalho synapse vêm com uma piscina pré-criada chamada **SQL on-demand**.
* **Selecione +Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina SQL**| `SQLDB1`|
    |**Nível de desempenho**|`DW100C`|
* Selecione **Review+criar** e, em seguida, **selecione Criar**.
* Sua piscina estará pronta em alguns minutos.

> [!NOTE]
> Uma piscina Synapse SQL corresponde ao que costumava ser chamado de "Armazém de Dados Azure SQL"

* Uma piscina SQL consome recursos faturados desde que esteja a funcionar. Então, você pode parar a piscina quando necessário para reduzir custos.
* Quando o seu pool SQL for criado, será associado a uma base de dados de piscina SQL também chamada **SQLDB1**.

## <a name="create-an-apache-spark-pool-for-azure-synapse-analytics"></a>Crie uma piscina Apache Spark para Azure Synapse Analytics

* No Estúdio Synapse, no lado esquerdo selecione **Manage > Apache Spark pools**
* **Selecione +Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina Apache Spark**|`Spark1`
    |**Tamanho do nó**| `Small`|
    |**Número de nós**| Desmarcar o mínimo para 3 e o máximo para 3|
    |||

* Selecione **Review+criar** e, em seguida, **selecione Criar**.
* Sua piscina Apache Spark estará pronta em alguns segundos.

> [!NOTE]
> Apesar do nome, uma piscina Apache Spark não é como uma piscina SQL. São apenas alguns metadados básicos que usas para informar o espaço de trabalho synapse como interagir com o Spark. 

* Por serem metadados, as piscinas Spark não podem ser iniciadas ou paradas. 
* Quando você faz qualquer atividade spark em Synapse, você especifica uma piscina Spark para usar. A piscina informa a Synapse quantos recursos da Spark usar. Só pagas pelos recursos que a Thar é usada. Quando deixar de utilizar ativamente a piscina, os recursos irão automaticamente esgotar-se e ser reciclados.
> [!NOTE]
> As bases de dados de faíscas são criadas independentemente a partir de piscinas Spark. Um espaço de trabalho tem sempre um Spark DB chamado **padrão** e pode criar bases de dados adicionais spark.

## <a name="sql-on-demand-pools"></a>Piscinas on-demand SQL

SQL on-demand é um tipo especial de piscina SQL que está sempre disponível com um espaço de trabalho Synapse. Permite-lhe trabalhar com a SQL sem ter de criar ou pensar em gerir uma piscina SYnapse SQL.

> [!NOTE]
> Ao contrário dos outros tipos de piscinas, a faturação para a Procura sQL baseia-se na quantidade de dados digitalizados para executar a consulta - e não no número de recursos utilizados para executar a consulta.

* A SQL on-demand também tem o seu próprio tipo de bases de dados sQL on-demand que existem independentemente de qualquer pool on-demand SQL.
* Atualmente, um espaço de trabalho tem sempre exatamente um pool SQL on-demand chamado **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregue os dados da amostra de táxi de NYC na base de dados SQLDB1

* No Estúdio Synapse, no menu mais azul, selecione o **?** Afixar.
* Select **Getting started > Getting started hub**
* No cartão rotulado Dados da **amostra de consulta** selecione o pool SQL nomeado`SQLDB1`
* Selecione **dados de consulta**. Verá uma notificação a dizer "Carregar dados da amostra" que aparecerão e depois desaparecerão.
* Verá uma barra de notificação azul-claro perto do topo do Estúdio Synapse indicando que os dados estão a ser carregados no SQLDB1. Espere até ficar verde e depois dispense-o.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explore os dados do táxi de NYC no SQL Pool

* No Estúdio Synapse, navegue para o centro de **dados**
* Navegue para **tabelas de > SQLDB1**. Verá que várias mesas foram carregadas.
* Clique na direita no **dbo. Mesa de viagem** e selecione **Novo Script SQL > Selecione TOP 100 Rows**
* Um novo script SQL será criado e executado automaticamente.
* Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada SQLDB1.
* Substitua o texto do script SQL por este código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

* Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros
* Na janela de resultados do script SQL altere a **Vista** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha

## <a name="create-a-spark-database-and-load-the-nyc-taxi-data-into-it"></a>Crie uma base de dados spark e carregue os dados do táxi de NYC nele

Temos dados disponíveis numa base de dados de piscinaS SQL. Agora colocamos numa base de dados do Spark.

* No Estúdio Synapse, navegue para o hub **Develop"
* **+** Selecione e selecione **Notebook**
* No topo do caderno, definir o **Anexo ao** valor para`Spark1`
* **Selecione Adicionar código** para adicionar uma célula de código portátil e colar o texto abaixo:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

 * Navegue para o centro de Dados, clique à direita nas bases de dados e selecione **Refresh**
 * Agora deve ver estas bases de dados:
     * SQLDB (piscina SQL)
     * nyctaxi (Faísca)
      
 ## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do táxi de NYC usando faíscas e cadernos

 * Volte ao seu caderno
 * Crie uma nova célula de código, introduza o texto abaixo e execute a célula

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

 * Execute este código para realizar a mesma análise que fizemos anteriormente com a piscina SQL

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

 * Nos resultados da célula, selecione **Gráfico** para ver os dados visualizados
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personalize dados de visualização de dados com Faísca seleções e cadernos

Com cadernos de faíscas pode controlar exatamente como renderiza gráficos. O seguinte código mostra um exemplo simples usando as bibliotecas populares matplotlib e nascidos no mar. Renderá o mesmo gráfico que viu ao executar as consultas SQL mais cedo.

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

Anteriormente copiámos dados de uma base de dados de piscinaS SQL para um Spark DB. Usando o Spark, agregamos os dados nos números de nyctaxi.passengercountstats. Agora, execute a célula abaixo num caderno e copiará a tabela agregada de volta para a base de dados de piscinaS SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analise os dados de táxi de NYC nas bases de dados spark usando a procura de SQL-on 

* As tabelas nas bases de dados Spark são automaticamente visíveis e consultadas pela SQL a pedido
* No Estúdio Synapse navegue para o hub Develop e crie um novo script SQL
* Definir **Ligar para** **sql a pedido** 
* Colar o seguinte texto no script:

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

* Selecione **Executar**
* NOTA: A primeira vez que executa isto levará cerca de 10 segundos para a SQL a pedido de recolher os recursos SQL necessários para executar as suas consultas. As consultas subsequentes não requerem desta vez.
  
## <a name="use-pipelines-to-orchestrate-activities"></a>Use oleodutos para orquestrar atividades

Você pode orquestrar uma grande variedade de tarefas em Azure Synapse. Nesta secção, verá como é fácil.

* No Estúdio Synapse, navegue até ao centro de Orchestrate.
* **+** Selecione e, em seguida, selecione **Pipeline**. Será criado um novo oleoduto.
* Navegue até ao centro de Desenvolvimento e encontre qualquer um dos cadernos que criou anteriormente.
* Arraste o caderno para o oleoduto.
* No gasoduto, **selecione Adicionar gatilho > Nova/edição**.
* Em **Escolha selecione** **Novo**, e, em seguida, em recorrência, despolete o gatilho a funcionar a cada 1 hora.
* Selecione **OK**.
* Selecione **Publicar Tudo** e o gasoduto será executado a cada hora.
* Se quiser fazer o gasoduto funcionar agora sem esperar pela próxima hora, adicione **o gatilho > Novo/editar**.

## <a name="working-with-data-in-a-storage-account"></a>Trabalhar com dados numa conta de armazenamento

Até agora, cobrimos cenários que residem em bases de dados. Agora vamos mostrar como o Azure Synapse pode analisar ficheiros simples numa conta de armazenamento. Neste cenário usaremos a conta de armazenamento e o contentor a que ligamos o espaço de trabalho.

O nome da conta de armazenamento: contosolake O nome do contentor na conta de armazenamento: utilizadores

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Criação de ficheiros CSV e Parquet na sua conta de Armazenamento

Execute o seguinte código num caderno. Cria um CSV e dados de parquet na conta de armazenamento

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analisar dados numa conta de armazenamento

* No Estúdio Synapse, navegue para o centro de **dados**
* Selecione **Linked**
* Navegar para **contas de armazenamento > nome do espaço de trabalho (Primary - contosolake)**
* Selecione **utilizadores (Primário)"**
* Devia ver uma pasta chamada "NYCTaxi". No seu interior deverá ver duas pastas 'PassengerCountStats.csv' e 'PassengerCountStats.parquet'.
* Navegue na pasta 'PassengerCountStats.parquet'.
* Clique à direita no ficheiro parquet no interior e selecione um novo caderno, irá criar um caderno com uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

* Execute a célula para analisar o arquivo de parquet com faísca.
* Clique à direita no ficheiro parquet no interior e selecione o **novo script SQL > SELECT TOP 100 linhas,** criará um caderno com uma célula como esta:

    ```py
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
* O script será anexado à **SQL on-demand** executar o script. Reparem que infere o esquema do ficheiro parquet.

## <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

Os seus dados podem agora ser facilmente analisados e visualizados no Power BI. A Synapse oferece uma integração única que lhe permite ligar um espaço de trabalho power BI ao seu espaço de trabalho Synapse. Antes de começar, siga primeiro os passos neste [arranque rápido](quickstart-power-bi.md) para ligar o seu espaço de trabalho Power BI.

### <a name="create-a-power-bi-workspace-and-link-it-to-your-synapse-workspace"></a>Crie um espaço de trabalho Power BI e ligue-o ao seu espaço de trabalho Synapse

* Inicie [sessão powerbi.microsoft.com](https://powerbi.microsoft.com/).
* Crie um novo espaço de trabalho Power BI chamado `NYCTaxiWorkspace1` .
* No Estúdio Synapse, navegue até ao **Manage > Linked Services.**
* Selecione **+ Novo** e selecione **Ligar ao Power BI** e definir estes campos:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome**|`NYCTaxiWorkspace1`|
    |**Nome da área de trabalho**|`NYCTaxiWorkspace1`|
    |||
    
* Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Synapse

* No Estúdio Synapse, navegue para o **Develop > Power BI**.
* Navegue para **NYCTaxiWorkspace1 > conjuntos** de dados power BI e selecione o conjunto de **dados New Power BI**.
* Passe sobre a base de dados SQLDB1 e selecione **download .pbids file**.
* Abra o `.pbids` ficheiro descarregado. Isto lançará o ambiente de trabalho Power BI e ligá-lo-á automaticamente ao SQLDB1 no seu espaço de trabalho sinapse.
* Se vir um dialog aparece chamado base de dados do **servidor SQL:**
    * Selecione **a conta microsoft**. 
    * Selecione **Iniciar sessão** e iniciar sessão.
    * Selecione **Ligar**.
* O diálogo **do Navigator** abrirá. Quando verificar a tabela **PassengerCountStats** e selecionar **Load**.
* Aparecerá um diálogo de definições de **ligação.** Selecione **DirectQuery** e selecione **OK**
* Selecione o botão **Relatório** à esquerda.
* Adicione **o gráfico** de linha ao seu relatório.
    * Arraste a coluna **PasssengerCount** para **Visualizações > Eixo**
    * Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações > Valores**.
* No separador **Home,** **selecione Publicar**.
* Perguntar-lhe-á se quer poupar as suas alterações. Selecione **Guardar**.
* Vai pedir-lhe para escolher um nome de arquivo. Escolha `PassengerAnalysis.pbix` e selecione **Guardar**.
* Pedirá-lhe que **selecione um destino** `NYCTaxiWorkspace1` e selecione **.**
* Espere que a publicação termine.

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

* Abrir [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **iniciar sessão**
* À esquerda, no âmbito dos **Espaços de Trabalho** selecione o espaço de trabalho a que `NYCTaxiWorkspace1` publicou.
* Dentro desse espaço de trabalho deve-se ver um conjunto de dados chamado `Passenger Analysis` e um relatório chamado `Passenger Analysis` .
* Passe sobre o conjunto de `PassengerAnalysis` dados e selecione o ícone com os três pontos e selecione **Definições**.
* Nas **credenciais de origem de dados,** delineie o método de Autenticação para **OAuth2** e selecione **Iniciar sessão**.

### <a name="edit-a-report-report-in-synapse-studio"></a>Editar um relatório no Estúdio Synapse

* Volte ao Estúdio Synapse e selecione **Close e refresh** agora deve ver:
    * No âmbito dos conjuntos de **dados Power BI,** um novo conjunto de dados chamado **PassengerAnalysis**.
    * No âmbito dos conjuntos de **dados Power BI,** um novo relatório chamado **PassengerAnalysis**.
* CLick no relatório **PassengerAnalysis.** 
    * Não mostrará nada porque ainda precisa de configurar a autenticação para o conjunto de dados.
* No SynapseStudio, navegue para **desenvolver > Power BI > O seu nome de espaço de trabalho > relatórios Power BI**.
* Feche as janelas que mostrem o relatório power bi.
* Refresque o nó de **relatórios power bi.**
* Selecione o relatório e agora pode editar o relatório diretamente no Estúdio Synapse.

## <a name="monitor-activities"></a>Monitorizar atividades

* No Estúdio Synapse, navegue para o centro de monitores.
* Neste local pode ver-se um histórico de todas as atividades que estão a decorrer no espaço de trabalho e quais as que estão ativas agora.
* Explore as execuções do **Pipeline,** **aplicações Apache Spark**e pedidos **SQL** e poderá ver o que já fez no espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Synapse Analytics (pré-visualização)](overview-what-is.md)

