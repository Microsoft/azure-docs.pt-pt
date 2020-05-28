---
title: Use curl para exportar dados com Apache Sqoop em Azure HDInsight
description: Saiba como submeter remotamente os trabalhos de Apache Sqoop ao Azure HDInsight usando o Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7bd0afe4d0ea01671c996a0f536151d943e4fca7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013015"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar empregos Apache Sqoop em HDInsight com Curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Curl para executar trabalhos apache Sqoop num cluster Apache Hadoop em HDInsight. Este artigo demonstra como exportar dados do Armazenamento Azure e importá-lo para uma base de dados do SQL Server usando o Curl. Este artigo é uma continuação do [Use Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

O curl é usado para demonstrar como pode interagir com o HDInsight usando pedidos CRUHTTP para executar, monitorizar e recuperar os resultados dos trabalhos da Sqoop. Isto funciona utilizando a WebHCat REST API (anteriormente conhecida como Templeton) fornecida pelo seu cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a partir de [Use Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar a Base de Dados Azure SQL. Considere utilizar o [Estúdio de Gestão de Servidores SQL](../../azure-sql/database/connect-query-ssms.md) ou o Código do [Estúdio Visual](../../azure-sql/database/connect-query-vscode.md).

* [Caracol.](https://curl.haxx.se/) Curl é uma ferramenta para transferir dados de ou para um cluster HDInsight.

* [jq](https://stedolan.github.io/jq/). O utilitário jq é usado para processar os dados JSON devolvidos a partir de pedidos REST.

* Familiaridade com Sqoop. Para mais informações, consulte o [Guia do Utilizador da Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Submeta os trabalhos apache Sqoop usando curl

Utilize o Curl para exportar dados utilizando os trabalhos apache Sqoop do Armazenamento Azure para o Servidor SQL.

> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.

Para os comandos nesta secção, `USERNAME` substitua-o pelo utilizador para autenticar o cluster e substitua-o `PASSWORD` pela palavra-passe da conta do utilizador. Substitua `CLUSTERNAME` pelo nome do cluster.

A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Para facilitar a utilização, desloque as variáveis abaixo. Este exemplo baseia-se num ambiente Windows, revisto conforme necessário para o seu ambiente.

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

   * **-d** - Uma vez `-G` que não é utilizado, o pedido não se aplica ao método POST. `-d`especifica os valores de dados enviados com o pedido.

       * **user.name** - O utilizador que está a comandar o comando.

       * **comando** - O comando Sqoop para executar.

       * **statusdir** - O diretório a que o estatuto deste trabalho será escrito.

     Este comando devolverá uma identificação de trabalho que pode ser usada para verificar o estado do trabalho.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Para verificar o estado do trabalho, utilize o seguinte comando. Substitua `JOBID` pelo valor devolvido no passo anterior. Por exemplo, se o valor de devolução `{"id":"job_1415651640909_0026"}` fosse, então `JOBID` seria `job_1415651640909_0026` . Reveja a `jq` localização, se necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Se o trabalho tiver terminado, o Estado será **bem sucedido.**

   > [!NOTE]  
   > Este pedido de Curl devolve um documento de notação de objetos JavaScript (JSON) com informações sobre o trabalho; jq é usado para recuperar apenas o valor do estado.

1. Uma vez que o estado do trabalho tenha mudado para **SUCCEED,** você pode recuperar os resultados do trabalho a partir do armazenamento Azure Blob. O `statusdir` parâmetro passado com a consulta contém a localização do ficheiro de saída; neste caso, `wasb:///example/data/sqoop/curl` . Este endereço armazena a saída do trabalho no diretório no recipiente de `example/data/sqoop/curl` armazenamento predefinido utilizado pelo seu cluster HDInsight.

    Pode utilizar o portal Azure para aceder a bolhas stderr e stdout.

1. Para verificar se os dados foram exportados, utilize as seguintes consultas do seu cliente SQL para visualizar os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o Microsoft SQL Server ou para a Base de Dados Azure SQL não suporta atualmente inserções a granel.
* Loteamento - Com o HDInsight baseado em Linux, ao utilizar o `-batch` interruptor ao executar as inserções, o Sqoop executará várias inserções em vez de encher as operações de inserção.

## <a name="summary"></a>Resumo

Como demonstrado neste documento, pode utilizar um pedido http cru para executar, monitorizar e ver os resultados dos trabalhos de Sqoop no seu cluster HDInsight.

Para mais informações sobre a interface REST utilizada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia API Do API do Apache Sqoop REST</a>.

## <a name="next-steps"></a>Próximos passos

[Use Apache Sqoop com Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para outros artigos da HDInsight que envolvam caracóis:

* [Crie aglomerados De Hadoop Apache usando a API Azure REST](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas de Hiv Apache com Apache Hadoop em HDInsight usando REST](apache-hadoop-use-hive-curl.md)
* [Executar mapeia Empregos com Apache Hadoop no HDInsight usando REST](apache-hadoop-use-mapreduce-curl.md)