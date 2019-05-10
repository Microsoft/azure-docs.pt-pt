---
title: Configurar o seu cluster autónomo de Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar o seu cluster do Azure Service Fabric autónomo ou no local.
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
ms.openlocfilehash: ae7fbef864634e47866de13384871a98b8ce4675
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209711"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster autónomo do Windows
Este artigo descreve as definições de configuração de um cluster autónomo de Azure Service Fabric que podem ser definidos na *ClusterConfig.json* ficheiro. Irá utilizar este ficheiro para especificar as informações sobre nós do cluster, configurações de segurança, bem como a topologia de rede em termos de domínios de falha e atualização.  Depois de alterar ou adicionar as definições de configuração, é possível [criar um cluster autónomo](service-fabric-cluster-creation-for-windows-server.md) ou [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Quando [transferir o pacote de recursos de infraestrutura do serviço autónomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), exemplos de ClusterConfig.json também estão incluídos. Os exemplos que tenham "DevCluster" nos respetivos nomes criar um cluster com todos os três nós no mesmo computador, utilizando nós lógicos. Partir de nós, pelo menos um deve ser marcado como um nó principal. Este tipo de cluster é útil para ambientes de desenvolvimento ou teste. Não é suportado como um cluster de produção. Os exemplos que tenham "MultiMachine" nos respetivos nomes ajudam a criar clusters de nível de produção com cada nó numa máquina separada. O número de nós principais para estes clusters baseia-se do cluster [nível de fiabilidade](#reliability). Na versão 5.7, versão de API 05-2017, removemos a propriedade de nível de fiabilidade. Em vez disso, o nosso código calcula o nível de fiabilidade mais otimizado para o seu cluster. Não tente definir um valor para esta propriedade em versões 5.7 ou posterior.

* ClusterConfig.Unsecure.DevCluster.json e ClusterConfig.Unsecure.MultiMachine.json mostram como criar um cluster de produção ou teste de não segura, respetivamente.

* ClusterConfig.Windows.DevCluster.json e ClusterConfig.Windows.MultiMachine.json mostram como criar clusters de produção ou teste que estão protegidos utilizando [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json e ClusterConfig.X509.MultiMachine.json mostram como criar clusters de produção ou teste que estão protegidos utilizando [X509 segurança baseada em certificado](service-fabric-windows-cluster-x509-security.md).

Agora vamos examinar as diversas secções de um ficheiro de ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Configurações de cluster geral
Configurações de cluster geral abrangem as configurações específicas do cluster amplo, conforme mostrado no fragmento JSON seguinte:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Pode permitir que qualquer nome amigável ao cluster do Service Fabric ao atribuí-lo para a variável de nome. O clusterConfigurationVersion é o número de versão do cluster. Aumentá-la sempre que atualizar o cluster do Service Fabric. Deixe o conjunto de apiVersion para o valor predefinido.

## <a name="nodes-on-the-cluster"></a>Nós no cluster
Pode configurar os nós no cluster do Service Fabric, utilizando a secção de nós, como o fragmento seguinte mostra:
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

Um cluster do Service Fabric tem de conter, pelo menos, três nós. Pode adicionar mais nós a esta secção, de acordo com sua configuração. A tabela seguinte explica as definições de configuração para cada nó:

| **Configuração do nó** | **Descrição** |
| --- | --- |
| nodeName |Pode permitir que qualquer nome amigável para o nó. |
| iPAddress |Descubra o endereço IP do seu nó ao abrir uma janela de comando e digitar `ipconfig`. Tenha em atenção o endereço IPV4 e atribui-o à variável iPAddress. |
| nodeTypeRef |Cada nó pode ser atribuído um tipo de nó diferente. O [tipos de nó](#node-types) são definidos na secção seguinte. |
| faultDomain |Domínios de falha permitem que os administradores de cluster definir os nós físicos que poderão falhar ao mesmo tempo devido a dependências de físicas partilhadas. |
| upgradeDomain |Domínios de atualização descrevem os conjuntos de nós que são encerrados para atualizações de Service Fabric no mesmo tempo. Pode escolher quais os nós para atribuir a quais os domínios de atualização, porque eles não estão limitados por quaisquer requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades do cluster
A secção de propriedades a ClusterConfig.json é utilizada para configurar o cluster, conforme mostrado:

### <a name="reliability"></a>Fiabilidade
O conceito de reliabilityLevel define o número de réplicas ou instâncias dos serviços de sistema do Service Fabric que podem ser executadas em nós do principal do cluster. Determina a confiabilidade desses serviços e, por conseguinte, o cluster. O valor é calculado pelo sistema no momento de criação e atualização do cluster.

### <a name="diagnostics"></a>Diagnóstico
Na secção diagnosticsStore, pode configurar parâmetros para ativar o diagnóstico e resolução de problemas de falhas de nó ou cluster, conforme mostrado no seguinte fragmento: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Os metadados é uma descrição de diagnóstico do cluster e podem ser definido de acordo com sua configuração. Estas variáveis de ajudar a recolher registos de rastreio ETW e nos despejos de memória, bem como contadores de desempenho. Para obter mais informações sobre os registos de rastreio ETW, consulte [registo de rastreio](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [rastreamento ETW](https://msdn.microsoft.com/library/ms751538.aspx). Todos os registos, incluindo [despejos de memória](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), pode ser direcionado para a pasta de connectionString no seu computador. Também pode utilizar AzureStorage para armazenar o diagnóstico. Consulte o seguinte fragmento de exemplo:

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
A secção de segurança é necessária para um cluster do Service Fabric seguro autónomo. O fragmento seguinte mostra uma parte desta secção:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Os metadados é uma descrição do seu cluster seguro e podem ser definido de acordo com sua configuração. O ClusterCredentialType e ServerCredentialType determinam o tipo de segurança que implementam o cluster e os nós. Eles podem ser definidos para o *X509* para uma segurança baseada em certificado ou *Windows* para segurança baseada no Active Directory. O restante da secção de segurança baseia-se no tipo de segurança. Para obter informações sobre como preencher o resto da secção de segurança, consulte [segurança baseada em certificados num cluster autónomo](service-fabric-windows-cluster-x509-security.md) ou [segurança do Windows num cluster autónomo](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Tipos de nó
A secção de nodeTypes descreve o tipo de nós que tem o seu cluster. Pelo menos um tipo de nó tem de ser especificado para um cluster, conforme mostrado no seguinte fragmento: 

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

O nome é o nome amigável para este tipo de nó específico. Para criar um nó deste tipo de nó, atribuir o nome amigável para a variável o nodeTypeRef para esse nó, como [mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos de extremidade de conexão que são utilizados. Pode escolher qualquer número de porta para estes pontos finais de ligação, desde que não entram em conflito com quaisquer outros pontos de extremidade neste cluster. Num cluster de vários nós, há um ou mais nós primários (ou seja, isPrimary está definido como *true*), consoante o [reliabilityLevel](#reliability). Para saber mais sobre os tipos de nó primário e não primárias, veja [considerações de planeamento de capacidade do cluster de Service Fabric](service-fabric-cluster-capacity.md) para obter informações sobre os reliabilityLevel e os nodeTypes. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos de extremidade utilizados para configurar os tipos de nó
* clientConnectionEndpointPort é a porta utilizada pelo cliente para ligar ao cluster quando são utilizadas APIs de cliente. 
* clusterConnectionEndpointPort é a porta em que os nós comunicam entre si.
* leaseDriverEndpointPort é a porta utilizada pelo controlador de concessão de cluster para saber se os nós ainda se encontrem ativos. 
* serviceConnectionEndpointPort é a porta utilizada pelas aplicações e serviços implementados num nó para comunicar com o cliente do Service Fabric nesse nó específico.
* httpGatewayEndpointPort é a porta usada pelo Service Fabric Explorer para ligar ao cluster.
* ephemeralPorts substituir a [portas dinâmicas utilizadas pelo sistema operacional](https://support.microsoft.com/kb/929851). O Service Fabric utiliza estas portas parte como portas da aplicação e os restantes estão disponíveis para o sistema operacional. Também mapeia neste intervalo para o intervalo de existente presente no sistema operacional, portanto, para todas as finalidades, pode usar os intervalos de dado dos ficheiros JSON de exemplo. Certifique-se de que a diferença entre o início e as portas de end é, pelo menos, 255. Podem ocorrer conflitos se essa diferença é demasiado baixa, porque este intervalo é partilhado com o sistema operacional. Para ver o intervalo de porta dinâmica configurada, execute `netsh int ipv4 show dynamicport tcp`.
* applicationPorts são as portas que são utilizadas pelas aplicações do Service Fabric. O intervalo de portas do aplicativo deve ser suficientemente grande para cobrir o requisito de ponto final de seus aplicativos. Este intervalo deve ser exclusivo a partir do intervalo de porta dinâmica na máquina, ou seja, o intervalo de ephemeralPorts conforme definido na configuração. Service Fabric utiliza estas portas, sempre que as novas portas são necessárias e se encarrega de abrir a firewall para estas portas. 
* reverseProxyEndpointPort é um ponto de extremidade do proxy inverso opcional. Para obter mais informações, consulte [proxy inverso do Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Definições de registo
Na secção fabricSettings, pode definir os diretórios de raiz para os registos e dados do Service Fabric. Pode personalizar estes diretórios apenas durante a criação de cluster inicial. Consulte o seguinte fragmento de exemplo desta secção:

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

Recomendamos que utilize uma unidade de não-OS como a FabricDataRoot e FabricLogRoot. Ele fornece mais confiabilidade evitar situações quando o sistema operacional para de responder. Se personalizar só a raiz de dados, a raiz do registo é colocada um nível abaixo da raiz de dados.

### <a name="stateful-reliable-services-settings"></a>Definições de Reliable Services com monitorização de estado
Na secção KtlLogger, pode definir as definições de configuração global para Reliable Services. Para obter mais informações sobre estas definições, consulte [configurar Stateful Reliable Services](service-fabric-reliable-services-configuration.md). O exemplo seguinte mostra como alterar o registo de transações partilhado que é criado para fazer uma cópia de todas as coleções fiáveis para serviços com estado:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funcionalidades de suplemento
Para configurar funcionalidades de suplemento, configurar a apiVersion como 04 2017 ou superior e configure o addonFeatures, conforme mostrado aqui:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Suporte de contentor
Para ativar o suporte do contentor para contentores do Windows Server e contentores de Hyper-V para clusters autónomos, tem de estar ativada a funcionalidade de suplemento DnsService.

## <a name="next-steps"></a>Passos Seguintes
Depois de ter uma completa *ClusterConfig.json* ficheiro configurado de acordo com a configuração de cluster autónomo, pode implementar o cluster. Siga os passos em [criar um cluster do Service Fabric autónomo](service-fabric-cluster-creation-for-windows-server.md). 

Se tiver um cluster autónomo implementado, pode também [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Saiba como [visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

