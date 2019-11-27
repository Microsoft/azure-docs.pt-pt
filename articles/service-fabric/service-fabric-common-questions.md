---
title: Perguntas comuns sobre Microsoft Azure Service Fabric | Microsoft Docs
description: Perguntas frequentes sobre Service Fabric e suas respostas
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: dd514bb7c600c99518983855dae1d3b7fb8a1efb
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481644"
---
# <a name="commonly-asked-service-fabric-questions"></a>Perguntas frequentes sobre Service Fabric

Há muitas perguntas frequentes sobre o que Service Fabric pode fazer e como ele deve ser usado. Este documento aborda muitas dessas perguntas comuns e suas respostas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Configuração e gerenciamento de cluster

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Como fazer reverter meu Service Fabric certificado de cluster?

Reverter qualquer atualização para seu aplicativo requer detecção de falha de integridade antes de seu Service Fabric quorum de cluster confirmando a alteração; as alterações confirmadas só podem ser roladas para frente. O engenheiro de escalonamento por meio de serviços de atendimento ao cliente pode ser necessário para recuperar o cluster, se uma alteração de certificado não monitorado for introduzida.  A [atualização de aplicativo do Service Fabric](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) aplica [parâmetros de atualização de aplicativos](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)e oferece zero tempo de atualização de inatividade.  Seguindo nosso modo de atualização de aplicativo recomendado, o progresso automático por meio de domínios de atualização baseia-se nas verificações de integridade passando, revertendo automaticamente se a atualização de um serviço padrão falhar.
 
Se o cluster ainda estiver aproveitando a propriedade de impressão digital do certificado clássico em seu modelo do Resource Manager, é recomendável [alterar o cluster da impressão digital do certificado para o nome comum](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), para aproveitar os recursos modernos de gerenciamento de segredos.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Posso criar um cluster que abranja várias regiões do Azure ou meus próprios data centers?

Sim. 

A principal tecnologia de clustering de Service Fabric pode ser usada para combinar computadores em execução em qualquer lugar do mundo, desde que eles tenham conectividade de rede entre si. No entanto, criar e executar esse tipo de cluster pode ser complicado.

