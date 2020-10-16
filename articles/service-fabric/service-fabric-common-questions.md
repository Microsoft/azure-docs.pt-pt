---
title: Perguntas comuns sobre o Microsoft Azure Service Fabric
description: Perguntas frequentes sobre o Service Fabric, incluindo capacidades, casos de uso e cenários comuns.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 1655a8ed03b1f678cc5dba0a165e0bcca1d2517a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292856"
---
# <a name="commonly-asked-service-fabric-questions"></a>Perguntas comuns acerca do Service Fabric

Há muitas perguntas comumente sobre o que o Service Fabric pode fazer e como deve ser usado. Este documento abrange muitas dessas perguntas comuns e as suas respostas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Configuração e gestão de clusters

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Como posso reverter o meu certificado de cluster de tecido de serviço?

Reverter qualquer atualização para a sua aplicação requer uma deteção de falhas de saúde antes do quórum do cluster de tecido de serviço que comete a alteração; alterações comprometidas só podem ser lançadas para a frente. O engenheiro de escalada através dos Serviços de Apoio ao Cliente, pode ser necessário para recuperar o seu cluster, se tiver sido introduzida uma alteração não monitorizada do certificado de quebra.  [A atualização de aplicações da Service Fabric](./service-fabric-application-upgrade.md?branch=master) aplica [parâmetros de atualização de aplicações](./service-fabric-application-upgrade-parameters.md?branch=master)e oferece zero promessa de upgrade de tempo de inatividade.  Seguindo o modo monitorizado pela atualização da aplicação recomendada, o progresso automático através de domínios de atualização baseia-se na passagem de verificações de saúde, recuando automaticamente se a atualização de um serviço predefinido falhar.
 
Se o seu cluster ainda estiver aproveitando a propriedade clássica de impressão digital de certificado no seu modelo de Gestor de Recursos, recomenda-lhe alterar [o cluster da impressão digital do certificado para o nome comum,](./service-fabric-cluster-change-cert-thumbprint-to-cn.md)para alavancar as funcionalidades de gestão de segredos modernos.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Posso criar um cluster que abrange várias regiões do Azure ou os meus próprios centros de dados?

Sim. 

A tecnologia de clustering core Service Fabric pode ser usada para combinar máquinas que correm em qualquer parte do mundo, desde que tenham conectividade de rede entre si. No entanto, construir e executar tal aglomerado pode ser complicado.

