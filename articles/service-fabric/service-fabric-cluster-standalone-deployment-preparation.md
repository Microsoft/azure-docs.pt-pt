---
title: Preparação de implantação de cluster autónomo
description: Documentação relacionada com a preparação do ambiente e a criação da configuração do cluster, a considerar antes da implantação de um cluster destinado a lidar com uma carga de trabalho de produção.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 9e5ad37d803b2042fd57b0a325570e69d7b73038
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842959"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planeie e prepare a sua implantação de cluster autónomo de tecido de serviço

<a id="preparemachines"></a>Execute os seguintes passos antes de criar o seu cluster.

## <a name="plan-your-cluster-infrastructure"></a>Planeie a sua infraestrutura de cluster
Está prestes a criar um cluster de Tecidos de Serviço em máquinas que "possui", para que possa decidir que tipo de falhas quer que o cluster sobreviva. Por exemplo, precisa de linhas de energia separadas ou ligações à Internet fornecidas a estas máquinas? Além disso, considere a segurança física destas máquinas. Onde estão as máquinas e quem precisa de acesso a elas? Depois de tomar estas decisões, pode logicamente mapear as máquinas para vários domínios de avaria (ver passo seguinte). O planeamento das infraestruturas para os clusters de produção está mais envolvido do que nos clusters de ensaio.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Determinar o número de domínios de avaria e de atualização de domínios
Um [ *domínio de avaria* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) é uma unidade física de falha e está diretamente relacionado com a infraestrutura física nos centros de dados. Um domínio de avaria consiste em componentes de hardware (computadores, interruptores, redes e muito mais) que partilham um único ponto de falha. Embora não exista um mapeamento 1:1 entre domínios de avaria e prateleiras, falando vagamente, cada rack pode ser considerado um domínio de falha.

Quando especificar FDs em ClusterConfig.jsligado, pode escolher o nome de cada FD. O Service Fabric suporta FDs hierárquicos, para que possa refletir a sua topologia de infraestrutura neles.  Por exemplo, os seguintes FDs são válidos:

* "FaultDomain": "fd:/Room1/Rack1/Machine1"
* "FaultDomain": "fd:/FD1"
* "FaultDomain": "fd:/Room1/Rack1/PDU1/M1"

Um *domínio de upgrade* (UD) é uma unidade lógica de nós. Durante as atualizações orquestradas do Service Fabric (seja uma atualização de aplicação ou uma atualização de cluster), todos os nós de uma UD são retirados para executar a atualização enquanto os nós em outros UDs permanecem disponíveis para servir pedidos. As atualizações de firmware que executa nas suas máquinas não honram os UDs, por isso deve fazer-lhes uma máquina de cada vez.

A forma mais simples de pensar sobre estes conceitos é considerar os FDs como a unidade de falha não planeada e UDs como a unidade de manutenção planeada.

Quando especificar UDs em ClusterConfig.jsligado, pode escolher o nome de cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Azul"

Para obter informações mais detalhadas sobre FDs e UDs, consulte [descrevendo um cluster de tecido de serviço.](service-fabric-cluster-resource-manager-cluster-description.md)

Um cluster de produção deve abranger pelo menos três FDs para ser apoiado num ambiente de produção, se tiver total controlo sobre a manutenção e gestão dos nós, ou seja, você é responsável pela atualização e substituição de máquinas. Para clusters em ambientes (isto é, instâncias VM da Amazon Web Services) onde você não tem controlo total sobre as máquinas, você deve ter um mínimo de cinco FDs no seu cluster. Cada FD pode ter um ou mais nós. Isto é para evitar problemas causados por atualizações e atualizações de máquinas, que dependendo do seu tempo, podem interferir com o funcionamento de aplicações e serviços em clusters.

## <a name="determine-the-initial-cluster-size"></a>Determinar o tamanho inicial do cluster

Geralmente, o número de nós no seu cluster é determinado com base nas necessidades do seu negócio, ou seja, quantos serviços e contentores estarão funcionando no cluster e quantos recursos precisa para sustentar as suas cargas de trabalho. Para os clusters de produção, recomendamos ter pelo menos cinco nós no seu cluster, abrangendo 5 FDs. No entanto, como descrito acima, se você tem controlo total sobre os seus nós e pode abranger três FDs, então três nós também devem fazer o trabalho.