Se você estiver interessado neste cenário, incentivaremos você a entrar em contato por meio da [lista de problemas Service Fabric GitHub](https://github.com/azure/service-fabric-issues) ou por meio de seu representante de suporte para obter diretrizes adicionais. A equipe de Service Fabric está trabalhando para fornecer mais clareza, diretrizes e recomendações para esse cenário. 

Alguns aspetos a ter em conta: 

1. O recurso de Cluster Service Fabric no Azure é regional hoje, assim como os conjuntos de dimensionamento de máquinas virtuais nos quais o cluster se baseia. Isso significa que, no caso de uma falha regional, você pode perder a capacidade de gerenciar o cluster por meio do Azure Resource Manager ou do portal do Azure. Isso pode acontecer mesmo que o cluster permaneça em execução e você possa interagir com ele diretamente. Além disso, o Azure atualmente não oferece a capacidade de ter uma única rede virtual que possa ser usada entre regiões. Isso significa que um cluster de várias regiões no Azure requer um [endereços IP públicos para cada VM nos conjuntos de dimensionamento de VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) ou [gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Essas opções de rede têm impactos diferentes nos custos, no desempenho e no design de aplicativos de certo grau, portanto, a análise cuidadosa e o planejamento são necessários antes de se acumular nesse ambiente.
2. A manutenção, o gerenciamento e o monitoramento dessas máquinas podem se tornar complicados, especialmente quando distribuídas entre _tipos_ de ambientes, como entre diferentes provedores de nuvem ou entre recursos locais e o Azure. Deve-se ter cuidado para garantir que atualizações, monitoramento, gerenciamento e diagnóstico sejam compreendidos tanto para o cluster quanto para os aplicativos antes de executar cargas de trabalho de produção em um ambiente desse tipo. Se você já tiver experiência para resolver esses problemas no Azure ou em seus próprios datacenters, é provável que essas mesmas soluções possam ser aplicadas ao compilar ou executar o cluster de Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>OS nós de Service Fabric recebem automaticamente as atualizações do sistema operacional?

Você pode usar o recurso de [atualização de imagem do sistema operacional automático do conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) geralmente disponível hoje.

Para clusters que não são executados no Azure, nós [fornecemos um aplicativo](service-fabric-patch-orchestration-application.md) para corrigir os sistemas operacionais sob seus nós de Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Posso usar grandes conjuntos de dimensionamento de máquinas virtuais no cluster do meu it? 

**Resposta curta** -não. 

**Resposta longa** -embora os grandes conjuntos de dimensionamento de máquinas virtuais permitam dimensionar um conjunto de dimensionamento de máquinas virtuais até 1000 instâncias de VM, ele faz isso pelo uso de grupos de posicionamento (PGS). Os domínios de falha (FDs) e os domínios de atualização (UDs) são consistentes apenas em um grupo de posicionamento que o Service Fabric usa FDs e UDs para tomar decisões de posicionamento de suas instâncias de serviço/réplicas de serviço. Como o FDs e o UDs são comparáveis somente dentro de um grupo de posicionamento, o it não pode usá-lo. Por exemplo, se VM1 em PG1 tiver uma topologia de FD = 0 e VM9 em PG2 tiver uma topologia de FD = 4, isso não significa que VM1 e VM2 estejam em dois racks de hardware diferentes, portanto, o it não poderá usar os valores de FD nesse caso para tomar decisões de posicionamento.

Atualmente, há outros problemas com grandes conjuntos de dimensionamento de máquinas virtuais, como a falta de suporte para balanceamento de carga de nível 4. Consulte para obter [detalhes sobre conjuntos de dimensionamento grandes](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual é o tamanho mínimo de um cluster de Service Fabric? Por que ele não pode ser menor?

O tamanho mínimo com suporte para um cluster de Service Fabric que executa cargas de trabalho de produção é de cinco nós. Para cenários de desenvolvimento, damos suporte a um nó (otimizado para rápida experiência de desenvolvimento no Visual Studio) e cinco clusters de nós.

Exigimos que um cluster de produção tenha pelo menos 5 nós devido aos três motivos a seguir:
1. Mesmo quando nenhum serviço de usuário está em execução, um cluster de Service Fabric executa um conjunto de serviços de sistema com estado, incluindo o serviço de cadastramento e o serviço de Gerenciador de failover. Esses serviços do sistema são essenciais para que o cluster permaneça operacional.
2. Sempre colocamos uma réplica de um serviço por nó, portanto, o tamanho do cluster é o limite superior para o número de réplicas que um serviço (na verdade, uma partição) pode ter.
3. Como uma atualização de cluster desativará pelo menos um nó, queremos ter um buffer de pelo menos um nó, portanto, queremos que um cluster de produção tenha pelo menos *dois nós além do mínimo* . O mínimo é o tamanho do quorum de um serviço do sistema, conforme explicado abaixo.  

Queremos que o cluster esteja disponível em face de falha simultânea de dois nós. Para que um Cluster Service Fabric esteja disponível, os serviços do sistema devem estar disponíveis. Serviços de sistema com estado como serviço de nomenclatura e serviço de Gerenciador de failover, que controlam quais serviços foram implantados no cluster e onde estão atualmente hospedados, dependem da consistência forte. Essa consistência forte, por sua vez, depende da capacidade de adquirir um *Quorum* para qualquer atualização específica para o estado desses serviços, em que um quorum representa uma maioria estrita das réplicas (N/2 + 1) para um determinado serviço. Portanto, se quisermos ser resilientes contra a perda simultânea de dois nós (assim, perda simultânea de duas réplicas de um serviço do sistema), devemos ter ClusterSize-QuorumSize > = 2, o que força o tamanho mínimo a ser cinco. Para ver isso, considere que o cluster tem N nós e que há N réplicas de um serviço do sistema, um em cada nó. O tamanho do quorum para um serviço do sistema é (N/2 + 1). A desigualdade acima é semelhante a N-(N/2 + 1) > = 2. Há dois casos a serem considerados: quando N é par e quando N é ímpar. Se N for par, digamos N = 2\*m em que m > = 1, a desigualdade é semelhante a 2\*m-(2\*m/2 + 1) > = 2 ou m > = 3. O mínimo para N é 6 e é obtido quando m = 3. Por outro lado, se N for ímpar, digamos N = 2\*m + 1 em que m > = 1, a desigualdade é semelhante a 2\*m + 1-(2\*m + 1)/2 + 1) > = 2 ou 2\*m + 1-(m + 1) > = 2 ou m > = 2. O mínimo para N é 5 e isso é obtido quando m = 2. Portanto, entre todos os valores de N que satisfazem a desigualdade de ClusterSize-QuorumSize > = 2, o mínimo é 5.

Observe que, no argumento acima, assumimos que cada nó tem uma réplica de um serviço do sistema, portanto, o tamanho do quorum é calculado com base no número de nós no cluster. No entanto, ao alterar *TargetReplicaSetSize* , poderíamos tornar o tamanho do quorum menor que (N/2 + 1), que pode dar a impressão de que poderíamos ter um cluster menor que 5 nós e ainda ter dois nós extras acima do tamanho do quorum. Por exemplo, em um cluster de 4 nós, se definirmos o TargetReplicaSetSize como 3, o tamanho do quorum baseado em TargetReplicaSetSize será (3/2 + 1) ou 2, portanto, temos ClusterSize-QuorumSize = 4-2 > = 2. No entanto, não podemos garantir que o serviço do sistema estará no ou acima do quorum se perdermos qualquer par de nós simultaneamente, poderia ser que os dois nós que perdemos estavam hospedando duas réplicas, de modo que o serviço do sistema entrará em perda de quorum (tendo apenas uma única réplica à esquerda) o nd ficará indisponível.

Com esse plano de fundo, vamos examinar algumas configurações de cluster possíveis:

**Um nó**: essa opção não fornece alta disponibilidade, pois a perda do único nó por qualquer motivo significa a perda do cluster inteiro.

**Dois nós**: um quorum para um serviço implantado em dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica é perdida, é impossível criar um quorum. Como a execução de uma atualização de serviço requer a desativação temporária de uma réplica, essa não é uma configuração útil.

**Três nós**: com três nós (N = 3), o requisito para criar um quorum ainda é dois nós (3/2 + 1 = 2). Isso significa que você pode perder um nó individual e ainda manter o quorum, mas a falha simultânea de dois nós orientará os serviços do sistema na perda de quorum e fará com que o cluster fique indisponível.

**Quatro nós**: com quatro nós (N = 4), o requisito para criar um quorum é de três nós (4/2 + 1 = 3). Isso significa que você pode perder um nó individual e ainda manter o quorum, mas a falha simultânea de dois nós orientará os serviços do sistema na perda de quorum e fará com que o cluster fique indisponível.

**Cinco nós**: com cinco nós (N = 5), o requisito para criar um quorum ainda é três nós (5/2 + 1 = 3). Isso significa que você pode perder dois nós ao mesmo tempo e ainda manter o quorum para os serviços do sistema.

Para cargas de trabalho de produção, você deve ser resiliente a falhas simultâneas de pelo menos dois nós (por exemplo, um devido à atualização do cluster, um devido a outros motivos), portanto, cinco nós são necessários.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desativar meu cluster à noite/finais de semana para economizar custos?

Em geral, não. O Service Fabric armazena o estado em discos locais e efêmeros, o que significa que, se a máquina virtual for movida para um host diferente, os dados não serão movidos com ele. Na operação normal, isso não é um problema, pois o novo nó é atualizado por outros nós. No entanto, se você parar todos os nós e reiniciá-los mais tarde, haverá uma possibilidade significativa de que a maioria dos nós inicie em novos hosts e faça com que o sistema não possa ser recuperado.

Se você quiser criar clusters para testar seu aplicativo antes que ele seja implantado, recomendamos que você crie esses clusters dinamicamente como parte de seu [pipeline de implantação contínua/integração contínua](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como fazer atualizar meu sistema operacional (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Enquanto estamos trabalhando em uma experiência aprimorada, hoje, você é responsável pela atualização. Você deve atualizar a imagem do sistema operacional nas máquinas virtuais do cluster, uma VM por vez. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso criptografar discos de dados anexados em um tipo de nó de cluster (conjunto de dimensionamento de máquinas virtuais)?
Sim.  Para obter mais informações, consulte [criar um cluster com discos de dados anexados](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) e [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso usar VMs de baixa prioridade em um tipo de nó de cluster (conjunto de dimensionamento de máquinas virtuais)?
Não. Não há suporte para VMs de baixa prioridade. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Quais são os diretórios e processos que preciso excluir ao executar um programa antivírus em meu cluster?

| **Diretórios excluídos antivírus** |
| --- |
| Programa Files\Microsoft Service Fabric |
| FabricDataRoot (a partir de configuração de cluster) |
| FabricLogRoot (a partir de configuração de cluster) |

| **Processos de antivírus excluídos** |
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
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Como meu aplicativo pode se autenticar no keyvault para obter segredos?
Veja a seguir os meios para que seu aplicativo obtenha credenciais para autenticação no keyvault:

A. Durante o trabalho de criação/empacotamento de aplicativos, você pode efetuar pull de um certificado no pacote de dados do seu aplicativo de it e usá-lo para autenticar no keyvault.
B. Para hosts habilitados para MSI do conjunto de dimensionamento de máquinas virtuais, você pode desenvolver um SetupEntryPoint simples do PowerShell para seu aplicativo de it para obter [um token de acesso do ponto de extremidade do MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)e, em seguida, [recuperar seus segredos do keyvault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Design do aplicativo

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual é a melhor maneira de consultar dados entre partições de uma coleção confiável?

As coleções confiáveis são normalmente [particionadas](service-fabric-concepts-partitioning.md) para habilitar o scale out para maior desempenho e taxa de transferência. Isso significa que o estado de um determinado serviço pode ser distribuído entre dezenas ou centenas de computadores. Para executar operações nesse conjunto de dados completo, você tem algumas opções:

- Crie um serviço que consulta todas as partições de outro serviço para extrair os dados necessários.
- Crie um serviço que possa receber dados de todas as partições de outro serviço.
- Envie dados por push periodicamente de cada serviço para um repositório externo. Essa abordagem só será apropriada se as consultas que você estiver executando não fizerem parte da lógica de negócios principal, pois os dados do repositório externo ficarão obsoletos.
- Como alternativa, armazene dados que devem dar suporte à consulta em todos os registros diretamente em um repositório de dados em vez de em uma coleção confiável. Isso elimina o problema com dados obsoletos, mas não permite que as vantagens das coleções confiáveis sejam aproveitadas.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual é a melhor maneira de consultar dados em meus atores?

Os atores são projetados para serem unidades independentes de estado e computação, portanto, não é recomendável executar consultas amplas de estado de ator em tempo de execução. Se você tiver a necessidade de consultar em todo o conjunto de estado de ator, considere o:

- Substituir os serviços de ator por serviços confiáveis com estado, para que o número de solicitações de rede colete todos os dados do número de atores para o número de partições em seu serviço.
- Projetar seus atores para enviar periodicamente seu estado para um repositório externo para facilitar a consulta. Como acima, essa abordagem só será viável se as consultas que você estiver executando não forem necessárias para seu comportamento de tempo de execução.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Que quantidade de dados posso armazenar em uma coleção confiável?

Os Reliable Services normalmente são particionados, portanto, o valor que você pode armazenar é limitado apenas pelo número de máquinas que você tem no cluster e pela quantidade de memória disponível nessas máquinas.

Por exemplo, suponha que você tenha uma coleção confiável em um serviço com 100 partições e 3 réplicas, armazenando objetos com média de 1 KB de tamanho. Agora suponha que você tenha um cluster de 10 máquinas com 16 GB de memória por máquina. Para simplificar e ser conservador, suponha que o sistema operacional e os serviços do sistema, o tempo de execução de Service Fabric e seus serviços consomem 6 GB disso, deixando 10 GB disponíveis por computador ou 100 GB para o cluster.

Tendo em mente que cada objeto deve ser armazenado três vezes (uma primária e duas réplicas), você teria memória suficiente para aproximadamente 35 milhões objetos em sua coleção ao operar com capacidade total. No entanto, é recomendável ser resiliente à perda simultânea de um domínio de falha e de um domínio de atualização, que representa cerca de 1/3 de capacidade e reduziria o número para aproximadamente 23 milhões.

Observe que esse cálculo também pressupõe:

- A distribuição de dados entre as partições é aproximadamente uniforme ou que você está relatando métricas de carga para o Gerenciador de recursos de cluster. Por padrão, o Service Fabric carrega o saldo com base na contagem de réplicas. No exemplo anterior, isso colocaria 10 réplicas primárias e 20 réplicas secundárias em cada nó no cluster. Isso funciona bem para a carga que é distribuída uniformemente entre as partições. Se a carga não for uniforme, você deverá relatar o carregamento para que o Gerenciador de recursos possa empacotar réplicas menores juntas e permitir que réplicas maiores consumam mais memória em um nó individual.

- O serviço confiável em questão é o único estado de armazenamento no cluster. Como é possível implantar vários serviços em um cluster, você precisa estar atento aos recursos que cada um precisa para executar e gerenciar seu estado.

- Se o próprio cluster não está crescendo ou diminuindo. Se você adicionar mais máquinas, Service Fabric reequilibrará as réplicas para aproveitar a capacidade adicional até que o número de máquinas ultrapasse o número de partições em seu serviço, já que uma réplica individual não pode abranger máquinas. Por outro lado, se você reduzir o tamanho do cluster removendo computadores, suas réplicas serão empacotadas mais rigidamente e terão menos capacidade geral.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Que quantidade de dados posso armazenar em um ator?

Assim como acontece com os Reliable Services, a quantidade de dados que você pode armazenar em um serviço de ator é limitada apenas pelo espaço total em disco e pela memória disponível em todos os nós no cluster. No entanto, os atores individuais são mais eficazes quando são usados para encapsular uma pequena quantidade de estado e lógica de negócios associada. Como regra geral, um ator individual deve ter um estado medido em kilobytes.

## <a name="other-questions"></a>Outras perguntas

### <a name="how-does-service-fabric-relate-to-containers"></a>Como Service Fabric se relaciona a contêineres?

Os contêineres oferecem uma maneira simples de empacotar serviços e suas dependências, de forma que eles sejam executados consistentemente em todos os ambientes e possam operar de maneira isolada em um único computador. O Service Fabric oferece uma maneira de implantar e gerenciar serviços, incluindo [serviços que foram empacotados em um contêiner](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Você está planejando o Service Fabric de código-fonte aberto?

Temos partes com software livre de Service Fabric (estrutura de[Reliable Services](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [estrutura de Reliable Actors](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [bibliotecas de integração ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)e [CLI de Service Fabric](https://github.com/Azure/service-fabric-cli)) no GitHub e aceitam contribuições da Comunidade para esses projetos. 

[Recentemente, anunciamos](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) que planejamos abrir o código do Service Fabric em tempo de execução. Neste ponto, temos o [Service Fabric repositório](https://github.com/Microsoft/service-fabric/) no GitHub com ferramentas de teste e compilação do Linux, o que significa que você pode clonar o repositório, criar Service Fabric para Linux, executar testes básicos, abrir problemas e enviar solicitações de pull. Estamos trabalhando muito para obter o ambiente de Build do Windows migrado também, junto com um ambiente completo de CI.

Siga o [blog Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para obter mais detalhes conforme eles são anunciados.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [conceitos principais de Service Fabric](service-fabric-technical-overview.md) e [as práticas recomendadas](service-fabric-best-practices-overview.md) conceitos de malha Ice] (Service-Fabric-Technical-Overview.MD) e [as práticas recomendadas](service-fabric-best-practices-overview.md)
