---
title: Use Curl para exportar dados com Apache Sqoop em Azure HDInsight
description: Aprenda a enviar remotamente os empregos da Apache Sqoop para a Azure HDInsight usando o Curl.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 4de42bf30824fd71228aa27cc478a54ec3741da9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928368"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar apaches sqoop empregos em HDInsight com Curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Curl para gerir os empregos da Apache Sqoop num aglomerado Apache Hadoop em HDInsight. Este artigo demonstra como exportar dados do Azure Storage e importá-lo numa base de dados do SQL Server utilizando o Curl. Este artigo é uma continuação de [Use Apache Sqoop com Hadoop in HDInsight](./hdinsight-use-sqoop.md).

O curl é usado para demonstrar como pode interagir com o HDInsight usando pedidos HTTP brutos para executar, monitorizar e recuperar os resultados dos trabalhos da Sqoop. Isto funciona utilizando a API WebHCat REST (anteriormente conhecida como Templeton) fornecida pelo seu cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da [Utilização de Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar a Base de Dados Azure SQL. Considere usar [o SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou o Visual Studio [Code](../../azure-sql/database/connect-query-vscode.md).

* [Enrolar.](https://curl.haxx.se/) Curl é uma ferramenta para transferir dados de ou para um cluster HDInsight.

* [jq](https://stedolan.github.io/jq/). O utilitário jq é utilizado para processar os dados JSON devolvidos a partir de pedidos de REST.

* Familiaridade com Sqoop. Para mais informações, consulte [o Guia do Utilizador sqoop.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Submeta os empregos da Apache Sqoop utilizando o Curl

Use curl para exportar dados usando empregos Apache Sqoop de Azure Storage para SQL Server.

> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.

Para os comandos desta secção, `USERNAME` substitua-o pelo utilizador para autenticar no cluster e substitua-o `PASSWORD` pela palavra-passe da conta de utilizador. Substitua `CLUSTERNAME` pelo nome do cluster.

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Para facilitar a utilização, desa um ponto das variáveis abaixo. Este exemplo baseia-se num ambiente Windows, reveja conforme necessário para o seu ambiente.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber uma resposta semelhante ao seguinte:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Utilize o seguinte para submeter um trabalho de sqoop:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-d** - Uma vez `-G` que não é utilizado, o pedido é desresposto ao método POST. `-d` especifica os valores de dados que são enviados com o pedido.

       * **user.name** - O utilizador que está a executar o comando.

       * **comando** - O comando Sqoop para executar.

       * **statusdir** - O diretório para o qual o estatuto para este trabalho será escrito.

     Este comando devolverá uma identificação de trabalho que pode ser usada para verificar o estado do trabalho.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Para verificar o estado do trabalho, utilize o seguinte comando. `JOBID`Substitua-o pelo valor devolvido no escalão anterior. Por exemplo, se o valor de retorno `{"id":"job_1415651640909_0026"}` fosse, então `JOBID` seria `job_1415651640909_0026` . Rever a localização de `jq` que for necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Se o trabalho tiver terminado, o Estado será **bem sucedido.**

   > [!NOTE]  
   > Este pedido curl devolve um documento de Notação de Objetos JavaScript (JSON) com informações sobre o trabalho; jq é usado para recuperar apenas o valor do estado.

1. Uma vez alterado o estado do trabalho para **SERDI,** pode recuperar os resultados do trabalho a partir do armazenamento da Azure Blob. O `statusdir` parâmetro passado com a consulta contém a localização do ficheiro de saída; neste caso, `wasb:///example/data/sqoop/curl` . Este endereço armazena a saída do trabalho no `example/data/sqoop/curl` diretório no recipiente de armazenamento predefinido utilizado pelo seu cluster HDInsight.

    Pode utilizar o portal Azure para aceder a manchas stderr e stdout.

1. Para verificar se os dados foram exportados, utilize as seguintes consultas do seu cliente SQL para visualizar os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o Microsoft SQL Server ou Azure SQL Database não suporta atualmente inserções a granel.
* Loteamento - Com o HDInsight baseado em Linux, quando utilizar o `-batch` interruptor ao efetuar inserções, a Sqoop efetuará múltiplas inserções em vez de lotar as operações de inserção.

## <a name="summary"></a>Resumo

Como demonstrado neste documento, pode utilizar um pedido HTTP bruto para executar, monitorizar e ver os resultados dos trabalhos da Sqoop no seu cluster HDInsight.

Para obter mais informações sobre a interface REST utilizada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia apache Sqoop REST API</a>.

## <a name="next-steps"></a>Passos seguintes

[Use Apache Sqoop com Apache Hadoop em HDInsight](hdinsight-use-sqoop.md)

Para outros artigos hdInsight envolvendo caracóis:

* [Criar clusters Apache Hadoop usando a API Azure REST](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas de Colmeia Apache com Apache Hadoop em HDInsight usando REST](apache-hadoop-use-hive-curl.md)
* [Executar trabalhos de MapReduce com Apache Hadoop em HDInsight usando REST](apache-hadoop-use-mapreduce-curl.md)
