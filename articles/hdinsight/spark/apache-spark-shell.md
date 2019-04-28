---
title: Utilizar uma Shell interativa do Spark no HDInsight do Azure
description: Uma Shell interativa do Spark fornece um processo de impressão leitura executar para executar o Spark comandos um por vez e observando os resultados.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116078"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Execute o Apache Spark a partir da Shell do Spark

Interativo [Apache Spark](https://spark.apache.org/) Shell fornece um ambiente REPL (leitura-executar-print loop) para executar o Spark comandos um por vez e observando os resultados. Este processo é útil para desenvolvimento e depuração. Spark fornece um shell para cada um dos seus idiomas suportados: Scala, Python e R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Obter uma Shell do Apache Spark com SSH

Aceda a uma Shell do Apache Spark no HDInsight ao ligar ao nó principal primário do cluster através de SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Pode obter o comando SSH completado para o seu cluster no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para o painel para o seu cluster do HDInsight Spark.
3. Select Secure Shell (SSH).

    ![Painel de HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Copie o comando SSH apresentado e executá-lo no seu terminal.

    ![SSH do HDInsight painel no portal do Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Para obter detalhes sobre como utilizar o SSH para ligar ao HDInsight, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Executar uma Shell do Apache Spark

Spark fornece shells para Scala (spark-shell), o Python (pyspark) e R (sparkR). Na sua sessão SSH para o nó principal do cluster do HDInsight, introduza um dos seguintes comandos:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Agora pode inserir comandos do Spark no idioma apropriado.

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias SparkSession e SparkContext

Por predefinição quando executa o Shell do Spark, instâncias de SparkSession e SparkContext são automaticamente instanciadas para.

Para acessar a instância de SparkSession, introduza `spark`. Para acessar a instância de SparkContext, introduza `sc`.

## <a name="important-shell-parameters"></a>Parâmetros de shell importante

O comando do Shell do Spark (`spark-shell`, `pyspark`, ou `sparkR`) oferece suporte a muitos parâmetros da linha de comandos. Para ver uma lista completa de parâmetros, inicie a Shell do Spark com o comutador `--help`. Tenha em atenção que alguns desses parâmetros talvez só se aplicam a `spark-submit`, que encapsula o Shell do Spark.

| Comutador | description | Exemplo |
| --- | --- | --- |
| --master MASTER_URL | Especifica o URL principal. No HDInsight, este valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada por vírgulas de jars locais para incluir nos caminhos de controlador e do executor. No HDInsight, esta lista é composta por caminhos para o sistema de ficheiros predefinido no armazenamento do Azure ou o armazenamento do Data Lake. | `--jars /path/to/examples.jar` |
| – MAVEN_COORDS de pacotes | Lista separada por vírgulas de coordenadas do maven do Intune para incluir nos caminhos de controlador e do executor. Procura o repositório local maven, em seguida, maven central, em seguida, quaisquer repositórios remotos adicionais especificados com `--repositories`. É o formato para as coordenadas *groupId*:*artifactId*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| LISTA de ficheiros – py | Para Python apenas, uma lista separada por vírgulas de ficheiros. zip, .egg ou. PY para colocar no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passos Seguintes

- Ver [introdução ao Apache Spark no Azure HDInsight](apache-spark-overview.md) para uma descrição geral.
- Ver [criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters do Spark e SparkSQL.
- Ver [o que é o Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) para escrever aplicativos que processam dados de transmissão em fluxo com o Spark.
