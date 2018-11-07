---
title: Utilizar o Spark do Livy para submeter tarefas ao cluster do Spark no Azure HDInsight
description: Saiba como utilizar a API de REST do Apache Spark para submeter tarefas do Spark remotamente a um cluster do HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 86a047fe291c7872fe275ba7246b9f3e59044723
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236828"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Utilize a API de REST do Apache Spark para submeter as tarefas remotas para um cluster do Spark do HDInsight

Saiba como utilizar o Livy, a API de REST do Apache Spark, que é utilizado para submeter tarefas remotas para um cluster do HDInsight Spark do Azure. Para obter documentação detalhada, consulte [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Pode usar o Livy para executar o Spark interativa shells ou submeter as tarefas de lote para ser executado no Spark. Este artigo fala sobre a utilização do Livy para submeter trabalhos de lote. Os trechos de código neste artigo utilizam o cURL para fazer chamadas de REST API para o ponto de extremidade do Livy Spark.

**Pré-requisitos:**

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Este artigo utiliza o cURL para demonstrar como fazer chamadas de REST API em relação a um cluster do Spark do HDInsight.

## <a name="submit-a-livy-spark-batch-job"></a>Submeter uma tarefa de lote de Spark do Livy
Antes de submeter uma tarefa do batch, tem de carregar o aplicativo jar no armazenamento de cluster associado ao cluster. Para o fazer, pode utilizar [**AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos. Existem vários outros clientes, que pode utilizar para carregar dados. Pode saber mais sobre esses clientes em [Upload data for Hadoop jobs in HDInsight](../hdinsight-upload-data.md) (Carregar dados para trabalhos do Hadoop no HDInsight).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"

**Exemplos**:

* Se o ficheiro jar no armazenamento de cluster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
* Se quiser passar o nome de ficheiro jar e o classname como parte de um ficheiro de entrada (neste exemplo, Input)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obtenha informações sobre os lotes de Livy Spark em execução no cluster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se quiser obter todos os lotes de Livy Spark em execução no cluster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
* Se quiser recuperar um lote específico com um determinada batchId
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Eliminar uma tarefa de lote do Spark do Livy
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark e a elevada disponibilidade
Livy fornece elevada disponibilidade para o Spark tarefas em execução no cluster. Eis alguns exemplos.

* Se o serviço do Livy ficar inativo após submeter uma tarefa remotamente para um cluster do Spark, o trabalho continua em execução em segundo plano. Quando estiver Livy cópia de segurança, ele restaura o estado do trabalho e dos relatórios-lo novamente.
* Blocos de notas do Jupyter para HDInsight têm a tecnologia Livy no back-end. Se um bloco de notas é executada uma tarefa do Spark e o serviço do Livy é reiniciado, o bloco de notas continua executar as células de código. 

## <a name="show-me-an-example"></a>Mostre-me um exemplo
Nesta secção, vamos ver exemplos para utilizar o Spark do Livy para submeter a tarefa de lote, monitorizar o progresso da tarefa e, em seguida, eliminá-lo. O aplicativo usamos neste exemplo é a desenvolvidos no artigo [criar uma aplicação Scala autónoma e a executar no cluster do HDInsight Spark](apache-spark-create-standalone-application.md). Os passos aqui descritos partem do princípio de que:

* Já tiver copiado sobre o jar do aplicativo para a conta de armazenamento associada ao cluster.
* Tiver CuRL instalado no computador onde está a tentar estes passos.

Execute os seguintes passos:

1. Diga-pela primeira vez Certifique-se de que o Livy Spark está em execução no cluster. Podemos fazer isso obtendo uma lista de execução de lotes. Se estiver a executar uma tarefa com o Livy pela primeira vez, a saída deverá retornar zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Deve obter um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observe como a última linha na saída diz **total: 0**, que sugere que não existem lotes em execução.

2. Diga-nos agora submeta uma tarefa do batch. O fragmento seguinte utiliza um ficheiro de entrada (Input) para transmitir o nome de jar e o nome da classe como parâmetros. Se estiver a executar estes passos de um computador Windows, utilizando um ficheiro de entrada é a abordagem recomendada.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
   
    Os parâmetros no ficheiro **Input** são definidos da seguinte forma:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
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
   
    Observe como a última linha da saída diz **Estado: a iniciar**. Também indica **id: 0**. Aqui, **0** é o ID de lote.

3. Agora pode obter o Estado neste lote específico com o ID de lote.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Resultado agora mostra **Estado: êxito**, que sugere que a tarefa foi concluída com êxito.

4. Se desejar, agora é possível eliminar o lote.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    A última linha da saída mostra que o batch foi eliminado com êxito. Também eliminar uma tarefa, enquanto está em execução, interrompe a tarefa. Se eliminar uma tarefa que foi concluída com êxito ou caso contrário, ele elimina completamente as informações de tarefa.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Atualizações à configuração do Livy a partir da versão do HDInsight 3.5

HDInsight 3.5 clusters e acima, por predefinição, desativar a utilização de caminhos de ficheiro local para aceder aos ficheiros de dados de exemplo ou jars. Nós o encorajamos a utilizar o `wasb://` caminho em vez disso, para aceder a jars ou dados de exemplo ficheiros do cluster. Se pretender utilizar o caminho local, tem de atualizar a configuração de Ambari em conformidade. Para tal:

1. Aceda ao portal do Ambari do cluster. A interface do Usuário da Web de Ambari está disponível no seu cluster do HDInsight em https://**CLUSTERNAME**. azurehdidnsight.net, em que CLUSTERNAME é o nome do cluster.

2. No painel de navegação esquerda, clique em **Livy**e, em seguida, clique em **configurações**.

3. Sob **livy predefinidas** adicionar o nome da propriedade `livy.file.local-dir-whitelist` e defina seu valor como **"/"** se pretender permitir o acesso total ao sistema de ficheiros. Se pretender permitir o acesso apenas a um diretório específico, forneça o caminho para esse diretório como o valor.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Submeter tarefas do Livy para um cluster dentro de uma rede virtual do Azure

Se ligar a um cluster do Spark do HDInsight a partir de uma rede Virtual do Azure, pode ligar diretamente ao Livy no cluster. Nesse caso, é o URL para o ponto final do Livy `http://<IP address of the headnode>:8998/batches`. Aqui, **8998** é a porta em que o Livy é executado no nó principal do cluster. Para obter mais informações sobre como acessar serviços em portas não públicas, consulte [portas utilizadas pelo serviços do Hadoop no HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se alguns problemas que podem ocorrer durante o uso do Livy para submissão de trabalho remoto para os clusters do Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Não é suportado utilizar um jar externo do armazenamento adicional

**Problema:** se a sua tarefa do Spark do Livy referencia um jar externo da conta de armazenamento adicional associada ao cluster, a tarefa falha.

**Resolução:** Certifique-se de que o jar que pretende utilizar está disponível no armazenamento predefinida associado ao cluster do HDInsight.





## <a name="next-step"></a>Passo seguinte

* [Documentação da API de REST do Livy](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

