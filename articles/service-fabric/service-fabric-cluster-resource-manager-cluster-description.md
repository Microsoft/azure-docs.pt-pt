---
title: Descreva um cluster utilizando o Cluster Resource Manager
description: Descreva um cluster de Tecido de Serviço especificando domínios de avaria, domínios de upgrade, propriedades de nó e capacidades de nó para Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92911771"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descreva um cluster de tecido de serviço utilizando o Cluster Resource Manager

A funcionalidade Cluster Resource Manager da Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Atualizar domínios
* Propriedades do nó
* Capacidades do nó

Durante o tempo de funcionamento, o Cluster Resource Manager utiliza esta informação para garantir uma elevada disponibilidade dos serviços em execução no cluster. Ao impor estas regras importantes, também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="fault-domains"></a>Domínios de falha

Um domínio de avaria é qualquer área de falha coordenada. Uma única máquina é um domínio de falha. Pode falhar por si só por várias razões, desde falhas na fonte de alimentação até falhas no firmware NIC.

As máquinas ligadas ao mesmo interruptor Ethernet estão no mesmo domínio de avaria. Assim como as máquinas que partilham uma única fonte de energia ou num único local.

Como é natural que as falhas de hardware se sobreponham, os domínios de falhas são inerentemente hierárquicos. São representados como URIs em Tecido de Serviço.

É importante que os domínios de avaria sejam configurado corretamente porque o Service Fabric utiliza esta informação para colocar serviços de forma segura. O Service Fabric não quer colocar serviços de tal forma que a perda de um domínio de avaria (causado pela falha de algum componente) faz com que um serviço desça.

No ambiente Azure, o Service Fabric utiliza as informações de domínio de avaria fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome. Para casos autónomos de Tecido de Serviço, os domínios de avaria são definidos no momento em que o cluster é configurado.

> [!WARNING]
> É importante que as informações de domínio de avaria fornecidas ao Service Fabric sejam precisas. Por exemplo, digamos que os nós do seu cluster de tecido de serviço estão a funcionar dentro de 10 máquinas virtuais, funcionando em 5 anfitriões físicos. Neste caso, apesar de existirem 10 máquinas virtuais, existem apenas 5 domínios de avarias diferentes (de nível superior). A partilha do mesmo hospedeiro físico faz com que os VM partilhem o mesmo domínio de falha de raiz, porque os VM sofrem uma falha coordenada se o seu hospedeiro físico falhar.  
>
> O Service Fabric espera que o domínio da falha de um nó não se altere. Outros mecanismos para garantir uma elevada disponibilidade dos VMs, como os [HA-VMs,](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))podem causar conflitos com o Tecido de Serviço. Estes mecanismos utilizam a migração transparente de VMs de um hospedeiro para outro. Não reconfiguram ou notificam o código de funcionamento dentro do VM. Como tal, não são *suportados* como ambientes para gerir clusters de tecidos de serviço. 
>
> O Tecido de Serviço deve ser a única tecnologia de alta disponibilidade utilizada. Não são necessários mecanismos como a migração de VM e as SANs vivas. Se estes mecanismos forem utilizados em conjunto com o Service Fabric, _reduzem_ a disponibilidade e a fiabilidade da aplicação. A razão é que introduzem complexidade adicional, adicionam fontes centralizadas de insucesso, e utilizam estratégias de fiabilidade e disponibilidade que entram em conflito com as do Tecido de Serviço.
>
>

No gráfico seguinte, coloramos todas as entidades que contribuem para os domínios de falhas e listamos todos os diferentes domínios de falha que resultam. Neste exemplo, temos datacenters ("DC"), racks ("R"), e lâminas ("B"). Se cada lâmina tiver mais do que uma máquina virtual, pode haver outra camada na hierarquia do domínio de avaria.

![Nódes organizados através de domínios de falhas][Image1]

Durante o tempo de funcionação, o Service Fabric Cluster Resource Manager considera os domínios de avaria no cluster e planeia layouts. As réplicas imponentes ou os casos apátridas para um serviço são distribuídos por isso estão em domínios de falhas separados. A distribuição do serviço por domínios de avaria garante que a disponibilidade do serviço não seja comprometida quando um domínio de avaria falha em qualquer nível da hierarquia.

O Cluster Resource Manager não se importa com quantas camadas existem na hierarquia do domínio de falhas. Tenta garantir que a perda de qualquer parte da hierarquia não afete os serviços que nela correm.

