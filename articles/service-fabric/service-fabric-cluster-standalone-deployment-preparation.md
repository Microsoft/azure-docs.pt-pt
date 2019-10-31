---
title: Preparação da implantação de cluster autônomo do Azure Service Fabric | Microsoft Docs
description: Documentação relacionada à preparação do ambiente e à criação da configuração do cluster, a ser considerada antes da implantação de um cluster destinado a lidar com uma carga de trabalho de produção.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 96956e1ad935933572b1f2d31b70ef64f8b92501
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175854"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planejar e preparar sua implantação de Cluster Service Fabric autônomo

<a id="preparemachines"></a>Execute as etapas a seguir antes de criar o cluster.

## <a name="plan-your-cluster-infrastructure"></a>Planejar sua infraestrutura de cluster
Você está prestes a criar um Cluster Service Fabric nos computadores que "possui", de modo que você pode decidir quais tipos de falhas deseja que o cluster sobreviver. Por exemplo, você precisa de linhas de alimentação separadas ou conexões de Internet fornecidas a esses computadores? Além disso, considere a segurança física dessas máquinas. Onde os computadores estão localizados e quem precisa acessá-los? Depois de tomar essas decisões, você pode mapear logicamente os computadores para vários domínios de falha (consulte a próxima etapa). O planejamento de infraestrutura para clusters de produção é mais envolvido do que para clusters de teste.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Determinar o número de domínios de falha e domínios de atualização
Um [ *domínio de falha* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) é uma unidade física de falha e está diretamente relacionada à infraestrutura física nos data centers. Um domínio de falha consiste em componentes de hardware (computadores, comutadores, redes e muito mais) que compartilham um ponto único de falha. Embora não haja nenhum mapeamento 1:1 entre os domínios de falha e os racks, de forma flexível, cada rack pode ser considerado um domínio de falha.

Ao especificar FDs em ClusterConfig. JSON, você pode escolher o nome de cada FD. O Service Fabric dá suporte a FDs hierárquicos, para que você possa refletir sua topologia de infraestrutura neles.  Por exemplo, os seguintes FDs são válidos:

* "faultDomain": "FD:/Room1/Rack1/machine1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/Room1/Rack1/PDU1/M1"

Um *domínio de atualização* (UD) é uma unidade lógica de nós. Durante Service Fabric atualizações orquestradas (uma atualização de aplicativo ou uma atualização de cluster), todos os nós em um UD são desativados para executar a atualização enquanto os nós em outros UDs permanecem disponíveis para atender às solicitações. As atualizações de firmware que você executa em seus computadores não obedecem a UDs, portanto, você deve fazer uma máquina por vez.

A maneira mais simples de pensar sobre esses conceitos é considerar o FDs como a unidade de falha não planejada e o UDs como a unidade de manutenção planejada.

