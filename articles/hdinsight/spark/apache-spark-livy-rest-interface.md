---
title: Usar o Livy Spark para enviar trabalhos para o cluster Spark no Azure HDInsight
description: Saiba como usar Apache Spark API REST para enviar trabalhos do Spark remotamente para um cluster do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: da654beec730d0bfc04548402c1158ebaaf80c6f
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748362"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Usar Apache Spark API REST para enviar trabalhos remotos para um cluster HDInsight Spark

Saiba como usar o [Apache Livy](https://livy.incubator.apache.org/), a API REST do [Apache Spark](https://spark.apache.org/) , que é usada para enviar trabalhos remotos para um cluster Azure HDInsight Spark. Para obter a documentação detalhada, consulte [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/).

Você pode usar o Livy para executar shells interativos do Spark ou enviar trabalhos em lotes para serem executados no Spark. Este artigo fala sobre como usar o Livy para enviar trabalhos em lotes. Os trechos neste artigo usam a rotação para fazer chamadas à API REST para o ponto de extremidade do Livy Spark.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). Este artigo usa a rotação para demonstrar como fazer chamadas à API REST em um cluster HDInsight Spark.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Enviar um trabalho em lotes do Apache Livy Spark

Antes de enviar um trabalho em lotes, você deve carregar o JAR do aplicativo no armazenamento do cluster associado ao cluster. Você pode usar o [AzCopy](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há vários outros clientes que você pode usar para carregar dados. Você pode encontrar mais informações sobre eles em [carregar dados para trabalhos de Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exemplos

* Se o arquivo JAR estiver no armazenamento de cluster (WASB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Se você quiser passar o nome de arquivo JAR e o ClassName como parte de um arquivo de entrada (neste exemplo, Input. txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obter informações sobre lotes do Livy Spark em execução no cluster

Sintaxe:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exemplos

* Se você quiser recuperar todos os lotes do Spark do Livy em execução no cluster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* Se você quiser recuperar um lote específico com uma determinada ID de lote

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Excluir um trabalho em lotes do Livy Spark

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exemplo

Excluindo um trabalho em lotes com a ID de lote `5`.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark e alta disponibilidade

O Livy fornece alta disponibilidade para trabalhos do Spark em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço Livy falhar depois que você tiver enviado um trabalho remotamente para um cluster Spark, o trabalho continuará a ser executado em segundo plano. Quando o Livy faz backup, ele restaura o status do trabalho e o relata de volta.
* Os notebooks Jupyter para HDInsight são alimentados por Livy no back-end. Se um bloco de anotações estiver executando um trabalho do Spark e o serviço Livy for reiniciado, o bloco de anotações continuará a executar as células de código. 

## <a name="show-me-an-example"></a>Mostre-me um exemplo

Nesta seção, veremos exemplos para usar o Livy Spark para enviar o trabalho em lotes, monitorar o progresso do trabalho e, em seguida, excluí-lo. O aplicativo que usamos neste exemplo é o desenvolvido no artigo [criar um aplicativo escalar autônomo e executar no cluster HDInsight Spark](apache-spark-create-standalone-application.md). As etapas aqui pressupõem que:

* Você já copiou o JAR do aplicativo para a conta de armazenamento associada ao cluster.
* Você está com a rotação instalada no computador em que está tentando essas etapas.

Execute as seguintes etapas:

1. Primeiro, vamos verificar se o Livy Spark está em execução no cluster. Podemos fazer isso obtendo uma lista de lotes em execução. Se você estiver executando um trabalho usando Livy pela primeira vez, a saída deverá retornar zero.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    Você deve obter uma saída semelhante ao trecho a seguir:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe como a última linha na saída diz **total: 0**, o que sugere que não há lotes em execução.

2. Agora, vamos enviar um trabalho em lotes. O trecho a seguir usa um arquivo de entrada (Input. txt) para passar o nome do jar e o nome da classe como parâmetros. Se você estiver executando essas etapas em um computador Windows, o uso de um arquivo de entrada é a abordagem recomendada.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Os parâmetros no arquivo **Input. txt** são definidos da seguinte maneira:

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe como a última linha da saída diz **State: Iniciando**. Ele também diz, **ID: 0**. Aqui, **0** é a ID do lote.

3. Agora você pode recuperar o status desse lote específico usando a ID do lote.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A saída agora mostra o **estado: êxito**, que sugere que o trabalho foi concluído com êxito.

4. Se desejar, agora você pode excluir o lote.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A última linha da saída mostra que o lote foi excluído com êxito. A exclusão de um trabalho, enquanto ele está em execução, também elimina o trabalho. Se você excluir um trabalho que foi concluído, com êxito ou não, ele excluirá as informações do trabalho completamente.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Atualizações para a configuração do Livy começando com a versão 3,5 do HDInsight

Os clusters HDInsight 3,5 e superiores, por padrão, desabilitam o uso de caminhos de arquivo local para acessar arquivos de dados de exemplo ou JARs. Incentivamos você a usar o caminho `wasb://` em vez de acessar os jars ou os arquivos de dados de exemplo do cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Enviando trabalhos do Livy para um cluster em uma rede virtual do Azure

Se você se conectar a um cluster HDInsight Spark de dentro de uma rede virtual do Azure, você poderá se conectar diretamente ao Livy no cluster. Nesse caso, a URL para o ponto de extremidade Livy é `http://<IP address of the headnode>:8998/batches`. Aqui, **8998** é a porta na qual o Livy é executado no cluster cabeçalho. Para obter mais informações sobre como acessar serviços em portas não públicas, consulte [portas usadas por serviços de Apache Hadoop no HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Passos seguintes

* [Documentação da API REST do Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)