É melhor se o mesmo número de nós estiver em cada nível de profundidade na hierarquia do domínio de falhas. Se a "árvore" dos domínios de falhas estiver desequilibrada no seu cluster, é mais difícil para o Cluster Resource Manager descobrir a melhor alocação de serviços. Os layouts de domínio de avarias desequilibrados significam que a perda de alguns domínios afeta mais a disponibilidade de serviços do que outros domínios. Como resultado, cluster Resource Manager está dividido entre dois objetivos:

* Quer usar as máquinas nesse domínio "pesado", colocando-lhes serviços. 
* Quer colocar serviços noutros domínios para que a perda de um domínio não cause problemas.

Como são os domínios desequilibrados? O diagrama seguinte mostra dois layouts de cluster diferentes. No primeiro exemplo, os nós são distribuídos uniformemente pelos domínios de avaria. No segundo exemplo, um domínio de falha tem muitos mais nós do que os outros domínios de falha.

![Dois layouts de cluster diferentes][Image2]

Em Azure, a escolha do domínio de avaria que contém um nó é gerida para si. Mas dependendo do número de nós que fornece, ainda pode acabar com domínios de falhas que têm mais nós do que em outros.

Por exemplo, digamos que tem cinco domínios de falha no cluster, mas prevê sete nó para um tipo de nó **(NodeType).** Neste caso, os dois primeiros domínios de avaria acabam com mais nós. Se continuar a implementar mais casos **de NodeType** com apenas algumas instâncias, o problema agrava-se. Por esta razão, recomendamos que o número de nós em cada tipo de nó seja um múltiplo do número de domínios de avaria.

## <a name="upgrade-domains"></a>Atualizar domínios

Os domínios de upgrade são outra característica que ajuda o Service Fabric Cluster Resource Manager a compreender o layout do cluster. Os domínios de atualização definem conjuntos de nós que são atualizados ao mesmo tempo. Os domínios de upgrade ajudam o Cluster Resource Manager a compreender e a orquestrar operações de gestão como upgrades.

Os domínios de upgrade são muito parecidos com domínios de falhas, mas com algumas diferenças chave. Em primeiro lugar, as áreas de falhas de hardware coordenadas definem domínios de falhas. Os domínios de atualização, por outro lado, são definidos pela política. Pode decidir quantos quer, em vez de deixar o ambiente ditar o número. Pode ter tantos domínios de upgrade como os nós. Outra diferença entre domínios de falhas e domínios de upgrade é que os domínios de upgrade não são hierárquicos. Em vez disso, são mais como uma etiqueta simples.

O diagrama seguinte mostra três domínios de atualização listrados em três domínios de avaria. Também mostra uma possível colocação para três réplicas diferentes de um serviço imponente, onde cada um acaba em diferentes domínios de falha e upgrade. Esta colocação permite a perda de um domínio de avaria enquanto está no meio de uma atualização de serviço e ainda tem uma cópia do código e dados.  

![Colocação Com domínios de falha e de upgrade][Image3]

Há prós e contras para ter um grande número de domínios de upgrade. Mais domínios de upgrade significam que cada passo da atualização é mais granular e afeta um número menor de nós ou serviços. Menos serviços têm de se mover de cada vez, introduzindo menos agitação no sistema. Isto tende a melhorar a fiabilidade, porque menos do serviço é afetado por qualquer problema introduzido durante a atualização. Mais domínios de upgrade também significam que você precisa de menos tampão disponível em outros nós para lidar com o impacto da atualização.

Por exemplo, se tiver cinco domínios de atualização, os nós em cada um deles estão a manusear cerca de 20% do tráfego. Se precisar de retirar o domínio de upgrade para uma atualização, essa carga geralmente precisa de ir a algum lugar. Como tem quatro domínios de upgrade restantes, cada um deve ter espaço para cerca de 25% do tráfego total. Mais domínios de atualização significam que precisa de menos tampão nos nós do cluster.

Considere se tinha 10 domínios de atualização. Nesse caso, cada domínio de atualização manusearia apenas cerca de 10% do tráfego total. Quando um upgrade passa pelo cluster, cada domínio teria de ter espaço para apenas cerca de 11% do tráfego total. Mais domínios de atualização geralmente permitem-lhe executar os seus nós numa utilização mais elevada, porque precisa de uma capacidade menos reservada. O mesmo se aplica aos domínios de falhas.  