Os agrupamentos de ensaio que executam cargas de trabalho declaradas devem ter três nós, enquanto que os agrupamentos de ensaio que apenas executam cargas de trabalho apátridas só precisam de um nó. Note-se também que, para fins de desenvolvimento, pode ter mais do que um nó numa determinada máquina. No entanto, num ambiente de produção, o Service Fabric suporta apenas um nó por máquina física ou virtual.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Prepare as máquinas que servirão de nó

Aqui estão as especificações recomendadas para máquinas num cluster de tecido de serviço:

* Um mínimo de 16 GB de RAM
* Um mínimo de 40 de ESPAÇO de disco disponível gb
* Um núcleo de 4 ou maior CPU
* Conectividade a uma rede ou redes seguras para todas as máquinas
* Windows Server OS instalado (versões válidas: 2012 R2, 2016, 1709 ou 1803). A versão 6.4.654.9590 do Tecido de Serviço e, mais tarde, também suporta o Server 2019 e o 1809.
* [.NET Quadro 4.5.1 ou superior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell?view=powershell-7)
* O [serviço RemoteRegistry](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) deve estar a funcionar em todas as máquinas
* **Unidade de instalação de tecido de serviço deve ser sistema de arquivo NTFS**
* **Serviços windows *Registos de desempenho & alertas* e *registo de eventos do Windows* devem ser [ativados](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))**.

