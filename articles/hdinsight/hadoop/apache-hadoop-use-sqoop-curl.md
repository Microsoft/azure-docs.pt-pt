---
title: Utilize o Curl para exportar dados com o Apache Sqoop no Azure HDInsight
description: Saiba como remotamente submeter as tarefas de Apache Sqoop para HDInsight com o Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: ede0538e90e9f35797546f34bfed757c2727b194
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508871"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar tarefas do Apache Sqoop no HDInsight com Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Curl para executar tarefas de Apache Sqoop num cluster do Apache Hadoop no HDInsight. Este artigo demonstra como exportar dados do armazenamento do Azure e importe-o para uma base de dados do SQL Server com o Curl. Este artigo é uma continuação de [utilizar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

Curl é utilizado para demonstrar como pode interagir com o HDInsight através da utilização de pedidos HTTP não processados para executar, monitorizar e recuperar os resultados de tarefas de Sqoop. Isso funciona com a API de REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) partir [utilização Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar a base de dados SQL do Azure. Considere a utilização [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl é uma ferramenta para transferir dados de ou para um cluster do HDInsight.

* [jq](https://stedolan.github.io/jq/). O utilitário de jq é usado para processar os dados JSON retornados de pedidos REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Submeter tarefas do Apache Sqoop com o Curl

Utilize o Curl para exportar dados com o Apache Sqoop trabalhos a partir do armazenamento do Azure para o SQL Server.

> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.

Para os comandos nesta secção, substitua `USERNAME` com o utilizador autenticar para o cluster e substitua `PASSWORD` com a palavra-passe da conta de utilizador. Substitua `CLUSTERNAME` pelo nome do cluster.
 
A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber uma resposta semelhante ao seguinte:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Substitua `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` com os valores adequados dos pré-requisitos. Utilize o seguinte para submeter uma tarefa de sqoop:

    ```cmd
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

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua `JOBID` com o valor devolvido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, `JOBID` seria `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se a tarefa foi concluída, o estado será **bem-sucedido**.
   
   > [!NOTE]  
   > Este pedido Curl retorna um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa; jq é usado para recuperar apenas o valor de estado.

4. Assim que o estado da tarefa foi alterado para **bem-sucedido**, pode recuperar os resultados da tarefa de armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; nesse caso, `wasb:///example/data/sqoop/curl`. Este endereço armazena a saída da tarefa no `example/data/sqoop/curl` diretório no contentor de armazenamento padrão utilizado pelo cluster do HDInsight.

    Pode utilizar o portal do Azure para aceder a blobs stderr e stdout.

5. Para verificar que foram exportados dados, utilize as seguintes consultas a partir do seu cliente SQL para ver os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações
* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop realizará várias inserções em vez das operações de inserção de criação de batches.

## <a name="summary"></a>Resumo
Como demonstrado neste documento, pode utilizar um pedido HTTP não processado para executar, monitorizar e ver os resultados das tarefas de Sqoop no seu cluster do HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte a <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia de API de REST do Apache Sqoop</a>.

## <a name="next-steps"></a>Passos Seguintes
[Utilizar o Apache Sqoop com o Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para outro HDInsight artigos que envolvem o curl:
 
* [Criar clusters do Apache Hadoop com a API de REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas do Apache Hive com o Apache Hadoop no HDInsight com REST](apache-hadoop-use-hive-curl.md)
* [Executar tarefas de MapReduce com o Apache Hadoop no HDInsight com REST](apache-hadoop-use-mapreduce-curl.md)

