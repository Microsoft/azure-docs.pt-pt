---
title: Kernels para o caderno Jupyter em clusters Spark em Azure HDInsight
description: Conheça os núcleos PySpark, PySpark3 e Spark para o notebook Jupyter disponível com clusters Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/24/2020
ms.openlocfilehash: 01aad05995f4df5181a82bdedf630d4082760c38
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137438"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels para o caderno Jupyter em clusters Apache Spark em Azure HDInsight

Os clusters HDInsight Spark fornecem núcleos que pode usar com o caderno Jupyter na [Apache Spark](./apache-spark-overview.md) para testar as suas aplicações. Um núcleo é um programa que executa e interpreta o seu código. Os três núcleos são:

- **PySpark** - para aplicações escritas em Python2.
- **PySpark3** - para aplicações escritas em Python3.
- **Spark** - para candidaturas escritas em Scala.

Neste artigo, aprende-se a usar estes núcleos e os benefícios de os utilizar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache Spark em HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Crie um caderno Jupyter no Spark HDInsight

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster Spark.  Consulte [a Lista e mostre](../hdinsight-administer-use-portal-linux.md#showClusters) os agrupamentos para obter as instruções. A vista **geral** abre.

2. A partir da vista **geral,** na caixa de **painéis** cluster, selecione **o caderno Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Caderno de jupyter em Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Caderno de jupyter em Faísca")
  
   > [!NOTE]  
   > Também pode chegar ao caderno Jupyter no cluster Spark abrindo o seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecione **Novo**, e, em seguida, selecione **pyspark**, **PySpark3**, ou **Spark** para criar um portátil. Utilize o kernel Spark para aplicações Scala, kernel PySpark para aplicações Python2 e kernel PySpark3 para aplicações Python3.

    ![Kernels para o caderno Jupyter em Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels para o caderno Jupyter em Spark")

4. Um caderno abre com o núcleo que selecionou.

## <a name="benefits-of-using-the-kernels"></a>Benefícios da utilização dos núcleos

Aqui estão alguns benefícios de usar os novos núcleos com o caderno Jupyter nos clusters Spark HDInsight.

- **Contextos predefinidos.** Com **pySpark**, **PySpark3**, ou os kernels **Spark,** você não precisa definir os contextos Spark ou Hive explicitamente antes de começar a trabalhar com as suas aplicações. Estes contextos estão disponíveis por defeito. Estes contextos são:

  - **sc** - para o contexto spark
  - **sqlContext** - para o contexto da Colmeia

    Por isso, **não** é preciso fazer declarações como as seguintes para definir os contextos:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Em vez disso, pode utilizar diretamente os contextos predefinidos na sua aplicação.

- **Magias celulares.** O kernel PySpark fornece algumas "magias" predefinidas, que `%%` são comandos especiais que pode ligar (por exemplo, `%%MAGIC` `<args>`). O comando mágico deve ser a primeira palavra numa célula de código e permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na cela. Adicionar qualquer coisa antes da magia, até comentários, causa um erro.     Para mais informações sobre magia, consulte [aqui.](https://ipython.readthedocs.org/en/stable/interactive/magics.html)

    A tabela seguinte lista as diferentes magias disponíveis através dos núcleos.

   | Magia | Exemplo | Descrição |
   | --- | --- | --- |
   | Ajuda |`%%help` |Gera uma tabela de todas as magias disponíveis com exemplo e descrição |
   | informações |`%%info` |Informações da sessão de saídas para o atual ponto final da Livy |
   | configurar |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Refigura os parâmetros para a criação de uma sessão. A bandeira`-f`de força é obrigatória se já tiver sido criada uma sessão, que garante que a sessão seja retirada e recriada. Veja o [Post/Sessões da Livy Request Body](https://github.com/cloudera/livy#request-body) para uma lista de parâmetros válidos. Os parâmetros devem ser passados como uma corda JSON e devem estar na linha seguinte após a magia, como mostra a coluna de exemplo. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta de Colmeia contra o sqlContext. Se `-o` o parâmetro for aprovado, o resultado da consulta é persistente no contexto %%local python como um quadro de dados [Pandas.](https://pandas.pydata.org/) |
   | local |`%%local`<br>`a=1` |Todo o código em linhas posteriores é executado localmente. O código deve ser válido código Python2, não importa qual o núcleo que estiver a usar. Por isso, mesmo que tenha selecionado os kernels **PySpark3** ou `%%local` **Spark** enquanto criao o caderno, se utilizar a magia numa célula, essa célula só deve ter um código Python2 válido. |
   | registos |`%%logs` |Produz os registos para a atual sessão da Livy. |
   | delete |`%%delete -f -s <session number>` |Elimina uma sessão específica do atual ponto final da Livy. Não é possível apagar a sessão que se inicia para o próprio núcleo. |
   | limpeza |`%%cleanup -f` |Elimina todas as sessões para o atual ponto final da Livy, incluindo a sessão deste caderno. A bandeira da força f é obrigatória. |

   > [!NOTE]  
   > Além das magias adicionadas pelo kernel PySpark, também pode usar as `%%sh` [magias IPython incorporadas](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo . Podeusar a `%%sh` magia para executar scripts e bloco de código no nó do cluster.

- **Visualização automática**. O kernel Pyspark visualiza automaticamente a saída de consultas de Hive e SQL. Você pode escolher entre vários tipos diferentes de visualizações, incluindo Tabela, Torta, Linha, Área, Bar.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros suportados com a magia %%sql

A `%%sql` magia suporta diferentes parâmetros que pode usar para controlar o tipo de saída que recebe quando executa consultas. A tabela seguinte lista a saída.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Utilize este parâmetro para persistir o resultado da consulta, no contexto de Python local%%, como um quadro de dados [pandas.](https://pandas.pydata.org/) O nome da variável dataframe é o nome variável que especifica. |
| -q |`-q` |Utilize este parâmetro para desligar as visualizações da célula. Se não quiser visualizar automaticamente o conteúdo de uma célula e apenas quiser `-q -o <VARIABLE>`capturá-lo como um quadro de dados, então use . Se quiser desativar as visualizações sem captar os resultados (por exemplo, `CREATE TABLE` para executar `-q` uma consulta `-o` SQL, como uma declaração), use sem especificar um argumento. |
| -m |`-m <METHOD>` |Quando o **MÉTODO** for **recolhido** ou **amostra** (a predefinição é **tomada).** Se o **`take`** método for, o núcleo recolhe elementos da parte superior do conjunto de dados de resultados especificado por MAXROWS (descrito mais tarde nesta tabela). Se o método for **amostra**, o núcleo amostra aleatoriamente elementos do conjunto de dados de acordo com o `-r` parâmetro, descrito seletivamente nesta tabela. |
| -r |`-r <FRACTION>` |Aqui a **FRAÉFica** é um número de ponto flutuante entre 0,0 e 1,0. Se o método da amostra para `sample`a consulta SQL for, então o núcleo amostra aleatoriamente a fração especificada dos elementos do resultado definido para si. Por exemplo, se fizer uma consulta SQL `-m sample -r 0.01`com os argumentos, então 1% das linhas de resultados são amostradas aleatoriamente. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor inteiro. O núcleo limita o número de linhas de saída a **MAXROWS**. Se **maxrows** é um número negativo como **-1**, então o número de linhas no conjunto de resultados não é limitado. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A declaração acima faz as seguintes ações:

- Seleciona todos os registos a partir de **colmeias.**
- Porque usamos -q, desliga a auto-visualização.
- Porque utilizamos, `-m sample -r 0.1 -n 500`amostras aleatórias de 10% das linhas na colmeia e limita o tamanho do resultado definido para 500 linhas.
- Finalmente, porque `-o query2` a usamos também guarda a saída num quadro de dados chamado **consulta2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao usar os novos núcleos

Qualquer que seja o núcleo que usas, deixar os cadernos a funcionar consome os recursos do cluster.  Com estes núcleos, porque os contextos estão predefinidos, simplesmente sair dos cadernos não mata o contexto. E assim os recursos de cluster continuam a ser usados. Uma boa prática é usar a opção **Close and Halt** do menu **'Arquivo'** do caderno quando terminar de usar o caderno. O encerramento mata o contexto e depois sai do caderno.

## <a name="where-are-the-notebooks-stored"></a>Onde estão guardados os cadernos?

Se o seu cluster utilizar o Armazenamento Azure como conta de armazenamento predefinido, os cadernos Jupyter são guardados para a conta de armazenamento sob a pasta **/HdiNotebooks.**  Os cadernos, ficheiros de texto e pastas que cria a partir de jupyter são acessíveis a partir da conta de armazenamento.  Por exemplo, se utilizar o Jupyter para criar uma pasta **`myfolder`** e um **myfolder/mynotebook.ipynb,** pode aceder a esse caderno dentro `/HdiNotebooks/myfolder/mynotebook.ipynb` da conta de armazenamento.  O inverso também é verdade, isto é, se fizer o `/HdiNotebooks/mynotebook1.ipynb`upload de um caderno diretamente para a sua conta de armazenamento em , o caderno também é visível de Jupyter.  Os cadernos permanecem na conta de armazenamento mesmo depois de o cluster ser eliminado.

> [!NOTE]  
> Os clusters HDInsight com o Armazenamento do Lago Azure Data, uma vez que o armazenamento predefinido não armazena cadernos em armazenamento associado.

A forma como os cadernos são guardados na conta de armazenamento é compatível com [O HDFS Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Se o SSH entrar no cluster, pode utilizar os comandos de gestão de ficheiros:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Quer o cluster utilize o Armazenamento Azure ou o Armazenamento de Lagos de Dados Azure como `/var/lib/jupyter`conta de armazenamento padrão, os cadernos também são guardados no headnode do cluster em .

## <a name="supported-browser"></a>Browser suportado

Os cadernos jupyter nos clusters Spark HDInsight são suportados apenas no Google Chrome.

## <a name="feedback"></a>Comentários

Os novos núcleos estão em fase de evolução e irão amadurecer com o tempo. Para que as APIs possam mudar à medida que estes núcleos amadurecem. Gostaríamos de ter qualquer feedback que tenha enquanto usa estes novos núcleos. O feedback é útil na formação da libertação final destes núcleos. Pode deixar os seus comentários/feedback na secção **Feedback** na parte inferior deste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
- [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
- [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
