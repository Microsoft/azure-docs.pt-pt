---
title: 'Quickstart: Crie uma piscina Apache Spark (pré-visualização) no Azure Synapse Analytics'
description: Este quickstart mostra como usar as ferramentas web para criar uma piscina Apache Spark (pré-visualização) em Azure Synapse Analytics, e executar uma consulta Spark SQL.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424301"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Quickstart: Crie uma piscina Apache Spark (pré-visualização) no Synapse Analytics usando ferramentas web

Neste arranque rápido, aprende-se a criar uma piscina Apache Spark (pré-visualização) no Azure Synapse Analytics utilizando ferramentas web. Em seguida, aprenda a ligar-se à piscina Apache Spark e execute consultas Spark SQL contra ficheiros e tabelas. O Apache Spark permite uma análise de dados e computação de clusters rápidas através do processamento dentro da memória. Para obter informações sobre Spark on Synapse Analytics, consulte [a visão geral: Apache Spark on Azure Synapse Analytics](apache-spark-overview.md).

> [!IMPORTANT]
> A faturação dos casos Spark é pronunciada por minuto, quer esteja a usá-las ou não. Certifique-se de que encerra a sua instância Spark depois de terminar de usá-la, ou demarcar um curto prazo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-spark-pool"></a>Crie uma piscina apache spark

Este artigo mostra-lhe como criar uma nova piscina Apache Spark usando ferramentas web.

## <a name="create-a-notebook"></a>Criar um bloco de notas

Um caderno é um ambiente interativo que suporta várias linguagens de programação. O caderno permite-lhe interagir com os seus dados, combinar código com marcação, texto e realizar visualizações simples.

