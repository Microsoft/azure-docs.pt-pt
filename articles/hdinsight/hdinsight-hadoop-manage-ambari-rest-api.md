---
title: Monitorizar e gerir Hadoop com o Ambari REST API - Azure HDInsight
description: Saiba como utilizar Ambari para monitorizar e gerir clusters do Hadoop no HDInsight do Azure. Neste documento, aprenderá a utilizar a API de REST do Ambari incluídos com clusters do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 38d845e133b5ae1a091fd9fee698b2bf801135e1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880607"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerir clusters do HDInsight com a API de REST do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Saiba como utilizar a API de REST do Apache Ambari para gerir e monitorizar clusters do Apache Hadoop no HDInsight do Azure.

## <a id="whatis"></a>O que é o Apache Ambari
[Apache Ambari](https://ambari.apache.org) simplifica a gestão e monitorização de clusters do Hadoop, fornecendo uma web fácil de utilizar da interface do Usuário apoiada por seus [REST APIs](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por predefinição com clusters do HDInsight baseado em Linux.

## <a name="prerequisites"></a>Pré-requisitos
* **Um cluster do Hadoop no HDInsight**. Ver [introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash no Ubuntu no Windows 10**.  Os exemplos neste artigo utilizam o shell de Bash no Windows 10. Ver [subsistema Windows para o guia de instalação de Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter os passos de instalação.  Outros [shells do Unix](https://www.gnu.org/software/bash/) funcionará bem.  Os exemplos, com algumas pequenas modificações, podem trabalhar num prompt de comando do Windows.  Em alternativa, pode usar o Windows PowerShell.

* **jq**, um processador JSON da linha de comandos.  Ver [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Em alternativa, pode utilizar [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Base de URI para a API de Rest do Ambari

 É o URI de base para a API de REST do Ambari no HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, onde `CLUSTERNAME` é o nome do seu cluster.  Os nomes de cluster nos URIs são **diferencia maiúsculas de minúsculas**.  Embora o nome do cluster na parte de nome (FQDN) de domínio completamente qualificado do URI (CLUSTERNAME.azurehdinsight.net) seja maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Authentication

Ligar ao Ambari no HDInsight requer HTTPS. Utilize o nome de conta de administrador (a predefinição é **administrador**) e palavra-passe que indicou durante a criação do cluster.

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Programa de configuração (Preserve credenciais)
Preserva as suas credenciais para evitar a entrá-los novamente para cada exemplo.  O nome do cluster será mantido num passo separado.

**A. Bash**  
Edite o script abaixo, substituindo `PASSWORD` com a palavra-passe real.  Em seguida, introduza o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster corretamente com maiúsculas e minúsculas
A real letras maiúsculas e minúsculas no nome do cluster podem ser diferente do que o esperado dependendo de como o cluster foi criado.  Os passos aqui descritos mostrará as maiúsculas e minúsculas real e, em seguida, armazená-los numa variável de todos os exemplos subsequentes.

Editar os scripts abaixo para substituir `CLUSTERNAME` com o nome do cluster. Em seguida, introduza o comando. (O nome do cluster para o FQDN não diferencia maiúsculas de minúsculas.)

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLSUTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
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

### <a name="parsing-json-data"></a>Analisar dados JSON

O exemplo seguinte utiliza [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e apresentar apenas o `health_report` informações dos resultados.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Obtenha o FQDN de nós de cluster

Ao trabalhar com o HDInsight, terá de saber o nome de domínio completamente qualificado (FQDN) de um nó de cluster. Pode obter facilmente o FQDN para vários nós no cluster com os exemplos seguintes:

**Todos os nós**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nós principais**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós de trabalho**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós do zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Obtenha o endereço IP interno de nós de cluster

Os endereços IP retornados pelos exemplos nesta secção não estão diretamente acessíveis na Internet. Apenas estão acessíveis dentro da rede Virtual do Azure que contém o cluster do HDInsight.

Para obter mais informações sobre como trabalhar com o HDInsight e redes virtuais, consulte [capacidades de estender o HDInsight ao utilizar uma rede Virtual do Azure personalizadas](hdinsight-extend-hadoop-virtual-network.md).

Para encontrar o endereço IP, tem de saber o nome de domínio completamente qualificado (FQDN) interno de nós do cluster. Assim que tiver o FQDN, em seguida, pode obter o endereço IP do anfitrião. Os exemplos seguintes primeiro consultar Ambari para o FQDN de todos os nós de host, em seguida, consultar Ambari para o endereço IP de cada anfitrião.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Obter o armazenamento predefinido

Quando cria um cluster do HDInsight, tem de utilizar uma conta de armazenamento do Azure ou o armazenamento do Data Lake como armazenamento predefinido para o cluster. Pode utilizar Ambari para recuperar essas informações depois do cluster ter sido criado. Por exemplo, se quiser leitura/escrita dados para o contentor fora do HDInsight.

Os exemplos seguintes obter a configuração de armazenamento predefinida do cluster:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Estes exemplos podem devolver a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém essas informações. Se recupera um valor que foi modificado após a criação de cluster, terá de listar as versões de configuração e obter aquele mais recente.

O valor de retorno é semelhante a um dos exemplos seguintes:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` -Este valor indica que o cluster está a utilizar uma conta de armazenamento do Azure para armazenamento predefinido. O `ACCOUNTNAME` valor é o nome da conta de armazenamento. O `CONTAINER` parte é o nome do contentor de BLOBs na conta de armazenamento. O contentor é a raiz do armazenamento compatível com HDFS do cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` -Este valor indica que o cluster está a utilizar o Gen2 de armazenamento do Azure Data Lake para o armazenamento predefinido. O `ACCOUNTNAME` e `CONTAINER` valores de ter o mesmo significado como para o armazenamento do Azure, mencionado anteriormente.

* `adl://home` -Este valor indica que o cluster está a utilizar o Gen1 de armazenamento do Azure Data Lake para o armazenamento predefinido.

    Para localizar o nome da conta de armazenamento do Data Lake, utilize os exemplos seguintes:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    O valor de retorno é semelhante à `ACCOUNTNAME.azuredatalakestore.net`, onde `ACCOUNTNAME` é o nome da conta de armazenamento do Data Lake.

    Para localizar o diretório no armazenamento do Data Lake que contém o armazenamento para o cluster, utilize os exemplos seguintes:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    O valor de retorno é semelhante ao `/clusters/CLUSTERNAME/`. Este valor é um caminho dentro da conta de armazenamento do Data Lake. Este caminho é a raiz do sistema de ficheiros compatível com HDFS para o cluster.  

> [!NOTE]  
> O [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet fornecido pelo [Azure PowerShell](/powershell/azure/overview) também devolve as informações de armazenamento para o cluster.


### <a name="get-all-configurations"></a> Obter todas as configurações

Obtenha as configurações que estão disponíveis para o seu cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual (identificados pela *marca* valor) para os componentes instalados no cluster. O exemplo seguinte é um extrato dos dados retornados de um tipo de cluster do Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Obter configuração de componente específico

Obter a configuração para o componente que está interessado. No exemplo seguinte, substitua `INITIAL` com o valor de etiqueta devolvido do pedido anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual para o `livy2-conf` componente.

### <a name="update-configuration"></a>Atualizar a configuração

1. Criar `newconfig.json`.  
   Modificar e, em seguida, introduza os comandos abaixo:

   * Substitua `livy2-conf` com o componente pretendido.
   * Substitua `INITIAL` com o valor real obtida para `tag` partir [obter todas as configurações de](#Get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **Powershell b.**  
     Utiliza o script do PowerShell [jq](https://stedolan.github.io/jq/).  Editar `C:\HD\jq\jq-win64` abaixo para refletir o seu caminho real e a versão do [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq é utilizada para transformar os dados obtidos a partir do HDInsight num novo modelo de configuração. Especificamente, esses exemplos efetuar as seguintes ações:

   * Cria um valor exclusivo que contém a cadeia de caracteres "versão" e a data, o que é armazenada em `newtag`.

   * Cria um documento de raiz para a nova configuração desejada.

   * Obtém o conteúdo do `.items[]` matriz e adiciona-o para baixo a **desired_config** elemento.

   * Elimina o `href`, `version`, e `Config` elementos, como esses elementos não são necessários para submeter uma nova configuração.

   * Adiciona uma `tag` elemento com um valor de `version#################`. A parte numérica baseia-se a data atual. Cada configuração tem de ter uma etiqueta exclusiva.

     Por fim, os dados sejam guardados para a `newconfig.json` documento. A estrutura do documento deverá ser semelhante ao seguinte exemplo:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Editar `newconfig.json`.  
   Abra o `newconfig.json` documento e modificar/adicione valores no `properties` objeto. O exemplo seguinte altera o valor de `"livy.server.csrf_protection.enabled"` partir `"true"` para `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Quando tiver terminado a modificações efetuadas, guarde o ficheiro.

3. Submeter `newconfig.json`.  
   Utilize os seguintes comandos para submeter a configuração atualizada para Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Estes comandos submeter o conteúdo do **newconfig.json** ficheiro para o cluster como a nova configuração desejada. A solicitação retorna um documento JSON. O **versionTag** elemento neste documento deve corresponder à versão submetido, e o **configurações** objeto contém as alterações de configuração pretendida.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste momento, se examinar a IU web do Ambari, o serviço do Spark indica que ele precisa ser reiniciado antes da nova configuração pode entrar em vigor. Utilize os seguintes passos para reiniciar o serviço.

1. Utilize o seguinte para ativar o modo de manutenção para o serviço de Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Verifique se o modo de manutenção  

    Estes comandos enviam um documento JSON para o servidor que ativa o modo de manutenção. Pode verificar que o serviço está agora no modo de manutenção com o pedido seguinte:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    O valor de retorno é `ON`.

3. Em seguida, utilize o seguinte para desativar o serviço de Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    A resposta é semelhante ao seguinte exemplo:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > O `href` valor devolvido por este URI está a utilizar o endereço IP interno do nó de cluster. Para usá-lo de fora do cluster, substitua a parte '10.0.0.18:8080' com o FQDN do cluster.  

4. Verifique se o pedido.  
    Editar o comando abaixo, substituindo `29` com o valor real para `id` devolvido do passo anterior.  Os seguintes comandos obtêm o estado do pedido:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Uma resposta de `COMPLETED` indica que o pedido foi concluída.

5. Quando o pedido anterior for concluída, utilize o seguinte para iniciar o serviço de Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    O serviço está agora a utilizar a nova configuração.

6. Por fim, utilize o seguinte para desativar o modo de manutenção.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Passos Seguintes

Para obter uma referência completa da REST API, consulte [V1 de referência de API do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