A desvantagem de ter muitos domínios de upgrade é que as atualizações tendem a demorar mais tempo. O Service Fabric aguarda um curto período após a conclusão de um domínio de atualização e efetua verificações antes de começar a atualizar o próximo. Estes atrasos permitem detetar problemas introduzidos pela atualização antes da atualização. A compensação é aceitável porque evita que as más alterações afetem demasiado o serviço de cada vez.

A presença de poucos domínios de upgrade tem muitos efeitos colaterais negativos. Enquanto cada domínio de upgrade está em baixo e a ser atualizado, uma grande parte da sua capacidade global não está disponível. Por exemplo, se tiver apenas três domínios de upgrade, está a retirar cerca de um terço do seu serviço geral ou capacidade de cluster de cada vez. Ter tanto do seu serviço ao mesmo tempo não é desejável porque precisa de capacidade suficiente no resto do seu cluster para lidar com a carga de trabalho. Manter este tampão significa que, durante o funcionamento normal, esses nós estão menos carregados do que seriam de outra forma. Isto aumenta o custo de funcionamento do seu serviço.

Não há limite real para o número total de domínios de falha ou upgrade num ambiente, ou restrições na forma como se sobrepõem. Mas há padrões comuns:

* Domínios de avaria e domínios de upgrade mapeados 1:1
* Um domínio de upgrade por nó (instância física ou virtual do SO)
* Um modelo "listrado" ou "matriz" onde os domínios de avaria e os domínios de atualização formam uma matriz com máquinas que normalmente escoram pelas diagonais

![Layouts de domínios de falha e de upgrade][Image4]

Não há a melhor resposta para que layout escolher. Cada um tem prós e contras. Por exemplo, o modelo 1FD:1UD é simples de configurar. O modelo de um modelo de upgrade por modelo de nó é mais parecido com o que as pessoas estão habituadas. Durante as atualizações, cada nó é atualizado de forma independente. Isto é semelhante ao modo como pequenos conjuntos de máquinas foram atualizados manualmente no passado.

O modelo mais comum é a matriz FD/UD, onde os domínios de avaria e os domínios de atualização formam uma tabela e os nós são colocados a partir da diagonal. Este é o modelo utilizado por padrão em clusters de tecido de serviço em Azure. Para aglomerados com muitos nós, tudo acaba parecendo um padrão de matriz denso.

> [!NOTE]
> Os clusters de tecido de serviço alojados no Azure não suportam alterar a estratégia padrão. Apenas aglomerados autónomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Falhas e atualização de restrições de domínio e comportamento resultante
### <a name="default-approach"></a>Abordagem padrão

Por predefinição, o Cluster Resource Manager mantém os serviços equilibrados entre domínios de falha e atualização. Isto é modelado como uma [restrição.](service-fabric-cluster-resource-manager-management-integration.md) O constrangimento dos domínios de avaria e de atualização diz: "Para uma determinada divisão de serviço, nunca deve haver uma diferença maior do que uma no número de objetos de serviço (instâncias de serviço apátridas ou réplicas de serviços estatais) entre dois domínios no mesmo nível de hierarquia."

Digamos que esta restrição proporciona uma garantia de "diferença máxima". A restrição para os domínios de avaria e de atualização impede certos movimentos ou arranjos que violem a regra.

Por exemplo, digamos que temos um cluster com seis nós, configurado com cinco domínios de falha e cinco domínios de upgrade.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos dizer que criamos um serviço com um **targetReplicaSetSize** (ou, para um serviço apátrida, **InstanceCount**) valor de cinco. As réplicas aterram na N1-N5. Na verdade, o N6 nunca é usado, não importa quantos serviços como este crie. Mas porquê? Vamos ver a diferença entre o layout atual e o que aconteceria se n6 fosse escolhido.

Aqui está o layout que temos e o número total de réplicas por falha e domínio de upgrade:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de nós por domínio de avaria e domínio de upgrade. Também é equilibrado em termos do número de réplicas por falha e domínio de upgrade. Cada domínio tem o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como é que as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Este layout viola a nossa definição da garantia de "diferença máxima" para a restrição de domínio de avaria. FD0 tem duas réplicas, enquanto FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, o que é maior do que a diferença máxima de um. Como a restrição é violada, o Cluster Resource Manager não permite este arranjo.

