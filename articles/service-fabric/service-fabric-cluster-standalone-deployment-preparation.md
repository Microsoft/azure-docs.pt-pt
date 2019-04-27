---
title: O Azure Service Fabric Cluster autónomo preparação para implantação | Documentos da Microsoft
description: Documentação relacionada ao preparar o ambiente e a criação de configuração do cluster, para ser considerados antes de implementar um cluster que se destina a processar uma carga de trabalho de produção.
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
ms.openlocfilehash: e5fa46930a3be3c85cd76e655fac3164cc45d957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544743"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planear e preparar a implementação de cluster autónomo do Service Fabric

<a id="preparemachines"></a>Execute os seguintes passos antes de criar o cluster.

## <a name="plan-your-cluster-infrastructure"></a>Planear a sua infraestrutura de cluster
Está prestes a criar um cluster do Service Fabric em máquinas é "proprietário", para que possa decidir quais são os tipos de falhas de que pretende que o cluster para sobreviver. Por exemplo, precisar de separar linhas de energia ou ligações à Internet fornecidas a essas máquinas? Além disso, considere a segurança física destas máquinas. Onde estão as máquinas localizadas e quem precisa de acesso a eles? Depois de tomar essas decisões, logicamente pode mapear as máquinas para vários domínios de falha (consulte a próxima etapa). A planeamento para clusters de produção da infraestrutura é mais envolvida, que, para clusters de teste.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Determinar o número de domínios de falha e domínios de atualização
R [ *domínio de falha* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) é uma unidade física de falha e está diretamente relacionada com a infraestrutura física nos centros de dados. Um domínio de falha consiste em componentes de hardware (computadores, comutadores, redes e muito mais) que compartilham um ponto único de falha. Embora não exista nenhum mapeamento 1:1 entre domínios de falha e racks, livremente falando, cada bastidor pode ser considerado um domínio de falha.

Quando especificar domínios de falha na ClusterConfig.json, pode escolher o nome para cada FD. O Service Fabric suporta hierárquicos domínios de falha, pelo que pode refletir a topologia de infraestrutura nos mesmos.  Por exemplo, os seguintes domínios de falha são válidos:

* "faultDomain": "fd: / Room1/Rack1/Machine1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/Rack1/PDU1/M1"

Uma *domínio de atualização* (UD) é uma unidade lógica de nós. Durante atualizações de Service Fabric orquestradas (uma atualização da aplicação ou uma atualização de cluster), todos os nós num UD direcionados para baixo para efetuar a atualização, enquanto nós de outros domínios de atualização disponíveis para servir pedidos. As atualizações de firmware efetuar nos seus computadores não respeitam esses domínios de atualização, portanto, é necessário efetuá-los um máquinas cada vez.

A forma mais simples de pensar sobre esses conceitos é considerar os domínios de falha como a unidade de falha não planeada e domínios de atualização, como a unidade da manutenção planeada.

Quando especificar domínios de atualização na ClusterConfig.json, pode escolher o nome para cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Azul"

