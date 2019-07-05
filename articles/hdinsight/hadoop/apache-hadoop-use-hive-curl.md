---
title: Utilizar o Apache Hadoop Hive com o Curl no HDInsight - Azure
description: Saiba como remotamente submeter as tarefas de Apache Pig para HDInsight com o Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508119"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Executar consultas do Apache Hive com o Apache Hadoop no HDInsight com REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como utilizar a API de REST do WebHCat para executar consultas do Apache Hive com o Apache Hadoop no cluster de HDInsight do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente REST. Este documento usa [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) no Windows PowerShell e [Curl](https://curl.haxx.se/) no [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Se utilizar Bash, também precisará jq, um processador JSON da linha de comandos.  Ver [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Base de URI para a API Rest

É a base identificador URI (Uniform Resource) para a API REST do HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, onde `CLUSTERNAME` é o nome do seu cluster.  Os nomes de cluster nos URIs são **diferencia maiúsculas de minúsculas**.  Embora o nome do cluster na parte de nome (FQDN) de domínio completamente qualificado do URI (`CLUSTERNAME.azurehdinsight.net`) diferencia maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Autenticação

Quando utilizar cURL ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos ao fornecer o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que as suas credenciais são enviadas de forma segura para o servidor, sempre efetuar pedidos utilizando HTTP Secure (HTTPS).

### <a name="setup-preserve-credentials"></a>Programa de configuração (Preserve credenciais)
Preserva as suas credenciais para evitar a entrá-los novamente para cada exemplo.  O nome do cluster será mantido num passo separado.

**A. Bash**  
Edite o script abaixo, substituindo `PASSWORD` com a palavra-passe real.  Em seguida, introduza o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell** executar o código abaixo e introduza as credenciais na janela de pop-up:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster corretamente com maiúsculas e minúsculas
A real letras maiúsculas e minúsculas no nome do cluster podem ser diferente do que o esperado, dependendo de como o cluster foi criado.  Os passos aqui descritos mostrará as maiúsculas e minúsculas real e, em seguida, armazená-los numa variável de todos os exemplos subsequentes.

Editar os scripts abaixo para substituir `CLUSTERNAME` com o nome do cluster. Em seguida, introduza o comando. (O nome do cluster para o FQDN não diferencia maiúsculas de minúsculas.)

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

## <a id="curl"></a>Executar uma consulta do Hive

1. Para verificar se consegue ligar ao seu cluster do HDInsight, utilize um dos seguintes comandos:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Receber uma resposta semelhante ao seguinte texto:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * `-u` -O nome de utilizador e palavra-passe utilizada para autenticar o pedido.
    * `-G` -Indica que este pedido é uma operação de obtenção.

1. O início do URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todos os pedidos. O caminho, `/status`, indica que o pedido deve retornar um status de WebHCat (também conhecido como Templeton) para o servidor. Também pode solicitar a versão do ramo de registo utilizando o seguinte comando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Este pedido devolve uma resposta semelhante ao seguinte texto:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Utilize o seguinte para criar uma tabela chamada **log4jLogs**:

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

    Esse pedido usa o método de mensagem, que envia dados como parte do pedido para a API REST. Os seguintes valores de dados são enviados com o pedido:

     * `user.name` -O utilizador que está a executar o comando.
     * `execute` -As declarações HiveQL para executar.
     * `statusdir` -O diretório que o estado para esta tarefa é escrito.

   Essas instruções executam as seguintes ações:

   * `DROP TABLE` -Se a tabela já existir, este é eliminado.
   * `CREATE EXTERNAL TABLE` -Cria uma nova tabela de "externa" no Hive. Tabelas externas armazenam apenas a definição de tabela no Hive. Os dados são deixados na localização original.

     > [!NOTE]  
     > Tabelas externas devem ser usadas quando espera que os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automatizada ou outra operação de MapReduce.
     >
     > Remover uma tabela externa faz **não** eliminar os dados, apenas a definição da tabela.

   * `ROW FORMAT` -Como os dados estiverem formatados. Os campos em cada registo são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION` -Onde os dados são armazenados (o diretório/dados de exemplo) e que são armazenados como texto.
   * `SELECT` -Seleciona uma contagem de todas as linhas em que coluna **t4** contém o valor **[erro]** . Esta declaração devolve um valor de **3** porque há três linhas que contêm este valor.

     > [!NOTE]  
     > Tenha em atenção que os espaços entre declarações HiveQL são substituídos pelo `+` caráter quando utilizado com o Curl. Valores com aspas simples que contém um espaço como, por exemplo, o delimitador, não devem ser substituídos por `+`.

      Este comando devolve uma ID da tarefa que pode ser utilizado para verificar o estado da tarefa.

1. Para verificar o estado da tarefa, utilize o seguinte comando:

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

    Se a tarefa foi concluída, o estado é **bem-sucedido**.

1. Assim que o estado da tarefa foi alterado para **bem-sucedido**, pode recuperar os resultados da tarefa de armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; nesse caso, `/example/rest`. Este endereço armazena a saída no `example/curl` diretório no armazenamento do padrão de clusters.

    Pode listar e transferir estes ficheiros ao utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como utilizar a CLI do Azure com armazenamento do Azure, consulte a [CLI do Azure de utilização com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) documento.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Hive com o HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre a API REST utilizada neste documento, consulte a [referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) documento.