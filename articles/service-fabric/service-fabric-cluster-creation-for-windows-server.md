---
title: Criar um cluster autónomo de Azure Service Fabric | Documentos da Microsoft
description: Criar um cluster do Azure Service Fabric em qualquer máquina (física ou virtual) com o Windows Server, quer seja no local ou em qualquer cloud.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 9c59da35f5dafcdcf62901689814e696dce3cd20
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674222"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autónomo no Windows Server
Pode utilizar o Azure Service Fabric para criar clusters do Service Fabric em máquinas virtuais ou computadores que executam o Windows Server. Isso significa que pode implementar e executar aplicações do Service Fabric em qualquer ambiente que contém um conjunto de computadores do Windows Server interconectadas, seja no local ou com qualquer fornecedor de cloud. O Service Fabric fornece um pacote de configuração para criar clusters do Service Fabric chamados pacote autónomo do Windows Server.

Este artigo orienta-o através dos passos para criar um cluster autónomo do Service Fabric.

> [!NOTE]
> Este pacote do Windows Server autónomo está comercialmente disponível e pode ser utilizado para implementações de produção. Esse pacote pode conter os novos recursos do Service Fabric que estão em "Pré-visualização". Desloque para baixo para "[incluídas nesse pacote de funcionalidades de pré-visualização](#previewfeatures_anchor)." secção para obter a lista de funcionalidades de pré-visualização. Pode [transferir uma cópia do EULA](https://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obter suporte para o pacote de recursos de infraestrutura de serviço para o Windows Server
* Pergunte à Comunidade sobre o pacote autónomo do Service Fabric para o Windows Server no [fórum do Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra um pedido para [suporte profissional para o Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o suporte profissional da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Também pode obter suporte para este pacote como uma parte [o Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Para obter mais detalhes, veja [opções de suporte do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Para recolher registos para fins de suporte, execute o [recoletor de registos de autónomo do Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Transfira o pacote do Service Fabric para o Windows Server
Para criar o cluster, utilize o pacote de recursos de infraestrutura de serviço para o Windows Server (Windows Server 2012 R2 e versões mais recentes) encontrada aqui: <br>
[Baixe o Link - pacote de autónomo do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Obter detalhes sobre o conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de runtime do Service Fabric é transferido automaticamente no momento da criação do cluster. Se implementar a partir de uma máquina não ligada à internet, transfira o pacote de runtime fora de banda a partir daqui: <br>
[Baixe o Link - tempo de execução do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Encontre exemplos de configuração de Cluster autónomo no: <br>
[Exemplos de configuração de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
São instalados vários ficheiros de configuração de cluster de exemplo com o pacote de configuração. O *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster inseguro com três nós em execução num único computador.  Outros ficheiros de configuração descrevem clusters únicos ou de várias máquinas protegidas com certificados X.509 ou de segurança do Windows.  Não precisa de modificar qualquer uma das definições de configuração predefinida para este tutorial, mas examine o ficheiro de configuração e familiarize-se com as definições.  A secção **nós** descreve os três nós do cluster: nome, endereço IP, [tipo de nó, domínio de falha e o domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A secção **propriedades** define a [segurança, o nível de fiabilidade, a recolha de diagnóstico e os tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

O cluster que criou neste artigo não é seguro.  Qualquer pessoa pode ligar de forma anónima e efetuar operações de gestão, para que os clusters de produção sejam sempre protegidos ao utilizar certificados X.509 ou segurança do Windows.  A segurança só está configurada no momento de criação do cluster e não é possível ativá-la depois de o cluster ser criado. Ativar o ficheiro de configuração de atualização [segurança de certificado](service-fabric-windows-cluster-x509-security.md) ou [segurança do Windows](service-fabric-windows-cluster-windows-security.md). Leia [Secure a cluster (Proteger um cluster)](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.

### <a name="step-1-create-the-cluster"></a>Passo 1: Criar o cluster

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Cenário A: Criar um cluster de desenvolvimento local sem segurança
Service Fabric podem ser implementado para um cluster de desenvolvimento de uma máquina ao utilizar o *ClusterConfig.Unsecure.DevCluster.json* ficheiro incluído no [exemplos](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Descompacte o pacote autônomo para o seu computador, copie o ficheiro de configuração de exemplo para a máquina local, em seguida, execute o *CreateServiceFabricCluster.ps1* script por meio de uma sessão do PowerShell de administrador, a partir da pasta do pacote autónomo .

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a seção de configuração do ambiente no [planear e preparar a implementação de cluster](service-fabric-cluster-standalone-deployment-preparation.md) para detalhes de resolução de problemas.

Se terminou de cenários de desenvolvimento em execução, pode remover o cluster do Service Fabric da máquina ao referir-se aos passos na secção "[remover um cluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Cenário B: Criar um cluster com várias máquinas
Depois de já leu o planejamento e passos de preparação detalhadas ao [planear e preparar a implementação de cluster](service-fabric-cluster-standalone-deployment-preparation.md), está pronto para criar o cluster de produção utilizando o ficheiro de configuração de cluster.

O administrador do cluster que irá implementar e configurar o cluster tem de ter privilégios de administrador no computador. Não pode instalar o Service Fabric num controlador de domínio.

1. O script *TestConfiguration.ps1* no pacote autónomo é utilizado como um analisador de melhores práticas para validar se um cluster pode ser implementado num determinado ambiente. A [preparação da implementação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e os requisitos do ambiente. Execute o script para verificar se pode criar o cluster de desenvolvimento:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Deverá ver um resultado semelhante ao seguinte. Se o campo de na parte inferior "para aprovado" é retornado como "True", verificações de sanidade passaram e o cluster parecer fosse implantado com base na configuração da entrada.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Crie o cluster:  Executar o *CreateServiceFabricCluster.ps1* script de implementação de cluster do Service Fabric em cada máquina na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Os rastreios de implementação são escritos na VM/máquina em que executou o script do PowerShell CreateServiceFabricCluster.ps1. Pode encontrá-los na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi corretamente implementado numa máquina, encontre os ficheiros instalados no diretório FabricDataRoot, conforme detalhado na secção FabricSettings do ficheiro de configuração do cluster (por predefinição C:\ProgramData\SF). Desta forma, os processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gestor de Tarefas.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Cenário c: Criar um cluster offline (sem conexão de internet)
O pacote de runtime do Service Fabric é transferido automaticamente durante a criação do cluster. Ao implementar um cluster para as máquinas não ligadas à internet, terá de transferir o pacote de runtime do Service Fabric em separado e forneça o caminho para o mesmo durante a criação do cluster.
O pacote de runtime pode ser transferido em separado, de outro computador ligado à internet, no [Link transferir - tempo de execução do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de tempo de execução para onde está a implementar o cluster offline a partir de e criar o cluster, executando `CreateServiceFabricCluster.ps1` com o `-FabricRuntimePackagePath` parâmetro incluído, como mostrado neste exemplo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* e *.\MicrosoftAzureServiceFabric.cab* são os caminhos para a configuração de cluster e o arquivo. cab de tempo de execução, respetivamente.

### <a name="step-2-connect-to-the-cluster"></a>Passo 2: Ligar ao cluster
Ligar ao cluster para verificar se que o cluster está em execução e disponíveis. O módulo do ServiceFabric PowerShell é instalado com o runtime.  Pode ligar ao cluster a partir de um de nós do cluster ou de um computador remoto com o tempo de execução do Service Fabric.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma ligação ao cluster.

Para ligar a um cluster inseguro, execute o seguinte comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Por exemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Consulte [Connect to a secure cluster (Ligar a um cluster seguro)](service-fabric-connect-to-secure-cluster.md) para outros exemplos de ligação a um cluster. Depois de ligar ao cluster, utilize o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para apresentar uma lista de nós no cluster e informações de estado para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Passo 3: Visualize o cluster com o explorador do Service Fabric
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o seu cluster e gerir aplicações.  Service Fabric Explorer é um serviço que é executado no cluster, o qual poderá aceder através de um browser ao navegar para o [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer).

O dashboard do cluster fornece uma descrição geral do cluster, incluindo um resumo de aplicações e do estado de funcionamento do nó. A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Pode adicionar ou remover nós para o cluster do Service Fabric autónomo, conforme as necessidades da empresa são alteradas. Veja [Adicionar ou Remover nós para um cluster autónomo do Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md) para obter passos detalhados.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script *RemoveServiceFabricCluster.ps1* do PowerShell a partir da pasta do pacote e passe no caminho para o ficheiro de configuração do JSON. Opcionalmente, pode especificar uma localização para o registo da eliminação.

Este script pode ser executado em qualquer máquina que tenha acesso de administrador para todas as máquinas que estão listadas como nós no ficheiro de configuração de cluster. A máquina que este script é executado não tem de ser parte do cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Os dados telemétricos recolhidos e como opção de desativá-lo
Como padrão, o produto recolhe telemetria sobre a utilização do Service Fabric para melhorar o produto. O analisador de melhores práticas que é executado como parte da configuração verifica a existência de conectividade [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Se não estiver acessível, a configuração falhar, a menos que optar por telemetria.

1. Tenta carregar os dados seguintes para o pipeline de telemetria [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) uma vez por dia. É um carregamento de melhor esforço e não tem impacto sobre a funcionalidade de cluster. A telemetria é enviada apenas a partir do nó que executa a ativação pós-falha Gestor primário. Não existem outros nós enviam telemetria.
2. A telemetria consiste no seguinte:

* Número de serviços
* Número de ServiceTypes
* Número de Aplicações
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de Réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de nós
* IsContextComplete: Verdadeiro/Falso
* ClusterId: Este é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou máquina a partir do qual a telemetria é carregada

Para desativar a telemetria, adicione o seguinte ao *propriedades* na sua configuração de cluster: *ativar telemetria: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funcionalidades de pré-visualização incluídas nesse pacote
Nenhum.


> [!NOTE]
> Começando com a nova [versão de disponibilidade geral do cluster autónomo para o Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), pode atualizar o seu cluster para versões futuras, manual ou automaticamente. Consulte a [atualizar uma versão de cluster do Service Fabric autónomo](service-fabric-cluster-upgrade-windows-server.md) documento para obter detalhes.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Implementar e remover aplicações com o PowerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração para cluster autónomo do Windows](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós para um cluster autónomo do Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualizar uma versão de cluster do Service Fabric autónomo](service-fabric-cluster-upgrade-windows-server.md)
* [Criar um cluster autónomo do Service Fabric com VMs do Azure a executar o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger um cluster autónomo no Windows através da segurança do Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autónomo no Windows com X509 certificados](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png