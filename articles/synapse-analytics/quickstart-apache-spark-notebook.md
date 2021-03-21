---
title: 'Quickstart: Criar uma piscina Apache Spark sem servidor usando ferramentas web'
description: Este quickstart mostra como usar as ferramentas web para criar uma piscina Apache Spark sem servidor em Azure Synapse Analytics e como executar uma consulta Spark SQL.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669485"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Quickstart: Criar uma piscina Apache Spark sem servidor em Azure Synapse Analytics usando ferramentas web

Neste arranque rápido, você aprende a criar uma piscina Apache Spark sem servidor em Azure Synapse usando ferramentas web. Em seguida, aprende a ligar-se à piscina Apache Spark e a executar consultas Spark SQL contra ficheiros e tabelas. O Apache Spark permite uma análise de dados e computação de clusters rápidas através do processamento dentro da memória. Para obter informações sobre a Spark in Azure Synapse, consulte [a visão geral: Apache Spark on Azure Synapse](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> A faturação de casos de Spark é prostimada por minuto, quer esteja a usá-las ou não. Certifique-se de que desliga a sua instância Spark depois de ter terminado de a utilizar ou de definir um curto período de tempo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho Synapse Analytics](quickstart-create-workspace.md)
- [Piscina apache spark sem servidor](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-notebook"></a>Criar um bloco de notas

Um caderno é um ambiente interativo que suporta várias linguagens de programação. O caderno permite interagir com os seus dados, combinar código com marcação, texto e realizar visualizações simples.

1. A partir da vista do portal Azure para o espaço de trabalho Azure Synapse que pretende utilizar, selecione **Launch Synapse Studio**.
2. Assim que o Synapse Studio tiver sido lançado, **selecione Develop**. Em seguida, selecione o **+** ícone " " para adicionar um novo recurso.
3. A partir daí, selecione **Caderno.** Um novo caderno é criado e aberto com um nome gerado automaticamente.
 
     ![Novo caderno](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Novo caderno")

4. Na janela **Propriedades,** forneça um nome para o caderno.
5. Na barra de ferramentas, clique em **Publicar.**
6. Se existe apenas uma piscina Apache Spark no seu espaço de trabalho, então é selecionada por padrão. Utilize o drop-down para selecionar a piscina Apache Spark correta se nenhuma for selecionada.
7. Clique **em Adicionar código**. A língua padrão é `Pyspark` . Você vai usar uma mistura de Pyspark e Spark SQL, então a escolha padrão é bom. Outras línguas suportadas são Scala e .NET para Spark.
8. Em seguida, cria-se um simples objeto Spark DataFrame para manipular. Neste caso, cria-se a partir do código. Há três linhas e três colunas:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Agora, executar a célula usando um dos seguintes métodos:

   - MUDANÇA DE IMPRENSA **+ ENTER**.
   - Selecione o ícone de reprodução azul para a esquerda da célula.
   - Selecione o botão **Executar todos os** botões na barra de ferramentas.

       ![Criar objeto de quadro de dados](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Se a instância da piscina Apache Spark ainda não está em funcionamento, é automaticamente iniciada. Pode ver o estado da divisão Apache Spark abaixo da célula que está a executar e também no painel de estado na parte inferior do caderno. Dependendo do tamanho da piscina, o arranque deve demorar 2-5 minutos. Uma vez terminado o código, as informações abaixo dos ecrãs da célula mostram quanto tempo demorou a ser executado e a sua execução. Na célula de saída, vê-se a saída.

    ![Saída da execução de uma célula](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Os dados existem agora num DataFrame a partir daí, pode utilizar os dados de muitas maneiras diferentes. Vais precisar dele em formatos diferentes para o resto deste arranque rápido.
12. Introduza o código abaixo em outra célula e execute-o, isto cria uma tabela Spark, um CSV, e um arquivo Parquet todos com cópias dos dados:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Se utilizar o explorador de armazenamento, é possível ver o impacto das duas formas diferentes de escrever um ficheiro usado acima. Quando nenhum sistema de ficheiros é especificado, então o padrão é utilizado, neste caso `default>user>trusted-service-user>demo_df` . Os dados são guardados para a localização do sistema de ficheiros especificado.

    Note nos formatos "csv" e "parquet", as operações de escrita são criadas com muitos ficheiros divididos.

    ![Visão explorador de armazenamento da saída](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "Visão explorador de armazenamento da saída")

    ![Screenshot que realça o caminho de demodata > padrão > demo_df.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Visão explorador de armazenamento da saída")

## <a name="run-spark-sql-statements"></a>Executar instruções SQL do Spark

A Linguagem De Consulta Estruturada (SQL) é a língua mais comum e amplamente utilizada para consulta e definição de dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Cole o seguinte código numa cela vazia e, em seguida, executar o código. O comando lista as mesas na piscina.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Quando utilizar um Caderno com a sua piscina Azure Synapse Apache Spark, obtém uma predefinição `sqlContext` que pode utilizar para executar consultas utilizando o Spark SQL. `%%sql` diz ao caderno para usar a predefinição `sqlContext` para executar a consulta. A consulta recupera as 10 primeiras linhas de uma tabela de sistema que vem com todas as piscinas Azure Synapse Apache Spark por padrão.

2. Execute outra consulta para ver os dados no `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    O código produz duas células de saída, uma que contém dados resultando a outra, que mostra a visão do trabalho.

    Por predefinição, a visualização dos resultados mostra uma grelha. Mas, há um interruptor de vista por baixo da grelha que permite que a vista altere entre a grelha e as vistas do gráfico.

    ![Saída de consulta em Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Saída de consulta em Azure Synapse Spark")

3. No comutador **'Ver',** selecione **Chart**.
4. Selecione o ícone **de opções Ver** do lado direito.
5. No campo **do tipo Gráfico,** selecione "gráfico de barras".
6. No campo da coluna do eixo X, selecione "estado".
7. No campo da coluna Y-axis, selecione "salário".
8. No campo **agregação,** selecione para "AVG".
9. Selecione **Aplicar**.

   ![Saída do gráfico em Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Saída do gráfico em Azure Synapse Spark")

10. É possível obter a mesma experiência de executar SQL, mas sem ter que mudar de linguagem. Pode fazê-lo substituindo a célula SQL acima por esta célula PySpark, a experiência de saída é a mesma porque o comando de **exibição** é utilizado:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Cada uma das células que anteriormente executavam tinha a opção de ir ao **History Server** and **Monitor .** Clicar nos links leva-o a diferentes partes da Experiência do Utilizador.

> [!NOTE]
> Parte da [documentação oficial](https://spark.apache.org/docs/latest/) da Apache Spark baseia-se na utilização da consola Spark, que não está disponível no Synapse Spark. Utilize o [caderno](quickstart-apache-spark-notebook.md) ou as experiências [IntelliJ.](./spark/intellij-tool-synapse.md)

## <a name="clean-up-resources"></a>Limpar os recursos

A Azure Synapse guarda os seus dados no Azure Data Lake Storage. Pode deixar desligar uma instância Spark com segurança quando não estiver a ser utilizada. É cobrado por uma piscina Apache Spark sem servidor, desde que esteja em funcionamento, mesmo quando não está a ser utilizado. 

Uma vez que os encargos para a piscina são muitas vezes mais do que os encargos de armazenamento, faz sentido económico deixar que os casos de Spark sejam encerrados quando não estão a ser utilizados.

Para garantir que a instância Spark seja desligada, termine quaisquer sessões ligadas (cadernos). A piscina fecha quando o **tempo de marcha** lenta especificado na piscina Apache Spark é atingido. Também pode selecionar a **sessão final** a partir da barra de estado na parte inferior do caderno.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você aprendeu a criar uma piscina Apache Spark sem servidor e executar uma consulta básica spark SQL.

- [Azure Synapse Analytics](overview-what-is.md)
- [.NET para documentação Apache Spark](/dotnet/spark)



