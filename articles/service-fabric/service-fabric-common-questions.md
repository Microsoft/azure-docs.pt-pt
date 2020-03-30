---
title: Perguntas comuns sobre o Tecido de Serviço Microsoft Azure
description: Perguntas frequentes sobre o Tecido de Serviço, incluindo capacidades, casos de uso e cenários comuns.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254891"
---
# <a name="commonly-asked-service-fabric-questions"></a>Perguntas comuns acerca do Service Fabric

Há muitas perguntas comumente feitas sobre o que o Service Fabric pode fazer e como deve ser usado. Este documento aborda muitas dessas perguntas comuns e as suas respostas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Configuração e gestão de clusters

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Como retiro o meu certificado de cluster Service Fabric?

Reverter qualquer atualização para a sua aplicação requer deteção de falhas de saúde antes do quórum do cluster de tecido de serviço cometer a alteração; alterações comprometidas só podem ser lançadas para a frente. O engenheiro de escalada através dos Serviços de Apoio ao Cliente pode ser obrigado a recuperar o seu cluster, se for introduzida uma alteração de certificado de rutura não monitorizada.  [A atualização de aplicações do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) aplica parâmetros de atualização da [aplicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)e oferece zero promessas de atualização de tempo de inatividade.  Seguindo o nosso modo de atualização de aplicações recomendado, o progresso automático através de domínios de atualização baseia-se na passagem de verificações de saúde, recuando automaticamente se a atualização de um serviço predefinido falhar.
 
Se o seu cluster ainda estiver a aproveitar a propriedade clássica de impressão digital do certificado no seu modelo de Gestor de Recursos, recomenda-se que [mude o cluster da impressão digital do certificado para o nome comum,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)para alavancar as funcionalidades de gestão de segredos modernos.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Posso criar um cluster que se estende por várias regiões azure ou os meus próprios centros de dados?

Sim. 

A tecnologia de clustering core Service Fabric pode ser usada para combinar máquinas que correm em qualquer parte do mundo, desde que tenham conectividade de rede entre si. No entanto, construir e gerir tal aglomerado pode ser complicado.

