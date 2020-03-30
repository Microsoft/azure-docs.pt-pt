---
title: Descreva um cluster usando cluster Resource Manager
description: Descreva um cluster de tecido de serviço especificando domínios de falha, domínios de upgrade, propriedades do nó e capacidades de nó para Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258775"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descreva um cluster de tecido de serviço usando cluster Resource Manager
A funcionalidade cluster Resource Manager da Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Domínios de upgrade
* Propriedades do nó
* Capacidades de nó

Durante o tempo de funcionamento, o Cluster Resource Manager utiliza estas informações para garantir uma elevada disponibilidade dos serviços em funcionamento no cluster. Ao mesmo tempo que aplica estas regras importantes, também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área de falha coordenada. Uma única máquina é um domínio de falha. Pode falhar por si só por várias razões, desde falhas de alimentação para conduzir falhas a firmware NIC mau. 

As máquinas ligadas ao mesmo interruptor Ethernet estão no mesmo domínio de avaria. Assim como as máquinas que partilham uma única fonte de energia ou num único local. 

Porque é natural que falhas de hardware se sobreponham, os domínios de falha são inerentemente hierárquicos. Estão representados como URIs em Tecido de Serviço.

É importante que os domínios de avaria sejam configurados corretamente porque o Service Fabric utiliza esta informação para colocar serviços com segurança. O Serviço Tecido não quer colocar serviços de modo a que a perda de um domínio de avaria (causada pela falha de algum componente) faça com que um serviço desça. 

No ambiente Azure, o Tecido de Serviço utiliza as informações de domínio de avaria fornecidas pelo ambiente para configurar corretamente os nós do cluster em seu nome. Para casos autónomos de Tecido de Serviço, os domínios de avaria são definidos no momento em que o cluster é configurado. 

