---
title: Configure o seu cluster autónomo de tecido de serviço Azure
description: Saiba como configurar o seu cluster de tecido de serviço autónomo ou autónomo.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843163"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster de Windows autónomo
Este artigo descreve as definições de configuração de um cluster autónomo de tecido de serviço Azure que pode ser definido no *ClusterConfig.jsno* ficheiro. Utilizará este ficheiro para especificar informações sobre os nós do cluster, configurações de segurança, bem como a topologia da rede em termos de domínios de falha e de upgrade.  Depois de alterar ou adicionar configurações de configuração, pode [criar um cluster autónomo](service-fabric-cluster-creation-for-windows-server.md) ou atualizar a [configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Quando [descarrega o pacote Self Fabric autónomo,](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)também estão incluídos ClusterConfig.jsem amostras. As amostras que têm "DevCluster" nos seus nomes criam um aglomerado com os três nós na mesma máquina, usando nós lógicos. Destes nós, pelo menos um deve ser marcado como um nó primário. Este tipo de aglomerado é útil para ambientes de desenvolvimento ou teste. Não é apoiado como um cluster de produção. As amostras que têm "MultiMachine" nos seus nomes ajudam a criar clusters de nível de produção, com cada nó numa máquina separada. O número de nós primários para estes clusters baseia-se no nível de [fiabilidade](#reliability)do cluster. No lançamento 5.7, Versão API 05-2017, removemos a propriedade de nível de fiabilidade. Em vez disso, o nosso código calcula o nível de fiabilidade mais otimizado para o seu cluster. Não tente definir um valor para este imóvel nas versões 5.7 em diante.

* ClusterConfig.Unsecure.DevCluster.jse ClusterConfig.Unsecure.MultiMachine.jsmostrar como criar um cluster de teste ou de produção não seguro, respectivamente.

* ClusterConfig.Windows.DevCluster.jse ClusterConfig.Windows.MultiMachine.jsem mostrar como criar clusters de teste ou de produção que são protegidos através da [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.jse ClusterConfig.X509.MultiMachine.jsmostrar como criar clusters de teste ou de produção que são garantidos através da utilização de [segurança baseada em certificados X509](service-fabric-windows-cluster-x509-security.md).

Agora vamos examinar as várias secções de um ClusterConfig.jsarquivado.

## <a name="general-cluster-configurations"></a>Configurações gerais de cluster
As configurações gerais do cluster cobrem as configurações específicas do cluster, como mostrado no seguinte corte JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Pode dar qualquer nome amigável ao seu cluster de Tecido de Serviço atribuindo-o à variável nome. O clusterConfigurationVersion é o número de versão do seu cluster. Aumente-o sempre que atualizar o seu cluster de Tecido de Serviço. Deixe a apiversionação definida para o valor predefinido.

## <a name="nodes-on-the-cluster"></a>Nódes no aglomerado
Pode configurar os nóns no seu cluster de Tecido de Serviço utilizando a secção de nó, como mostra o seguinte corte:
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

Um conjunto de tecido de serviço deve conter pelo menos três nós. Pode adicionar mais nós a esta secção de acordo com a sua configuração. A tabela seguinte explica as definições de configuração para cada nó:

| **Configuração do nó** | **Descrição** |
| --- | --- |
| nome de nó |Pode dar qualquer nome amigável ao nó. |
| iPAddress |Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig` . Observe o endereço IPV4 e atribua-o à variável iPAddress. |
| nodeTypeRef |Cada nó pode ser atribuído a um tipo diferente de nó. Os [tipos de nó](#node-types) são definidos na secção seguinte. |
| faultDomain |Os domínios de avaria permitem aos administradores de cluster definir os nós físicos que podem falhar ao mesmo tempo devido às dependências físicas partilhadas. |
| upgradeDomain |Os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações de Tecido de Serviço ao mesmo tempo. Pode escolher quais os nós a atribuir aos domínios de upgrade, porque não são limitados por quaisquer requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades de cluster
A secção de propriedades no ClusterConfig.jsé usada para configurar o cluster como mostrado:

### <a name="reliability"></a>Fiabilidade
O conceito de fiabilidadeLevel define o número de réplicas ou instâncias dos serviços do sistema Service Fabric que podem funcionar nos nós primários do cluster. Determina a fiabilidade destes serviços e, consequentemente, o cluster. O valor é calculado pelo sistema no tempo de criação e atualização do cluster.

### <a name="diagnostics"></a>Diagnóstico
Na secção de loja de diagnóstico, pode configurar parâmetros para permitir diagnósticos e falhas de resolução de problemas no nó ou aglomerados, como mostra o seguinte corte: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Os metadados são uma descrição dos seus diagnósticos de cluster e podem ser definidos de acordo com a sua configuração. Estas variáveis ajudam na recolha de registos de vestígios etw e depósitos de colisão, bem como contadores de desempenho. Para obter mais informações sobre os registos de vestígios da ETW, consulte [o rastreio de Tracelog](/windows-hardware/drivers/devtest/tracelog) e [ETW](/dotnet/framework/wcf/samples/etw-tracing). Todos os registos, incluindo [depósitos de falhas](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) e [contadores de desempenho,](/windows/win32/perfctrs/performance-counters-portal)podem ser direcionados para a pasta de ligaçãoStragem na sua máquina. Também pode utilizar o AzureStorage para armazenar diagnósticos. Consulte o seguinte corte de amostra:

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

Os metadados são uma descrição do seu cluster seguro e podem ser definidos de acordo com a sua configuração. O ClusterCredentialType e o ServerCredentialType determinam o tipo de segurança que o cluster e os nós implementam. Podem ser configuradas para *X509* para uma segurança baseada em *certificados* ou windows para segurança baseada em diretório ativo. O resto da secção de segurança baseia-se no tipo de segurança. Para obter informações sobre como preencher o resto da secção de segurança, consulte a [segurança baseada em Certificados num cluster autónomo](service-fabric-windows-cluster-x509-security.md) ou segurança do Windows num cluster [autónomo](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Tipos de nó
A secção de nóTypes descreve o tipo de nós que o seu cluster tem. Deve ser especificado, pelo menos, um tipo de nó para um cluster, tal como indicado no seguinte corte: 

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

O nome é o nome amigável para este tipo de nó particular. Para criar um nó deste tipo de nó, atribua o seu nome amigável à variável nóTypeRef para esse nó, como [mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos finais de ligação utilizados. Pode escolher qualquer número de porta para estes pontos finais de ligação, desde que não entrem em conflito com outros pontos finais neste cluster. Num cluster multinódeo, existem um ou mais nós primários (isto é, o "Primary" é definido como *verdadeiro,* dependendo da [fiabilidadeLevel](#reliability). Para saber mais sobre os tipos de nó primários e não [primários,](service-fabric-cluster-capacity.md) consulte considerações de planeamento de capacidade de cluster de tecido de serviço para obter informações sobre os nótypes e fiabilidadeLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos finais utilizados para configurar os tipos de nó
* clientConnectionEndpointPort é a porta utilizada pelo cliente para se conectar ao cluster quando as APIs do cliente são utilizadas. 
* clusterConnectionEndpointPort é a porta onde os nós comunicam entre si.
* leaseDriverEndpointPort é a porta utilizada pelo controlador de aluguer de cluster para saber se os nós ainda estão ativos. 
* serviçoConnectionEndpointPort é a porta utilizada pelas aplicações e serviços implantados num nó para comunicar com o cliente do Service Fabric nesse nó particular.
* httpGatewayEndpointPort é a porta utilizada pelo Service Fabric Explorer para se ligar ao cluster.
* EfeméridesPorts sobrepõem-se às [portas dinâmicas utilizadas pelo SISTEMA.](https://support.microsoft.com/kb/929851) O Service Fabric utiliza uma parte destas portas como portas de aplicação, e os restantes estão disponíveis para o SO. Também mapeia esta gama para a gama existente presente no SO, para todos os efeitos, você pode usar as gamas dadas nos ficheiros JSON da amostra. Certifique-se de que a diferença entre as portas de arranque e de extremidade é de, pelo menos, 255. Podes desatenção de conflitos se esta diferença for demasiado baixa, porque esta gama é partilhada com o SISTEMA. Para ver a gama dinâmica de portas configurada, corra `netsh int ipv4 show dynamicport tcp` .
* aplicaçõesPorts são as portas que são utilizadas pelas aplicações Service Fabric. A gama de portas de aplicação deve ser suficientemente grande para cobrir os requisitos do ponto final das suas aplicações. Esta gama deve ser exclusiva da gama de portas dinâmicas da máquina, ou seja, a gama efémera DePorts como definida na configuração. O Tecido de Serviço utiliza estas portas sempre que são necessárias novas portas e cuida de abrir a firewall para estas portas. 
* reverseProxyEndpointPort é um ponto final opcional de procuração inversa. Para obter mais informações, consulte [o proxy reverso do Service Fabric.](service-fabric-reverseproxy.md) 

### <a name="log-settings"></a>Definições de registo
Na secção fabricSettings, pode definir os diretórios de raiz para os dados e registos do Tecido de Serviço. Você pode personalizar estes diretórios apenas durante a criação inicial do cluster. Consulte o seguinte corte de amostra desta secção:

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

Recomendamos que utilize uma unidade não-OS como FabricDataRoot e FabricLogRoot. Proporciona mais fiabilidade na evitar situações em que o SO deixa de responder. Se personalizar apenas a raiz de dados, a raiz de registo é colocada um nível abaixo da raiz de dados.

### <a name="stateful-reliable-services-settings"></a>Definições de serviços fiáveis estatais
Na secção KtlLogger, pode definir as definições de configuração global para Serviços Fiáveis. Para obter mais informações sobre estas definições, consulte [Configure Stateful Reliable Services](service-fabric-reliable-services-configuration.md). O exemplo a seguir mostra como alterar o registo de transações partilhado que é criado para apoiar quaisquer cobranças fiáveis para serviços estatais:

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
Para configurar as funcionalidades adicionais, configurar a apiVersão como 04-2017 ou superior, e configurar as addonFeatures como mostrado aqui:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Todas as funcionalidades adicionais disponíveis podem ser vistas na [Referência API do Tecido de Serviço.](/rest/api/servicefabric/sfrp-model-addonfeatures)

### <a name="container-support"></a>Suporte de contentor
Para permitir o suporte de contentores tanto para recipientes windows server como para recipientes Hiper-V para aglomerados autónomos, a função de complemento dnsService deve ser ativada.

## <a name="next-steps"></a>Passos seguintes
Depois de ter umaClusterConfig.jscompleta * no* ficheiro configurada de acordo com a configuração do seu cluster autónomo, pode implantar o seu cluster. Siga os passos em [Criar um cluster de tecido de serviço autónomo.](service-fabric-cluster-creation-for-windows-server.md) 

Se tiver um cluster autónomo implantado, também pode [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Saiba como [visualizar o seu cluster com o Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)
