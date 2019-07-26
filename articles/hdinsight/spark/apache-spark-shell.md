---
title: Usar um shell do Spark interativo no Azure HDInsight
description: Um shell do Spark interativo fornece um processo de leitura-execução-impressão para executar comandos do Spark um de cada vez e ver os resultados.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441898"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Executar Apache Spark do shell do Spark

Um shell [Apache Spark](https://spark.apache.org/) interativo fornece um ambiente repl (Read-execute-Print loop) para executar comandos do Spark um de cada vez e ver os resultados. Esse processo é útil para desenvolvimento e depuração. O Spark fornece um shell para cada um dos seus idiomas com suporte: Escala, Python e R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Obter um shell de Apache Spark com SSH

Acesse um shell do Apache Spark no HDInsight conectando-se ao nó principal primário do cluster usando o SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Você pode obter o comando SSH completo para o cluster do portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até o painel do cluster HDInsight Spark.
3. Selecione Secure Shell (SSH).

    ![Painel do HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Copie o comando SSH exibido e execute-o em seu terminal.

    ![Painel do SSH do HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Para obter detalhes sobre como usar o SSH para se conectar ao HDInsight, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Executar um Apache Spark Shell

O Spark fornece shells para escalabilidade (Spark-Shell), Python (pyspark) e R (sparkr). Em sua sessão SSH no nó principal do cluster HDInsight, insira um dos seguintes comandos:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Agora você pode inserir comandos do Spark no idioma apropriado.

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias de SparkSession e SparkContext

Por padrão, quando você executa o Shell do Spark, as instâncias de SparkSession e SparkContext são instanciadas automaticamente para você.

Para acessar a instância do SparkSession, `spark`digite. Para acessar a instância do SparkContext, `sc`digite.

## <a name="important-shell-parameters"></a>Parâmetros de shell importantes

O comando do shell do`spark-shell`Spark `pyspark`(, `sparkR`ou) dá suporte a muitos parâmetros de linha de comando. Para ver uma lista completa de parâmetros, inicie o Shell do Spark com a `--help`opção. Observe que alguns desses parâmetros só podem ser aplicados a `spark-submit`, que o Shell do Spark encapsula.

| comutador | description | Exemplo |
| --- | --- | --- |
| --MASTER_URL mestre | Especifica a URL mestra. No HDInsight, esse valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada por vírgulas de JARs locais para incluir nos classpaths do driver e do executor. No HDInsight, essa lista é composta de caminhos para o sistema de arquivos padrão no armazenamento do Azure ou Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pacotes MAVEN_COORDS | Lista separada por vírgulas de coordenadas do Maven de jars para incluir nos classpaths do driver e do executor. Pesquisa o repositório Maven local e, em seguida, o Maven central, em seguida, todos os `--repositories`repositórios remotos adicionais especificados com o. O formato das coordenadas é *GroupId*:*artefatoid*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-lista de arquivos | Somente para Python, uma lista separada por vírgulas de arquivos. zip,. ovo ou. py a serem colocados no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passos seguintes

- Consulte [introdução ao Apache Spark no Azure HDInsight](apache-spark-overview.md) para obter uma visão geral.
- Consulte [criar um cluster apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Veja [o que é Apache Spark o streaming estruturado?](apache-spark-streaming-overview.md) para escrever aplicativos que processam dados de streaming com o Spark.