Da mesma forma, se escolhermos N2 e N6 (em vez de N1 e N2), teremos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de domínios de avaria. Mas agora está a violar a restrição de domínio de upgrade, porque o UD0 tem zero réplicas e a UD1 tem duas. Este layout também é inválido e não será escolhido pelo Cluster Resource Manager.

Esta abordagem para a distribuição de réplicas estatal ou instâncias apátridas proporciona a melhor tolerância possível de falhas. Se um domínio descer, perde-se o número mínimo de réplicas/instâncias.

Por outro lado, esta abordagem pode ser demasiado rigorosa e não permitir que o cluster utilize todos os recursos. Para certas configurações de cluster, certos nós não podem ser usados. Isto pode fazer com que o Service Fabric não coloque os seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns dos nós de cluster não podem ser utilizados (N6 no exemplo). Mesmo que adicionasse nós a esse cluster (N7-N10), réplicas/instâncias seriam colocadas apenas em N1-N5 devido a restrições nos domínios de falha e de upgrade.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Abordagem alternativa

Cluster Resource Manager suporta outra versão da restrição para domínios de falha e upgrade. Permite a colocação, garantindo ainda um nível mínimo de segurança. A restrição alternativa pode ser indicada da seguinte forma: "Para uma determinada divisão de serviço, a distribuição de réplicas através dos domínios deve garantir que a partição não sofra uma perda de quórum." Digamos que esta restrição fornece uma garantia de "quórum seguro".

> [!NOTE]
> Para um serviço estatal, definimos *a perda de quórum* numa situação em que a maioria das réplicas de partição estão em baixo ao mesmo tempo. Por exemplo, se **o TargetReplicaSetSize** for cinco, um conjunto de três réplicas representa quórum. Da mesma forma, se **o TargetReplicaSetSize** for de seis, quatro réplicas são necessárias para o quórum. Em ambos os casos, não mais do que duas réplicas podem ser desabatidos ao mesmo tempo se a partição quiser continuar a funcionar normalmente.
>
> Para um serviço apátrida, não existe tal coisa como *perda de quórum.* Os serviços apátridas continuam a funcionar normalmente, mesmo que a maioria dos casos caia ao mesmo tempo. Por isso, vamos concentrar-nos em serviços estatais no resto deste artigo.
>

Voltemos ao exemplo anterior. Com a versão "quórum seguro" da restrição, os três layouts seriam válidos. Mesmo que fD0 falhasse no segundo layout ou UD1 falhasse no terceiro layout, a partição ainda teria quórum. (A maioria das réplicas ainda estaria de pé.) Com esta versão do constrangimento, o N6 pode quase sempre ser utilizado.

A abordagem "quórum seguro" proporciona mais flexibilidade do que a abordagem da "diferença máxima". A razão é que é mais fácil encontrar distribuições de réplicas que são válidas em quase qualquer topologia de cluster. No entanto, esta abordagem não pode garantir as melhores características de tolerância à falha porque algumas falhas são piores do que outras.

Na pior das hipóteses, a maioria das réplicas pode perder-se com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de três falhas serem necessárias para perder quórum com cinco réplicas ou instâncias, pode agora perder a maioria com apenas duas falhas.

### <a name="adaptive-approach"></a>Abordagem adaptativa

Como ambas as abordagens têm pontos fortes e fracos, introduzimos uma abordagem adaptativa que combina estas duas estratégias.

> [!NOTE]
> Este é o comportamento padrão a começar pela versão 6.2 do Service Fabric.
>
> A abordagem adaptativa utiliza a lógica da "diferença máxima" por defeito e muda para a lógica "quórum seguro" apenas quando necessário. O Cluster Resource Manager descobre automaticamente qual a estratégia necessária, analisando a configuração do cluster e dos serviços.
>
> O Cluster Resource Manager deve utilizar a lógica "baseada em quórum" para um serviço que ambas estas condições são verdadeiras:
>
> * **TargetReplicaSetSize** para o serviço é igualmente divisível pelo número de domínios de avaria e pelo número de domínios de atualização.
> * O número de nós é inferior ou igual ao número de domínios de avaria multiplicados pelo número de domínios de atualização.
>
> Tenha em mente que o Cluster Resource Manager utilizará esta abordagem tanto para serviços apátridas como para serviços estatais, embora a perda de quórum não seja relevante para os serviços apátridas.

