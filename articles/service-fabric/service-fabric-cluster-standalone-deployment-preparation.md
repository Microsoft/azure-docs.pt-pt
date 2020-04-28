---
title: Preparação autónoma de implantação de clusters
description: Documentação relacionada com a preparação do ambiente e a criação da configuração do cluster, a considerar antes da implantação de um cluster destinado a manusear uma carga de trabalho de produção.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75610068"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planeie e prepare a sua implantação autónoma de tecido de serviço

<a id="preparemachines"></a>Execute os seguintes passos antes de criar o seu cluster.

## <a name="plan-your-cluster-infrastructure"></a>Planeie a sua infraestrutura de cluster
Está prestes a criar um cluster de Tecido de Serviço em máquinas que "possui", para que possa decidir que tipo de falhas quer que o cluster sobreviva. Por exemplo, precisa de linhas de alimentação separadas ou ligações à Internet fornecidas a estas máquinas? Além disso, considere a segurança física destas máquinas. Onde estão as máquinas localizadas e quem precisa de acesso? Depois de tomar estas decisões, pode logicamente mapear as máquinas para vários domínios de falha (ver próximo passo). O planeamento de infraestruturas para aglomerados de produção está mais envolvido do que em grupos de ensaio.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Determinar o número de domínios de avaria e atualizar domínios
Um domínio de [ *avaria* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) é uma unidade física de falha e está diretamente relacionado com a infraestrutura física nos centros de dados. Um domínio de falha é composto por componentes de hardware (computadores, interruptores, redes e muito mais) que partilham um único ponto de falha. Embora não exista um mapeamento 1:1 entre domínios de falha e racks, vagamente falando, cada rack pode ser considerado um domínio de falha.

Quando especificar FDs em ClusterConfig.json, pode escolher o nome para cada FD. O Service Fabric suporta FDs hierárquicos, para que possa refletir a sua topologia de infraestrutura neles.  Por exemplo, os seguintes FDs são válidos:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Um domínio de *atualização* (UD) é uma unidade lógica de nós. Durante as atualizações orquestradas pelo Service Fabric (seja uma atualização de aplicações ou uma atualização de cluster), todos os nós de um UD são retirados para realizar a atualização enquanto os nós de outros UDs permanecem disponíveis para servir pedidos. As atualizações de firmware que executa nas suas máquinas não honram Os UDs, por isso deve fazer-lhes uma máquina de cada vez.

A forma mais simples de pensar sobre estes conceitos é considerar os FDs como a unidade de falhas não planeadas e UDs como a unidade de manutenção planeada.

Quando especificar UDs em ClusterConfig.json, pode escolher o nome para cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomínio": "UD0"
* "upgradeDomínio": "UD1A"
* "upgradeDomínio": "DomainRed"
* "upgradeDomínio": "Azul"

Para obter informações mais detalhadas sobre FDs e UDs, consulte [descrever um cluster de tecido](service-fabric-cluster-resource-manager-cluster-description.md)de serviço .

Um cluster de produção deve abranger pelo menos três FDs para ser suportado em ambiente de produção, se tiver controlo total sobre a manutenção e gestão dos nós, ou seja, é responsável pela atualização e substituição de máquinas. Para clusters em funcionamento em ambientes (isto é, amazon Web Services VM) onde você não tem controlo total sobre as máquinas, você deve ter um mínimo de cinco FDs no seu cluster. Cada FD pode ter um ou mais nós. Isto é para prevenir problemas causados por atualizações e atualizações de máquinas, que dependendo do seu tempo, podem interferir com o funcionamento de aplicações e serviços em clusters.

## <a name="determine-the-initial-cluster-size"></a>Determinar o tamanho inicial do cluster

Geralmente, o número de nós no seu cluster é determinado com base nas necessidades do seu negócio, ou seja, quantos serviços e contentores estarão a funcionar no cluster e quantos recursos precisa para sustentar as suas cargas de trabalho. Para os clusters de produção, recomendamos ter pelo menos cinco nós no seu cluster, abrangendo 5 FDs. No entanto, como acima descrito, se tiver controlo total sobre os seus nós e puder abranger três FDs, então três nós também devem fazer o trabalho.

