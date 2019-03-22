---
title: Utilizar o Apache Sqoop com o Curl no HDInsight - Azure
description: Saiba como remotamente submeter as tarefas de Apache Sqoop para HDInsight com o Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ad716e2ef5e597424c860378e7a63d5c2de53f54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834562"
---
# <a name="run-apache-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Executar tarefas de Apache Sqoop com o Hadoop no HDInsight com Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Curl para executar tarefas de Apache Sqoop num cluster do Apache Hadoop no HDInsight.

Curl é utilizado para demonstrar como pode interagir com o HDInsight através da utilização de pedidos HTTP não processados para executar, monitorizar e recuperar os resultados de tarefas de Sqoop. Isso funciona com a API de REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir os passos neste artigo, precisará do seguinte:


* Concluída [utilizar o Apache Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) para configurar um ambiente com um cluster do HDInsight e uma base de dados SQL do Azure.
* [Curl](https://curl.haxx.se/). Curl é uma ferramenta para transferir dados de ou para um cluster do HDInsight.
* [jq](https://stedolan.github.io/jq/). O utilitário de jq é usado para processar os dados JSON retornados de pedidos REST.


## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Submeter tarefas do Apache Sqoop com o Curl
> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.
> 
> Para os comandos nesta secção, substitua **USERNAME** pelo utilizador para autenticar-se o cluster e substitua **PASSWORD** pela palavra-passe da conta de utilizador. Substitua **CLUSTERNAME** pelo nome do cluster.
> 
> A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.
> 
> 

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber uma resposta semelhante ao seguinte:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Os parâmetros utilizados neste comando são os seguintes:
   
   * **-u** -o nome de utilizador e palavra-passe utilizada para autenticar o pedido.
   * **-G** -indica que se trata de um pedido GET.
     
     O início da URL; **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos. O caminho **/status**, indica que o pedido deve retornar um status de WebHCat (também conhecido como Templeton) para o servidor. 
2. Utilize o seguinte para submeter uma tarefa de sqoop:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-d** – desde `-G` não for utilizado, o pedido é predefinido para o método POST. `-d` Especifica os valores de dados que são enviados com o pedido.

       * **User.name** -o utilizador que está a executar o comando.

       * **comando** -Sqoop o comando a executar.

       * **statusdir** -o diretório que o estado para esta tarefa de escrita.

     Este comando deverá devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua **JOBID** com o valor devolvido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, **JOBID** seria `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se a tarefa foi concluída, o estado será **bem-sucedido**.
   
   > [!NOTE]  
   > Este pedido Curl retorna um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa; jq é usado para recuperar apenas o valor de estado.

4. Assim que o estado da tarefa foi alterado para **bem-sucedido**, pode recuperar os resultados da tarefa de armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; nesse caso, **wasb: / / / exemplo/dados/sqoop/curl**. Este endereço armazena a saída da tarefa no **exemplo/dados/sqoop/curl** diretório no contentor de armazenamento padrão utilizado pelo cluster do HDInsight.
   
    Pode utilizar o portal do Azure para aceder a blobs stderr e stdout.  Também pode utilizar o Microsoft SQL Server Management Studio para verificar os dados que são carregados para a tabela de log4jlogs.

## <a name="limitations"></a>Limitações
* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop realizará várias inserções em vez das operações de inserção de criação de batches.

## <a name="summary"></a>Resumo
Como demonstrado neste documento, pode utilizar um pedido HTTP não processado para executar, monitorizar e ver os resultados das tarefas de Sqoop no seu cluster do HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte a <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia de API de REST do Apache Sqoop</a>.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações gerais sobre o Hive com o HDInsight:

* [Utilizar o Apache Sqoop com o Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para outro HDInsight artigos que envolvem o curl:
 
* [Criar clusters do Apache Hadoop com a API de REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas do Apache Hive com o Apache Hadoop no HDInsight com REST](apache-hadoop-use-hive-curl.md)
* [Executar tarefas de MapReduce com o Apache Hadoop no HDInsight com REST](apache-hadoop-use-mapreduce-curl.md)
* [Executar tarefas do Apache Pig com o Apache Hadoop no HDInsight com o cURL](apache-hadoop-use-pig-curl.md)