Vamos voltar ao exemplo anterior e assumir que um cluster agora tem oito nós. O cluster ainda está configurado com cinco domínios de falha e cinco domínios de upgrade, e o valor **targetReplicaSetSize** de um serviço hospedado nesse cluster permanece em cinco.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Como todas as condições necessárias estão satisfeitas, o Cluster Resource Manager utilizará a lógica "baseada no quórum" na distribuição do serviço. Isto permite o uso de N6-N8. Uma possível distribuição de serviço neste caso pode ser assim:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se o valor **TargetReplicaSetSize** do seu serviço for reduzido para quatro (por exemplo), o Cluster Resource Manager notará essa alteração. Será retomado usando a lógica da "diferença máxima", porque **o TargetReplicaSetSize** já não é dividido pelo número de domínios de avaria e por domínios de atualização. Como resultado, certos movimentos de réplica ocorrerão para distribuir as restantes quatro réplicas nos nós N1-N5. Desta forma, a versão de "diferença máxima" do domínio de avaria e lógica de domínio de upgrade não é violada.

No layout anterior, se o valor **TargetReplicaSetSize** for de cinco e o N1 for removido do cluster, o número de domínios de atualização torna-se igual a quatro. Mais uma vez, o Cluster Resource Manager começa a usar a lógica de "diferença máxima", porque o número de domínios de upgrade já não divide uniformemente o valor **targetReplicaSetSize** do serviço. Como resultado, a réplica R1, quando construída novamente, tem de pousar em N4 para que o constrangimento para o domínio da falha e do upgrade não seja violado.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurar domínios de falha e de atualização

Nas implementações de Tecido de Serviço hospedados no Azure, os domínios de avaria e os domínios de atualização são definidos automaticamente. Service Fabric recolhe e utiliza a informação ambiental da Azure.

Se estiver a criar o seu próprio cluster (ou quiser executar uma determinada topologia em desenvolvimento), pode fornecer o domínio de falhas e atualizar a informação de domínio por si mesmo. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três datacenters (cada um com três racks). Este cluster também tem três domínios de upgrade listrados através desses três datacenters. Aqui está um exemplo da configuração em ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Este exemplo utiliza ClusterConfig.jspara implementações autónomas:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Ao definir clusters via Azure Resource Manager, o Azure atribui domínios de falhas e domínios de atualização. Assim, a definição dos seus tipos de nó e conjuntos de escala de máquina virtual no seu modelo de Gestor de Recursos Azure não inclui informações sobre o domínio de falhas ou o domínio de upgrade.
>

## <a name="node-properties-and-placement-constraints"></a>Propriedades do nó e restrições de colocação

Às vezes (na verdade, na maioria das vezes) você vai querer garantir que certas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem requerer GPUs ou SSDs, e outros podem não.

Um grande exemplo de direcionar o hardware para determinadas cargas de trabalho é quase todas as arquiteturas de nível n. Certas máquinas servem como o lado frontal ou a api-serve da aplicação e estão expostas aos clientes ou à internet. Máquinas diferentes, muitas vezes com diferentes recursos de hardware, lidam com o trabalho das camadas de computação ou armazenamento. Estes geralmente _não_ são diretamente expostos a clientes ou à internet.

A Service Fabric espera que, em alguns casos, determinadas cargas de trabalho possam ter de ser executadas em configurações específicas de hardware. Por exemplo:

* Uma aplicação n-tier existente foi "levantada e transferida" para um ambiente de Tecido de Serviço.
* Uma carga de trabalho deve ser executada em hardware específico por razões de desempenho, escala ou isolamento de segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por razões de política ou de consumo de recursos.

Para suportar este tipo de configurações, o Service Fabric inclui etiquetas que pode aplicar aos nós. Estas etiquetas são chamadas *propriedades de nó.* *Constrangimentos de colocação* são as declarações anexas a serviços individuais que seleciona para uma ou mais propriedades de nó. Os constrangimentos de colocação definem onde os serviços devem ser executados. O conjunto de constrangimentos é extensível. Qualquer par de chaves/valor pode funcionar.

![Cargas de trabalho diferentes para um layout de cluster][Image5]

### <a name="built-in-node-properties"></a>Propriedades de nó incorporado

