---
title: Monitorize e gere a Hadoop com a AMBAri REST API - Azure HDInsight
description: Aprenda a usar a Ambari para monitorizar e gerir os clusters Hadoop em Azure HDInsight. Neste documento, você aprenderá a usar a API Ambari REST incluída com clusters HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1d4e6f0d6a0242cda919364965a61e4314927d87
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945581"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerir os clusters HDInsight utilizando a API Apache Ambari REST

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar a API Apache Ambari REST para gerir e monitorizar os clusters Apache Hadoop em Azure HDInsight.

## <a name="what-is-apache-ambari"></a>O que é Apache Ambari

A Apache Ambari simplifica a gestão e monitorização dos clusters Hadoop, proporcionando uma UI web fácil de usar apoiada pelas suas [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por padrão com clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado hadoop em HDInsight. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bata no Ubuntu no Windows 10.  Os exemplos deste artigo utilizam a concha Bash no Windows 10. Consulte [o subsistema Windows para o Guia de Instalação do Linux para o Windows 10](/windows/wsl/install-win10) para obter etapas de instalação.  Outras [conchas Unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos, com algumas modificações ligeiras, podem funcionar numa solicitação do Windows Command.  Ou pode usar o Windows PowerShell.

* jq, um processador JSON de linha de comando.  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)Ver.

* Windows PowerShell.  Ou podes usar o Bash.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Identificador de recursos uniformes base para Ambari Rest API

 O identificador de recursos uniformes base (URI) para a API Ambari REST em HDInsight é `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , onde está o nome do seu `CLUSTERNAME` cluster.  Os nomes de cluster em URIs são **sensíveis a casos.**  Embora o nome do cluster na parte do nome de domínio totalmente qualificado (FQDN) do URI () seja sensível a `CLUSTERNAME.azurehdinsight.net` caso, outras ocorrências no URI são sensíveis a casos.

## <a name="authentication"></a>Autenticação

A ligação a Ambari no HDInsight requer HTTPS. Utilize o nome da conta de administração (o padrão é **administrador)** e a palavra-passe que forneceu durante a criação do cluster.

Para os clusters de pacotes de segurança empresarial, em vez `admin` de, utilize um nome de utilizador totalmente qualificado como `username@domain.onmicrosoft.com` .

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Configuração (Preservar credenciais)

Preserve as suas credenciais para evitar reentrá-las em cada exemplo.  O nome do cluster será preservado num passo separado.

**A. Bash**  
Edite o script abaixo substituindo a `PASSWORD` sua senha real.  Então entre no comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster corretamente arquivado

O invólucro real do nome do cluster pode ser diferente do que se espera.  Os passos aqui mostrarão o invólucro real, e depois armazená-lo-ão numa variável para todos os exemplos posteriores.

Edite os scripts abaixo para substituir `CLUSTERNAME` pelo nome do seu cluster. Então entre no comando. (O nome do cluster para o FQDN não é sensível a casos.)

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

### <a name="parsing-json-data"></a>Análise de dados JSON

O exemplo a seguir utiliza [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e exibir apenas as `health_report` informações dos resultados.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Obtenha o FQDN dos nosdes de cluster

Poderá necessitar de saber o nome de domínio totalmente qualificado (FQDN) de um nó de cluster. Pode facilmente recuperar o FQDN para os vários nos dois no cluster utilizando os seguintes exemplos:

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

**Nó de cabeça**  

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

**Nódoas operárias**  

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

**Nódoa de zookeeper**

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

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Obtenha o endereço IP interno dos nosdes de cluster

Os endereços IP devolvidos pelos exemplos desta secção não estão diretamente acessíveis através da internet. Só são acessíveis dentro da Rede Virtual Azure que contém o cluster HDInsight.

Para obter mais informações sobre o trabalho com a HDInsight e redes virtuais, consulte [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para encontrar o endereço IP, deve conhecer o nome de domínio interno totalmente qualificado (FQDN) dos nós de cluster. Assim que tiver o FQDN, poderá obter o endereço IP do anfitrião. Os seguintes exemplos primeiro consulta Ambari para o FQDN de todos os nós hospedeiros. Em seguida, questione Ambari para o endereço IP de cada anfitrião.

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

### <a name="get-the-default-storage"></a>Obtenha o armazenamento padrão

Os clusters HDInsight devem utilizar uma conta de armazenamento Azure ou armazenamento do Lago de Dados como armazenamento predefinido. Você pode usar Ambari para recuperar esta informação após a criação do cluster. Por exemplo, se pretender ler/escrever dados para o recipiente fora do HDInsight.

Os seguintes exemplos recuperam a configuração de armazenamento padrão do cluster:

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
> Estes exemplos devolvem a primeira configuração aplicada ao servidor ( `service_config_version=1` ) que contém esta informação. Se recuperar um valor que foi modificado após a criação do cluster, poderá ter de listar as versões de configuração e recuperar a mais recente.

O valor de retorno é semelhante a um dos seguintes exemplos:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Este valor indica que o cluster está a utilizar uma conta de Armazenamento Azure para armazenamento predefinido. O `ACCOUNTNAME` valor é o nome da conta de armazenamento. A `CONTAINER` parte é o nome do recipiente blob na conta de armazenamento. O recipiente é a raiz do armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - Este valor indica que o cluster está a utilizar a Azure Data Lake Storage Gen2 para armazenamento predefinido. Os `ACCOUNTNAME` `CONTAINER` valores e valores têm os mesmos significados que para o Azure Storage mencionado anteriormente.

* `adl://home` - Este valor indica que o cluster está a utilizar a Azure Data Lake Storage Gen1 para armazenamento predefinido.

    Para encontrar o nome da conta de armazenamento do Data Lake, utilize os seguintes exemplos:

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

    O valor de retorno é semelhante ao `ACCOUNTNAME.azuredatalakestore.net` , onde está o nome da conta de Armazenamento do Lago de `ACCOUNTNAME` Dados.

    Para encontrar o diretório dentro do Data Lake Storage que contém o armazenamento para o cluster, utilize os seguintes exemplos:

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

    O valor de retorno é semelhante a `/clusters/CLUSTERNAME/` . Este valor é um caminho dentro da conta de Armazenamento do Lago de Dados. Este caminho é a raiz do sistema de ficheiros compatível com HDFS para o cluster.  

> [!NOTE]  
> A cmdlet [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) fornecida pela [Azure PowerShell](/powershell/azure/) também devolve as informações de armazenamento do cluster.

### <a name="get-all-configurations"></a>Obtenha todas as configurações

Obtenha as configurações que estão disponíveis para o seu cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Este exemplo devolve um documento JSON contendo a configuração atual para componentes instalados. Veja o valor da *etiqueta.* O exemplo a seguir é um excerto dos dados devolvidos de um tipo de cluster Spark.

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

### <a name="get-configuration-for-specific-component"></a>Obtenha configuração para componente específico

Obtenha a configuração do componente que lhe interessa. No exemplo seguinte, `INITIAL` substitua-se pelo valor da etiqueta devolvido do pedido anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo devolve um documento JSON contendo a configuração atual do `livy2-conf` componente.

### <a name="update-configuration"></a>Configuração de atualização

1. Criar `newconfig.json` .  
   Modifique e, em seguida, insira os comandos abaixo:

   * `livy2-conf`Substitua-a pelo novo componente.
   * `INITIAL`Substitua-o pelo valor real recuperado para `tag` todas [as configurações](#get-all-configurations).

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     O script PowerShell utiliza [jq](https://stedolan.github.io/jq/).  Edite `C:\HD\jq\jq-win64` abaixo para refletir o seu caminho real e versão do [JQ](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq é usado para transformar os dados obtidos de HDInsight em um novo modelo de configuração. Especificamente, estes exemplos fazem as seguintes ações:

   * Cria um valor único contendo a "versão" da cadeia e a data, que é armazenada em `newtag` .

   * Cria um documento de raiz para a nova configuração.

   * Obtém o conteúdo da `.items[]` matriz e adiciona-o sob o elemento **desired_config.**

   * Elimina o `href` `version` , e `Config` elementos, uma vez que estes elementos não são necessários para submeter uma nova configuração.

   * Adiciona um `tag` elemento com um valor de `version#################` . A porção numérica baseia-se na data atual. Cada configuração deve ter uma etiqueta única.

     Finalmente, os dados são guardados no `newconfig.json` documento. A estrutura do documento deve ser semelhante ao seguinte exemplo:

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

2. `newconfig.json`Editar.  
   Abra o `newconfig.json` documento e modifique/adicione valores no `properties` objeto. O exemplo a seguir altera o valor `"livy.server.csrf_protection.enabled"` de `"true"` `"false"` .

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Guarde o ficheiro assim que terminar de fazer modificações.

3. Submeter `newconfig.json` .  
   Utilize os seguintes comandos para submeter a configuração atualizada a Ambari.

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

    Estes comandos submetem o conteúdo do **newconfig.jsficheiro** ao cluster como a nova configuração. O pedido devolve um documento JSON. O elemento **versãoTag** neste documento deve coincidir com a versão que submeteu e o objeto **configs** contém as alterações de configuração solicitadas.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, o UI web Ambari indica que o serviço Spark precisa de ser reiniciado antes que a nova configuração possa entrar em vigor. Utilize os seguintes passos para reiniciar o serviço.

1. Utilize o seguinte modo de manutenção para o serviço Spark2:

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

    Estes comandos enviam um documento JSON para o servidor que liga o modo de manutenção. Pode verificar se o serviço está agora em modo de manutenção utilizando o seguinte pedido:

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

    O valor de retorno é `ON` .

3. Em seguida, utilize o seguinte para desligar o serviço Spark2:

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
    > O `href` valor devolvido por este URI está a utilizar o endereço IP interno do nó de cluster. Para utilizá-lo a partir do exterior do cluster, substitua a `10.0.0.18:8080` parte pelo FQDN do cluster.  

4. Verifique o pedido.  
    Editar o comando abaixo substituindo `29` pelo valor real para devolvido a partir do passo `id` anterior.  Os seguintes comandos recuperam o estado do pedido:

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

    Uma resposta `COMPLETED` indica que o pedido terminou.

5. Uma vez concluído o pedido anterior, utilize o seguinte para iniciar o serviço Spark2.

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

    O serviço está agora a utilizar a nova configuração.

6. Por fim, utilize o seguinte para desligar o modo de manutenção.

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

## <a name="next-steps"></a>Próximos passos

Para obter uma referência completa da API REST, consulte [a Referência API AMBAri Apache Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Consulte também, [Autorizar utilizadores para Vistas Apache Ambari](./hdinsight-authorize-users-to-ambari.md)