Se estiver interessado neste cenário, encorajamo-lo a entrar em contacto através da Lista de Problemas do [Tecido de Serviço GitHub](https://github.com/azure/service-fabric-issues) ou através do seu representante de suporte, a fim de obter orientação adicional. A equipa de Tecido saqueado está a trabalhar para fornecer clareza adicional, orientação e recomendações para este cenário. 

Algumas coisas a considerar: 

1. O recurso de cluster Service Fabric em Azure é regional hoje em dia, assim como os conjuntos de escala de máquinavirtual em que o cluster é construído. Isto significa que em caso de falha regional poderá perder a capacidade de gerir o cluster através do Gestor de Recursos Azure ou do portal Azure. Isto pode acontecer mesmo que o aglomerado continue a funcionar e tu serias capaz de interagir diretamente com ele. Além disso, o Azure hoje não oferece a capacidade de ter uma única rede virtual que seja utilizável em todas as regiões. Isto significa que um cluster multi-região em Azure requer [endereços IP públicos para cada VM nos conjuntos](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) de escala VM ou [Gateways VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Estas escolhas de networking têm diferentes impactos nos custos, desempenho e, em certa medida, no design de aplicações, pelo que é necessária uma análise cuidadosa e planeamento antes de se levantar em tal ambiente.
2. A manutenção, gestão e monitorização destas máquinas podem tornar-se complicadas, especialmente quando atravessadas por _tipos_ de ambientes, como entre diferentes fornecedores de nuvem ou entre recursos no local e O Azure. Há que ter cuidado para garantir que as atualizações, o acompanhamento, a gestão e os diagnósticos sejam entendidos tanto para o cluster como para as aplicações antes de executar as cargas de trabalho de produção em tal ambiente. Se já tem experiência em resolver estes problemas no Azure ou nos seus próprios datacenters, então é provável que essas mesmas soluções possam ser aplicadas ao construir ou executar o seu cluster de Tecido de Serviço. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Os nós do Tecido de Serviço recebem automaticamente atualizações de OS?

Pode utilizar a funcionalidade de [atualização automática de imagem do Os](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) O.S. De modo geral disponível hoje em dia.

Para clusters que NÃO são executados em Azure, [fornecemos uma aplicação](service-fabric-patch-orchestration-application.md) para remendar os sistemas operativos por baixo dos seus nós de Tecido de Serviço.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Posso usar grandes conjuntos de escala de máquinas virtuais no meu cluster SF? 

**Resposta curta** - Não. 

**Resposta longa** - Embora os grandes conjuntos de escala de máquinavirtual lhe permitam escalar uma escala virtual de máquina seletiva até 1000 casos de VM, fá-lo através da utilização de Grupos de Colocação (PGs). Os domínios de avaria (FDs) e os domínios de atualização (UDs) só são consistentes dentro de um grupo de colocação O tecido de serviço utiliza FDs e UDs para tomar decisões de colocação das suas réplicas de serviço/instâncias de serviço. Uma vez que os FDs e UDs são comparáveis apenas dentro de um grupo de colocação, a SF não pode usá-lo. Por exemplo, se o VM1 em PG1 tiver uma topologia de FD=0 e VM9 em PG2 tem uma topologia de FD=4, não significa que VM1 e VM2 estejam em duas prateleiras de hardware diferentes, por isso a SF não pode usar os valores de FD neste caso para tomar decisões de colocação.

Existem outros problemas com grandes conjuntos de escala de máquinavirtual atualmente, como a falta de suporte de equilíbrio de carga nível 4. Consulte [para mais detalhes em conjuntos](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de grande escala



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual é o tamanho mínimo de um cluster de tecido de serviço? Por que não pode ser menor?

O tamanho mínimo suportado para um cluster de fabricação de tecido de serviço que executa cargas de produção é de cinco nós. Para cenários de dev, apoiamos um nó (otimizado para uma experiência de desenvolvimento rápido no Estúdio Visual) e cinco clusters de nós.

Exigimos que um aglomerado de produção tenha pelo menos 5 nós devido às seguintes três razões:
1. Mesmo quando nenhum serviço de utilizador está em execução, um cluster de Tecido de Serviço executa um conjunto de serviços de sistema estatais, incluindo o serviço de nomeação e o serviço de gestor de failover. Estes serviços de sistema são essenciais para que o cluster permaneça operacional.
2. Colocamos sempre uma réplica de um serviço por nó, por isso o tamanho do cluster é o limite superior para o número de réplicas que um serviço (na verdade uma partição) pode ter.
3. Uma vez que uma atualização do cluster vai derrubar pelo menos um nó, queremos ter um tampão de pelo menos um nó, por isso, queremos que um cluster de produção tenha pelo menos dois nós para *além* do mínimo. O mínimo é o tamanho quórum de um serviço de sistema, como explicado abaixo.  

Queremos que o cluster esteja disponível face à falha simultânea de dois nós. Para que um cluster de Tecido de Serviço esteja disponível, os serviços do sistema devem estar disponíveis. Serviços de sistema sinuosos como serviço de nomeação e serviço de gestor de failover, que rastreiam os serviços que foram implantados para o cluster e onde estão atualmente hospedados, dependem de uma forte consistência. Essa forte consistência, por sua vez, depende da capacidade de adquirir um *quórum* para qualquer atualização dada ao estado desses serviços, onde um quórum representa uma maioria estrita das réplicas (N/2+1) para um determinado serviço. Assim, se queremos ser resilientes contra a perda simultânea de dois nós (assim perda simultânea de duas réplicas de um serviço de sistema), temos de ter clusterSize - QuorumSize >= 2, o que força o tamanho mínimo a ser cinco. Para ver isso, considere que o cluster tem nós N e há réplicas N de um serviço de sistema - um em cada nó. O tamanho do quórum para um serviço de sistema é (N/2 + 1). A desigualdade acima parece N - (N/2 + 1) >= 2. Há dois casos a considerar: quando N é par e quando N é estranho. Se N é mesmo,\*digamos N = 2 m onde m\*>=\*1, a desigualdade parece 2 m - (2 m/2 + 1) >= 2 ou m >= 3. O mínimo para N é 6 e que é alcançado quando m = 3. Por outro lado, se N é estranho, diga N =\*2 m+1 onde\*m >= 1, a desigualdade parece 2 m+1 - (2\*m+1)/2 + 1 ) >= 2 ou 2\*m+1 - (m+1) >= 2 ou m >= 2. O mínimo para N é 5 e que é alcançado quando m = 2. Portanto, entre todos os valores de N que satisfazem o ClusterSize de desigualdade - QuorumSize >= 2, o mínimo é 5.

Note-se, no argumento acima assumimos que cada nó tem uma réplica de um serviço de sistema, assim o tamanho do quórum é calculado com base no número de nós no cluster. No entanto, alterando *targetReplicaSetSize* poderíamos fazer o tamanho do quórum menos do que (N/2+1) o que pode dar a impressão de que poderíamos ter um cluster menor que 5 nós e ainda ter 2 nós extra acima do tamanho do quórum. Por exemplo, num cluster de 4 nós, se definirmos o TargetReplicaSetSize para 3, o tamanho quórum baseado no TargetReplicaSetSize é (3/2 + 1) ou 2, portanto temos ClusterSize - QuorumSize = 4-2 >= 2. No entanto, não podemos garantir que o serviço de sistema estará em quórum ou acima se perdermos qualquer par de nós simultaneamente, pode ser que os dois nós que perdemos estivessem a acolher duas réplicas, pelo que o serviço do sistema entrará em perda de quórum (tendo apenas uma réplica restante) e ficará indisponível.

Com esse pano de fundo, vamos examinar algumas possíveis configurações de cluster:

**Um nó:** esta opção não proporciona uma elevada disponibilidade, uma vez que a perda do nó único por qualquer motivo significa a perda de todo o cluster.

**Dois nós:** um quórum para um serviço implantado em dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica se perde, é impossível criar um quórum. Uma vez que a realização de uma atualização de serviço requer temporariamente a desativação de uma réplica, esta não é uma configuração útil.

**Três nós**: com três nós (N=3), a exigência de criar um quórum ainda é de dois nós (3/2 + 1 = 2). Isto significa que pode perder um nó individual e ainda manter o quórum, mas a falha simultânea de dois nós irá levar os serviços do sistema à perda de quórum e fará com que o cluster fique indisponível.

**Quatro nós**: com quatro nós (N=4), a exigência de criar um quórum é de três nós (4/2 + 1 = 3). Isto significa que pode perder um nó individual e ainda manter o quórum, mas a falha simultânea de dois nós irá levar os serviços do sistema à perda de quórum e fará com que o cluster fique indisponível.

**Cinco nós**: com cinco nós (N=5), a exigência de criar um quórum ainda é de três nós (5/2 + 1 = 3). Isto significa que pode perder dois nós ao mesmo tempo e ainda manter quórum para os serviços do sistema.

Para cargas de trabalho de produção, deve ser resiliente a uma falha simultânea de pelo menos dois nós (por exemplo, um devido à atualização do cluster, um por outras razões), pelo que são necessários cinco nós.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desligar o meu agrupamento à noite/fins de semana para poupar custos?

Geralmente, não. As lojas Service Fabric afirmam em discos locais e efémeros, o que significa que se a máquina virtual for movida para um hospedeiro diferente, os dados não se movem com ele. Em funcionamento normal, isso não é um problema, uma vez que o novo nó é atualizado por outros nós. No entanto, se parar todos os nós e reiniciá-los mais tarde, existe uma possibilidade significativa de que a maioria dos nós comecem em novos anfitriões e tornem o sistema incapaz de recuperar.

Se quiser criar clusters para testar a sua aplicação antes de ser implementada, recomendamos que crie esses clusters dinamicamente como parte do seu pipeline de [integração contínua/implantação contínua](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como atualizo o meu Sistema Operativo (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Enquanto estamos a trabalhar numa experiência melhorada, hoje, és responsável pela atualização. Tem de atualizar a imagem de OS nas máquinas virtuais do cluster um VM de cada vez. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso encriptar discos de dados anexados num tipo de nó de cluster (conjunto de escala de máquina virtual)?
Sim.  Para obter mais informações, consulte Criar um cluster com discos de [dados anexados](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) e encriptação de [disco azure para conjuntos](../virtual-machine-scale-sets/disk-encryption-overview.md)de escala de máquina virtual .

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso usar VMs de baixa prioridade num tipo de nó de cluster (conjunto de escala de máquina virtual)?
Não. Os VM sde baixa prioridade não são apoiados. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Quais são os diretórios e processos que preciso excluir quando executo um programa antivírus no meu cluster?

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
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Como pode a minha aplicação autenticar o KeyVault para obter segredos?
Seguem-se os meios para a sua aplicação obter credenciais para autenticação no KeyVault:

R. Durante o trabalho de construção/embalagem de aplicações, pode colocar um certificado no pacote de dados da sua aplicação SF e usá-lo para autenticar o KeyVault.
B. Para os anfitriões ativados por msi de escala virtual, pode desenvolver um simples PowerShell SetupEntryPoint para a sua aplicação SF para obter [um sinal de acesso do ponto final do MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)e, em seguida, recuperar os seus [segredos do KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Design de aplicações

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual é a melhor maneira de consultar dados através de divisórias de uma Coleção Fiável?

Coleções fiáveis são tipicamente [divididas](service-fabric-concepts-partitioning.md) para permitir a escala para um maior desempenho e rendimento. Isto significa que o Estado para um determinado serviço pode ser espalhado por dezenas ou centenas de máquinas. Para efetuar operações sobre esse conjunto completo de dados, tem algumas opções:

- Crie um serviço que questione todas as divisórias de outro serviço para puxar os dados necessários.
- Criar um serviço que possa receber dados de todas as divisórias de outro serviço.
- Empurre periodicamente os dados de cada serviço para uma loja externa. Esta abordagem só é apropriada se as consultas que está a realizar não fizerem parte da sua lógica de negócio principal, uma vez que os dados da loja externa serão fracos.
- Em alternativa, guarde dados que devem suportar a consulta em todos os registos diretamente numa loja de dados e não numa recolha fiável. Isto elimina o problema com dados antigos, mas não permite que as vantagens de coleções fiáveis sejam alavancadas.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual é a melhor maneira de consultar dados através dos meus atores?

Os atores são projetados para serem unidades independentes de estado e computação, pelo que não é recomendado realizar amplas consultas de estado ator em tempo de execução. Se você tem a necessidade de consultar em todo o conjunto de estado ator, você deve considerar qualquer um:

- Substituir os seus serviços de ator por serviços confiáveis e imponentes, de modo a que o número de pedidos de rede recolha todos os dados do número de atores para o número de divisórias no seu serviço.
- Conceber os seus atores para empurrar periodicamente o seu estado para uma loja externa para uma consulta mais fácil. Como acima, esta abordagem só é viável se as consultas que está a realizar não forem necessárias para o seu comportamento de tempo de corrido.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quantos dados posso armazenar numa Coleção Fiável?

Os serviços fiáveis são normalmente divididos, pelo que o valor que pode armazenar é limitado apenas pelo número de máquinas que tem no cluster, e pela quantidade de memória disponível nessas máquinas.

Como exemplo, suponha que você tem uma coleção confiável em um serviço com 100 divisórias e 3 réplicas, armazenando objetos que média de 1 kb de tamanho. Agora suponha que tem um aglomerado de 10 máquinas com 16gb de memória por máquina. Para a simplicidade e para ser conservador, assuma que o sistema operativo e os serviços de sistema, o tempo de funcionamento do Tecido de Serviço, e os seus serviços consomem 6gb disso, deixando 10gb disponíveis por máquina, ou 100 gb para o cluster.

Tendo em conta que cada objeto deve ser armazenado três vezes (uma primária e duas réplicas), teria memória suficiente para cerca de 35 milhões de objetos na sua coleção quando operasse em plena capacidade. No entanto, recomendamos ser resiliente à perda simultânea de um domínio de falha e de um domínio de atualização, que representa cerca de 1/3 de capacidade, e reduziria o número para cerca de 23 milhões.

Note que este cálculo também assume:

- Que a distribuição de dados através das divisórias é aproximadamente uniforme ou que está a reportar métricas de carga ao Cluster Resource Manager. Por padrão, o Tecido de Serviço carrega o equilíbrio com base na contagem de réplicas. No exemplo anterior, isso colocaria 10 réplicas primárias e 20 réplicas secundárias em cada nó do cluster. Isso funciona bem para a carga que é distribuída uniformemente pelas divisórias. Se a carga não for igual, deve reportar carga para que o Gestor de Recursos possa embalar réplicas menores e permitir que réplicas maiores consumam mais memória num nó individual.

- Que o serviço fiável em questão é o único estado de armazenamento no aglomerado. Uma vez que pode implementar vários serviços para um cluster, precisa de estar atento aos recursos que cada um precisa para gerir e gerir o seu estado.

- Que o aglomerado em si não está a crescer ou a encolher. Se adicionar mais máquinas, o Tecido de Serviço reequilibrará as suas réplicas para alavancar a capacidade adicional até que o número de máquinas ultrapasse o número de divisórias no seu serviço, uma vez que uma réplica individual não pode abranger máquinas. Em contraste, se reduzir o tamanho do cluster removendo as máquinas, as suas réplicas são embaladas com mais força e têm menos capacidade global.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quantos dados posso armazenar num ator?

Tal como acontece com serviços fiáveis, a quantidade de dados que pode armazenar num serviço de ator é limitada apenas pelo espaço total do disco e memória disponível em todos os nós do seu cluster. No entanto, os atores individuais são mais eficazes quando são usados para encapsular uma pequena quantidade de lógica empresarial estatal e associada. Regra geral, um ator individual deve ter um estado medido em quilobytes.

## <a name="other-questions"></a>Outras questões

### <a name="how-does-service-fabric-relate-to-containers"></a>Como é que o Tecido de Serviço se relaciona com os contentores?

Os contentores oferecem uma forma simples de embalar serviços e suas dependências de modo a que funcionem de forma consistente em todos os ambientes e possam operar de forma isolada numa única máquina. A Service Fabric oferece uma forma de implantar e gerir serviços, incluindo [serviços que foram embalados num contentor.](service-fabric-containers-overview.md)

### <a name="are-you-planning-to-open-source-service-fabric"></a>Está a planear abrir o serviço de tecido?

Temos partes abertas de Tecido de Serviço ( quadro de[serviços fiáveis,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [enquadramento de atores fiáveis,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [bibliotecas de integração ASP.NET Core,](https://github.com/Azure/service-fabric-aspnetcore) [Service Fabric Explorer,](https://github.com/Azure/service-fabric-explorer)e [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) no GitHub e aceitamos contribuições comunitárias para esses projetos. 

Recentemente [anunciamos](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) que planeamos abrir o tempo de execução do Tecido de Serviço. Neste ponto temos o [repo](https://github.com/Microsoft/service-fabric/) de tecido de serviço no GitHub com ferramentas de construção e teste Linux, o que significa que você pode clonar o repo, construir Tecido de Serviço para Linux, executar testes básicos, questões abertas e apresentar pedidos de pull. Estamos a trabalhar arduamente para que o ambiente de construção do Windows também emigrado, juntamente com um ambiente ci completo.

Siga o [blog Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para mais detalhes à medida que forem anunciados.

## <a name="next-steps"></a>Passos seguintes

Conheça os [conceitos core Service Fabric](service-fabric-technical-overview.md) e [as melhores práticas](service-fabric-best-practices-overview.md)
