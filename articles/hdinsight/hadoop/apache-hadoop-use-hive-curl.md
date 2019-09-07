---
title: Usar Apache Hadoop Hive com ondulação no HDInsight – Azure
description: Saiba como enviar remotamente trabalhos do Apache Pig para o Azure HDInsight usando a rotação.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: e1fbeb48acdfd9d09cad2616aed9793e2ff513ad
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736088"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Executar Apache Hive consultas com Apache Hadoop no HDInsight usando REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como usar a API REST do amWebHCat para executar Apache Hive consultas com Apache Hadoop no cluster do Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente REST. Este documento usa [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) no Windows PowerShell e a [ondulação](https://curl.haxx.se/) no [bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Se você usar bash, também precisará de JQ, um processador de linha de comando JSON.  Consulte [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>URI de base para a API REST

O URI (Uniform Resource Identifier base) para a API REST no HDInsight é `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, em `CLUSTERNAME` que é o nome do cluster.  Os nomes de cluster nos URIs diferenciam **maiúsculas de minúsculas**.  Embora o nome do cluster na parte do FQDN (nome de domínio totalmente qualificado) do URI`CLUSTERNAME.azurehdinsight.net`() não diferencia maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Authentication

Ao usar a ondulação ou qualquer outra comunicação REST com o WebHCat, você deve autenticar as solicitações fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor, sempre faça solicitações usando HTTP seguro (HTTPS).

### <a name="setup-preserve-credentials"></a>Instalação (preservar credenciais)
Preserve suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado em uma etapa separada.

**UM. Raso**  
Edite o script a seguir `PASSWORD` substituindo pela sua senha real.  Em seguida, digite o comando.

```bash
export password='PASSWORD'
```  

**B. O** PowerShell executa o código abaixo e insere suas credenciais na janela pop-up:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster em maiúscula corretamente
A capitalização real do nome do cluster pode ser diferente do esperado, dependendo de como o cluster foi criado.  As etapas aqui mostrarão o capital real e, em seguida, o armazenará em uma variável para todos os exemplos subsequentes.

Edite os scripts abaixo para `CLUSTERNAME` substituir pelo nome do cluster. Em seguida, digite o comando. (O nome do cluster para o FQDN não diferencia maiúsculas de minúsculas.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a id="curl"></a>Executar uma consulta de Hive

1. Para verificar se você pode se conectar ao seu cluster HDInsight, use um dos seguintes comandos:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Você recebe uma resposta semelhante ao seguinte texto:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * `-u`-O nome de usuário e a senha usados para autenticar a solicitação.
    * `-G`-Indica que essa solicitação é uma operação GET.

1. O início da URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações. O caminho, `/status`, indica que a solicitação é para retornar um status de WebHCat (também conhecido como Templeton) para o servidor. Você também pode solicitar a versão do hive usando o seguinte comando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Essa solicitação retorna uma resposta semelhante ao seguinte texto:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Use o seguinte para criar uma tabela chamada **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Essa solicitação usa o método POST, que envia dados como parte da solicitação para a API REST. Os seguintes valores de dados são enviados com a solicitação:

     * `user.name`-O usuário que está executando o comando.
     * `execute`-As instruções HiveQL a serem executadas.
     * `statusdir`-O diretório no qual o status desse trabalho é gravado.

   Essas instruções executam as seguintes ações:

   * `DROP TABLE`-Se a tabela já existir, ela será excluída.
   * `CREATE EXTERNAL TABLE`-Cria uma nova tabela ' externa ' no hive. As tabelas externas armazenam apenas a definição de tabela no hive. Os dados são deixados no local original.

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de carregamento de dados automatizado ou outra operação MapReduce.
     >
     > Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

   * `ROW FORMAT`-Como os dados são formatados. Os campos em cada log são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION`-Onde os dados são armazenados (o diretório de exemplo/dados) e que são armazenados como texto.
   * `SELECT`– Seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]** . Essa instrução retorna um valor de **3** , pois há três linhas que contêm esse valor.

     > [!NOTE]  
     > Observe que os espaços entre as instruções HiveQL são substituídos `+` pelo caractere quando usados com ondulação. Valores entre aspas que contêm um espaço, como o delimitador, não devem ser `+`substituídos por.

      Esse comando retorna uma ID de trabalho que pode ser usada para verificar o status do trabalho.

1. Para verificar o status do trabalho, use o seguinte comando:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Se o trabalho tiver sido concluído, o estado será **bem-sucedido**.

1. Depois que o estado do trabalho for alterado para **êxito**, você poderá recuperar os resultados do trabalho do armazenamento de BLOBs do Azure. O `statusdir` parâmetro passado com a consulta contém o local do arquivo de saída; nesse caso, `/example/rest`. Esse endereço armazena a saída no `example/curl` diretório no armazenamento padrão de clusters.

    Você pode listar e baixar esses arquivos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como usar o CLI do Azure com o armazenamento do Azure, consulte o documento [usar CLI do Azure com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) .

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o hive com o HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre a API REST usada neste documento, consulte o documento de [referência do WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .