---
title: Como obter os nomes hospedeiros dos nosdes de cluster Azure HDInsight
description: Saiba como obter nomes de anfitriões e o nome FQDN dos nós do cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111468"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Encontre os nomes dos anfitriões dos Nóns de Cluster

O cluster HDInsight é criado com clustername.azurehdinsight.net de DNS públicos. Quando você SSH para nós individuais ou definir ligação aos nós de cluster com na mesma rede virtual personalizada, você precisa usar o nome do anfitrião, ou nomes de domínio totalmente qualificados (FQDN) de nós de cluster.

Neste artigo, aprende-se a obter os nomes hospedeiros dos nós do cluster. Pode obtê-lo manualmente através da Ambari Web UI ou automaticamente através da Ambari REST API.

> [!WARNING]
> Por favor, utilize as seguintes abordagens recomendadas para obter nomes de anfitriões de nós de cluster. Os números no nome do anfitrião não são garantidos em sequência e o HDInsight pode alterar o formato do nome do anfitrião para alinhar com VMs com a atualização de lançamento. Não tome a dependência de qualquer convenção de nomeação que existe hoje. 
>

Você pode obter os nomes de anfitrião através da Ambari UI ou Ambari REST API. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Obtenha os nomes dos anfitriões da Ambari Web UI
Você pode usar Ambari Web UI para obter os nomes de anfitrião quando você SSH para o nó. A vista de anfitriões Ambari Web UI está disponível no seu cluster HDInsight em `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , onde está o nome do seu `CLUSTERNAME` cluster.

![Get-Host-Names-In-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Obtenha os nomes dos anfitriões da Ambari REST API
Ao construir scripts de automação, pode utilizar a API Ambari REST para obter os nomes dos anfitriões antes de fazer ligações aos anfitriões. Os números no nome do anfitrião não são garantidos em sequência e o HDInsight pode alterar o formato do nome do anfitrião para alinhar com VMs com a atualização de lançamento. Não tome a dependência de qualquer convenção de nomeação que existe hoje. 

Aqui estão alguns exemplos de como recuperar o FQDN para os nós no cluster. Para obter mais informações sobre a Ambari REST API, consulte [Gerir os clusters HDInsight utilizando a API Apache Ambari REST](.\hdinsight-hadoop-manage-ambari-rest-api.md)

O exemplo a seguir utiliza [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e exibir apenas os nomes dos anfitriões.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```