> [!WARNING]
> É importante que as informações de domínio de avaria fornecidas ao Tecido de Serviço sejam precisas. Por exemplo, digamos que os nós do seu cluster de tecido de serviço estão a funcionar dentro de 10 máquinas virtuais, funcionando em 5 hospedeiros físicos. Neste caso, apesar de existirem 10 máquinas virtuais, existem apenas 5 domínios diferentes (de nível superior). Partilhar o mesmo hospedeiro físico faz com que os VMs partilhem o mesmo domínio de falha de raiz, porque os VMs experimentam falhas coordenadas se o seu hospedeiro físico falhar.  
>
> O Service Fabric espera que o domínio de avaria de um nó não mude. Outros mecanismos para garantir uma elevada disponibilidade dos VMs, como os [HA-VMs,](https://technet.microsoft.com/library/cc967323.aspx)podem causar conflitos com o Tecido de Serviço. Estes mecanismos utilizam a migração transparente de VMs de um hospedeiro para outro. Não reconfiguram nem notificam o código de execução dentro do VM. Como tal, não são *suportados* como ambientes para executar clusters de tecido de serviço. 
>
> O Tecido de Serviço deve ser a única tecnologia de alta disponibilidade utilizada. Não são necessários mecanismos como a migração vm viva e os SANs. Se estes mecanismos forem utilizados em conjunto com o Service Fabric, _reduzem_ a disponibilidade e a fiabilidade da aplicação. A razão é que introduzem complexidade adicional, acrescentam fontes centralizadas de falha, e usam estratégias de fiabilidade e disponibilidade que entram em conflito com as do Tecido de Serviço. 
>
>

No gráfico seguinte, colorimos todas as entidades que contribuem para os domínios de falha e listamos todos os diferentes domínios de falha que resultam. Neste exemplo, temos datacenters ("DC"), racks ("R"), e lâminas ("B"). Se cada lâmina tiver mais do que uma máquina virtual, pode haver outra camada na hierarquia do domínio de avaria.

<center>

![Nós organizados através de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Gestor de Recursos de Cluster de Tecidos de Serviço considera os domínios de falha nos layouts do cluster e planeia. As réplicas apátridas ou instâncias apátridas para um serviço são distribuídas por isso estão em domínios de falhas separados. A distribuição do serviço através de domínios de avaria garante que a disponibilidade do serviço não fica comprometida quando um domínio de avaria falha em qualquer nível da hierarquia.

Cluster Resource Manager não se importa com quantas camadas existem na hierarquia do domínio de falhas. Tenta garantir que a perda de qualquer parte da hierarquia não afeta os serviços que nela estão em funcionamento. 

É melhor se o mesmo número de nós estiver em cada nível de profundidade na hierarquia do domínio de falhas. Se a "árvore" dos domínios de avaria é desequilibrada no seu cluster, é mais difícil para o Cluster Resource Manager descobrir a melhor alocação de serviços. Os layouts de domínio de falha desequilibrados significam que a perda de alguns domínios afeta mais a disponibilidade de serviços do que outros domínios. Como resultado, cluster Resource Manager está dividido entre dois objetivos: 

* Quer utilizar as máquinas nesse domínio "pesado", colocando-lhes serviços. 
* Quer colocar serviços noutros domínios para que a perda de um domínio não cause problemas. 

Como são os domínios desequilibrados? O diagrama seguinte mostra dois layouts de cluster diferentes. No primeiro exemplo, os nós são distribuídos uniformemente pelos domínios de avaria. No segundo exemplo, um domínio de falha tem muito mais nós do que os outros domínios de falha. 

<center>

![Dois layouts diferentes de cluster][Image2]
</center>

Em Azure, a escolha de qual domínio de falha contém um nó é gerida para si. Mas dependendo do número de nós que você providenciar, você ainda pode acabar com domínios de falhas que têm mais nós neles do que em outros. 

Por exemplo, diga que tem cinco domínios de falha no cluster, mas fornecer sete nós para um tipo de nó **(NodeType**). Neste caso, os dois primeiros domínios de falha acabam com mais nós. Se continuar a implementar mais casos **noNótipo** com apenas alguns casos, o problema piora. Por esta razão, recomendamos que o número de nós em cada tipo de nó seja um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de upgrade
Os domínios de upgrade são outra funcionalidade que ajuda o Gestor de Recursos de Cluster de Tecidos de Serviço a compreender o layout do cluster. Os domínios de atualização definem conjuntos de nódosos que são atualizados ao mesmo tempo. Os domínios de upgrade ajudam o Cluster Resource Manager a compreender e orquestrar operações de gestão como upgrades.

Os domínios de atualização são muito parecidos com domínios de falhas, mas com algumas diferenças-chave. Em primeiro lugar, áreas de falhas coordenadas de hardware definem domínios de falha. Os domínios de atualização, por outro lado, são definidos pela política. Podes decidir quantos queres, em vez de deixares o ambiente ditar o número. Podes ter tantos domínios de atualização como os nódosos. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, são mais como uma etiqueta simples. 

O diagrama seguinte mostra três domínios de atualização listrados em três domínios de falha. Também mostra uma possível colocação para três réplicas diferentes de um serviço imponente, onde cada uma acaba em diferentes domínios de falha e upgrade. Esta colocação permite a perda de um domínio de avaria enquanto está no meio de uma atualização de serviço e ainda tem uma cópia do código e dados.  

<center>

![Colocação com domínios de avaria e upgrade][Image3]
</center>

Há prós e contras para ter um grande número de domínios de upgrade. Mais domínios de atualização significam que cada passo da atualização é mais granular e afeta um número menor de nós ou serviços. Menos serviços têm de se mover de cada vez, introduzindo menos agitação no sistema. Isto tende a melhorar a fiabilidade, porque menos do serviço é afetado por qualquer problema introduzido durante a atualização. Mais domínios de upgrade também significam que você precisa de menos tampão disponível em outros nós para lidar com o impacto da atualização. 

Por exemplo, se tiver cinco domínios de upgrade, os nós em cada um estão a manusear cerca de 20% do seu tráfego. Se precisar de retirar o domínio de atualização para uma atualização, essa carga normalmente precisa de ir a algum lado. Como tem quatro domínios de atualização restantes, cada um deve ter espaço para cerca de 5% do tráfego total. Mais domínios de atualização significam que precisa de menos tampão nos nós do cluster. 

Considere se tinha 10 domínios de upgrade. Nesse caso, cada domínio de atualização estaria a manusear apenas cerca de 10% do tráfego total. Quando um upgrade passa pelo cluster, cada domínio precisaria de ter espaço para apenas cerca de 1,1% do tráfego total. Mais domínios de upgrade geralmente permitem-lhe executar os seus nós com uma utilização mais elevada, porque você precisa de uma capacidade menos reservada. O mesmo se aplica aos domínios de falhas.  

A desvantagem de ter muitos domínios de upgrade é que as atualizações tendem a demorar mais tempo. O Serviço Fabric aguarda um curto período após a conclusão de um domínio de atualização e executa verificações antes de começar a atualizar o próximo. Estes atrasos permitem detetar problemas introduzidos pela atualização antes da atualização. A compensação é aceitável porque impede que as más alterações afetem demasiado o serviço de cada vez.

A presença de poucos domínios de atualização tem muitos efeitos colaterais negativos. Enquanto cada domínio de atualização está em baixo e a ser atualizado, uma grande parte da sua capacidade global não está disponível. Por exemplo, se tiver apenas três domínios de upgrade, está a retirar cerca de um terço do seu serviço total ou capacidade de cluster de cada vez. Ter tanto do seu serviço para baixo ao mesmo tempo não é desejável porque você precisa de capacidade suficiente no resto do seu cluster para lidar com a carga de trabalho. Manter esse tampão significa que, durante o funcionamento normal, esses nós estão menos carregados do que seriam de outra forma. Isto aumenta o custo de executar o seu serviço.

Não há um limite real para o número total de domínios de avaria ou upgrade em um ambiente, ou restrições sobre como se sobrepõem. Mas há padrões comuns:

- Domínios de falha e domínios de atualização mapeados 1:1
- Um domínio de atualização por nó (instância de SO físico ou virtual)
- Um modelo "listrado" ou "matriz" onde os domínios de avaria e os domínios de atualização formam uma matriz com máquinas que normalmente escorrem pelas diagonais

<center>

![Layouts de domínios de falha e upgrade][Image4]
</center>

Não há melhor resposta para qual layout escolher. Cada um tem prós e contras. Por exemplo, o modelo 1FD:1UD é simples de configurar. O modelo de um domínio de upgrade por modelo de nó é mais parecido com o que as pessoas estão habituadas. Durante as atualizações, cada nó é atualizado de forma independente. Isto é semelhante ao modo como pequenos conjuntos de máquinas foram atualizados manualmente no passado.

O modelo mais comum é a matriz FD/UD, onde os domínios de avaria e os domínios de atualização formam uma tabela e os nós são colocados a partir da diagonal. Este é o modelo utilizado por padrão em clusters de Tecido de Serviço em Azure. Para aglomerados com muitos nós, tudo acaba parecendo um padrão dematriz denso.

> [!NOTE]
> Os clusters service Fabric hospedados no Azure não suportam alterar a estratégia de incumprimento. Apenas clusters autónomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Falhas e atualização de restrições de domínio e comportamento resultante
### <a name="default-approach"></a>Abordagem padrão
Por padrão, o Cluster Resource Manager mantém os serviços equilibrados entre domínios de falha e atualização. Isto é modelado como um [constrangimento.](service-fabric-cluster-resource-manager-management-integration.md) A restrição para os domínios de avaria e atualização afirma: "Para uma determinada partição de serviço, nunca deve haver uma diferença maior do que uma no número de objetos de serviço (instâncias de serviço apátridas ou réplicas de serviço satisfeitutos) entre quaisquer dois domínios no mesmo nível de hierarquia.

Digamos que esta restrição proporciona uma garantia de "diferença máxima". A restrição para domínios de avaria e atualização previne certos movimentos ou arranjos que violem a regra.

Por exemplo, digamos que temos um cluster com seis nós, configurado com cinco domínios de falha e cinco domínios de upgrade.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos dizer que criamos um serviço com um **TargetReplicaSetSize** (ou, para um serviço apátrida, **Instância Count)** de cinco. As réplicas aterram na N1-N5. Na verdade, o N6 nunca é usado, não importa quantos serviços como este crie. Mas porquê? Vamos ver a diferença entre o layout atual e o que aconteceria se n6 fosse escolhido.

Aqui está o layout que temos e o número total de réplicas por falha e domínio de upgrade:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de nós por domínio de avaria e domínio de atualização. Também é equilibrado em termos do número de réplicas por falha e domínio de upgrade. Cada domínio tem o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como seriam distribuídas as réplicas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Este layout viola a nossa definição de garantia de "diferença máxima" para a restrição do domínio da falha. FD0 tem duas réplicas, enquanto FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, o que é maior do que a diferença máxima de um. Como a restrição é violada, o Cluster Resource Manager não permite este arranjo. Da mesma forma, se escolhemos N2 e N6 (em vez de N1 e N2), teríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de domínios de falha. Mas agora está a violar a restrição de domínio de atualização, porque a UD0 tem réplicas zero e a UD1 tem duas. Este layout também é inválido e não será escolhido pelo Cluster Resource Manager.

Esta abordagem à distribuição de réplicas apátridas ou instâncias apátridas proporciona a melhor tolerância possível à falha. Se um domínio descer, perde-se o número mínimo de réplicas/instâncias. 

Por outro lado, esta abordagem pode ser demasiado rigorosa e não permitir que o cluster utilize todos os recursos. Para certas configurações de cluster, certos nós não podem ser usados. Isto pode fazer com que o Tecido de Serviço não coloque os seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns dos nós do cluster não podem ser usados (N6 no exemplo). Mesmo que adicione nós a esse cluster (N7-N10), réplicas/instâncias seriam colocadas apenas em N1-N5 devido a restrições nos domínios de avaria e atualização. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Abordagem alternativa

Cluster Resource Manager suporta outra versão do constrangimento para domínios de avaria e atualização. Permite a colocação, garantindo um nível mínimo de segurança. A restrição alternativa pode ser indicada da seguinte forma: "Para uma determinada partilha de serviço, a distribuição de réplicas entre domínios deve garantir que a partição não sofre uma perda de quórum." Digamos que esta restrição fornece uma garantia de "quórum seguro". 

> [!NOTE]
> Para um serviço imponente, definimos a perda de *quórum* numa situação em que a maioria das réplicas de divisórias estão em baixo ao mesmo tempo. Por exemplo, se **o TargetReplicaSetSize** for de cinco, um conjunto de três réplicas representa quórum. Da mesma forma, se **o TargetReplicaSetSize** for de seis, são necessárias quatro réplicas para o quórum. Em ambos os casos, não mais do que duas réplicas podem ser baixas ao mesmo tempo se a partição quiser continuar a funcionar normalmente. 
>
> Para um serviço apátrida, não existe tal coisa como perda de *quórum.* Os serviços apátridas continuam a funcionar normalmente, mesmo que a maioria dos casos desça ao mesmo tempo. Então, vamos focar-nos em serviços estatais no resto deste artigo.
>

Voltemos ao exemplo anterior. Com a versão "quórum safe" da restrição, os três layouts seriam válidos. Mesmo que o FD0 falhasse no segundo layout ou a UD1 falhasse no terceiro layout, a partição continuaria a ter quórum. (A maioria das réplicas ainda estaria em pé.) Com esta versão do constrangimento, o N6 pode quase sempre ser utilizado.

A abordagem "quórum safe" proporciona mais flexibilidade do que a abordagem "diferença máxima". A razão é que é mais fácil encontrar distribuições de réplicas que são válidas em quase qualquer topologia de cluster. No entanto, esta abordagem não pode garantir as melhores características de tolerância a falhas porque algumas falhas são piores que outras. 

Na pior das hipóteses, a maioria das réplicas pode perder-se com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de três falhas serem necessárias para perder quórum com cinco réplicas ou instâncias, pode agora perder a maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>Abordagem adaptativa
Como ambas as abordagens têm pontos fortes e fracos, introduzimos uma abordagem adaptativa que combina estas duas estratégias.

> [!NOTE]
> Este é o comportamento predefinido a começar pela versão 6.2 do Tecido de Serviço. 
> 
> A abordagem adaptativa utiliza a lógica da "diferença máxima" por padrão e muda para a lógica "quórum safe" apenas quando necessário. Cluster Resource Manager descobre automaticamente qual a estratégia necessária, analisando a forma como o cluster e os serviços são configurados.
> 
> Cluster Resource Manager deve usar a lógica "baseada em quórum" para um serviço que ambas as condições são verdadeiras:
>
> * **TargetReplicaSetSize** para o serviço é uniformemente divisível pelo número de domínios de falha e pelo número de domínios de upgrade.
> * O número de nós é inferior ou igual ao número de domínios de avaria multiplicados pelo número de domínios de atualização.
>
> Tenha em mente que o Cluster Resource Manager utilizará esta abordagem tanto para serviços apátridas como apátridas, embora a perda de quórum não seja relevante para serviços apátridas.

Vamos voltar ao exemplo anterior e assumir que um aglomerado agora tem oito nós. O cluster ainda está configurado com cinco domínios de falha e cinco domínios de upgrade, e o valor **TargetReplicaSetSize** de um serviço hospedado nesse cluster permanece cinco. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Como todas as condições necessárias estão satisfeitas, o Cluster Resource Manager utilizará a lógica "baseada em quórum" na distribuição do serviço. Isto permite o uso de N6-N8. Uma possível distribuição de serviços neste caso pode ser assim:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se o valor **targetReplicaSetSize** do seu serviço for reduzido a quatro (por exemplo), o Cluster Resource Manager irá notar essa alteração. Será retomado usando a lógica da "diferença máxima", uma vez que o **TargetReplicaSetSize** já não é dividável pelo número de domínios de falha e domínios de atualização. Como resultado, ocorrerão certos movimentos de réplica para distribuir as quatro réplicas restantes nos nós N1-N5. Desta forma, a versão de "diferença máxima" do domínio da falha e da lógica de domínio de atualização não é violada. 

No layout anterior, se o valor **TargetReplicaSetSize** for de cinco e o N1 for removido do cluster, o número de domínios de atualização torna-se igual a quatro. Mais uma vez, o Cluster Resource Manager começa a usar a lógica da "diferença máxima" porque o número de domínios de atualização já não divide uniformemente o valor **targetReplicaSetSize** do serviço. Como resultado, a réplica R1, quando construída novamente, tem de aterrar na N4 para que o constrangimento para o domínio de avaria e atualização não seja violado.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurar domínios de avaria e atualização
Nas implementações de tecidode serviço azure, os domínios de avaria e os domínios de atualização são definidos automaticamente. Service Fabric recolhe e utiliza a informação ambiental do Azure.

Se estiver a criar o seu próprio cluster (ou quiser executar uma topologia específica em desenvolvimento), pode fornecer o domínio de avaria e atualizar a informação de domínio. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três datacenters (cada um com três prateleiras). Este cluster também tem três domínios de upgrade listrados através desses três datacenters. Aqui está um exemplo da configuração em ClusterManifest.xml:

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

Este exemplo utiliza clusterConfig.json para implementações autónomas:

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
> Ao definir clusters através do Azure Resource Manager, o Azure atribui domínios de avaria e atualiza domínios. Assim, a definição dos tipos de nó e conjuntos de escala de máquina virtual no seu modelo de Gestor de Recursos Azure não inclui informações sobre domínio de falha ou domínio de upgrade.
>

## <a name="node-properties-and-placement-constraints"></a>Propriedades do nó e restrições de colocação
Às vezes (na verdade, na maioria das vezes) você vai querer garantir que certas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem requerer GPUs ou SSDs, e outras podem não. 

Um grande exemplo de direcionar hardware para cargas de trabalho específicas é quase todas as arquiteturas n-tier. Algumas máquinas servem como a extremidade frontal ou o lado api-serve da aplicação e estão expostas aos clientes ou à internet. Diferentes máquinas, muitas vezes com diferentes recursos de hardware, lidam com o trabalho da computação ou das camadas de armazenamento. Estes _geralmente não_ são diretamente expostos a clientes ou à internet. 

O Service Fabric espera que, em alguns casos, cargas de trabalho específicas possam ter de ser executadas em configurações específicas de hardware. Por exemplo:

* Uma aplicação n-tier existente foi "levantada e transferida" para um ambiente de Tecido de Serviço.
* Uma carga de trabalho deve ser executada em hardware específico por razões de desempenho, escala ou isolamento de segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por razões políticas ou de consumo de recursos.

Para suportar este tipo de configurações, o Service Fabric inclui etiquetas que pode aplicar aos nós. Estas etiquetas são chamadas propriedades do *nó.* *Os constrangimentos* de colocação são as declarações anexas aos serviços individuais que seleciona para uma ou mais propriedades do nó. Os constrangimentos de colocação definem onde os serviços devem ser executados. O conjunto de restrições é extensível. Qualquer par chave/valor pode funcionar. 

<center>

![Diferentes cargas de trabalho para um layout de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriedades do nó embutidos
O Tecido de Serviço define algumas propriedades de nó padrão que podem ser usadas automaticamente para que não tenha que defini-las. As propriedades predefinidas definidas em cada nó são **NodeType** e **NodeName**. 

Por exemplo, pode escrever uma `"(NodeType == NodeType03)"`restrição de colocação como . **NodeType** é uma propriedade comumente usada. É útil porque corresponde a 1:1 com um tipo de máquina. Cada tipo de máquina corresponde a um tipo de carga de trabalho numa aplicação tradicional n-tier.

<center>

![Restrições de colocação e propriedades do nó][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de colocação e sintaxe de propriedade nó 
O valor especificado na propriedade do nó pode ser uma corda, Boolean, ou assinada por muito tempo. A declaração no serviço é chamada de *restrição* de colocação porque limita onde o serviço pode funcionar no cluster. O constrangimento pode ser qualquer declaração booleana que opere nas propriedades do nó no cluster. Os selecionadores válidos nestas declarações booleanas são:

* Verificações condicionais para a criação de declarações específicas:

  | Declaração | Sintaxe |
  | --- |:---:|
  | "igual a" | "==" |
  | "não é igual a" | "!=" |
  | "maior que" | ">" |
  | "maior ou igual a" | ">=" |
  | "menos que" | "<" |
  | "menos ou igual a" | "<=" |

* Declarações booleanas para agrupamento e operações lógicas:

  | Declaração | Sintaxe |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "grupo como única declaração" | "()" |

Aqui estão alguns exemplos de declarações básicas de restrição:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Apenas os nódosos em que a declaração de restrição de colocação global avalia "True" podem ter o serviço colocado nele. Os nódosos que não têm uma propriedade definida não correspondem a qualquer restrição de colocação que contenha a propriedade.

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

O exemplo seguinte mostra propriedades do nó definidas via ClusterConfig.json para implementações autónomas ou Template.json para clusters hospedados em Azure. 

> [!NOTE]
> No seu modelo de Gestor de Recursos Azure, o tipo de nó é geralmente parametrizado. Seria mais `"[parameters('vmNodeType1Name')]"` do que nodeType01.
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

Se todos os nós de NodeType01 forem válidos, também `"(NodeType == NodeType01)"`pode selecionar esse tipo de nó com a restrição .

Os constrangimentos de colocação de um serviço podem ser atualizados dinamicamente durante o tempo de execução. Se precisar, pode mover um serviço no cluster, adicionar e remover requisitos, e assim por diante. O Service Fabric garante que o serviço se mantém em pé e disponível mesmo quando este tipo de alterações são feitas.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Os constrangimentos de colocação são especificados para cada instância de serviço nomeada. As atualizações tomam sempre o lugar de (sobreutilização) do que foi previamente especificado.

A definição de cluster define as propriedades num nó. Alterar as propriedades de um nó requer uma atualização para a configuração do cluster. A atualização das propriedades de um nó requer que cada nó afetado recomece a reportar as suas novas propriedades. O Service Fabric gere estas atualizações de rolamento.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerir recursos de cluster
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerir o consumo de recursos no cluster. Gerir recursos de cluster pode significar um par de coisas diferentes. 

Primeiro, há a garantia de que as máquinas não estão sobrecarregadas. Isto significa garantir que as máquinas não estão a executar mais serviços do que podem suportar. 

Em segundo lugar, há o equilíbrio e a otimização, que são cruciais para gerir os serviços de forma eficiente. Ofertas de serviço supreendidas ou sensíveis ao desempenho não permitem que alguns nódos os nossos nódos fiquem quentes enquanto outros estão frios. Nódonos quentes levam à contenção de recursos e mau desempenho. Os nódoos frios representam recursos desperdiçados e custos acrescidos. 

O Tecido de Serviço representa recursos como *métricas.* As métricas são qualquer recurso lógico ou físico que queira descrever ao Tecido de Serviço. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que o Service Fabric pode governar em nós, consulte a governação dos [Recursos.](service-fabric-resource-governance.md) Para obter informações sobre as métricas padrão utilizadas pelo Cluster Resource Manager e como configurar métricas personalizadas, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de colocação e propriedades do nó. As propriedades do nó são descritores estáticos dos próprios nódosos. As métricas descrevem os recursos que os nódosos têm e que os serviços consomem quando funcionam num nó. Uma propriedade nó pode ser **HasSSD** e pode ser definido como verdadeiro ou falso. A quantidade de espaço disponível nesse SSD e quanto é consumido pelos serviços seria uma métrica como "DriveSpaceInMb". 

Tal como para os constrangimentos de colocação e propriedades do nó, o Gestor de Recursos de Cluster de Tecidos de Serviço não entende o que os nomes das métricas significam. Nomes métricos são apenas cordas. É uma boa prática declarar unidades como parte dos nomes métricos que se criam quando podem ser ambíguas.

## <a name="capacity"></a>Capacidade
Se desligasse todo o *equilíbrio*de recursos, o Gestor de Recursos de Cluster de Tecidos de Serviço ainda garantiria que nenhum nó ultrapassasse a sua capacidade. A gestão de excedentes de capacidade é possível a menos que o cluster esteja demasiado cheio ou a carga de trabalho seja maior do que qualquer nó. A capacidade é outra *restrição* que o Cluster Resource Manager usa para entender a quantidade de um recurso que um nó tem. A capacidade remanescente também é rastreada para o cluster como um todo. 

Tanto a capacidade como o consumo ao nível do serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um nó pode ter uma capacidade para "ClientConnections" de 32.768. Outros nódosos podem ter outros limites. Um serviço em execução nesse nó pode dizer que está a consumir 32.256 da métrica "ClientConnections".

Durante o tempo de execução, cluster Resource Manager rastreia a capacidade restante no cluster e em nós. Para controlar a capacidade, o Cluster Resource Manager subtrai o uso de cada serviço a partir da capacidade de um nó onde o serviço funciona. Com esta informação, o Cluster Resource Manager pode descobrir onde colocar ou mover réplicas para que os nós não ultrapassem a capacidade.

<center>

![Nós de cluster e capacidade][Image7]
</center>

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

Pode ver-se capacidades definidas no manifesto do cluster. Aqui está um exemplo para ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Aqui está um exemplo de capacidades definidas via ClusterConfig.json para implementações autónomas ou Template.json para clusters hospedados em Azure: 

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

A carga de um serviço muda muitas vezes dinamicamente. Diga que a carga de "ClientConnections" de uma réplica passou de 1.024 para 2.048. O nó em que estava a funcionar tinha uma capacidade de apenas 512 restantes para aquela métrica. Agora que a réplica ou a colocação do exemplo é inválida, porque não há espaço suficiente naquele nó. Cluster Resource Manager tem que colocar o nó de volta abaixo da capacidade. Reduz a carga no nó que está acima da capacidade movendo uma ou mais réplicas ou instâncias desse nó para outros nós. 

Cluster Resource Manager tenta minimizar o custo das réplicas em movimento. Pode aprender mais sobre [o custo](service-fabric-cluster-resource-manager-movement-cost.md) de movimento e sobre o reequilíbrio de estratégias [e regras.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Capacidade de cluster
Como é que o Gestor de Recursos de Cluster de Tecidos de Serviço impede que o cluster global esteja demasiado cheio? Com carga dinâmica, não há muito que possa fazer. Os serviços podem ter o seu pico de carga independentemente das ações que o Cluster Resource Manager toma. Como resultado, o seu aglomerado com muita sala de cabeceira hoje pode estar sub-alimentado se houver um pico amanhã. 

Os controlos no Cluster Resource Manager ajudam a prevenir problemas. A primeira coisa que pode fazer é evitar a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que cria um serviço apátrida, e tem alguma carga associada a ele. O serviço preocupa-se com a métrica "DiskSpaceInMb". O serviço consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Quer criar três instâncias do serviço. Isto significa que precisa de 15 unidades de "DiskSpaceInMb" para estar presente no cluster para que possa até criar estas instâncias de serviço.

Cluster Resource Manager calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante no cluster. Se não houver espaço suficiente, o Cluster Resource Manager rejeita a chamada para criar um serviço.

Como a exigência é apenas que 15 unidades estarão disponíveis, você pode alocar este espaço de várias maneiras diferentes. Por exemplo, pode haver uma unidade de capacidade restante em 15 nós diferentes, ou três unidades restantes de capacidade em cinco nós diferentes. Se o Cluster Resource Manager pode reorganizar as coisas para que existam cinco unidades disponíveis em três nós, coloca o serviço. Reorganizar o cluster é geralmente possível a menos que o cluster esteja quase cheio ou os serviços existentes não possam ser consolidados por alguma razão.

## <a name="buffered-capacity"></a>Capacidade tamponada
A capacidade tamponada é outra característica do Cluster Resource Manager. Permite a reserva de alguma parte da capacidade total do nó. Este tampão de capacidade é utilizado apenas para colocar serviços durante atualizações e falhas no nó. 

A capacidade tamponada é especificada globalmente por métrica para todos os nós. O valor que escolhe para a capacidade reservada é uma função do número de domínios de avaria e atualização que tem no cluster. Mais domínios de avaria e atualização significam que pode escolher um número mais baixo para a sua capacidade tamponada. Se tiver mais domínios, pode esperar que quantidades menores do seu cluster não estejam disponíveis durante atualizações e falhas. Especificar a capacidade tampão só faz sentido se também tiver especificado a capacidade do nó para uma métrica.

Aqui está um exemplo de como especificar a capacidade tampão em ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Aqui está um exemplo de como especificar a capacidade tampão através do ClusterConfig.json para implementações autónomas ou Template.json para clusters hospedados em Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster está fora da capacidade tamponada para uma métrica. Impedir a criação de novos serviços para preservar o tampão garante que as melhorias e falhas não fazem com que os nós ultrapassem a capacidade. A capacidade tamponada é opcional, mas recomendamo-la em qualquer cluster que defina uma capacidade para uma métrica.

Cluster Resource Manager expõe esta informação de carga. Para cada métrica, esta informação inclui: 
- As definições de capacidade tamponadas.
- A capacidade total.
- O consumo atual.
- Se cada métrica é considerada equilibrada ou não.
- Estatísticas sobre o desvio padrão.
- Os nódosos que têm mais e menos carga.  
  
O seguinte código mostra um exemplo dessa saída:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Passos seguintes
* Para obter informações sobre a arquitetura e fluxo de informação dentro do Cluster Resource Manager, consulte a visão geral da [arquitetura do Cluster Resource Manager.](service-fabric-cluster-resource-manager-architecture.md)
* Definir métricas de desfragmentação é uma forma de consolidar a carga nos nódosos em vez de espalhá-la. Para aprender a configurar a desfragmentação, consulte [desfragmentação de métricas e carga em Tecido de Serviço](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Comece do início e obtenha uma introdução ao Gestor de Recursos de Cluster de [Tecidos de Serviço](service-fabric-cluster-resource-manager-introduction.md).
* Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, consulte [Balanceing your Service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