Os agrupamentos de ensaio que executam cargas de trabalho audais devem ter três nós, enquanto os agrupamentos de ensaio que executam apenas cargas de trabalho apátridas só precisam de um nó. Note-se também que, para fins de desenvolvimento, pode ter mais do que um nó numa determinada máquina. No entanto, num ambiente de produção, o Service Fabric suporta apenas um nó por máquina física ou virtual.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Prepare as máquinas que servirão de nó

Aqui estão algumas especificações recomendadas para cada máquina que pretende adicionar ao cluster:

* Um mínimo de 16 GB de RAM
* Um mínimo de 40 de gb de espaço disponível em disco
* Um núcleo 4 ou um CPU maior
* Conectividade a uma rede ou redes seguras para todas as máquinas
* Sistema operativo Windows Server (versões válidas: 2012 R2, 2016, 1709 ou 1803). Serviço Fabric versão 6.4.654.9590 e mais tarde também suporta Server 2019 e 1809.
* [.NET Quadro 4.5.1 ou superior,](https://www.microsoft.com/download/details.aspx?id=40773)instalação completa
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* O [serviço RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve estar em funcionamento em todas as máquinas
* Unidade de instalação de tecido de serviço deve ser sistema de ficheiros NTFS

O administrador do cluster que implementa e configura o cluster deve ter [privilégios](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) de administrador em cada uma das máquinas. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Descarregue o pacote autónomo service Fabric para windows server
[Descarregue link - Pacote Autónomo](https://go.microsoft.com/fwlink/?LinkId=730690) de Tecido de Serviço - Windows Server e desaperte o pacote, quer para uma máquina de implementação que não faça parte do cluster, quer para uma das máquinas que fará parte do seu cluster.

## <a name="modify-cluster-configuration"></a>Modificar a configuração do cluster
Para criar um cluster autónomo, tem de criar um ficheiro cluster de configuração autónoma ClusterConfig.json, que descreve a especificação do cluster. Pode basear o ficheiro de configuração nos modelos encontrados no link abaixo. <br>
[Configurações autónomas de cluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para mais informações sobre as secções deste ficheiro, consulte as definições de [Configuração para o cluster Windows autónomo](service-fabric-cluster-manifest.md).

Abra um dos ficheiros ClusterConfig.json a partir do pacote que descarregou e modifique as seguintes definições:

| **Definição de configuração** | **Descrição** |
| --- | --- |
| **Tipos de nó** |Os tipos de nó permitem separar os seus nós de cluster em vários grupos. Um cluster deve ter pelo menos um Nó. Todos os nós de um grupo têm as seguintes características comuns: <br> **Nome** - Este é o nome do tipo nó. <br>**Endpoint Ports** - Estes são vários pontos finais nomeados (portas) que estão associados a este tipo de nó. Pode utilizar qualquer número de porta que deseje, desde que não entre em conflito com mais nada neste manifesto e não esteja já a ser utilizado por qualquer outra aplicação que esteja a funcionar na máquina/VM. <br> **Propriedades de Colocação** - Estes descrevem propriedades para este tipo de nó que você usa como restrições de colocação para os serviços do sistema ou seus serviços. Estas propriedades são pares de chaves/valor definidos pelo utilizador que fornecem dados de meta extra para um determinado nó. Exemplos de propriedades nódea seriam se o nó tem um disco rígido ou uma placa gráfica, o número de fusos no seu disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades** - As capacidades do nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB disponíveis por padrão. Estas capacidades são utilizadas em tempo de execução para garantir que os serviços que exigem quantidades específicas de recursos sejam colocados nos nódosos que dispõem desses recursos disponíveis nos montantes necessários.<br>**IsPrimary** - Se tiver mais do que um NodeType definido, certifique-se de que apenas um está definido para primário com o valor *verdadeiro*, que é onde os serviços do sistema funcionam. Todos os outros tipos de nó devem ser definidos para o valor *falso* |
| **Nós** |Estes são os detalhes para cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de avaria e domínio de atualização do nó). As máquinas que pretende que o cluster seja criado precisam de ser listadas aqui com os seus endereços IP. <br> Se utilizar o mesmo endereço IP para todos os nós, então é criado um cluster de uma caixa, que pode utilizar para fins de teste. Não utilize clusters de caixa única para a implementação de cargas de trabalho de produção. |

Depois de a configuração do cluster ter todas as configurações configuradas para o ambiente, pode ser testada contra o ambiente de cluster (passo 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuração de ambiente

Quando um administrador de cluster configura um cluster autónomo do Tecido de Serviço, o ambiente tem de ser criado com os seguintes critérios: <br>
1. O utilizador que cria o cluster deve ter privilégios de segurança ao nível do administrador para todas as máquinas listadas como nós no ficheiro de configuração do cluster.
2. Máquina a partir da qual o cluster é criado, bem como cada máquina de nó cluster deve:
   * Ter sdk de tecido de serviço desinstalado
   * Ter o tempo de execução do tecido de serviço desinstalado
   * Ter o serviço Windows Firewall (mpssvc) ativado
   * Ter o Serviço de Registo Remoto (registo remoto) ativado
   * Ter partilha de ficheiros (SMB) ativada
   * Ter portas necessárias abertas, com base em portas de configuração de cluster
   * Ter portas necessárias abertas para windows SMB e serviço de registo remoto: 135, 137, 138, 139 e 445
   * Ter conectividade de rede uns com os outros
3. Nenhuma das máquinas de nó de cluster deve ser controlador de domínio.
4. Se o cluster a ser implantado for um cluster seguro, valide os pré-requisitos de segurança necessários e estejam configurados e estejam corretamente configurados contra a configuração.
5. Se as máquinas de cluster não estiverem acessíveis à Internet, desempente o seguinte na configuração do cluster:
   * Desativar a telemetria: Em propriedades *definidas* *"enableTelemetria": falsa*
   * Desative a versão automática fabric a descarregar & notificações de que a versão atual do cluster está a aproximar-se do fim do suporte: Em *propriedades* *definidas "fabricClusterAutoupgradeEnabled": falso*
   * Alternativamente, se o acesso à internet da rede estiver limitado a domínios listados em branco, os domínios abaixo são necessários para atualização automática: go.microsoft.com download.microsoft.com

6. Deteto as exclusões adequadas do antivírus do tecido de serviço:

| **Diretórios excluídos antivírus** |
| --- |
| Ficheiros de Programa\Microsoft Service Fabric |
| FabricDataRoot (a partir da configuração do cluster) |
| FabricLogRoot (a partir da configuração do cluster) |

| **Processos antivírus excluídos** |
| --- |
| Tecido.exe |
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

## <a name="validate-environment-using-testconfiguration-script"></a>Valide o ambiente usando o script TestConfiguration
O script TestConfiguration.ps1 pode ser encontrado no pacote autónomo. É usado como um Analisador de Boas Práticas para validar alguns dos critérios acima e deve ser usado como um cheque de sanidade para validar se um cluster pode ser implantado em um determinado ambiente. Se houver alguma falha, consulte a lista em [configuração](service-fabric-cluster-standalone-deployment-preparation.md) do ambiente para resolução de problemas.

Este script pode ser executado em qualquer máquina que tenha acesso ao administrador a todas as máquinas listadas como nós no ficheiro de configuração do cluster. A máquina em que este guião é executado não tem de fazer parte do cluster.

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

Atualmente, este módulo de teste de configuração não valida a configuração de segurança, pelo que este tem de ser feito de forma independente.

> [!NOTE]
> Estamos continuamente a fazer melhorias para tornar este módulo mais robusto, por isso, se houver um caso defeituoso ou em falta que você acredita não estar atualmente apanhado pela TestConfiguration, por favor, avise-nos através dos nossos [canais](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)de suporte .
>
>

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster autónomo em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md)