O Tecido de Serviço define algumas propriedades de nó padrão que podem ser usadas automaticamente para que não tenha que defini-las. As propriedades predefinidas definidas em cada nó são **NodeType** e **NodeName**.

Por exemplo, pode escrever uma restrição de colocação como `"(NodeType == NodeType03)"` . **NodeType** é uma propriedade comumente usada. É útil porque corresponde 1:1 com um tipo de máquina. Cada tipo de máquina corresponde a um tipo de carga de trabalho numa aplicação tradicional n-tier.

![Restrições de colocação e propriedades de nó][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de colocação e sintaxe de propriedade de nó

O valor especificado na propriedade do nó pode ser uma corda, Boolean, ou assinado por muito tempo. A declaração no serviço é chamada de *restrição* de colocação porque limita onde o serviço pode funcionar no cluster. A restrição pode ser qualquer declaração booleana que opere nas propriedades do nó no cluster. Os selecionadores válidos nestas declarações booleanas são:

* Verificações condicionais para a criação de declarações específicas:

  | Instrução | Syntax |
  | --- |:---:|
  | "igual a" | "==" |
  | "não é igual a" | "!=" |
  | "maior do que" | ">" |
  | "maior ou igual a" | ">=" |
  | "menos que" | "<" |
  | "menos ou igual a" | "<=" |

* Declarações booleanas para agrupar e operações lógicas:

  | Instrução | Syntax |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "grupo como única declaração" | "()" |

Aqui estão alguns exemplos de declarações básicas de restrição:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Apenas os nós onde a declaração de restrição de colocação geral avalia para "Verdadeiro" pode ter o serviço colocado nele. Nós que não têm uma propriedade definida não correspondem a qualquer restrição de colocação que contenha a propriedade.

Digamos que as seguintes propriedades do nó foram definidas para um tipo de nó em ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

O exemplo a seguir mostra propriedades de nó definidas via ClusterConfig.jspara implementações autónomas ou Template.jspara clusters hospedados em Azure.

> [!NOTE]
> No seu modelo de Gestor de Recursos Azure, o tipo de nó é geralmente parametrizado. Pareceria `"[parameters('vmNodeType1Name')]"` mais do que NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Pode criar *restrições* de colocação de serviço para um serviço da seguinte forma:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós de NodeType01 forem válidos, também pode selecionar esse tipo de nó com a restrição `"(NodeType == NodeType01)"` .

As restrições de colocação de um serviço podem ser atualizadas dinamicamente durante o tempo de funcionação. Se precisar, pode mover um serviço no cluster, adicionar e remover os requisitos, e assim por diante. O Service Fabric garante que o serviço se mantém disponível mesmo quando este tipo de alterações são feitas.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

As restrições de colocação são especificadas para cada instância de serviço nomeada. As atualizações tomam sempre o lugar de (overwrite) o que foi previamente especificado.

A definição de cluster define as propriedades num nó. Alterar as propriedades de um nó requer um upgrade para a configuração do cluster. A atualização das propriedades de um nó requer que cada nó afetado reinicie para reportar as suas novas propriedades. A Service Fabric gere estas atualizações.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerir recursos de cluster

Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerir o consumo de recursos no cluster. Gerir recursos de cluster pode significar um par de coisas diferentes.

Primeiro, há a garantia de que as máquinas não estão sobrecarregadas. Isto significa garantir que as máquinas não estão a fazer mais serviços do que podem suportar.

Em segundo lugar, há equilíbrio e otimização, que são cruciais para executar os serviços de forma eficiente. Ofertas de serviços rentáveis ou sensíveis ao desempenho não podem permitir que alguns nós fiquem quentes enquanto outros estão frios. Os nós quentes levam à contenção de recursos e ao mau desempenho. Os nós frios representam recursos desperdiçados e custos acrescidos.

O Tecido de Serviço representa os recursos como *métricas.* As métricas são qualquer recurso lógico ou físico que queira descrever ao Service Fabric. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que o Service Fabric pode governar em nós, consulte [a governação de Recursos.](service-fabric-resource-governance.md) Para obter informações sobre as métricas padrão utilizadas pelo Cluster Resource Manager e como configurar métricas personalizadas, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de colocação e das propriedades dos nó. As propriedades dos nó são descritores estáticos dos próprios nós. As métricas descrevem os recursos que os nós têm e que os serviços consomem quando funcionam num nó. Uma propriedade de nó pode ser **HasSSD** e pode ser definida como verdadeira ou falsa. A quantidade de espaço disponível nesse SSD e quanto é consumido pelos serviços seria uma métrica como "DriveSpaceInMb".

Tal como para as restrições de colocação e propriedades de nó, o Service Fabric Cluster Resource Manager não entende o que os nomes das métricas significam. Os nomes métricos são apenas cordas. É uma boa prática declarar unidades como parte dos nomes métricos que se criam quando podem ser ambíguas.

## <a name="capacity"></a>Capacidade

Se desliguei todo *o equilíbrio de* recursos, o Service Fabric Cluster Resource Manager ainda garantirá que nenhum nó ultrapassa a sua capacidade. A gestão das superações de capacidade é possível a menos que o cluster esteja demasiado cheio ou a carga de trabalho seja maior do que qualquer nó. A capacidade é outra *restrição* que o Cluster Resource Manager usa para entender a quantidade de um recurso que um nó tem. A capacidade remanescente também é rastreada para o cluster como um todo.

Tanto a capacidade como o consumo ao nível do serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um nó pode ter uma capacidade para "ClientConnections" de 32.768. Outros nós podem ter outros limites. Um serviço em execução nesse nó pode dizer que está a consumir 32.256 das métricas "ClientConnections".

Durante o tempo de funcionação, o Cluster Resource Manager rastreia a capacidade remanescente no cluster e nos nós. Para controlar a capacidade, o Cluster Resource Manager subtrai a utilização de cada serviço a partir da capacidade de um nó onde o serviço funciona. Com esta informação, o Cluster Resource Manager pode descobrir onde colocar ou mover réplicas para que os nós não ultrapassem a capacidade.

![Nódoas de cluster e capacidade][Image7]

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Pode ver capacidades definidas no manifesto do cluster. Aqui está um exemplo para ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Aqui está um exemplo das capacidades definidas através de ClusterConfig.jspara implantações autónomas ou Template.jspara clusters hospedados no Azure:

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

A carga de um serviço muda frequentemente de forma dinâmica. Digamos que a carga de "ClientConnections" de uma réplica passou de 1.024 para 2.048. O nó em que estava a funcionar tinha então uma capacidade de apenas 512 restantes para aquela métrica. Agora que a réplica ou a colocação do caso é inválida, porque não há espaço suficiente nesse nó. O Cluster Resource Manager tem de colocar o nó de volta abaixo da capacidade. Reduz a carga no nó que está acima da capacidade movendo uma ou mais das réplicas ou instâncias desse nó para outros nós.

O Cluster Resource Manager tenta minimizar o custo das réplicas em movimento. Você pode aprender mais sobre [o custo de movimento](service-fabric-cluster-resource-manager-movement-cost.md) e sobre o [reequilíbrio estratégias e regras.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Capacidade de cluster

Como é que o Service Fabric Cluster Resource Manager impede que o cluster global esteja demasiado cheio? Com carga dinâmica, não há muito que possa fazer. Os serviços podem ter o seu pico de carga independentemente das ações que o Cluster Resource Manager toma. Como resultado, o seu aglomerado com muita cabeceira hoje pode estar subjugado se houver um pico amanhã.

Os controlos no Cluster Resource Manager ajudam a prevenir problemas. A primeira coisa que pode fazer é impedir a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que crias um serviço apátrida, e tem alguma carga associada a ele. O serviço preocupa-se com a métrica "DiskSpaceInMb". O serviço consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Quer criar três instâncias do serviço. Isto significa que precisa de 15 unidades de "DiskSpaceInMb" para estar presente no cluster para que até crie estas instâncias de serviço.

O Cluster Resource Manager calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade remanescente no cluster. Se não houver espaço suficiente, o Cluster Resource Manager rejeita a chamada para criar um serviço.

Como o requisito é apenas que 15 unidades estarão disponíveis, você pode alocar este espaço de muitas maneiras diferentes. Por exemplo, pode haver uma unidade de capacidade restante em 15 nós diferentes, ou três unidades restantes de capacidade em cinco nós diferentes. Se o Cluster Resource Manager conseguir reorganizar as coisas para que existam cinco unidades disponíveis em três nós, coloca o serviço. Reorganizar o cluster é geralmente possível a menos que o cluster esteja quase cheio ou os serviços existentes não possam ser consolidados por alguma razão.

## <a name="node-buffer-and-overbooking-capacity"></a>Tampão de nó e capacidade de sobrerreserva

Se for especificada uma capacidade de nó para uma métrica, o Cluster Resource Manager nunca colocará ou moverá réplicas para um nó se a carga total for superior à capacidade do nó especificado. Isto pode, por vezes, impedir a colocação de réplicas novas ou substituir réplicas falhadas se o cluster estiver perto da capacidade total e uma réplica com uma carga grande deve ser colocada, substituída ou movida.

Para proporcionar mais flexibilidade, pode especificar a capacidade de tampão de nó ou sobrerreserva. Quando a capacidade de tampão de nó ou sobrerreserva for especificada para uma métrica, o Cluster Resource Manager tentará colocar ou mover réplicas de modo a que a capacidade de tampão ou sobrerreserva não seja utilizada, mas permite que o tampão ou a capacidade de sobrerreserva sejam utilizados se necessário para ações que aumentem a disponibilidade do serviço, tais como:

* Nova colocação de réplicas ou substituição de réplicas falhadas
* Colocação durante as atualizações
* Fixação de violações de restrições suaves e duras
* Desfragmentação

A capacidade do tampão do nó representa uma parte reservada da capacidade do nó abaixo da capacidade do nó especificado e a capacidade de sobrereserva representa uma parte da capacidade extra acima da capacidade do nó especificado. Em ambos os casos, o Cluster Resource Manager tentará manter esta capacidade livre.

Por exemplo, se um nó tiver uma capacidade específica para *a CpuUtilização* métrica de 100 e a percentagem de tampão de nó para essa métrica for fixada em 20%, então as capacidades totais e não acumuladas serão de 100 e 80, respectivamente, e o Cluster Resource Manager não colocará mais de 80 unidades de carga no nó em circunstâncias normais.

![A capacidade total é igual à capacidade do nó (tampão de nó + nãobuilado)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

O tampão de nó deve ser utilizado quando pretender reservar uma parte da capacidade do nó que só será utilizada para ações que aumentem a disponibilidade de serviço acima mencionada.

Por outro lado, se a percentagem de sobrerreserva de nó for utilizada e fixada em 20% então as capacidades totais e não acumuladas serão de 120 e 100, respectivamente.

![A capacidade total é igual à capacidade de sobrerreserva mais capacidade do nó (Overbooking + Unbuffered)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

A capacidade de sobrereserva deve ser utilizada quando pretender permitir que o Cluster Resource Manager coloque réplicas num nó, mesmo que a sua utilização total de recursos exceda a capacidade. Isto pode ser usado para fornecer disponibilidade adicional para serviços em detrimento do desempenho. Se for utilizado o overbooking, a lógica da aplicação do utilizador tem de ser capaz de funcionar com menos recursos físicos do que poderia exigir.

Se forem especificadas capacidades de tampão de nó ou sobrereserva, o Cluster Resource Manager não move nem coloca réplicas se a carga total no nó-alvo ultrapassar a capacidade total (capacidade do nó no caso de tampão de nó e capacidade de nó + capacidade de sobrereserva em caso de sobrerreserva).

A capacidade de sobrerreserva também pode ser especificada como infinita. Neste caso, o Cluster Resource Manager tentará manter a carga total no nó abaixo da capacidade do nó especificado, mas é permitido colocar uma carga muito maior no nó que pode levar a uma degradação grave do desempenho.

Uma métrica não pode ter tanto a capacidade de tampão de nó como a capacidade de sobrereserva especificadas para ele ao mesmo tempo.

Aqui está um exemplo de como especificar as capacidades de tampão de nó ou sobrerreserva em *ClusterManifest.xml:*

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Aqui está um exemplo de como especificar as capacidades de tampão de nó ou sobrerreserva através *deClusterConfig.js* para implementações autónomas ouTemplate.jspara clusters hospedados *em* Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a arquitetura e o fluxo de informação dentro do Cluster Resource Manager, consulte a [visão geral do Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md).
* Definir métricas de desfragmentação é uma forma de consolidar a carga nos nós em vez de espalhá-la. Para aprender a configurar a desfragmentação, consulte [a desfragmentação das métricas e a carga em Tecido de Serviço.](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Comece do início e [obtenha uma introdução ao Service Fabric Cluster Resource Manager.](service-fabric-cluster-resource-manager-introduction.md)
* Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, consulte [Balanceing your Service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
