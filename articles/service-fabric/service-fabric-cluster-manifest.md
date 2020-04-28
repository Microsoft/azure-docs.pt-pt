---
title: Configure o seu cluster autónomo de tecido de serviço Azure
description: Saiba como configurar o seu cluster de tecido de serviço Azure autónomo ou no local.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75458371"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster Windows autónomo
Este artigo descreve as configurações de configuração de um cluster de tecido de serviço Azure autónomo que pode ser definido no ficheiro *ClusterConfig.json.* Utilizará este ficheiro para especificar informações sobre os nós do cluster, configurações de segurança, bem como a topologia da rede em termos de domínios de falha e atualização.  Depois de alterar ou adicionar configurações de configuração, pode [criar um cluster autónomo](service-fabric-cluster-creation-for-windows-server.md) ou atualizar a [configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Ao [descarregar o pacote de tecido](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)de serviço autónomo, as amostras clusterConfig.json também estão incluídas. As amostras que têm "DevCluster" em seus nomes criam um cluster com os três nós na mesma máquina, usando nós lógicos. Destes nós, pelo menos um deve ser marcado como um nó primário. Este tipo de cluster é útil para ambientes de desenvolvimento ou teste. Não é suportado como um cluster de produção. As amostras que têm "MultiMachine" nos seus nomes ajudam a criar clusters de grau de produção, com cada nó numa máquina separada. O número de nós primários para estes clusters baseia-se no nível de [fiabilidade](#reliability)do cluster . No lançamento 5.7, API Version 05-2017, removemos a propriedade de nível de fiabilidade. Em vez disso, o nosso código calcula o nível de fiabilidade mais otimizado para o seu cluster. Não tente definir um valor para este imóvel nas versões 5.7.

* ClusterConfig.Unsecure.DevCluster.json e ClusterConfig.Unsecure.MultiMachine.json mostram como criar um cluster de teste ou de produção não seguro, respectivamente.

* ClusterConfig.Windows.DevCluster.json e ClusterConfig.Windows.MultiMachine.json mostram como criar clusters de teste ou de produção que são protegidos utilizando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json e ClusterConfig.X509.MultiMachine.json mostram como criar clusters de teste ou de produção que são protegidos utilizando [a segurança baseada em certificados X509](service-fabric-windows-cluster-x509-security.md).

Agora vamos examinar as várias secções de um ficheiro ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Configurações gerais de cluster
As configurações gerais do cluster cobrem as configurações amplas específicas do cluster, como mostra o seguinte corte JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Pode dar qualquer nome amigável ao seu cluster De Tecido de Serviço atribuindo-o à variável de nome. O clusterConfigurationVersion é o número de versão do seu cluster. Aumente-o sempre que atualizar o seu cluster de Tecido de Serviço. Deixe a apiVersão definida ao valor padrão.

## <a name="nodes-on-the-cluster"></a>Nós no aglomerado
Pode configurar os nós do seu cluster De Tecido de Serviço utilizando a secção de nós, como mostra o seguinte corte:
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

Um cluster de tecido de serviço deve conter pelo menos três nós. Pode adicionar mais nós a esta secção de acordo com a sua configuração. A tabela que se segue explica as definições de configuração para cada nó:

| **Configuração do nó** | **Descrição** |
| --- | --- |
| nome do nó |Pode dar qualquer nome amigável ao nó. |
| iPAddress |Descubra o endereço IP do seu nó abrindo `ipconfig`uma janela de comando e digitando . Note o endereço IPV4 e atribua-o à variável iPAddress. |
| nóTypeRef |Cada nó pode ser atribuído um tipo diferente de nó. Os [tipos de nó](#node-types) são definidos na seguinte secção. |
| faultDomínio |Os domínios de falha permitem que os administradores do cluster definam os nós físicos que podem falhar ao mesmo tempo devido a dependências físicas partilhadas. |
| upgradeDomínio |Os domínios de atualização descrevem conjuntos de nódosos que são desligados para upgrades de Tecido de Serviço ao mesmo tempo. Pode escolher quais nós atribuir a que domínios de upgrade, porque não são limitados por quaisquer requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades de cluster
A secção de propriedades no ClusterConfig.json é usada para configurar o cluster como mostrado:

### <a name="reliability"></a>Fiabilidade
O conceito de fiabilidadeLevel define o número de réplicas ou instâncias dos serviços do sistema Service Fabric que podem funcionar nos nós primários do cluster. Determina a fiabilidade destes serviços e, consequentemente, do cluster. O valor é calculado pelo sistema no tempo de criação e atualização do cluster.

### <a name="diagnostics"></a>Diagnóstico
Na secção de diagnósticoSLoja, pode configurar parâmetros para permitir diagnósticos e falhas no nó de resolução de problemas ou falhas de cluster, como mostra o seguinte corte: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Os metadados são uma descrição dos diagnósticos do seu cluster e podem ser definidos de acordo com a sua configuração. Estas variáveis ajudam na recolha de registos de rastreio seletivas e depósitos de colisões, bem como contadores de desempenho. Para obter mais informações sobre os registos de rastreio seleções da ETW, consulte [tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [rastreio eTW](https://msdn.microsoft.com/library/ms751538.aspx). Todos os registos, incluindo depósitos de [colisão](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e contadores de [desempenho,](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)podem ser direcionados para a pasta de ligaçãoString na sua máquina. Também pode utilizar o AzureStorage para armazenar diagnósticos. Consulte o seguinte corte de amostra:

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
A secção de segurança é necessária para um cluster de tecido de serviço autónomo seguro. O seguinte corte mostra uma parte desta secção:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Os metadados são uma descrição do seu cluster seguro e podem ser definidos de acordo com a sua configuração. O ClusterCredentialType e serverCredentialType determinam o tipo de segurança que o cluster e os nós implementam. Podem ser definidos para *X509* para uma segurança baseada em certificados ou *Windows* para segurança baseada em Diretório Ativo. O resto da secção de segurança baseia-se no tipo de segurança. Para obter informações sobre como preencher o resto da secção de segurança, consulte a [segurança baseada em certificados num cluster autónomo](service-fabric-windows-cluster-x509-security.md) ou segurança do Windows num cluster [autónomo](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Tipos de nó
A secção nótypes descreve o tipo de nós que o seu cluster tem. Deve ser especificado pelo menos um tipo de nó para um cluster, tal como demonstrado no seguinte corte: 

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

O nome é o nome amigável para este tipo de nó em particular. Para criar um nó deste tipo de nó, atribua o seu nome amigável à variável nóTypeRef para esse nó, como [mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos finais de ligação utilizados. Pode escolher qualquer número de porta para estes pontos finais de ligação, desde que não entrem em conflito com outros pontos finais deste cluster. Num cluster multinódo, existem um ou mais nós primários (isto é, é a Primary está definida para *ser verdade),* dependendo do nível de [fiabilidade](#reliability). Para saber mais sobre os tipos de nó primário e não primário, consulte [considerações](service-fabric-cluster-capacity.md) de planeamento de capacidade de cluster de tecido de serviço para informações sobre nósTipos e fiabilidadeLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos finais usados para configurar os tipos de nó
* clienteConnectionEndpointPort é a porta utilizada pelo cliente para se ligar ao cluster quando são utilizadas APIs do cliente. 
* clusterConnectionEndpointPort é a porta onde os nós comunicam entre si.
* leaseDriverEndpointPort é a porta utilizada pelo controlador de locação de cluster para saber se os nós ainda estão ativos. 
* serviçoConnectionEndpointPort é a porta utilizada pelas aplicações e serviços implantados num nó para comunicar com o cliente do Tecido de Serviço nesse nó em particular.
* httpGatewayEndpointPort é a porta utilizada pelo Service Fabric Explorer para se ligar ao cluster.
* ephemeralPortos sobrepõem-se às [portas dinâmicas utilizadas pelo S](https://support.microsoft.com/kb/929851). O Serviço Fabric utiliza uma parte destas portas como portas de aplicação, e os restantes estão disponíveis para o SISTEMA. Também mapeia esta gama para a gama existente presente no S, para que, para todos os efeitos, possa utilizar as gamas dadas nos ficheiros JSON da amostra. Certifique-se de que a diferença entre o início e as portas finais é de pelo menos 255. Poderá entrar em conflitos se esta diferença for demasiado baixa, porque esta gama é partilhada com o SISTEMA. Para ver a gama de `netsh int ipv4 show dynamicport tcp`portas dinâmica configurada, corra .
* aplicaçõesAs Portas são as portas que são utilizadas pelas aplicações service Fabric. A gama de portas de aplicação deve ser suficientemente grande para cobrir a exigência de ponto final das suas aplicações. Esta gama deve ser exclusiva da gama de portas dinâmica na máquina, ou seja, a gama efémera Ports tal como definida na configuração. Serviço O tecido utiliza estas portas sempre que são necessárias novas portas e cuida de abrir a firewall para estas portas. 
* reverseProxyEndpointPort é um ponto final de procuração inversa opcional. Para mais informações, consulte [Service Fabric reverse proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Definições de registo
Na secção Definições de tecido, pode definir os diretórios de raiz para os dados e registos do Tecido de Serviço. Só é possível personalizar estes diretórios durante a criação inicial do cluster. Consulte os seguintes excertos desta secção:

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

Recomendamos que utilize uma unidade não-S como FabricDataRoot e FabricLogRoot. Proporciona mais fiabilidade na prevenção de situações quando o SO deixa de responder. Se personalizar apenas a raiz de dados, a raiz de registo é colocada um nível abaixo da raiz de dados.

### <a name="stateful-reliable-services-settings"></a>Definições de Serviços Fiáveis e estatais
Na secção KtlLogger, pode definir as definições globais de configuração para Serviços Fiáveis. Para obter mais informações sobre estas definições, consulte [Configure Stateful Reliable Services](service-fabric-reliable-services-configuration.md). O exemplo que se segue mostra como alterar o registo de transações partilhadas que é criado para apoiar quaisquer coleções fiáveis para serviços estatais:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funcionalidades adicionais
Para configurar as funcionalidades adicionais, configure a apiVersão como 04-2017 ou superior, e configure as funcionalidades de addonRecursos como mostrado aqui:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Todas as funcionalidades adicionais disponíveis podem ser vistas na [referência a API](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures)do Tecido de Serviço .

### <a name="container-support"></a>Suporte de contentor
Para ativar o suporte do recipiente tanto para os contentores do Windows Server como para os contentores Hyper-V para clusters autónomos, a funcionalidade de adição DnsService deve ser ativada.

## <a name="next-steps"></a>Passos seguintes
Depois de ter um ficheiro *ClusterConfig.json* completo configurado de acordo com a configuração autónoma do cluster, pode implantar o seu cluster. Siga os passos em [Criar um cluster de tecido](service-fabric-cluster-creation-for-windows-server.md)de serviço autónomo . 

Se tiver um cluster autónomo implantado, também pode [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Aprenda a [visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