Para obter mais informações sobre domínios de falha e domínios de atualização, consulte [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Um cluster em produção deve abranger, pelo menos, três domínios de falha para que sejam suportadas num ambiente de produção, se tem controle total sobre a manutenção e a gestão de nós, ou seja, é responsável por atualizar e substituindo máquinas. Para clusters em execução em ambientes (ou seja, instâncias de VM do Amazon Web Services) em que não tem controlo total sobre as máquinas, é necessário ter um mínimo de cinco domínios de falha no seu cluster. Cada FD pode ter um ou mais nós. Este procedimento impede que problemas causados por atualizações de máquina e atualizações, que, dependendo de seu tempo, pode interferir com a execução de aplicações e serviços em clusters.

## <a name="determine-the-initial-cluster-size"></a>Determinar o tamanho de cluster inicial

Geralmente, o número de nós do cluster é determinado com base nas necessidades da sua empresa, que é, quantos serviços e contentores serão executado no cluster e quantos recursos precisa para suportar cargas de trabalho. Para clusters de produção, é recomendável ter, pelo menos, cinco nós no seu cluster, que abrange 5 domínios de falha. No entanto, conforme descrito acima, se tem controle total sobre os nós e pode abranger três domínios de falha, em seguida, três nós devem também fazer a tarefa.

Executar cargas de trabalho com monitorização de estado de clusters de teste devem ter três nós, ao passo que os clusters de teste apenas executar apenas uma cargas de trabalho sem monitorização de estado precisam de um nó. Ele também de salientar que, para fins de desenvolvimento, pode ter mais de um nó num determinado computador. No entanto, num ambiente de produção Service Fabric suporta apenas um nó por máquina física ou virtual.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Preparar as máquinas que irão servir como nós

Aqui estão algumas especificações recomendadas para cada máquina que pretende adicionar ao cluster:

* Um mínimo de 16 GB de RAM
* Um mínimo de 40 GB de espaço em disco disponível
* Um núcleo de 4 ou CPU superior
* Conectividade a uma rede segura ou redes de todas as máquinas
* SO do Windows Server instalado (versões válidas: 2012 R2, 2016, 1709 ou versão 1803)
* [.NET framework 4.5.1 ou superior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* O [RemoteRegistry serviço](https://technet.microsoft.com/library/cc754820) deve ser executado em todas as máquinas

O administrador de cluster, implementar e configurar o cluster tem de ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada uma das máquinas. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Transferir o pacote de autónomo do Service Fabric para o Windows Server
[Baixe o Link - pacote de autónomo do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) e deszipe o pacote, para um computador de implementação que não faz parte do cluster ou para uma das máquinas que serão uma parte do seu cluster.

## <a name="modify-cluster-configuration"></a>Modificar a configuração de cluster
Para criar um cluster autónomo tem de criar autónomo cluster ClusterConfig.json ficheiro de configuração, que descreve a especificação do cluster. Pode basear o ficheiro de configuração sobre os modelos na ligação abaixo. <br>
[Configurações de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para obter detalhes sobre as secções neste ficheiro, consulte [definições de configuração para cluster autónomo do Windows](service-fabric-cluster-manifest.md).

Abra um dos ficheiros ClusterConfig.json do pacote que transferiu e modifique as seguintes definições:

| **Definição de configuração** | **Descrição** |
| --- | --- |
| **NodeTypes** |Tipos de nó permitem-lhe separar os nós do cluster em vários grupos. Um cluster tem de ter, pelo menos, um NodeType. Todos os nós de um grupo têm as seguintes características comuns: <br> **Nome** -este é o nome do tipo de nó. <br>**Portas de pontos finais** - estes são denominados vários pontos de extremidade (portas) que estão associados este tipo de nó. Pode utilizar qualquer número de porta que deseja, desde que eles não entram em conflito com qualquer coisa nesse manifesto e já não está a ser utilizada por qualquer outro aplicativo em execução na máquina/VM. <br> **As propriedades de colocação** -descrevem estas propriedades para este tipo de nó que usa como restrições de posicionamento para os serviços do sistema ou os seus serviços. Essas propriedades são pares de chave/valor definido pelo utilizador que fornecem dados extras de metadados de um nó específico. Exemplos de propriedades de nó seria se o nó tem um disco rígido ou a placa gráfica, o número de spindles no seu disco rígido, núcleos e outras propriedades físicas. <br> **As capacidades** -as capacidades de nós definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que ele tem capacidade para uma métrica chamada "MemoryInMb" e que tem a 2048 MB disponível por predefinição. Estas capacidades são utilizadas no tempo de execução para garantir que os serviços que necessitem de quantidades específicas de recursos são colocados em nós que que os recursos disponíveis nos valores necessários.<br>**IsPrimary** – se tiver mais do que um NodeType definido Certifique-se de que apenas um está definido como principal com o valor *true*, que é onde o sistema dos serviços de execução. Todos os outros tipos de nó devem ser definidos como o valor *FALSO* |
| **Nós** |Estes são os detalhes para cada um de nós que fazem parte do cluster (tipo de nó, o nome do nó, endereço IP, domínio de falha e o domínio de atualização do nó). As máquinas que pretende que o cluster seja criado na necessidade de ser listados aqui com seus endereços IP. <br> Se utilizar o mesmo endereço IP para todos os nós, em seguida, one box é criado um cluster, que pode utilizar para fins de teste. Não utilize clusters One-box para a implementação de cargas de trabalho de produção. |

Depois que a configuração do cluster tem tido todas as definições configuradas para o ambiente, ele pode ser testado contra o ambiente de cluster (etapa 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuração do ambiente

Quando um administrador de cluster configura um cluster autónomo do Service Fabric, o ambiente tem de ser configurado com os seguintes critérios: <br>
1. O utilizador criar o cluster deve ter privilégios de segurança de nível de administrador para todas as máquinas que estão listadas como nós no ficheiro de configuração de cluster.
2. Máquina a partir do qual o cluster for criado, bem como cada máquina do nó de cluster tem de:
   * SDK do Service Fabric ter desinstalado
   * Ter desinstalado de runtime do Service Fabric 
   * Ativou o serviço de Firewall do Windows (mpssvc)
   * Ativou o serviço de registo remoto (registo remoto)
   * Ter o ficheiro que partilha (SMB) ativado
   * Ter as portas necessárias abertas, com base nas portas de configuração de cluster
   * Ter as portas necessárias abertas para o serviço SMB do Windows e de registo remoto: 135, 137, 138, 139 e 445
   * Tem conectividade de rede entre si
3. Nenhuma das máquinas de nó de cluster deve ser um controlador de domínio.
4. Se o cluster ser implementado um cluster seguro, valide a pré-requisitos estão colocar e estão configurados corretamente em relação a configuração de segurança necessária.
5. Se as máquinas de cluster não estiverem acessível pela internet, defina o seguinte na configuração do cluster:
   * Desative a telemetria: Sob *propriedades* definir *"ativar telemetria": Falso*
   * Desative automática baixar versão de recursos de infraestrutura & notificações que a versão atual do cluster está prestes a fim de suporte: Sob *propriedades* definir *"fabricClusterAutoupgradeEnabled": Falso*
   * Em alternativa, se o acesso à internet de rede está limitado a domínios listados em branco, os domínios abaixo são necessários para a atualização automática: go.microsoft.com download.microsoft.com

6. Definir exclusões do antivírus do Service Fabric apropriadas:

| **Diretórios de excluído antivírus** |
| --- |
| Programa Files\Microsoft Service Fabric |
| FabricDataRoot (a partir de configuração de cluster) |
| FabricLogRoot (a partir de configuração de cluster) |

| **Antivírus excluídos processos** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Validar o ambiente usando o script de TestConfiguration
O script de TestConfiguration.ps1 pode ser encontrado no pacote autónomo. Ele é usado como um analisador de melhores práticas para validar a alguns dos critérios acima e deve ser utilizado como uma verificação da funcionalidade ao validar se um cluster pode ser implementado num determinado ambiente. Se houver qualquer falha, consulte a lista sob [configuração do ambiente](service-fabric-cluster-standalone-deployment-preparation.md) para resolução de problemas. 

Este script pode ser executado em qualquer máquina que tenha acesso de administrador para todas as máquinas que estão listadas como nós no ficheiro de configuração de cluster. A máquina que este script é executado não tem de ser parte do cluster.

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

Atualmente este módulo de testes de configuração não valida a configuração de segurança para que isso deve ser feita de forma independente.  

> [!NOTE]
> Estamos continuamente a efetuar melhoramentos para tornar este módulo mais robusto, portanto, se houver um caso de falha ou em falta que considerar que não é atualmente capturados por TestConfiguration, faça contato por meio de nosso [canais de suporte](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Criar um cluster autónomo no Windows Server](service-fabric-cluster-creation-for-windows-server.md)
