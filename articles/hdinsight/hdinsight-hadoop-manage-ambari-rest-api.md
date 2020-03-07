---
title: Monitorize e gerencie Hadoop com Ambari REST API - Azure HDInsight
description: Aprenda a usar Ambari para monitorizar e gerir os clusters Hadoop em Azure HDInsight. Neste documento, você aprenderá a usar a API Ambari REST incluída com clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: 1d684957939c5cb83aae05962c1694f7a8d8da23
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386436"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerir os clusters HDInsight utilizando a API De REPOUSO Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar a API Apache Ambari REST para gerir e monitorizar os clusters Apache Hadoop em Azure HDInsight.

## <a id="whatis"></a>O que é Apache Ambari

[Apache Ambari](https://ambari.apache.org) simplifica a gestão e monitorização dos clusters Hadoop, fornecendo uma UI web fácil de usar apoiada pelas suas [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por padrão com clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

* **Um aglomerado de Hadoop no HDInsight**. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bata em Ubuntu no Windows 10**.  Os exemplos deste artigo usam a concha bash no Windows 10. Consulte o Subsistema Windows para obter o Guia de [Instalação do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outras [conchas unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos, com algumas ligeiras modificações, podem funcionar num pedido de Comando windows.  Em alternativa, pode utilizar o Windows PowerShell.

* **jq**, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/. ](https://stedolan.github.io/jq/)

* **Windows PowerShell**.  Em alternativa, pode usar [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Base URI para Ambari Rest API

 O identificador de recursos uniformes base (URI) para a Ambari REST API no HDInsight é `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, onde `CLUSTERNAME` é o nome do seu cluster.  Os nomes de cluster em URIs são **sensíveis a casos.**  Embora o nome do cluster na parte de domínio totalmente qualificado (FQDN) do URI (`CLUSTERNAME.azurehdinsight.net`) seja insensível a casos, outras ocorrências no URI são sensíveis a casos.

## <a name="authentication"></a>Autenticação

Ligar a Ambari no HDInsight requer HTTPS. Utilize o nome da conta de administração (o predefinido é **administrador)** e a palavra-passe fornecida durante a criação do cluster.

Para os clusters do Pacote de Segurança Empresarial, em vez de `admin`, utilize um nome de utilizador totalmente qualificado como `username@domain.onmicrosoft.com`.

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Configuração (Preservar credenciais)
Preserve as suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado num passo separado.

**A. Bash**  
Edite o script abaixo substituindo `PASSWORD` pela sua senha real.  Em seguida, entre no comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifique o nome do cluster devidamente arquivado
O invólucro real do nome do cluster pode ser diferente do que se espera, dependendo de como o cluster foi criado.  Os passos aqui mostrarão o invólucro real, e depois armazená-lo-ão numa variável para todos os exemplos subsequentes.

Edite os scripts abaixo para substituir `CLUSTERNAME` pelo nome do cluster. Em seguida, entre no comando. (O nome do cluster para o FQDN não é sensível a casos.)

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

O exemplo seguinte utiliza [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e mostrar apenas as informações `health_report` a partir dos resultados.

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

### <a name="example-get-the-fqdn-of-cluster-nodes"></a>Obtenha o FQDN de nós de cluster

Ao trabalhar com o HDInsight, poderá necessitar de saber o nome de domínio totalmente qualificado (FQDN) de um nó de cluster. Pode facilmente recuperar o FQDN para os vários nós do cluster utilizando os seguintes exemplos:

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

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Obtenha o endereço IP interno dos nós do cluster

Os endereços IP devolvidos pelos exemplos desta secção não são diretamente acessíveis através da internet. Só são acessíveis dentro da Rede Virtual Azure que contém o cluster HDInsight.

Para obter mais informações sobre o trabalho com o HDInsight e redes virtuais, consulte [Plan a rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para encontrar o endereço IP, deve conhecer o nome de domínio interno totalmente qualificado (FQDN) dos nós do cluster. Assim que tiver o FQDN, poderá obter o endereço IP do anfitrião. Os exemplos seguintes primeiro consulta Ambari para o FQDN de todos os nós hospedeiros, em seguida, consulta Ambari para o endereço IP de cada anfitrião.

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

Quando criar um cluster HDInsight, deve utilizar uma Conta de Armazenamento Azure ou armazenamento de data lake como armazenamento padrão para o cluster. Pode usar Ambari para recuperar esta informação depois de o cluster ter sido criado. Por exemplo, se quiser ler/escrever dados para o recipiente fora do HDInsight.

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
> Estes exemplos devolvem a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém esta informação. Se recuperar um valor que tenha sido modificado após a criação do cluster, poderá ter de listar as versões de configuração e recuperar a mais recente.

O valor de retorno é semelhante a um dos seguintes exemplos:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Este valor indica que o cluster está a utilizar uma conta de Armazenamento Azure para armazenamento por defeito. O valor `ACCOUNTNAME` é o nome da conta de armazenamento. A parte `CONTAINER` é o nome do recipiente de bolhas na conta de armazenamento. O recipiente é a raiz do armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - Este valor indica que o cluster está a utilizar o Azure Data Lake Storage Gen2 para armazenamento predefinido. Os valores `ACCOUNTNAME` e `CONTAINER` têm os mesmos significados que para o Armazenamento Azure mencionadoanteriormente.

* `adl://home` - Este valor indica que o cluster está a utilizar o Azure Data Lake Storage Gen1 para armazenamento predefinido.

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

    O valor de devolução é semelhante ao `ACCOUNTNAME.azuredatalakestore.net`, onde `ACCOUNTNAME` é o nome da conta data Lake Storage.

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

    O valor de retorno é semelhante ao `/clusters/CLUSTERNAME/`. Este valor é um caminho dentro da conta data lake armazenamento. Este caminho é a raiz do sistema de ficheirocompatível com HDFS para o cluster.  

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

Este exemplo devolve um documento JSON contendo a configuração atual (identificada pelo valor da *etiqueta)* para os componentes instalados no cluster. O exemplo seguinte é um excerto dos dados devolvidos de um tipo de cluster Spark.

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

Obtenha a configuração do componente em que está interessado. No exemplo seguinte, substitua `INITIAL` pelo valor da etiqueta devolvido ao pedido anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo devolve um documento JSON contendo a configuração atual para o componente `livy2-conf`.

### <a name="update-configuration"></a>Configuração de atualização

1. Criar `newconfig.json`.  
   Modificar e, em seguida, introduzir os comandos abaixo:

   * Substitua `livy2-conf` pelo componente pretendido.
   * Substitua `INITIAL` pelo valor real recuperado para `tag` a partir de [Todas as configurações](#get-all-configurations).

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

     Jq é usado para transformar os dados recuperados do HDInsight num novo modelo de configuração. Especificamente, estes exemplos executam as seguintes ações:

   * Cria um valor único contendo a "versão" da corda e a data, que é armazenada em `newtag`.

   * Cria um documento de raiz para a nova configuração desejada.

   * Obtém o conteúdo da matriz de `.items[]` e adiciona-o sob o elemento **desired_config.**

   * Elimina os elementos `href`, `version`e `Config`, uma vez que estes elementos não são necessários para apresentar uma nova configuração.

   * Adiciona um elemento `tag` com um valor de `version#################`. A parte numérica baseia-se na data atual. Cada configuração deve ter uma etiqueta única.

     Finalmente, os dados são guardados no documento `newconfig.json`. A estrutura documental deve parecer semelhante ao seguinte exemplo:

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
   Abra o documento `newconfig.json` e modifique/adicione valores no `properties` objeto. O exemplo seguinte altera o valor das `"livy.server.csrf_protection.enabled"` de `"true"` para `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Guarde o ficheiro assim que terminar a efiza modificações.

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

    Estes comandos submetem o conteúdo do ficheiro **newconfig.json** ao cluster como a nova configuração desejada. O pedido devolve um documento DaJSON. O elemento **versãoTag** neste documento deve corresponder à versão que submeteu e o objeto **de configs** contém as alterações de configuração que solicitou.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, se olharmos para o UI web ambari, o serviço Spark indica que precisa de ser reiniciado antes que a nova configuração possa produzir efeito. Utilize os seguintes passos para reiniciar o serviço.

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

    O valor de devolução é `ON`.

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
    > O valor `href` devolvido por este URI está a utilizar o endereço IP interno do nó cluster. Para usá-lo de fora do cluster, substitua a parte `10.0.0.18:8080` com o FQDN do cluster.  

4. Verifique o pedido.  
    Editar o comando abaixo substituindo `29` pelo valor real para `id` devolvido do passo anterior.  Os seguintes comandos recuperam o estado do pedido:

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

    Uma resposta de `COMPLETED` indica que o pedido terminou.

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
