---
title: Monitorar e gerenciar o Hadoop com a API REST do Ambari-Azure HDInsight
description: Saiba como usar o Ambari para monitorar e gerenciar clusters Hadoop no Azure HDInsight. Neste documento, você aprenderá a usar a API REST do amAmbari incluída com clusters HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: hrasheed
ms.openlocfilehash: 5836dda186c7bd9baeafa193052b0b75d63e291b
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019609"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerenciar clusters HDInsight usando a API REST do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Saiba como usar a API REST do Apache Ambari para gerenciar e monitorar clusters Apache Hadoop no Azure HDInsight.

## <a id="whatis"></a>O que é o Apache Ambari

O [Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento e o monitoramento de clusters do Hadoop, fornecendo uma interface do usuário da Web fácil de usar com suporte de suas [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  O Ambari é fornecido por padrão com clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop no HDInsight**. Consulte [introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash no Ubuntu no Windows 10**.  Os exemplos neste artigo usam o shell bash no Windows 10. Consulte o [Guia de instalação do subsistema do Windows para Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outros [shells do UNIX](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos, com algumas pequenas modificações, podem funcionar em um prompt de comando do Windows.  Como alternativa, você pode usar o Windows PowerShell.

* **JQ**, um processador JSON de linha de comando.  Consulte [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Como alternativa, você pode usar o [bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>URI de base para API REST do Ambari

 O URI (Uniform Resource Identifier base) para a API REST do Ambari no HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`é, `CLUSTERNAME` em que é o nome do cluster.  Os nomes de cluster nosURIs diferenciam maiúsculas de minúsculas.  Embora o nome do cluster na parte do FQDN (nome de domínio totalmente qualificado) do URI`CLUSTERNAME.azurehdinsight.net`() não diferencia maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Authentication

Conectar-se ao Ambari no HDInsight requer HTTPS. Use o nome da conta de administrador (o padrão é **admin**) e a senha que você forneceu durante a criação do cluster.

Para Enterprise Security Package clusters, em vez `admin`de, use um nome de usuário `username@domain.onmicrosoft.com`totalmente qualificado como.

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Instalação (preservar credenciais)
Preserve suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado em uma etapa separada.

**UM. Raso**  
Edite o script a seguir `PASSWORD` substituindo pela sua senha real.  Em seguida, digite o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

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

### <a name="parsing-json-data"></a>Analisando dados JSON

O exemplo a seguir usa [JQ](https://stedolan.github.io/jq/) ou [ConvertFrom-JSON](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e exibir `health_report` apenas as informações dos resultados.

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

### <a name="example-get-the-fqdn-of-cluster-nodes"></a>Obter o FQDN de nós de cluster

Ao trabalhar com o HDInsight, talvez seja necessário saber o FQDN (nome de domínio totalmente qualificado) de um nó de cluster. Você pode recuperar facilmente o FQDN para os vários nós no cluster usando os seguintes exemplos:

**Todos os nós**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nós de cabeçalho**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Obter o endereço IP interno dos nós de cluster

Os endereços IP retornados pelos exemplos nesta seção não podem ser acessados diretamente pela Internet. Eles só podem ser acessados na rede virtual do Azure que contém o cluster HDInsight.

Para obter mais informações sobre como trabalhar com o HDInsight e redes virtuais, consulte [planejar uma rede virtual para o hdinsight](hdinsight-plan-virtual-network-deployment.md).

Para localizar o endereço IP, você deve saber o FQDN (nome de domínio totalmente qualificado) interno dos nós do cluster. Depois de ter o FQDN, você pode obter o endereço IP do host. Os exemplos a seguir primeiro consultam Ambari para o FQDN de todos os nós de host e, em seguida, consulta Ambari para o endereço IP de cada host.

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

### <a name="get-the-default-storage"></a>Obter o armazenamento padrão

Ao criar um cluster HDInsight, você deve usar uma conta de armazenamento do Azure ou Data Lake Storage como o armazenamento padrão para o cluster. Você pode usar Ambari para recuperar essas informações depois que o cluster tiver sido criado. Por exemplo, se você quiser ler/gravar dados no contêiner fora do HDInsight.

Os exemplos a seguir recuperam a configuração de armazenamento padrão do cluster:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Esses exemplos retornam a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém essas informações. Se você recuperar um valor que foi modificado após a criação do cluster, talvez seja necessário listar as versões de configuração e recuperar a versão mais recente.

O valor de retorno é semelhante a um dos seguintes exemplos:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-Esse valor indica que o cluster está usando uma conta de armazenamento do Azure para armazenamento padrão. O `ACCOUNTNAME` valor é o nome da conta de armazenamento. A `CONTAINER` parte é o nome do contêiner de blob na conta de armazenamento. O contêiner é a raiz do armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`-Esse valor indica que o cluster está usando Azure Data Lake Storage Gen2 para armazenamento padrão. Os `ACCOUNTNAME` valores `CONTAINER` e têm os mesmos significados para o armazenamento do Azure mencionados anteriormente.

* `adl://home`-Esse valor indica que o cluster está usando Azure Data Lake Storage Gen1 para armazenamento padrão.

    Para localizar o nome da conta de Data Lake Storage, use os seguintes exemplos:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    O valor de retorno é semelhante `ACCOUNTNAME.azuredatalakestore.net`a, `ACCOUNTNAME` em que é o nome da conta de data Lake Storage.

    Para localizar o diretório em Data Lake Storage que contém o armazenamento para o cluster, use os exemplos a seguir:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    O valor de retorno é semelhante `/clusters/CLUSTERNAME/`a. Esse valor é um caminho dentro da conta de Data Lake Storage. Esse caminho é a raiz do sistema de arquivos compatível com HDFS para o cluster.  

> [!NOTE]  
> O cmdlet [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) fornecido pelo [Azure PowerShell](/powershell/azure/overview) também retorna as informações de armazenamento para o cluster.

### <a name="get-all-configurations"></a>Obter todas as configurações

Obtenha as configurações que estão disponíveis para o cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual (identificada pelo valor de *marca* ) para os componentes instalados no cluster. O exemplo a seguir é um trecho dos dados retornados de um tipo de cluster Spark.

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

### <a name="get-configuration-for-specific-component"></a>Obter configuração para o componente específico

Obtenha a configuração para o componente no qual você está interessado. No exemplo a seguir, substitua `INITIAL` pelo valor de marca retornado da solicitação anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual do `livy2-conf` componente.

### <a name="update-configuration"></a>Atualizar configuração

1. Criar `newconfig.json`.  
   Modifique e, em seguida, insira os comandos abaixo:

   * Substitua `livy2-conf` pelo componente desejado.
   * Substituir `INITIAL` pelo valor real recuperado de `tag` de [obter todas as configurações](#get-all-configurations).

     **UM. Raso**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     O script do PowerShell usa [JQ](https://stedolan.github.io/jq/).  Edite `C:\HD\jq\jq-win64` abaixo para refletir o caminho real e a versão do [JQ](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ é usado para transformar os dados recuperados do HDInsight em um novo modelo de configuração. Especificamente, esses exemplos executam as seguintes ações:

   * Cria um valor exclusivo contendo a cadeia de caracteres "Version" e a data, que é `newtag`armazenada em.

   * Cria um documento raiz para a nova configuração desejada.

   * Obtém o conteúdo da `.items[]` matriz e o adiciona sob o elemento **desired_config** .

   * Exclui os `href`elementos `version`, e `Config` , pois esses elementos não são necessários para enviar uma nova configuração.

   * Adiciona um `tag` elemento com um valor de `version#################`. A parte numérica é baseada na data atual. Cada configuração deve ter uma marca exclusiva.

     Por fim, os dados são salvos `newconfig.json` no documento. A estrutura do documento deve ser semelhante ao exemplo a seguir:

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
   Abra o `newconfig.json` documento e modifique/adicione valores `properties` no objeto. O exemplo a seguir altera o valor `"livy.server.csrf_protection.enabled"` de `"true"` de `"false"`para.

        "livy.server.csrf_protection.enabled": "false",

    Salve o arquivo quando terminar de fazer modificações.

3. Enviar `newconfig.json`.  
   Use os comandos a seguir para enviar a configuração atualizada para o Ambari.

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

    Esses comandos enviam o conteúdo do arquivo **newconfig. JSON** para o cluster como a nova configuração desejada. A solicitação retorna um documento JSON. O elemento **versionTag** neste documento deve corresponder à versão que você enviou e o objeto **configs** contém as alterações de configuração solicitadas.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, se você examinar a interface do usuário da Web do amAmbari, o serviço Spark indicará que ele precisa ser reiniciado para que a nova configuração entre em vigor. Use as etapas a seguir para reiniciar o serviço.

1. Use o seguinte para habilitar o modo de manutenção para o serviço Spark2:

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
    ```

2. Verificar o modo de manutenção  

    Esses comandos enviam um documento JSON para o servidor que ativa o modo de manutenção. Você pode verificar se o serviço agora está em modo de manutenção usando a seguinte solicitação:

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

    O valor de retorno `ON`é.

3. Em seguida, use o seguinte para desativar o serviço Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > O `href` valor retornado por esse URI está usando o endereço IP interno do nó do cluster. Para usá-lo de fora do cluster, substitua `10.0.0.18:8080` a parte pelo FQDN do cluster.  

4. Verificar solicitação.  
    Edite o comando a seguir `29` substituindo pelo valor real `id` para retornado da etapa anterior.  Os comandos a seguir recuperam o status da solicitação:

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

    Uma resposta de `COMPLETED` indica que a solicitação foi concluída.

5. Depois que a solicitação anterior for concluída, use o seguinte para iniciar o serviço Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    O serviço agora está usando a nova configuração.

6. Por fim, use o seguinte para desativar o modo de manutenção.

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

## <a name="next-steps"></a>Passos seguintes

Para obter uma referência completa da API REST, consulte [referência da API do Apache Ambari v1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).
