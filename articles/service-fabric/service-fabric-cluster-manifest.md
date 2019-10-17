---
title: Configurar seu cluster do Azure Service Fabric autônomo | Microsoft Docs
description: Saiba como configurar seu cluster do Azure Service Fabric local ou autônomo.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: ca04539049766e1f053d74b3a8536f154c3fd830
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72383576"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster autônomo do Windows
Este artigo descreve as definições de configuração de um cluster autônomo do Azure Service Fabric que pode ser definido no arquivo *ClusterConfig. JSON* . Você usará esse arquivo para especificar informações sobre os nós do cluster, as configurações de segurança, bem como a topologia de rede em termos de domínios de falha e atualização.  Depois de alterar ou adicionar definições de configuração, você pode [criar um cluster autônomo](service-fabric-cluster-creation-for-windows-server.md) ou [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).

Quando você [baixa o pacote de Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), exemplos de ClusterConfig. JSON também são incluídos. Os exemplos que têm "DevCluster" em seus nomes criam um cluster com todos os três nós no mesmo computador, usando nós lógicos. Desses nós, pelo menos um deve ser marcado como um nó primário. Esse tipo de cluster é útil para ambientes de desenvolvimento ou teste. Não há suporte para ele como um cluster de produção. Os exemplos que têm "vários computadores" em seus nomes ajudam a criar clusters de nível de produção, com cada nó em um computador separado. O número de nós primários para esses clusters é baseado no nível de [confiabilidade](#reliability)do cluster. Na versão 5,7, API versão 05-2017, removemos a propriedade de nível de confiabilidade. Em vez disso, nosso código calcula o nível de confiabilidade mais otimizado para o cluster. Não tente definir um valor para essa propriedade nas versões de 5,7 em diante.

* ClusterConfig. Unsecure. DevCluster. JSON e ClusterConfig. Unsecure. Multimachine. JSON mostram como criar um cluster de teste ou de produção não seguro, respectivamente.

* ClusterConfig. Windows. DevCluster. JSON e ClusterConfig. Windows. Multimachine. JSON mostram como criar clusters de teste ou de produção que são protegidos usando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig. X509. DevCluster. JSON e ClusterConfig. X509. Multimachine. JSON mostram como criar clusters de teste ou de produção que são protegidos usando [a segurança baseada em certificado X509](service-fabric-windows-cluster-x509-security.md).

Agora, vamos examinar as várias seções de um arquivo ClusterConfig. JSON.

## <a name="general-cluster-configurations"></a>Configurações gerais de cluster
As configurações gerais de cluster abrangem as amplas configurações específicas de cluster, conforme mostrado no trecho de JSON a seguir:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Você pode fornecer qualquer nome amigável para o Cluster Service Fabric atribuindo-o à variável de nome. O clusterConfigurationVersion é o número de versão do cluster. Aumente-o sempre que atualizar o Cluster Service Fabric. Deixe apiVersion definido como o valor padrão.

## <a name="nodes-on-the-cluster"></a>Nós no cluster
Você pode configurar os nós no Cluster Service Fabric usando a seção nós, como mostra o trecho a seguir:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Um Cluster Service Fabric deve conter pelo menos três nós. Você pode adicionar mais nós a esta seção de acordo com sua configuração. A tabela a seguir explica as definições de configuração para cada nó:

| **Configuração de nó** | **Descrição** |
| --- | --- |
| nodeName |Você pode fornecer qualquer nome amigável para o nó. |
| IP |Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Anote o endereço IPV4 e atribua-o à variável iPAddress. |
| nodeTypeRef |Cada nó pode ser atribuído a um tipo de nó diferente. Os [tipos de nó](#node-types) são definidos na seção a seguir. |
| faultDomain |Os domínios de falha permitem que os administradores de cluster definam os nós físicos que podem falhar ao mesmo tempo devido a dependências físicas compartilhadas. |
| upgradeDomain |Os domínios de atualização descrevem conjuntos de nós que são desligados para Service Fabric atualizações ao mesmo tempo. Você pode escolher quais nós atribuir a quais domínios de atualização, porque eles não são limitados por quaisquer requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades do cluster
A seção de propriedades no ClusterConfig. JSON é usada para configurar o cluster, conforme mostrado:

### <a name="reliability"></a>Fiabilidade
O conceito de reliabilityLevel define o número de réplicas ou instâncias do Service Fabric serviços do sistema que podem ser executados nos nós primários do cluster. Ele determina a confiabilidade desses serviços e, portanto, o cluster. O valor é calculado pelo sistema durante a criação do cluster e o tempo de atualização.

### <a name="diagnostics"></a>Diagnósticos
Na seção diagnosticsStore, você pode configurar parâmetros para habilitar o diagnóstico e solucionar problemas de falhas de nó ou cluster, conforme mostrado no trecho a seguir: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Os metadados são uma descrição do seu diagnóstico de cluster e podem ser definidos de acordo com a sua configuração. Essas variáveis ajudam a coletar logs de rastreamento ETW e despejos de memória, bem como contadores de desempenho. Para obter mais informações sobre os logs de rastreamento do ETW, consulte rastreamento de [tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [ETW](https://msdn.microsoft.com/library/ms751538.aspx). Todos os logs, incluindo [despejos](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) de memória e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), podem ser direcionados para a pasta ConnectionString em seu computador. Você também pode usar o AzureStorage para armazenar diagnósticos. Consulte o seguinte trecho de código de exemplo:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Segurança
A seção segurança é necessária para um Cluster Service Fabric autônomo seguro. O trecho a seguir mostra uma parte desta seção:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Os metadados são uma descrição do seu cluster seguro e podem ser definidos de acordo com a sua configuração. O ClusterCredentialType e o ServerCredentialType determinam o tipo de segurança que o cluster e os nós implementam. Eles podem ser definidos como *X509* para uma segurança baseada em certificado ou *Windows* para segurança baseada em Active Directory. O restante da seção de segurança baseia-se no tipo de segurança. Para obter informações sobre como preencher o restante da seção de segurança, consulte [segurança baseada em certificados em um cluster autônomo](service-fabric-windows-cluster-x509-security.md) ou [segurança do Windows em um cluster autônomo](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Tipos de nó
A seção nodeTypes descreve o tipo de nós que seu cluster tem. Pelo menos um tipo de nó deve ser especificado para um cluster, conforme mostrado no trecho a seguir: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

O nome é o nome amigável para esse tipo de nó específico. Para criar um nó desse tipo de nó, atribua seu nome amigável à variável nodeTypeRef para esse nó, conforme [mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos de extremidade de conexão que são usados. Você pode escolher qualquer número de porta para esses pontos de extremidade de conexão, desde que eles não entrem em conflito com nenhum outro ponto de extremidade nesse cluster. Em um cluster de vários nós, há um ou mais nós primários (ou seja, IsPrimary é definido como *true*), dependendo do [reliabilityLevel](#reliability). Para saber mais sobre os tipos de nó primário e não primário, consulte [Service Fabric considerações de planejamento de capacidade de cluster](service-fabric-cluster-capacity.md) para obter informações sobre NodeTypes e reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos de extremidade usados para configurar os tipos de nó
* clientConnectionEndpointPort é a porta usada pelo cliente para se conectar ao cluster quando APIs de cliente são usadas. 
* clusterConnectionEndpointPort é a porta na qual os nós se comunicam entre si.
* leaseDriverEndpointPort é a porta usada pelo driver de concessão de cluster para descobrir se os nós ainda estão ativos. 
* serviceConnectionEndpointPort é a porta usada pelos aplicativos e serviços implantados em um nó para se comunicar com o cliente do Service Fabric nesse nó específico.
* httpGatewayEndpointPort é a porta usada pelo Service Fabric Explorer para se conectar ao cluster.
* ephemeralPorts substituir as [portas dinâmicas usadas pelo sistema operacional](https://support.microsoft.com/kb/929851). Service Fabric usa uma parte dessas portas como portas de aplicativo e as restantes estão disponíveis para o sistema operacional. Ele também mapeia esse intervalo para o intervalo existente presente no sistema operacional, portanto, para todas as finalidades, você pode usar os intervalos fornecidos nos arquivos JSON de exemplo. Certifique-se de que a diferença entre as portas inicial e final seja pelo menos 255. Você poderá se deparar com conflitos se essa diferença for muito baixa, pois esse intervalo é compartilhado com o sistema operacional. Para ver o intervalo de portas dinâmicas configurado, execute `netsh int ipv4 show dynamicport tcp`.
* applicationPorts são as portas usadas pelos aplicativos Service Fabric. O intervalo de portas do aplicativo deve ser grande o suficiente para cobrir o requisito de ponto de extremidade de seus aplicativos. Esse intervalo deve ser exclusivo do intervalo de portas dinâmicas no computador, ou seja, o intervalo ephemeralPorts conforme definido na configuração. O Service Fabric usa essas portas sempre que novas portas são necessárias e cuida da abertura do firewall para essas portas. 
* reverseProxyEndpointPort é um ponto de extremidade de proxy reverso opcional. Para obter mais informações, consulte [Service Fabric proxy reverso](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Configurações de log
Na seção fabricSettings, você pode definir os diretórios raiz para os Service Fabric dados e logs. Você pode personalizar esses diretórios somente durante a criação do cluster inicial. Consulte o seguinte trecho de código de exemplo desta seção:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Recomendamos que você use uma unidade que não seja do sistema operacional como FabricDataRoot e FabricLogRoot. Ele fornece mais confiabilidade ao evitar situações em que o sistema operacional para de responder. Se você personalizar apenas a raiz de dados, a raiz de log será colocada um nível abaixo da raiz de dados.

### <a name="stateful-reliable-services-settings"></a>Configurações de Reliable Services com estado
Na seção KtlLogger, você pode definir as definições de configuração global para Reliable Services. Para obter mais informações sobre essas configurações, consulte [configurar Reliable Services com estado](service-fabric-reliable-services-configuration.md). O exemplo a seguir mostra como alterar o log de transações compartilhado que é criado para fazer backup de coleções confiáveis para serviços com estado:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Recursos complementares
Para configurar recursos complementares, configure o apiVersion como 04-2017 ou superior e configure o addonFeatures, conforme mostrado aqui:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Todos os recursos complementares disponíveis podem ser vistos na [referência da API REST do Service Fabric](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Suporte de contentor
Para habilitar o suporte de contêiner para contêineres do Windows Server e do Hyper-V para clusters autônomos, o recurso de complemento DnsService deve estar habilitado.

## <a name="next-steps"></a>Passos seguintes
Depois de ter um arquivo *ClusterConfig. JSON* completo configurado de acordo com a configuração do cluster autônomo, você pode implantar o cluster. Siga as etapas em [criar um cluster Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md). 

Se você tiver um cluster autônomo implantado, também poderá [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Saiba como [Visualizar seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