1. A partir da vista do portal Azure para o espaço de trabalho Synapse Analytics que pretende utilizar, selecione **Launch Synapse Studio**.
2. Assim que o Estúdio Synapse Analytics tiver sido lançado, selecione **Develop**. Então, paire sobre a entrada dos **Cadernos.** Selecione a elipse **(...**).
3. A partir daí, selecione **Novo caderno**. Um novo caderno é criado e aberto com um nome gerado automaticamente.
  ![Novo caderno](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Novo caderno")

4. Na janela **Propriedades,** forneça um nome para o caderno.
5. Na barra de ferramentas, clique **em Publicar**.
6. Se houver apenas uma piscina Apache Spark no seu espaço de trabalho, então é selecionada por padrão. Utilize a gota para selecionar a piscina de Apache Spark correta se nenhuma for selecionada.
7. Clique em **Adicionar código**. A linguagem `Pyspark`padrão é . Você vai usar uma mistura de Pyspark e Spark SQL, então a escolha padrão é boa.
8. Em seguida, cria-se um simples objeto Spark DataFrame para manipular. Neste caso, cria-se a partir do código. Há três linhas e três colunas:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Agora, execute a célula usando um dos seguintes métodos:

   - Turno de pressão **+ ENTER**.
   - Selecione o ícone de reprodução azul à esquerda da célula.
   - Selecione o botão **Executar todos** na barra de ferramentas.

   ![Criar objeto de quadro de dados](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Saída do trabalho de Spark")

10. Se a instância da piscina Apache Spark ainda não estiver em funcionamento, é automaticamente iniciada. Pode ver o estado da piscina Apache Spark abaixo da célula que está a executar e também no painel de estado na parte inferior do caderno. Dependendo do tamanho da piscina, começar deve demorar 2-5 minutos. Uma vez que o código tenha terminado de funcionar, as informações abaixo dos ecrãs das células mostram quanto tempo demorou a correr e a sua execução. Na célula de saída, vê-se a saída.

    ![Saída da execução de uma célula](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Saída do trabalho de Spark")

11. Os dados existem agora num DataFrame a partir daí pode utilizar os dados de várias maneiras diferentes. Vai precisar em diferentes formatos para o resto deste arranque rápido.
12. Introduza o código abaixo em outra célula e execute-o, isto cria uma tabela Spark, um CSV, e um ficheiro Parquet todos com cópias dos dados:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Se utilizar o explorador de armazenamento, é possível ver o impacto das duas formas diferentes de escrever um ficheiro usado acima. Quando nenhum sistema de ficheiros é especificado, `default>user>trusted-service-user>demo_df`então a predefinição é utilizada, neste caso . Os dados são guardados na localização do sistema de ficheiros especificado.

    Note nos formatos "csv" e "parquet", escreva operações um diretório é criado com muitos ficheiros divididos.

    ![Vista exploradorde armazenamento da saída](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "Vista exploradorde armazenamento da saída")

    ![Vista exploradorde armazenamento da saída](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "Vista exploradorde armazenamento da saída")

## <a name="run-spark-sql-statements"></a>Executar instruções SQL do Spark

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e definir dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Colhe o seguinte código numa cela vazia e, em seguida, executar o código. O comando lista as mesas na piscina.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Quando utiliza um Notebook com a sua piscina Synapse `sqlContext` Analytics Apache Spark, obtém-se um predefinição que pode utilizar para executar consultas utilizando o Spark SQL. `%%sql`diz ao caderno para `sqlContext` usar o predefinido para executar a consulta. A consulta recupera as 10 melhores filas de uma tabela de sistemas que vem com todas as piscinas Synapse Analytics Apache Spark por padrão.

2. Execute outra consulta para ver os dados no `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    O código produz duas células de saída, uma que contém resultados de dados, a outra, que mostra a visão do trabalho.

    Por defeito, a vista de resultados mostra uma grelha, mas existe um interruptor de visualização por baixo da grelha que permite que a vista altere entre as vistas da grelha e do gráfico.

    ![Saída de consulta em Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Saída de consulta em Synapse Analytics Spark")

3. No comutador **'Ver',** selecione **Gráfico**
4. Selecione o ícone das **opções de Visualização** do lado direito.
5. No campo **do tipo Gráfico,** selecione "gráfico de barras".
6. No campo da coluna do eixo X, selecione "estado".
7. No campo da coluna do eixo Y, selecione "salário".
8. No campo **de Agregação,** selecione para "AVG".
9. Selecione **Aplicar**.

   ![Produção de gráficos em Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Produção de gráficos em Synapse Analytics Spark")

10. É possível obter a mesma experiência de executar SQL mas sem ter que mudar de idiomas. Pode fazê-lo substituindo a célula SQL acima por esta célula PySpark, a experiência de saída é a mesma porque o comando de **exibição** é utilizado:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Cada uma das células que anteriormente executadas tinha a opção de ir ao **History Server** e **à Monitorização.** Clicar nos links leva-o a diferentes partes da Experiência do Utilizador.

## <a name="clean-up-resources"></a>Limpar recursos

A Synapse Analytics guarda os seus dados no Armazenamento do Lago Azure Data. Pode deixar uma instância spark desligar-se com segurança quando não estiver a ser utilizada. Você é cobrado por uma piscina Synapse Analytics Apache Spark desde que esteja em funcionamento, mesmo quando não está em uso. Uma vez que as taxas para a piscina são muitas vezes mais do que as taxas de armazenamento, faz sentido económico deixar as instâncias spark encerrarquando não estão a ser utilizadas.

Para garantir que a instância Spark é encerrada, termine as sessões ligadas (cadernos). A piscina fecha quando o **tempo de inativo** especificado na piscina apache Spark é atingido. Também pode selecionar a **sessão final** a partir da barra de estado na parte inferior do caderno.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma piscina Synapse Analytics Apache Spark e executar uma consulta básica de Spark SQL.

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Documentação oficial de Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Parte da documentação oficial da Apache Spark baseia-se na utilização da consola Spark, que não está disponível no Azure Synapse Spark. Utilize o [caderno](../spark/apache-spark-notebook-create-spark-use-sql.md) ou as experiências [IntelliJ.](../spark/intellij-tool-synapse.md)
