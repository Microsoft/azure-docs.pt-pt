---
title: Monitorize e gerencie Hadoop com Ambari REST API - Azure HDInsight
description: Aprenda a usar Ambari para monitorizar e gerir os clusters Hadoop em Azure HDInsight. Neste documento, você aprenderá a usar a API Ambari REST incluída com clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381386"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerir os clusters HDInsight utilizando a API De REPOUSO Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar a API Apache Ambari REST para gerir e monitorizar os clusters Apache Hadoop em Azure HDInsight.

## <a name="what-is-apache-ambari"></a>O que é Apache Ambari

[Apache Ambari](https://ambari.apache.org) simplifica a gestão e monitorização dos clusters Hadoop, fornecendo uma UI web fácil de usar apoiada pelas suas [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por padrão com clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bata em Ubuntu no Windows 10.  Os exemplos deste artigo usam a concha bash no Windows 10. Consulte o Subsistema Windows para obter o Guia de [Instalação do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outras [conchas unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos, com algumas ligeiras modificações, podem funcionar num pedido de Comando windows.  Ou pode utilizar o Windows PowerShell.

* jq, um processador JSON de linha de comando.  Vê. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

* Windows PowerShell.  Ou podes usar [o Bash.](https://www.gnu.org/software/bash/)

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Identificador de recursos uniformes de base para Ambari Rest API

 O identificador de recursos uniformes base (URI) para a Ambari REST API no HDInsight é, `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`onde `CLUSTERNAME` está o nome do seu cluster.  Os nomes de cluster em URIs são **sensíveis a casos.**  Enquanto o nome do cluster na parte de domínio totalmente`CLUSTERNAME.azurehdinsight.net`qualificado (FQDN) do URI () é insensível a casos, outras ocorrências no URI são sensíveis a casos.

## <a name="authentication"></a>Autenticação

Ligar a Ambari no HDInsight requer HTTPS. Utilize o nome da conta de administração (o predefinido é **administrador)** e a palavra-passe fornecida durante a criação do cluster.

Para os clusters do `admin`Pacote de Segurança Empresarial, `username@domain.onmicrosoft.com`em vez de utilizar um nome de utilizador totalmente qualificado como .

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Configuração (Preservar credenciais)

Preserve as suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado num passo separado.

**A. Bash**  
Edite o script `PASSWORD` abaixo substituindo pela sua senha real.  Em seguida, entre no comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifique o nome do cluster devidamente arquivado

O invólucro real do nome do cluster pode ser diferente do que se espera.  Os passos aqui mostrarão o invólucro real, e depois armazená-lo-ão numa variável para todos os exemplos posteriores.

Edite os scripts `CLUSTERNAME` abaixo para substituir pelo nome do cluster. Em seguida, entre no comando. (O nome do cluster para o FQDN não é sensível a casos.)

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

### <a name="parsing-json-data"></a>Dados da JSON de análise

O exemplo seguinte utiliza [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o `health_report` documento de resposta JSON e mostrar apenas as informações dos resultados.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Obtenha o FQDN de nós de cluster

Pode ser necessário saber o nome de domínio totalmente qualificado (FQDN) de um nó de cluster. Pode facilmente recuperar o FQDN para os vários nós do cluster utilizando os seguintes exemplos:

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

**Nódosos da cabeça**  

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

**Nódosos operários**  

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

**Nódoa zookeeper**

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

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Obtenha o endereço IP interno dos nós do cluster

Os endereços IP devolvidos pelos exemplos desta secção não são diretamente acessíveis através da internet. Só são acessíveis dentro da Rede Virtual Azure que contém o cluster HDInsight.

Para obter mais informações sobre o trabalho com o HDInsight e redes virtuais, consulte [Plan a rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para encontrar o endereço IP, deve conhecer o nome de domínio interno totalmente qualificado (FQDN) dos nós do cluster. Assim que tiver o FQDN, poderá obter o endereço IP do anfitrião. Os seguintes exemplos primeira consulta Ambari para o FQDN de todos os nós hospedeiros. Em seguida, consulta Ambari para o endereço IP de cada anfitrião.

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

Os clusters HDInsight devem utilizar uma conta de armazenamento Azure ou armazenamento de data lake como armazenamento padrão. Pode usar Ambari para recuperar esta informação depois de o cluster ter sido criado. Por exemplo, se quiser ler/escrever dados para o recipiente fora do HDInsight.

Os seguintes exemplos recuperam a configuração de armazenamento predefinido do cluster:

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
> Estes exemplos devolvem a primeira`service_config_version=1`configuração aplicada ao servidor ( ) que contém esta informação. Se recuperar um valor que tenha sido modificado após a criação do cluster, poderá ter de listar as versões de configuração e recuperar a mais recente.

O valor de retorno é semelhante a um dos seguintes exemplos:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Este valor indica que o cluster está a utilizar uma conta de Armazenamento Azure para armazenamento por defeito. O `ACCOUNTNAME` valor é o nome da conta de armazenamento. A `CONTAINER` porção é o nome do recipiente de bolhas na conta de armazenamento. O recipiente é a raiz do armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Este valor indica que o cluster está a utilizar o Azure Data Lake Storage Gen2 para armazenamento por defeito. Os `ACCOUNTNAME` `CONTAINER` valores e valores têm os mesmos significados que para o Armazenamento Azure mencionadoanteriormente.

* `adl://home`- Este valor indica que o cluster está a utilizar o Azure Data Lake Storage Gen1 para armazenamento por defeito.

    Para encontrar o nome da conta data Lake Storage, use os seguintes exemplos:

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

    O valor de `ACCOUNTNAME.azuredatalakestore.net`devolução `ACCOUNTNAME` é semelhante ao, onde está o nome da conta data lake storage.

    Para encontrar o diretório dentro do Data Lake Storage que contém o armazenamento para o cluster, use os seguintes exemplos:

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

    O valor de `/clusters/CLUSTERNAME/`retorno é semelhante a . Este valor é um caminho dentro da conta data lake armazenamento. Este caminho é a raiz do sistema de ficheirocompatível com HDFS para o cluster.  

> [!NOTE]  
> O cmdlet [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) fornecido pela [Azure PowerShell](/powershell/azure/overview) também devolve as informações de armazenamento para o cluster.

### <a name="get-all-configurations"></a>Obtenha todas as configurações

Obtenha as configurações disponíveis para o seu cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Este exemplo devolve um documento JSON contendo a configuração atual para componentes instalados. Veja o valor da *etiqueta.* O exemplo seguinte é um excerto dos dados devolvidos de um tipo de cluster Spark.

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

Obtenha a configuração do componente em que está interessado. No exemplo seguinte, `INITIAL` substitua-o pelo valor da etiqueta devolvido ao pedido anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo devolve um documento JSON `livy2-conf` contendo a configuração atual do componente.

### <a name="update-configuration"></a>Configuração de atualização

1. Criar `newconfig.json`.  
   Modificar e, em seguida, introduzir os comandos abaixo:

   * Substitua-a `livy2-conf` com o novo componente.
   * Substitua `INITIAL` pelo valor `tag` real recuperado para obter [todas as configurações](#get-all-configurations).

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     O script PowerShell usa [jq](https://stedolan.github.io/jq/).  Edite `C:\HD\jq\jq-win64` abaixo para refletir o seu caminho real e versão do [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq é usado para transformar os dados recuperados do HDInsight num novo modelo de configuração. Especificamente, estes exemplos fazem as seguintes ações:

   * Cria um valor único contendo a "versão" da corda `newtag`e a data, que é armazenada em .

   * Cria um documento de raiz para a nova configuração.

   * Obtém o `.items[]` conteúdo da matriz e adiciona-o sob o elemento **desired_config.**

   * Elimina os `href` `version`elementos `Config` e elementos, uma vez que estes elementos não são necessários para submeter uma nova configuração.

   * Adiciona `tag` um elemento com `version#################`um valor de . A parte numérica baseia-se na data atual. Cada configuração deve ter uma etiqueta única.

     Finalmente, os dados são `newconfig.json` guardados no documento. A estrutura documental deve parecer semelhante ao seguinte exemplo:

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

2. Editar. `newconfig.json`  
   Abra `newconfig.json` o documento e modifique/adicione valores no `properties` objeto. O exemplo seguinte altera `"livy.server.csrf_protection.enabled"` `"true"` o `"false"`valor de .

        "livy.server.csrf_protection.enabled": "false",

    Guarde o ficheiro assim que terminar de fazer modificações.

3. Submeter `newconfig.json`.  
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

    Estes comandos submetem o conteúdo do ficheiro **newconfig.json** ao cluster como a nova configuração. O pedido devolve um documento DaJSON. O elemento **versãoTag** neste documento deve corresponder à versão que submeteu e o objeto **de configs** contém as alterações de configuração que solicitou.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, o UI web ambari indica que o serviço Spark precisa de ser reiniciado antes que a nova configuração possa produzir efeito. Utilize os seguintes passos para reiniciar o serviço.

1. Utilize o seguinte modo de ativação do modo de manutenção do serviço Spark2:

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

2. Verifique o modo de manutenção  

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

    O valor `ON`de retorno é .

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
    > O `href` valor devolvido por este URI está a utilizar o endereço IP interno do nó cluster. Para usá-lo de fora `10.0.0.18:8080` do cluster, substitua a porção com o FQDN do cluster.  

4. Verifique o pedido.  
    Editar o comando abaixo `29` substituindo pelo `id` valor real para devolvido do passo anterior.  Os seguintes comandos recuperam o estado do pedido:

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

    O serviço está agora a usar a nova configuração.

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

## <a name="next-steps"></a>Passos seguintes

Para obter uma referência completa da API REST, consulte [Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ver também, [Autorizar utilizadores para Apache Ambari Views](./hdinsight-authorize-users-to-ambari.md)