Se estiver interessado neste cenário, encorajamo-lo a entrar em contacto através da Lista de Problemas do [GitHub de Tecido de Serviço](https://github.com/azure/service-fabric-issues) ou através do seu representante de suporte, a fim de obter orientações adicionais. A equipa da Service Fabric está a trabalhar para fornecer clareza adicional, orientação e recomendações para este cenário. 

Algumas coisas a considerar: 

1. O recurso de cluster de Tecido de Serviço em Azure é regional hoje em dia, assim como os conjuntos de escala de máquina virtual em que o cluster é construído. Isto significa que em caso de falha regional poderá perder a capacidade de gerir o cluster através do Azure Resource Manager ou do portal Azure. Isto pode acontecer mesmo que o cluster continue a funcionar e tu sejas capaz de interagir diretamente com ele. Além disso, o Azure hoje em dia não oferece a capacidade de ter uma única rede virtual que seja utilizável em todas as regiões. Isto significa que um cluster multi-região em Azure requer [endereços IP públicos para cada VM nos conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) ou [gateways Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Estas escolhas de networking têm impactos diferentes nos custos, desempenho e, em certa medida, design de aplicações, pelo que é necessária uma análise e planeamento cuidadosos antes de se levantar em tal ambiente.
2. A manutenção, gestão e monitorização destas máquinas podem tornar-se complicadas, especialmente quando abrangedas por _tipos_ de ambientes, como entre diferentes fornecedores de nuvem ou entre recursos no local e Azure. Há que ter em ação para garantir que as atualizações, a monitorização, a gestão e os diagnósticos sejam compreendidos tanto para o cluster como para as aplicações antes de executar cargas de trabalho de produção num ambiente deste tipo. Se já tem experiência em resolver estes problemas no Azure ou dentro dos seus próprios centros de dados, então é provável que essas mesmas soluções possam ser aplicadas ao construir ou executar o seu cluster de Tecido de Serviço. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Os nós de Tecido de Serviço recebem automaticamente atualizações de SISTEMA?

Pode utilizar a [atualização automática de imagem do sistema operativo automático Os,](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) geralmente disponível hoje.

Para clusters que NÃO são executados em Azure, [fornecemos uma aplicação](service-fabric-patch-orchestration-application.md) para corrigir os sistemas operativos por baixo dos seus nós de Tecido de Serviço.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Posso usar grandes conjuntos de balanças de máquinas virtuais no meu cluster SF? 

**Resposta curta** - Não. 

**Resposta Longa** - Embora os grandes conjuntos de balanças de máquinas virtuais permitam escalar uma balança de máquina virtual configurada até 1000 instâncias VM, fá-lo através da utilização de Grupos de Colocação (PGs). Os domínios de avaria (FDs) e os domínios de atualização (UDs) só são consistentes dentro de um grupo de colocação O tecido de serviço utiliza FDs e UDs para tomar decisões de colocação das suas réplicas de serviço/instâncias de serviço. Uma vez que os FDs e uDs são comparáveis apenas dentro de um grupo de colocação, a SF não pode usá-lo. Por exemplo, Se o VM1 em PG1 tem uma topologia de FD=0 e VM9 em PG2 tem uma topologia de FD=4, não significa que VM1 e VM2 estejam em duas prateleiras de hardware diferentes, daí que a SF não possa usar os valores de FD neste caso para tomar decisões de colocação.

Existem outros problemas com grandes conjuntos de escala de máquina virtual atualmente, como a falta de suporte de equilíbrio de carga nível 4. Consulte para [mais detalhes em conjuntos de grande escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual é o tamanho mínimo de um cluster de tecido de serviço? Por que não pode ser menor?

O tamanho mínimo suportado para um cluster de tecido de serviço que executa cargas de trabalho de produção é de cinco nós. Para cenários de dev, apoiamos um nó (otimizado para uma experiência de desenvolvimento rápido em Visual Studio) e cinco clusters de nó.

Exigimos que um cluster de produção tenha pelo menos cinco nós devido às seguintes três razões:
1. Mesmo quando nenhum serviço de utilizador está em execução, um cluster De Tecido de Serviço executa um conjunto de serviços de sistema estatais, incluindo o serviço de nomeação e o serviço de gerente de failover. Estes serviços de sistema são essenciais para que o cluster permaneça operacional.
2. Colocamos sempre uma réplica de um serviço por nó, por isso o tamanho do cluster é o limite superior para o número de réplicas que um serviço (na verdade uma partição) pode ter.
3. Uma vez que uma atualização de cluster vai derrubar pelo menos um nó, queremos ter um tampão de pelo menos um nó, portanto, queremos que um cluster de produção tenha pelo menos dois nós para *além* do mínimo. O mínimo é o tamanho do quórum de um serviço de sistema, como explicado abaixo.  

Queremos que o cluster esteja disponível face à falha simultânea de dois nós. Para que um cluster de Tecido de Serviço esteja disponível, os serviços do sistema devem estar disponíveis. Os serviços de sistema estatal, como o serviço de nomeação e o serviço de gerente de failover, que rastreiam os serviços que foram implantados no cluster e onde estão atualmente hospedados, dependem de uma forte consistência. Essa forte consistência, por sua vez, depende da capacidade de adquirir um *quórum* para qualquer atualização dada ao estado desses serviços, onde um quórum representa uma maioria rigorosa das réplicas (N/2+1) para um determinado serviço. Assim, se queremos ser resilientes contra a perda simultânea de dois nós (assim perda simultânea de duas réplicas de um serviço de sistema), temos de ter ClusterSize - QuorumSize >= 2, que obriga o tamanho mínimo a cinco. Para ver isso, considere que o cluster tem nós N e há réplicas N de um serviço de sistema - um em cada nó. O tamanho do quórum para um serviço de sistema é (N/2 +1). A desigualdade acima parece N - (N/2 + 1) >= 2. Há dois casos a considerar: quando N é par e quando N é estranho. Se N estiver em patamar, digamos N = 2 \* m onde m >= 1, a desigualdade parece 2 m - \* (2 \* m/2 + 1) >= 2 ou m >= 3. O mínimo para N é 6 e que é alcançado quando m = 3. Por outro lado, se N é estranho, digamos N = 2 \* m+1 onde m >= 1, a desigualdade parece 2 \* m+1 - ( (2 \* m+1)/2 + 1 ) >= 2 ou 2 \* m+1 - (m+1) >= 2 ou m >= 2. O mínimo para N é 5 e que é alcançado quando m = 2. Portanto, entre todos os valores de N que satisfazem a desigualdade ClusterSize - QuorumSize >= 2, o mínimo é 5.

Note-se que, no argumento acima assumimos que cada nó tem uma réplica de um serviço de sistema, pelo que o tamanho do quórum é calculado com base no número de nós no cluster. No entanto, ao mudar *o TargetReplicaSetSize* poderíamos fazer o tamanho do quórum menos do que (N/2+1) o que poderia dar a impressão de que poderíamos ter um cluster menor que 5 nós e ainda ter 2 nós extra acima do tamanho do quórum. Por exemplo, num cluster de 4 nós, se definirmos o TargetReplicaSetSize para 3, o tamanho do quórum baseado no TargetReplicaSetSize é (3/2 + 1) ou 2, assim temos ClusterSize - QurumSize = 4-2 >= 2. No entanto, não podemos garantir que o serviço de sistema estará em quórum ou acima se perdermos qualquer par de nós simultaneamente, pode ser que os dois nós que perdemos estejam a alojar duas réplicas, pelo que o serviço do sistema entrará em perda de quórum (tendo apenas uma réplica à esquerda) e ficará indisponível.

Com este fundo, vamos examinar algumas possíveis configurações de cluster:

**Um nó:** esta opção não proporciona elevada disponibilidade uma vez que a perda do nó único por qualquer motivo significa a perda de todo o cluster.

**Dois nós:** um quórum para um serviço implantado em dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica é perdida, é impossível criar um quórum. Uma vez que realizar uma atualização de serviço requer temporariamente retirar uma réplica, esta não é uma configuração útil.

**Três nós**: com três nós (N=3), a exigência de criar um quórum ainda é dois nós (3/2 + 1 = 2). Isto significa que pode perder um nó individual e ainda manter o quórum, mas a falha simultânea de dois nós irá conduzir os serviços do sistema à perda de quórum e fará com que o cluster fique indisponível.

**Quatro nós**: com quatro nós (N=4), a exigência de criar um quórum é três nós (4/2 + 1 = 3). Isto significa que pode perder um nó individual e ainda manter o quórum, mas a falha simultânea de dois nós irá conduzir os serviços do sistema à perda de quórum e fará com que o cluster fique indisponível.

**Cinco nós**: com cinco nós (N=5), a exigência de criar um quórum ainda é três nós (5/2 + 1 = 3). Isto significa que pode perder dois nós ao mesmo tempo e manter o quórum para os serviços do sistema.

Para a carga de trabalho de produção, deve ser resiliente à falha simultânea de pelo menos dois nós (por exemplo, um devido à atualização do cluster, um por outras razões), pelo que são necessários cinco nós.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desligar o meu aglomerado à noite/fins de semana para poupar custos?

Geralmente, não. A Service Fabric armazena o estado em discos locais e efémeros, o que significa que se a máquina virtual for movida para um hospedeiro diferente, os dados não se movem com ele. Em funcionamento normal, isso não é um problema, uma vez que o novo nó é atualizado por outros nós. No entanto, se parar todos os nós e reiniciá-los mais tarde, existe uma possibilidade significativa de que a maioria dos nós comece em novos anfitriões e torne o sistema incapaz de recuperar.

Se pretender criar clusters para testar a sua aplicação antes de ser implantada, recomendamos que crie dinamicamente esses clusters como parte do seu gasoduto de [integração/implantação contínua.](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como faço o upgrade do meu Sistema Operativo (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Enquanto estamos a trabalhar numa experiência melhorada, hoje, és responsável pela atualização. Tem de atualizar a imagem de SO nas máquinas virtuais do cluster um VM de cada vez. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso encriptar os discos de dados anexados num tipo de nó de cluster (conjunto de escala de máquina virtual)?
Sim.  Para obter mais informações, consulte [Criar um cluster com discos de dados anexados](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) e [encriptação de disco Azure para conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso utilizar VMs de baixa prioridade num tipo de nó de cluster (conjunto de escala de máquina virtual)?
N.º Os VM de baixa prioridade não são suportados. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Quais são os diretórios e processos que preciso excluir quando executo um programa antivírus no meu grupo?

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
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Como pode a minha aplicação autenticar-se no Key Vault para obter segredos?
Seguem-se os meios para a sua aplicação para obter credenciais para autenticação no Cofre de Chaves:

A. Durante o trabalho de construção/embalagem das suas aplicações, pode puxar um certificado para o pacote de dados da sua aplicação SF e usá-lo para autenticar no Key Vault.
B. Para os anfitriões de escala de máquinas virtuais MSI habilitados, pode desenvolver um simples PowerShell SetupEntryPoint para a sua aplicação SF obter [um token de acesso a partir do ponto final do MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), e, em seguida, [recuperar os seus segredos a partir do Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Design de aplicações

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual é a melhor maneira de consultar dados através de divisórias de uma Coleção Fiável?

Coleções fiáveis são tipicamente [divididas](service-fabric-concepts-partitioning.md) para permitir uma escala para um maior desempenho e produção. Isso significa que o estado para um determinado serviço pode ser espalhado por dezenas ou centenas de máquinas. Para realizar operações ao longo desse conjunto completo de dados, tem algumas opções:

- Crie um serviço que questione todas as divisórias de outro serviço para obter os dados necessários.
- Crie um serviço que possa receber dados de todas as divisórias de outro serviço.
- Empurre periodicamente os dados de cada serviço para uma loja externa. Esta abordagem só é apropriada se as consultas que está a realizar não fizerem parte da sua lógica de negócio principal, uma vez que os dados da loja externa serão velhos.
- Em alternativa, armazenar dados que devem suportar consultas em todos os registos diretamente numa loja de dados e não numa recolha fiável. Isto elimina o problema com dados velhos, mas não permite que as vantagens de coleções fiáveis sejam alavancadas.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual é a melhor maneira de consultar os dados dos meus atores?

Os atores são projetados para serem unidades independentes de estado e computação, por isso não é recomendado realizar amplas consultas de estado de ator em tempo de execução. Se você tem necessidade de consultar todo o conjunto de estado de ator, você deve considerar:

- Substituindo os seus serviços de ator por serviços fidedignos e fiáveis, de modo a que o número de pedidos de rede recolha todos os dados desde o número de atores até ao número de divisórias no seu serviço.
- Projetar os seus atores para empurrar periodicamente o seu estado para uma loja externa para uma consulta mais fácil. Como acima, esta abordagem só é viável se as consultas que você está executando não forem necessárias para o seu comportamento de tempo de execução.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quantos dados posso armazenar numa Coleção Fiável?

Os serviços fiáveis são tipicamente divididos, pelo que a quantidade que pode armazenar é limitada apenas pelo número de máquinas que tem no cluster, e pela quantidade de memória disponível nessas máquinas.

Como exemplo, suponha que tem uma coleção confiável num serviço com 100 divisórias e 3 réplicas, armazenando objetos que em média 1 kb de tamanho. Agora suponha que tem um conjunto de máquinas de 10 com 16gb de memória por máquina. Para a simplicidade e para ser conservador, assuma que o sistema operativo e os serviços de sistema, o tempo de funcionamento do Service Fabric e os seus serviços consomem 6gb disso, deixando 10gb disponíveis por máquina, ou 100 gb para o cluster.

Tendo em conta que cada objeto deve ser armazenado três vezes (uma primária e duas réplicas), teria memória suficiente para aproximadamente 35 milhões de objetos na sua coleção quando estiver a funcionar em plena capacidade. No entanto, recomendamos ser resiliente à perda simultânea de um domínio de falha e de um domínio de upgrade, que representa cerca de 1/3 da capacidade, e reduziria o número para cerca de 23 milhões.

Este cálculo pressupõe ainda:

- Que a distribuição de dados através das divisórias é aproximadamente uniforme ou que está a reportar métricas de carga ao Gestor de Recursos do Cluster. Por predefinição, o Tecido de Serviço carrega o equilíbrio com base na contagem de réplicas. No exemplo anterior, que colocaria 10 réplicas primárias e 20 réplicas secundárias em cada nó no cluster. Isso funciona bem para a carga que é distribuída uniformemente através das divisórias. Se a carga não estiver igual, deve reportar a carga para que o Gestor de Recursos possa embalar réplicas menores juntas e permitir que réplicas maiores consumam mais memória num nó individual.

- Que o serviço fiável em questão é o único estado de armazenamento no aglomerado. Uma vez que pode implementar vários serviços num cluster, precisa estar atento aos recursos que cada um precisa para gerir e gerir o seu estado.

- Que o aglomerado em si não está a crescer ou a encolher. Se adicionar mais máquinas, o Service Fabric reequilibrará as suas réplicas para alavancar a capacidade adicional até que o número de máquinas ultrapasse o número de divisórias no seu serviço, uma vez que uma réplica individual não pode abranger máquinas. Em contraste, se reduzir o tamanho do cluster removendo máquinas, as suas réplicas são embaladas com mais força e têm menos capacidade global.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quantos dados posso armazenar num ator?

Tal como acontece com os serviços fiáveis, a quantidade de dados que pode armazenar num serviço de atores é limitada apenas pelo espaço total do disco e memória disponível nos nós do seu cluster. No entanto, os atores individuais são mais eficazes quando são usados para encapsular uma pequena quantidade de estado e lógica de negócio associada. Regra geral, um ator individual deve ter um estado que é medido em quilobytes.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Onde é que o Fornecedor de Recursos de Tecido de Serviço Azure armazena os dados dos clientes?

O Fornecedor de Recursos de Tecido de Serviço Azure não move nem armazena os dados dos clientes para fora da região em que está implantado.


## <a name="other-questions"></a>Outras questões

### <a name="how-does-service-fabric-relate-to-containers"></a>Como é que o Service Fabric se relaciona com os contentores?

Os contentores oferecem uma forma simples de embalar serviços e suas dependências de modo que funcionam de forma consistente em todos os ambientes e podem operar de forma isolada numa única máquina. A Service Fabric oferece uma forma de implantar e gerir serviços, incluindo [serviços que foram embalados num contentor.](service-fabric-containers-overview.md)

### <a name="are-you-planning-to-open-source-service-fabric"></a>Está a planear abrir o Serviço De tecido?

Dispomos de partes abertas do Service Fabric (quadro de[serviços fiáveis,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [quadro de atores fiáveis,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [bibliotecas de integração core ASP.NET,](https://github.com/Azure/service-fabric-aspnetcore) [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), e Service Fabric [CLI](https://github.com/Azure/service-fabric-cli)) no GitHub e aceitamos contribuições comunitárias para esses projetos. 

Recentemente [anunciamos](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) que planeamos abrir o tempo de funcionação do Tecido de Serviço. Neste momento, temos o Tecido de [Serviço a reergê-lo](https://github.com/Microsoft/service-fabric/) no GitHub com ferramentas de construção e teste linux, o que significa que você pode clonar o repo, construir tecido de serviço para Linux, executar testes básicos, questões abertas e apresentar pedidos de pull. Estamos a trabalhar arduamente para que o ambiente de construção do Windows seja migrado também, juntamente com um ambiente de CI completo.

Siga o [blog Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) para mais detalhes à medida que forem anunciados.

## <a name="next-steps"></a>Passos seguintes

Conheça os conceitos e [as melhores práticas](service-fabric-best-practices-overview.md) [do tecido de serviço](service-fabric-technical-overview.md)