Ao especificar UDs em ClusterConfig. JSON, você pode escolher o nome de cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Para obter informações mais detalhadas sobre FDs e UDs, consulte [descrevendo um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Um cluster em produção deve abranger pelo menos três FDs para ter suporte em um ambiente de produção, se você tiver controle total sobre a manutenção e o gerenciamento dos nós, ou seja, você será responsável por atualizar e substituir computadores. Para clusters em execução em ambientes (ou seja, Amazon Web Services instâncias de VM) em que você não tem controle total sobre os computadores, você deve ter um mínimo de cinco FDs em seu cluster. Cada FD pode ter um ou mais nós. Isso é para evitar problemas causados por atualizações de computador, que dependendo do seu tempo, podem interferir na execução de aplicativos e serviços em clusters.

## <a name="determine-the-initial-cluster-size"></a>Determinar o tamanho inicial do cluster

Em geral, o número de nós no cluster é determinado com base nas suas necessidades de negócios, ou seja, quantos serviços e contêineres serão executados no cluster e quantos recursos você precisa para sustentar suas cargas de trabalho. Para clusters de produção, recomendamos ter pelo menos cinco nós no cluster, abrangendo 5 FDs. No entanto, conforme descrito acima, se você tem controle total sobre seus nós e pode abranger três FDs, três nós também devem fazer o trabalho.

Os clusters de teste que executam cargas de trabalho com estado devem ter três nós, enquanto os clusters de teste somente executando cargas de trabalho sem estado precisam apenas de um nó. Também deve-se observar que, para fins de desenvolvimento, você pode ter mais de um nó em um determinado computador. No entanto, em um ambiente de produção, Service Fabric dá suporte apenas a um nó por máquina virtual ou física.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Preparar os computadores que funcionarão como nós

Aqui estão algumas especificações recomendadas para cada computador que você deseja adicionar ao cluster:

* Um mínimo de 16 GB de RAM
* Um mínimo de 40 de GB de espaço em disco disponível
* Uma CPU de 4 núcleos ou mais
* Conectividade com uma rede ou redes seguras para todas as máquinas
* Sistema operacional Windows Server instalado (versões válidas: 2012 R2, 2016, 1709 ou 1803). Service Fabric versão 6.4.654.9590 e posterior também dá suporte ao servidor 2019 e 1809.
* [.NET Framework 4.5.1 ou superior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa
* [Windows PowerShell 3,0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* O [serviço RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve estar em execução em todos os computadores
* Service Fabric unidade de instalação deve ser um sistema de arquivos NTFS

O administrador de cluster que está implantando e Configurando o cluster deve ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada uma das máquinas. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Baixar o pacote autônomo do Service Fabric para o Windows Server
[Link de download-Service Fabric pacote autônomo-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote, seja para um computador de implantação que não faz parte do cluster ou para um dos computadores que fará parte do cluster.

## <a name="modify-cluster-configuration"></a>Modificar configuração de cluster
Para criar um cluster autônomo, você precisa criar um arquivo ClusterConfig. JSON de configuração de cluster autônomo, que descreve a especificação do cluster. Você pode basear o arquivo de configuração nos modelos encontrados no link abaixo. <br>
[Configurações de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para obter detalhes sobre as seções neste arquivo, consulte [definições de configuração para o cluster autônomo do Windows](service-fabric-cluster-manifest.md).

Abra um dos arquivos ClusterConfig. JSON do pacote que você baixou e modifique as seguintes configurações:

| **Definição de configuração** | **Descrição** |
| --- | --- |
| **NodeTypes** |Os tipos de nó permitem separar os nós de cluster em vários grupos. Um cluster deve ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características comuns: <br> **Nome** – é o nome do tipo de nó. <br>**Portas de ponto de extremidade** -são vários pontos de extremidade nomeados (portas) que estão associados a esse tipo de nó. Você pode usar qualquer número de porta que desejar, desde que eles não entrem em conflito com nada mais neste manifesto e ainda não estejam em uso por nenhum outro aplicativo em execução no computador/VM. <br> **Propriedades de posicionamento** – descrevem as propriedades desse tipo de nó que você usa como restrições de posicionamento para os serviços do sistema ou seus serviços. Essas propriedades são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó seriam se o nó tem um disco rígido ou uma placa gráfica, o número de eixos em sua unidade de disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades** – as capacidades do nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que ele tenha capacidade para uma métrica chamada "MemoryInMb" e que tenha 2048 MB disponíveis por padrão. Essas capacidades são usadas em tempo de execução para garantir que os serviços que exigem quantidades específicas de recursos sejam colocados nos nós que têm esses recursos disponíveis nos valores necessários.<br>**IsPrimary** -se você tiver mais de um NodeType definido, verifique se apenas um está definido como Primary com o valor *true*, que é onde os serviços do sistema são executados. Todos os outros tipos de nó devem ser definidos como o valor *false* |
| **Nós** |Estes são os detalhes de cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). Os computadores nos quais você deseja que o cluster seja criado precisam ser listados aqui com seus endereços IP. <br> Se você usar o mesmo endereço IP para todos os nós, um cluster de uma caixa será criado, que poderá ser usado para fins de teste. Não use clusters de uma caixa para implantar cargas de trabalho de produção. |

Depois que a configuração do cluster tiver todas as configurações configuradas para o ambiente, ele poderá ser testado no ambiente de cluster (etapa 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuração do ambiente

Quando um administrador de cluster configura um Cluster Service Fabric autônomo, o ambiente precisa ser configurado com os seguintes critérios: <br>
1. O usuário que cria o cluster deve ter privilégios de segurança no nível de administrador para todos os computadores listados como nós no arquivo de configuração de cluster.
2. Computador do qual o cluster é criado, bem como cada máquina de nó de cluster deve:
   * Service Fabric SDK desinstalado
   * Ter Service Fabric tempo de execução desinstalado 
   * Habilitar o serviço de firewall do Windows (MpsSvc)
   * Habilitar o serviço de registro remoto (registro remoto)
   * Ter portas necessárias abertas, com base em portas de configuração de cluster
   * Ter as portas necessárias abertas para o serviço de registro remoto: 135, 137, 138 e 139
   * Ter conectividade de rede entre si
3. Nenhum dos computadores do nó de cluster deve ser um controlador de domínio.
4. Se o cluster a ser implantado for um cluster seguro, valide os pré-requisitos de segurança necessários e configurados corretamente em relação à configuração.
5. Se os computadores do cluster não forem acessíveis pela Internet, defina o seguinte na configuração do cluster:
   * Desabilitar telemetria: em *Propriedades* , defina *"enableTelemetry": false*
   * Desabilite a versão automática do Fabric baixando & notificações de que a versão atual do cluster está se aproximando do fim do suporte: em *Properties* Set *"fabricClusterAutoupgradeEnabled": false*
   * Como alternativa, se o acesso à rede na Internet estiver limitado a domínios listados em branco, os domínios abaixo serão necessários para a atualização automática: go.microsoft.com download.microsoft.com

6. Defina as exclusões de Service Fabric antivírus apropriadas:

| **Diretórios excluídos antivírus** |
| --- |
| Programas de Programas\microsoft Service Fabric |
| FabricDataRoot (da configuração de cluster) |
| FabricLogRoot (da configuração de cluster) |

| **Processos de antivírus excluídos** |
| --- |
| Fabric. exe |
| FabricHost. exe |
| FabricInstallerService. exe |
| FabricSetup. exe |
| FabricDeployer. exe |
| ImageBuilder. exe |
| FabricGateway. exe |
| O fabricdca. exe |
| FabricFAS. exe |
| FabricUOS. exe |
| FabricRM. exe |
| FileStoreService. exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Validar o ambiente usando o script TestConfiguration
O script TestConfiguration. ps1 pode ser encontrado no pacote autônomo. Ele é usado como uma Analisador de Práticas Recomendadas para validar alguns dos critérios acima e deve ser usado como uma verificação de sanidade para validar se um cluster pode ser implantado em um determinado ambiente. Se houver alguma falha, consulte a lista em configuração do [ambiente](service-fabric-cluster-standalone-deployment-preparation.md) para solução de problemas. 

Esse script pode ser executado em qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no arquivo de configuração de cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

Atualmente, este módulo de teste de configuração não valida a configuração de segurança para que isso tenha que ser feito de forma independente.  

> [!NOTE]
> Estamos continuamente fazendo melhorias para tornar este módulo mais robusto, portanto, se houver um caso inexistente ou ausente que você acredite que não é atualmente pego pelo TestConfiguration, informe-nos nos nossos [canais de suporte](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster autônomo em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md)
