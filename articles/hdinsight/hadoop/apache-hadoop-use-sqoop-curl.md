---
title: Use a rotação para exportar dados com o Apache Sqoop no Azure HDInsight
description: Saiba como enviar remotamente trabalhos do Apache Sqoop para o Azure HDInsight usando a rotação.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f70c0a0b68e24e3d61a6c0cef238d1f60911e271
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810726"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar trabalhos do Apache Sqoop no HDInsight com ondulação
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar a rotação para executar trabalhos do Apache Sqoop em um cluster Apache Hadoop no HDInsight. Este artigo demonstra como exportar dados do armazenamento do Azure e importá-los para um banco de dado SQL Server usando a ondulação. Este artigo é uma continuação do [uso do Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

A rotação é usada para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados dos trabalhos do Sqoop. Isso funciona usando a API REST do amWebHCat (anteriormente conhecida como Templeton) fornecida pelo seu cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar o banco de dados SQL do Azure. Considere usar [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Ondulação](https://curl.haxx.se/). A rotação é uma ferramenta para transferir dados de ou para um cluster HDInsight.

* [jq](https://stedolan.github.io/jq/). O utilitário JQ é usado para processar os dados JSON retornados de solicitações REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Enviar trabalhos do Apache Sqoop usando a ondulação

Use a rotação para exportar dados usando os trabalhos do Apache Sqoop do armazenamento do Azure para SQL Server.

> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.

Para os comandos nesta seção, substitua `USERNAME` pelo usuário para autenticar no cluster e substitua `PASSWORD` pela senha da conta de usuário. Substitua `CLUSTERNAME` pelo nome do cluster.
 
A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber uma resposta semelhante ao seguinte:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Substitua `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, pelosvaloresapropriadosdospré-requisitos.`SQLDATABASENAME` Use o seguinte para enviar um trabalho do sqoop:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-d** -como `-G` não é usado, a solicitação assume como padrão o método post. `-d`Especifica os valores de dados que são enviados com a solicitação.

       * **User.Name** – o usuário que está executando o comando.

       * **comando** – o comando Sqoop a ser executado.

       * **statusdir** -o diretório no qual o status desse trabalho será gravado.

     Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Para verificar o status do trabalho, use o comando a seguir. Substituir `JOBID` pelo valor retornado na etapa anterior. Por exemplo, se o valor de retorno `{"id":"job_1415651640909_0026"}`era `JOBID` , seria `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se o trabalho tiver sido concluído, o estado será **bem-sucedido**.
   
   > [!NOTE]  
   > Essa solicitação de rotação retorna um documento JavaScript Object Notation (JSON) com informações sobre o trabalho; JQ é usado para recuperar apenas o valor de estado.

4. Depois que o estado do trabalho for alterado para **êxito**, você poderá recuperar os resultados do trabalho do armazenamento de BLOBs do Azure. O `statusdir` parâmetro passado com a consulta contém o local do arquivo de saída; nesse caso, `wasb:///example/data/sqoop/curl`. Esse endereço armazena a saída do trabalho no `example/data/sqoop/curl` diretório no contêiner de armazenamento padrão usado pelo seu cluster HDInsight.

    Você pode usar o portal do Azure para acessar os BLOBs stderr e stdout.

5. Para verificar se os dados foram exportados, use as seguintes consultas do seu cliente SQL para exibir os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações
* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o Microsoft SQL Server ou o Azure SQL Database não oferece suporte a inserções em massa no momento.
* Envio em lote com o HDInsight baseado em Linux, ao usar `-batch` a opção ao executar inserções, o Sqoop executará várias inserções em vez de enviar as operações de inserção por lote.

## <a name="summary"></a>Resumo
Conforme demonstrado neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Sqoop em seu cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia da API REST do Apache Sqoop</a>.

## <a name="next-steps"></a>Passos seguintes
[Usar o Apache Sqoop com o Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para outros artigos do HDInsight envolvendo ondulação:
 
* [Criar clusters Apache Hadoop usando a API REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar Apache Hive consultas com Apache Hadoop no HDInsight usando REST](apache-hadoop-use-hive-curl.md)
* [Executar trabalhos MapReduce com Apache Hadoop no HDInsight usando REST](apache-hadoop-use-mapreduce-curl.md)

