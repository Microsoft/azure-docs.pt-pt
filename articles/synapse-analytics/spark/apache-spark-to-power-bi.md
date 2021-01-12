---
title: Blocos de notas do Azure Synapse Studio
description: Este tutorial fornece uma visão geral sobre como criar um painel Power BI usando Apache Spark e uma piscina SQL sem servidor.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121078"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Tutorial: Criar um relatório Power BI usando Apache Spark e Azure Synapse Analytics

As organizações precisam frequentemente de processar grandes volumes de dados antes de servirem aos principais stakeholders empresariais. Neste tutorial, aprenderá a aproveitar as experiências integradas no Azure Synapse Analytics para processar dados usando o Apache Spark e mais tarde servir os dados aos utilizadores finais através do Power BI e do Serverless SQL.

## <a name="before-you-begin"></a>Before you begin
- [Espaço de trabalho Azure Synapse Analytics](../quickstart-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento predefinido. 
- Power BI workspace e Power BI Desktop para visualizar dados. Para mais detalhes, consulte [criar um espaço de trabalho Power BI](/power-bi/service-create-the-new-workspaces) e instalar o ambiente de trabalho Power [BI](https://powerbi.microsoft.com/downloads/)
- Serviço ligado para ligar o seu Azure Synapse Analytics e espaços de trabalho Power BI. Para mais detalhes, consulte [a ligação a um espaço de trabalho Power BI](../quickstart-power-bi.md)
- Piscina Apache Spark sem servidor no seu espaço de trabalho Synapse Analytics. Para mais detalhes, consulte [criar uma piscina Apache Spark sem servidor](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados
Neste exemplo, você usará Apache Spark para realizar algumas análises sobre dados de dicas de viagem de táxi de Nova Iorque. Os dados estão disponíveis através [de Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o tempo e locais de início e fim, o custo e outros atributos interessantes.

1. Executar as seguintes linhas para criar um dataframe Spark colando o código numa nova célula. Isto recupera os dados através da API Open Datasets. Puxar todos estes dados gera cerca de 1,5 mil milhões de linhas. O seguinte exemplo de código utiliza start_date e end_date para aplicar um filtro que devolve um único mês de dados.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Usando o Apache Spark SQL, vamos criar uma base de dados chamada NycTlcTutorial. Utilizaremos esta base de dados para armazenar os resultados do nosso processamento de dados.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Em seguida, usaremos as operações do dataframe da Spark para processar os dados. No seguinte código, realizamos as seguintes transformações:
   1. A remoção de colunas que não são necessárias.
   2. A remoção de valores outliers/incorretos através da filtragem.
   3. A criação de novas funcionalidades como ```tripTimeSecs``` e ```tipped``` para análise adicional.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Finalmente, salvaremos o nosso dataframe usando o método Apache ```saveAsTable``` Spark. Isto permitir-lhe-á consultar e ligar-se à mesma mesa utilizando piscinas SQL sem servidor.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Dados de consulta usando piscinas SQL sem servidor
O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Apache Spark sem servidor e a piscina SQL sem servidor. Isto é alimentado através da capacidade [de gestão de metadados partilhados](../metadata/overview.md) da Synapse. Como resultado, o Spark criou bases de dados e as suas tabelas apoiadas por parquet tornam-se visíveis na piscina SQL sem servidores do espaço de trabalho.

Para consultar a sua mesa Apache Spark utilizando a sua piscina SQL sem servidor:
   1. Depois de ter guardado a sua tabela Apache Spark, mude para o separador **de dados.**
   
   2. Em **Workspaces**, encontre a tabela Apache Spark que acabou de criar e selecione **o novo script SQL** e, em seguida, **selecione TOP 100 linhas**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="Consulta SQL." border="true":::

   3. Pode continuar a aperfeiçoar a sua consulta ou até visualizar os seus resultados utilizando as opções de gráfico SQL.

## <a name="connect-to-power-bi"></a>Ligar ao Power BI
Em seguida, ligaremos a nossa piscina SQL sem servidor ao nosso espaço de trabalho Power BI. Uma vez ligado o seu espaço de trabalho, poderá criar relatórios Power BI tanto a partir do Azure Synapse Analytics como do desktop Power BI.

>[!Note]
> Antes de começar, terá de configurar um serviço ligado ao seu [espaço de trabalho Power BI](../quickstart-power-bi.md) e descarregar o ambiente de trabalho Power [BI](/power-bi/service-create-the-new-workspaces).  

Para ligar a nossa piscina SQL sem servidor ao nosso espaço de trabalho Power BI:

1.  Navegue para o **separador conjuntos de dados Power BI** e selecione a opção para **+ Novo Conjunto de Dados**. A partir do pedido, descarregue o ficheiro .pbids da base de dados SQL Analytics que pretende utilizar como fonte de dados. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Conjuntos de dados de Power BI." border="true":::

2.  Abra o ficheiro com o Power BI Desktop para criar um conjunto de dados. Assim que abrir o ficheiro, ligue-se à base de dados do servidor SQL utilizando a **conta Microsoft** e a opção **Import.** 
   

## <a name="create-a-power-bi-report"></a>Criar um relatório do Power BI
1. A partir do ambiente de trabalho Power BI, pode agora adicionar um gráfico **de influenciadores chave** ao seu relatório.
   
   1. Arraste a coluna *média de altura da ponta* para **analisar** o eixo.
   
   2. Arraste as colunas **WeekdayDstring**, **tripDistance** média e as **colunas de tripTimeSecs médias** para o **explain por** eixo. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. No separador Power BI desktop Home, **selecione Publicar** e **guardar** alterações. Introduza um nome de ficheiro e guarde este relatório para o *Espaço de Trabalho NycTaxiTutorial*.
   
3. Além disso, também pode criar visualizações Power BI dentro do seu espaço de trabalho Azure Synapse Analytics. Para isso, navegue no separador **Desenvolvimento** no seu espaço de trabalho Azure Synapse e abra o separador Power BI. A partir daqui, pode selecionar o seu relatório e continuar a construir visualizações adicionais. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Espaço de trabalho Azure Synapse Analytics." border="true":::

Para obter mais detalhes sobre como criar um conjunto de dados através de SQL sem servidor e ligar-se ao Power BI, pode visitar este tutorial sobre [a ligação ao ambiente de trabalho Power BI](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Passos seguintes
Pode continuar a aprender mais sobre as capacidades de visualização de dados no Azure Synapse Analytics visitando os seguintes documentos e tutoriais:
   - [Visualizar dados com piscinas Apache Spark sem servidor](../spark/apache-spark-data-visualization-tutorial.md)
   - [Visão geral da visualização de dados com piscinas Apache Spark](../spark/apache-spark-data-visualization.md)