> [!IMPORTANT]
> O administrador de cluster que implanta e configura o cluster deve ter [privilégios](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) de administrador em cada uma das máquinas. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Descarregue o pacote autónomo do Tecido de Serviço para Windows Server
[Baixar Link - Pacote Autónomo de Tecido](https://go.microsoft.com/fwlink/?LinkId=730690) de Serviço - Windows Server e desapertar o pacote, seja para uma máquina de implantação que não faça parte do cluster, quer para uma das máquinas que farão parte do seu cluster.

## <a name="modify-cluster-configuration"></a>Modificar a configuração do cluster
Para criar um cluster autónomo, tem de criar uma configuração de cluster autónoma ClusterConfig.jsno ficheiro, que descreve a especificação do cluster. Pode basear o ficheiro de configuração nos modelos encontrados no link abaixo. <br>
[Configurações de cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para obter mais informações sobre as secções deste ficheiro, consulte [as definições de Configuração para o cluster do Windows autónomo](service-fabric-cluster-manifest.md).

Abra uma das ClusterConfig.jsem ficheiros a partir do pacote que descarregou e modifique as seguintes definições:

| **Definição de configuração** | **Descrição** |
| --- | --- |
| **Nódias** |Os tipos de nó permitem-lhe separar os seus nós de cluster em vários grupos. Um aglomerado deve ter pelo menos um NodeType. Todos os nós de um grupo têm as seguintes características comuns: <br> **Nome** - Este é o nome do tipo nó. <br>**Portas endpoint** - Estes são vários pontos finais nomeados (portas) que estão associados a este tipo de nó. Pode utilizar qualquer número de porta que deseje, desde que não entrem em conflito com qualquer outra coisa neste manifesto e não estejam já a ser utilizados por qualquer outra aplicação em funcionamento na máquina/VM. <br> **Propriedades de colocação** - Estas descrevem propriedades para este tipo de nó que você usa como restrições de colocação para os serviços do sistema ou seus serviços. Estas propriedades são pares chave/valor definidos pelo utilizador que fornecem dados de meta-dado extra para um dado nó. Exemplos de propriedades de nó seria se o nó tem um disco rígido ou placa gráfica, o número de eixos no seu disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades** - As capacidades do nó definem o nome e a quantidade de um recurso particular que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB disponível por padrão. Estas capacidades são utilizadas em tempo de execução para garantir que os serviços que requerem quantidades específicas de recursos sejam colocados nos nós que têm esses recursos disponíveis nos montantes necessários.<br>**IsPrimary** - Se tiver mais do que um NodeType definido certifique-se de que apenas um está definido para o principal com o valor *verdadeiro*, que é onde os serviços do sistema funcionam. Todos os outros tipos de nós devem ser definidos para o valor *falso* |
| **Nós** |Estes são os detalhes de cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de avaria e domínio de upgrade do nó). As máquinas que pretende que o cluster seja criado precisam de ser listadas aqui com os seus endereços IP. <br> Se utilizar o mesmo endereço IP para todos os nós, então é criado um cluster de uma caixa, que pode utilizar para efeitos de teste. Não utilize clusters de caixa única para a implantação de cargas de trabalho de produção. |

Depois de a configuração do cluster ter todas as definições configuradas para o ambiente, pode ser testada contra o ambiente de cluster (passo 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuração de ambiente

Quando um administrador de cluster configura um cluster autónomo do Tecido de Serviço, o ambiente deve ser configurado com os seguintes critérios: <br>
1. O utilizador que cria o cluster deve ter privilégios de segurança ao nível do administrador para todas as máquinas que estejam listadas como nós no ficheiro de configuração do cluster.
2. Máquina a partir da qual o cluster é criado, bem como cada máquina de nó de agrupamento deve:
   * Ter SDK de tecido de serviço desinstalado
   * Ter tempo de funcionação do tecido de serviço desinstalado
   * Habilitar o serviço Windows Firewall (mpssvc)
   * Habilitar o Serviço de Registo Remoto (registo remoto)
   * Ter partilha de ficheiros (SMB) ativada
   * Ter portas necessárias abertas, com base em portas de configuração de cluster
   * Ter portas necessárias abertas para o serviço windows SMB e Registo Remoto: 135, 137, 138, 139 e 445
   * Ter conectividade de rede entre si
3. Nenhuma das máquinas de nó de agrupamento deve ser um controlador de domínio.
4. Se o cluster a ser implantado for um cluster seguro, valide os pré-requisitos de segurança necessários e esteja configurado corretamente contra a configuração.
5. Se as máquinas de cluster não estiverem acessíveis à Internet, desa um ponto na configuração do cluster:
   * Desativar a telemetria: Sob *propriedades* *definidas "enableTelemetry": falso*
   * Desativar a versão automática do Tecido descarregando & notificações de que a versão atual do cluster está a aproximar-se do fim do suporte: Em *propriedades* *definidas "fabricClusterAutoupgradeEnabled": falso*
   * Em alternativa, se o acesso à internet da rede for limitado a domínios listados em branco, os domínios abaixo são necessários para a atualização automática: go.microsoft.com download.microsoft.com

6. Definir exclusões antivírus de tecido de serviço adequadas:

| **Diretórios antivírus excluídos** |
| --- |
| Ficheiros de programas\Microsoft Service Fabric |
| FabricDataRoot (da configuração do cluster) |
| FabricLogRoot (da configuração do cluster) |

| **Processos antivírus excluídos** |
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

## <a name="validate-environment-using-testconfiguration-script"></a>Validar ambiente usando script testConfiguration
O TestConfiguration.ps1 script pode ser encontrado no pacote autónomo. É usado como um Analisador de Boas Práticas para validar alguns dos critérios acima e deve ser usado como um controlo de sanidade para validar se um cluster pode ser implantado em um determinado ambiente. Se houver alguma falha, consulte a lista em [Ambiente configurado](service-fabric-cluster-standalone-deployment-preparation.md) para resolução de problemas.

Este script pode ser executado em qualquer máquina que tenha acesso ao administrador a todas as máquinas que estão listadas como nós no ficheiro de configuração do cluster. A máquina em que este script é executado não tem de fazer parte do cluster.

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

Atualmente este módulo de teste de configuração não valida a configuração de segurança, pelo que este tem de ser feito de forma independente.

> [!NOTE]
> Estamos continuamente a fazer melhorias para tornar este módulo mais robusto, por isso, se houver um caso defeituoso ou em falta que acredita não ter sido apanhado pela TestConfiguration, por favor, informe-nos através dos [nossos canais de suporte.](./service-fabric-support.md)
>
>

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster autónomo em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md)
