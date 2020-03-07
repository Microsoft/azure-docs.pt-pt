---
title: Criar um cluster de tecido de serviço Azure autónomo
description: Crie um cluster de Tecido de Serviço Azure em qualquer máquina (física ou virtual) que execute o Windows Server, seja no local ou em qualquer nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389836"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autónomo em execução no Windows Server
Pode utilizar o Tecido de Serviço Azure para criar clusters de tecido de serviço em quaisquer máquinas virtuais ou computadores que executem o Windows Server. Isto significa que pode implementar e executar aplicações de Tecido de Serviço em qualquer ambiente que contenha um conjunto de computadores Windows Server interligados, seja nas instalações ou com qualquer fornecedor de nuvem. O Service Fabric fornece um pacote de configuração para criar clusters de tecido de serviço chamado pacote de Servidor Windows autónomo. Os clusters tradicionais de tecido de serviço em Azure estão disponíveis como um serviço gerido, enquanto os clusters autónomos de tecido de serviço são self-service. Para mais informações sobre as diferenças, consulte [Comparar os clusters De Tecido solado azure e autónomos.](./service-fabric-deploy-anywhere.md)

Este artigo percorre-o através dos passos para criar um cluster autónomo de Tecido de Serviço.

> [!NOTE]
> Este pacote autónomo do Windows Server está disponível comercialmente sem custos e pode ser utilizado para implementações de produção. Este pacote pode conter novas funcionalidades de Tecido de Serviço que se encontram em "Pré-visualização". Desloque-se para baixo para "[Visualizar funcionalidades incluídas neste pacote](#previewfeatures_anchor)." secção para a lista das funcionalidades de pré-visualização. Já pode [descarregar uma cópia da EULA.](https://go.microsoft.com/fwlink/?LinkID=733084)
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obtenha suporte para o tecido de serviço para o pacote Do Servidor windows
* Pergunte à comunidade sobre o pacote autónomo service Fabric para o Windows Server no [fórum Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra um bilhete para [Apoio Profissional para Tecido de Serviço.](https://support.microsoft.com/oas/default.aspx?prid=16146)  Saiba mais sobre o Suporte Profissional da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Também pode obter suporte para este pacote como parte do [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Para mais detalhes, consulte as opções de suporte do [Tecido de Serviço Azure.](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)
* Para recolher registos para fins de suporte, execute o [coletor](service-fabric-cluster-standalone-package-contents.md)de registo autónomo de tecido de serviço .

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Transfira o pacote do Service Fabric para o Windows Server
Para criar o cluster, utilize o tecido de serviço para o pacote Windows Server (Windows Server 2012 R2 e mais recente) aqui encontrado: <br>
[Link de descarregamento - Pacote Autónomo de Tecido de Serviço - Servidor windows](https://go.microsoft.com/fwlink/?LinkId=730690)

Encontre detalhes sobre o conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de tempo de funcionação do Tecido de Serviço é automaticamente descarregado no momento da criação do cluster. Se for implantado a partir de uma máquina não ligada à internet, por favor, descarregue o pacote de tempo de execução fora da banda a partir daqui: <br>
[Link de descarregamento - Tempo de execução de tecido de serviço - Servidor windows](https://go.microsoft.com/fwlink/?linkid=839354)

Encontre amostras de configuração autónoma de cluster em: <br>
[Amostras de configuração autónoma de cluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
São instalados vários ficheiros de configuração de cluster de exemplo com o pacote de configuração. O *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster inseguro com três nós em execução num único computador.  Outros ficheiros de configuração descrevem clusters únicos ou de várias máquinas protegidas com certificados X.509 ou de segurança do Windows.  Não precisa de modificar qualquer uma das definições de configuração predefinida para este tutorial, mas examine o ficheiro de configuração e familiarize-se com as definições.  A secção **nós** descreve os três nós do cluster: nome, endereço IP, [tipo de nó, domínio de falha e o domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A secção **propriedades** define a [segurança, o nível de fiabilidade, a recolha de diagnóstico e os tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

O cluster criado neste artigo é inseguro.  Qualquer pessoa pode ligar de forma anónima e efetuar operações de gestão, para que os clusters de produção sejam sempre protegidos ao utilizar certificados X.509 ou segurança do Windows.  A segurança só está configurada no momento de criação do cluster e não é possível ativá-la depois de o cluster ser criado. Atualize o ficheiro config para permitir [a segurança](service-fabric-windows-cluster-x509-security.md) do certificado ou a segurança [do Windows](service-fabric-windows-cluster-windows-security.md). Leia [Secure a cluster (Proteger um cluster)](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.

### <a name="step-1-create-the-cluster"></a>Passo 1: Criar o cluster

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Cenário A: Criar um cluster de desenvolvimento local não seguro
O tecido de serviço pode ser implantado num cluster de desenvolvimento de uma máquina utilizando o ficheiro *ClusterConfig.Unsecure.DevCluster.json* incluído em [Amostras](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Desembale o pacote autónomo para a sua máquina, copie o ficheiro de config da amostra para a máquina local e, em seguida, execute o script *CreateServiceFabricCluster.ps1* através de uma sessão de administrador PowerShell, a partir da pasta de pacote autónoma.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a secção de Configuração do Ambiente no [Plan e prepare a implementação do seu cluster](service-fabric-cluster-standalone-deployment-preparation.md) para obter detalhes de resolução de problemas.

Se terminar de executar cenários de desenvolvimento, pode remover o cluster de Tecido de Serviço da máquina, referindo-se aos passos na secção "[Remova um cluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Cenário B: Criar um cluster multi-máquina
Depois de ter passado pelos passos de planeamento e preparação detalhados no Plan e preparar a [implementação do cluster,](service-fabric-cluster-standalone-deployment-preparation.md)está pronto para criar o seu cluster de produção utilizando o ficheiro de configuração do cluster.

O administrador do cluster que irá implementar e configurar o cluster tem de ter privilégios de administrador no computador. Não pode instalar o Service Fabric num controlador de domínio.

1. O script *TestConfiguration.ps1* no pacote autónomo é utilizado como um analisador de melhores práticas para validar se um cluster pode ser implementado num determinado ambiente. A [preparação da implementação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e os requisitos do ambiente. Execute o script para verificar se pode criar o cluster de desenvolvimento:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Deverá ver um resultado semelhante ao seguinte. Se o campo inferior "Passado" for devolvido como "Verdadeiro", as verificações de sanidade passaram e o cluster parece ser implantável com base na configuração de entrada.

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

2. Criar o cluster: Executar o script *CreateServiceFabricCluster.ps1* para implantar o cluster De Serviço Tecido em cada máquina na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Os rastreios de implementação são escritos na VM/máquina em que executou o script do PowerShell CreateServiceFabricCluster.ps1. Pode encontrá-los na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi corretamente implementado numa máquina, encontre os ficheiros instalados no diretório FabricDataRoot, conforme detalhado na secção FabricSettings do ficheiro de configuração do cluster (por predefinição C:\ProgramData\SF). Desta forma, os processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gestor de Tarefas.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Cenário C: Criar um cluster offline (desligado da Internet)
O pacote de tempo de funcionação do Tecido de Serviço é automaticamente descarregado na criação do cluster. Ao implantar um cluster para máquinas não ligadas à internet, terá de descarregar separadamente o pacote de tempo de funcionamento do Tecido de Serviço e fornecer-lhe o caminho na criação de clusters.
O pacote de tempo de execução pode ser descarregado separadamente, a partir de outra máquina ligada à internet, no [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de tempo de funcionamento para onde está a implantar o cluster offline e crie o cluster executando `CreateServiceFabricCluster.ps1` com o parâmetro `-FabricRuntimePackagePath` incluído, como mostra este exemplo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* e *.\MicrosoftAzureServiceFabric.cab* são os caminhos para a configuração do cluster e o ficheiro de tempo de execução .cab, respectivamente.

### <a name="step-2-connect-to-the-cluster"></a>Passo 2: Ligar ao cluster
Ligue-se ao cluster para verificar se o cluster está em funcionamento e disponível. O módulo do ServiceFabric PowerShell é instalado com o runtime.  Pode ligar-se ao cluster a partir de um dos nós do cluster ou de um computador remoto com o tempo de execução do Tecido de Serviço.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma ligação ao cluster.

Para se ligar a um cluster não seguro, execute o seguinte comando PowerShell:

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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Passo 3: Visualizar o cluster utilizando o explorador de tecido de serviço
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o seu cluster e gerir aplicações.  Service Fabric Explorer é um serviço que funciona no cluster, a que você acede usando um navegador navegando para [http://localhost:19080/Explorer](http://localhost:19080/Explorer).

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

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria recolhidos e como optar por não o fazer
Como padrão, o produto recolhe telemetria no uso do Tecido de Serviço para melhorar o produto. O Analisador de Boas Práticas que funciona como parte da configuração verifica a conectividade com [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se não for alcançável, a configuração falha a menos que opte por não telemetria.

1. O gasoduto de telemetria tenta enviar os seguintes dados para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez por dia. É um upload de melhor esforço e não tem impacto na funcionalidade do cluster. A telemetria só é enviada do nó que dirige o gerente de failover primária. Nenhum outro nó envia telemetria.
2. A telemetria consiste no seguinte:

* Número de serviços
* Número de Tipos de Serviços
* Número de Candidaturas
* Número de Upgrades de Aplicações
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de Unidades não saudáveis failover
* Número de Réplicas
* Número de Réplicas inbuild
* Número de Réplicas StandBy
* Número de Réplicas Offline
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de nós
* IsContextComplete: True/False
* ClusterId: Este é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou máquina a partir da qual a telemetria é carregada

Para desativar a telemetria, adicione o seguinte às *propriedades* do seu cluster config: *enableTelemetria: falso*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funcionalidades de pré-visualização incluídas neste pacote
Nenhum.


> [!NOTE]
> A partir da nova [versão GA do cluster autónomo para o Windows Server (versão 5.3.204.x),](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)pode atualizar o seu cluster para futuras versões, manual ou automaticamente. Consulte o Upgrade de um documento de versão de [cluster de tecido](service-fabric-cluster-upgrade-windows-server.md) de serviço autónomo para mais detalhes.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Implementar e remover aplicações usando powerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração para cluster Windows autónomo](service-fabric-cluster-manifest.md)
* [Adicione ou remova nós para um cluster de tecido de serviço autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualize uma versão autónoma do cluster de cluster de tecido de serviço](service-fabric-cluster-upgrade-windows-server.md)
* [Crie um cluster de tecido de serviço autónomo com VMs Azure executando Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteja um cluster autónomo no Windows utilizando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteja um cluster autónomo no Windows utilizando certificados X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
