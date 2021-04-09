---
title: Kernels para Jupyter Notebook em clusters spark em Azure HDInsight
description: Conheça o PySpark, PySpark3 e Spark kernels para Jupyter Notebook disponível com clusters Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ef2bc5e00779200e5447c8829a437824657a2227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865983"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels para Jupyter Notebook em aglomerados Apache Spark em Azure HDInsight

Os clusters HDInsight Spark fornecem núcleos que pode utilizar com o Caderno Jupyter em [Apache Spark](./apache-spark-overview.md) para testar as suas aplicações. Um núcleo é um programa que executa e interpreta o seu código. Os três núcleos são:

- **PySpark** - para aplicações escritas em Python2.
- **PySpark3** - para aplicações escritas em Python3.
- **Spark** - para aplicações escritas em Scala.

Neste artigo, aprende-se a usar estes núcleos e os benefícios de os utilizar.

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado de Faíscas Apache em HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Crie um caderno Jupyter no Spark HDInsight

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster Spark.  Consulte [lista e mostre os agrupamentos](../hdinsight-administer-use-portal-linux.md#showClusters) para obter as instruções. A **vista geral** abre.

2. A partir da **vista geral,** na caixa **de dashboards cluster,** selecione **Jupyter Notebook**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Caderno Jupyter em Apache Spark" border="true":::
  
   > [!NOTE]  
   > Você também pode chegar ao Jupyter Notebook on Spark cluster abrindo o seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecione **New**, e, em seguida, selecione **Pyspark,** **PySpark3** ou **Spark** para criar um caderno. Use o núcleo spark para aplicações Scala, kernel PySpark para aplicações Python2 e kernel PySpark3 para aplicações Python3.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png " alt-text="Kernels para Jupyter Notebook on Spark" border="true":::

4. Um caderno abre com o núcleo que selecionou.

## <a name="benefits-of-using-the-kernels"></a>Benefícios da utilização dos núcleos

Aqui estão alguns benefícios de usar os novos núcleos com o Jupyter Notebook em clusters Spark HDInsight.

- **Contextos predefinidos**. Com  **o PySpark,** **PySpark3** ou os kernels **Spark,** não precisa de definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com as suas aplicações. Estes contextos estão disponíveis por padrão. Estes contextos são:

  - **sc** - para o contexto spark
  - **sqlContext** - para o contexto da Colmeia

    Então, **não** é preciso executar declarações como as seguintes para definir os contextos:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Em vez disso, pode utilizar diretamente os contextos predefinidos na sua aplicação.

- **Magia celular.** O kernel PySpark fornece algumas "magias" predefinidas, que são comandos especiais que pode chamar `%%` (por exemplo, `%%MAGIC` `<args>` ). O comando mágico deve ser a primeira palavra numa célula de código e permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na cela. Adicionar qualquer coisa antes da magia, mesmo comentários, causa um erro.     Para mais informações sobre magia, consulte [aqui.](https://ipython.readthedocs.org/en/stable/interactive/magics.html)

    A tabela que se segue lista as diferentes magias disponíveis através dos núcleos.

   | Magia | Exemplo | Descrição |
   | --- | --- | --- |
   | Ajuda |`%%help` |Gera uma tabela de todas as magias disponíveis com exemplo e descrição |
   | informações |`%%info` |Informações da sessão de saídas para o atual ponto final da Livy |
   | configurar |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura os parâmetros para a criação de uma sessão. A bandeira de força `-f` é obrigatória se já tiver sido criada uma sessão, o que garante que a sessão seja retirada e recriada. Veja o [Corpo de Pedido post/sessions da Livy](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Os parâmetros devem ser passados como uma corda JSON e devem estar na linha seguinte após a magia, como mostra a coluna de exemplo. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta de Colmeia contra o sqlContext. Se o `-o` parâmetro for aprovado, o resultado da consulta é persistido no contexto python %%local como um dataframe [pandas.](https://pandas.pydata.org/) |
   | local |`%%local`<br>`a=1` |Todo o código em linhas posteriores é executado localmente. O código deve ser o código Python2 válido, independentemente do núcleo que estiver a usar. Assim, mesmo que tenha selecionado **miolos PySpark3** ou **Spark** enquanto cria o caderno, se usar a `%%local` magia numa célula, essa célula deve ter apenas um código Python2 válido. |
   | registos |`%%logs` |Outputs os registos para a atual sessão livy. |
   | delete |`%%delete -f -s <session number>` |Elimina uma sessão específica do atual ponto final da Livy. Não pode apagar a sessão que começa para o próprio núcleo. |
   | limpeza |`%%cleanup -f` |Elimina todas as sessões para o atual ponto final da Livy, incluindo a sessão deste caderno. A bandeira da força -f é obrigatória. |

   > [!NOTE]  
   > Além das magias adicionadas pelo kernel PySpark, você também pode usar as [magias IPython incorporadas,](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)incluindo `%%sh` . Podes usar a `%%sh` magia para executar scripts e bloquear códigos no headnode do cluster.

- **Visualização automática**. O kernel Pyspark visualiza automaticamente a saída das consultas de Hive e SQL. Você pode escolher entre vários tipos diferentes de visualizações, incluindo Mesa, Torta, Linha, Área, Bar.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros suportados com a magia %%sql

A `%%sql` magia suporta diferentes parâmetros que podes usar para controlar o tipo de saída que recebes quando fazes consultas. A tabela a seguir lista a saída.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Utilize este parâmetro para persistir o resultado da consulta, no contexto python %%local, como um dataframe [pandas.](https://pandas.pydata.org/) O nome da variável dataframe é o nome variável que especifica. |
| -q |`-q` |Utilize este parâmetro para desligar as visualizações para a célula. Se não quiser visualizar automaticamente o conteúdo de uma célula e apenas pretender capturá-lo como um dataframe, então use `-q -o <VARIABLE>` . Se quiser desligar as visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL, como uma `CREATE TABLE` declaração), utilize `-q` sem especificar um `-o` argumento. |
| -m |`-m <METHOD>` |Quando **o MÉTODO** é **recolhido** ou **amostra** (o padrão é **tomada).** Se o método **`take`** for, o núcleo escolhe elementos da parte superior do conjunto de dados de resultados especificado por MAXROWS (descrito mais tarde nesta tabela). Se o método for **amostra,** o núcleo recolhe aleatoriamente elementos do conjunto de dados de acordo com o `-r` parâmetro, descrito a seguir nesta tabela. |
| -r |`-r <FRACTION>` |Aqui **FRAC** é um número de ponto flutuante entre 0.0 e 1.0. Se o método de amostra para a consulta SQL `sample` for, então o núcleo analisa aleatoriamente a fração especificada dos elementos do resultado definido para si. Por exemplo, se executar uma consulta SQL com os `-m sample -r 0.01` argumentos, então 1% das linhas de resultados são amostradas aleatoriamente. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor inteiro. O núcleo limita o número de linhas de saída ao **MAXROWS**. Se **MAXROWS** for um número negativo como **-1,** então o número de linhas no conjunto de resultados não é limitado. |

**Exemplo:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

A declaração acima faz as seguintes ações:

- Selecione todos os registos de **hivesmpletable**.
- Porque usamos -q, desliga a auto-visibilidade.
- Porque `-m sample -r 0.1 -n 500` usamos, ele experimenta aleatoriamente 10% das linhas no hivesampletable e limita o tamanho do resultado definido para 500 linhas.
- Finalmente, porque a utilizamos `-o query2` também guarda a saída num dataframe chamado **consulta2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações durante a utilização dos novos núcleos

Qualquer que seja o núcleo que usar, deixando os cadernos em funcionamento consome os recursos do cluster.  Com estes núcleos, porque os contextos estão predefinidos, simplesmente sair dos cadernos não mata o contexto. E assim os recursos de cluster continuam a ser usados. Uma boa prática é utilizar a opção **Close and Halt** do menu de **Ficheiros** do portátil quando terminar de usar o portátil. O encerramento mata o contexto e depois sai do caderno.

## <a name="where-are-the-notebooks-stored"></a>Onde estão os cadernos guardados?

Se o seu cluster utilizar o Azure Storage como conta de armazenamento predefinido, os Cadernos Jupyter são guardados para a conta de armazenamento na pasta **/HdiNotebooks.**  Os cadernos, ficheiros de texto e pastas que cria a partir do Jupyter estão acessíveis a partir da conta de armazenamento.  Por exemplo, se utilizar o Jupyter para criar uma pasta **`myfolder`** e um **myfolder/mynotebook.ipynb,** pode aceder a esse portátil `/HdiNotebooks/myfolder/mynotebook.ipynb` dentro da conta de armazenamento.  O inverso também é verdade, ou seja, se você carregar um caderno diretamente para a sua conta de armazenamento `/HdiNotebooks/mynotebook1.ipynb` em , o caderno também é visível a partir de Jupyter.  Os cadernos permanecem na conta de armazenamento mesmo depois de o cluster ser eliminado.

> [!NOTE]  
> Os clusters HDInsight com Azure Data Lake Storage como o armazenamento padrão não armazenam cadernos em armazenamento associado.

A forma como os cadernos são guardados na conta de armazenamento é compatível com [o Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Se entrar no cluster, pode utilizar os comandos de gestão de ficheiros:

| Comando | Descrição |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Liste tudo no diretório de raiz – tudo neste diretório é visível para Jupyter a partir da página inicial |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # Descarregue o conteúdo da pasta HdiNotebooks|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | Upload a notebook example.ipynb to the root fold so it's visible from Jupyter |

Quer o cluster utilize o Azure Storage ou o Azure Data Lake Storage como conta de armazenamento padrão, os cadernos também são guardados no headnode do cluster em `/var/lib/jupyter` .

## <a name="supported-browser"></a>Browser suportado

Os cadernos Jupyter em clusters Spark HDInsight são suportados apenas no Google Chrome.

## <a name="suggestions"></a>Sugestões

Os novos núcleos estão em evolução e irão amadurecer com o tempo. Assim, as APIs podem mudar à medida que estes núcleos amadurecem. Agradecemos qualquer feedback que tenha enquanto utiliza estes novos núcleos. O feedback é útil na formação da libertação final destes núcleos. Pode deixar os seus comentários/feedback na secção **De Feedback** na parte inferior deste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
- [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
- [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
