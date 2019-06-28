---
title: Descrever um cluster utilizando o Gestor de recursos de Cluster | Documentos da Microsoft
description: Descreva um cluster do Service Fabric ao especificar domínios de falha, domínios de atualização, propriedades de nó e as capacidades de nós para o Gestor de recursos do Cluster.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271637"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrever um cluster do Service Fabric utilizando o Gestor de recursos de Cluster
A funcionalidade do Gestor de recursos de Cluster do Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nós

Durante o tempo de execução, o Gestor de recursos de Cluster utiliza estas informações para garantir a elevada disponibilidade dos serviços em execução no cluster. Ao impor estas regras importantes, também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área de falha de coordenada. Uma única máquina é um domínio de falha. Ele pode falhar em seus próprio por várias razões, da power falhas de fornecimento para falhas de unidade ao firmware do NIC ruim. 

As máquinas ligadas ao mesmo comutador Ethernet estão no mesmo domínio de falha. Então, são máquinas que partilham uma única fonte de energia ou numa única localização. 

Uma vez que é natural para falhas de hardware para se sobrepõem, domínios de falha são inerentemente hierárquicos. São representadas como URIs no Service Fabric.

É importante que domínios de falha estão configurados corretamente porque o Service Fabric utiliza estas informações para colocar em segurança a serviços. Service Fabric não quer colocar os serviços, de modo a que a perda de um domínio de falha (causada pela falha de algum componente) faz com que um serviço descer. 

No ambiente do Azure, o Service Fabric utiliza as informações de domínio de falhas fornecidas pelo ambiente para configurar corretamente os nós do cluster em seu nome. Para as instâncias de autónomo do Service Fabric, os domínios de falha são definidos no momento em que o cluster está configurado. 

> [!WARNING]
> É importante que as informações de domínio de falhas fornecidas para o Service Fabric estão correta. Por exemplo, digamos que nós de cluster do Service Fabric estão em execução dentro de 10 máquinas virtuais em execução em 5 anfitriões físicos. Neste caso, muito embora haja 10 máquinas virtuais, existem apenas 5 diferentes (nível superior) domínios de falha. Partilharem o mesmo anfitrião físico faz com que as VMs partilhar o mesmo domínio de falha de raiz, uma vez que as VMs ocorrer falha coordenada, se falhar o respetivo anfitrião físico.  
>
> Service Fabric espera que o domínio de falhas de nó não para alterar. Outros mecanismos de garantir a elevada disponibilidade de VMs, como [HA-VMs](https://technet.microsoft.com/library/cc967323.aspx), poderá causar conflitos com o Service Fabric. Estes mecanismos utilizam migração transparente das VMs de um anfitrião para outro. Eles não reconfigurar ou notificar o código em execução dentro da VM. Como tal, eles são *nepodporuje* como ambientes para executar o Service Fabric clusters. 
>
> Recursos de infraestrutura do serviço devem ser a tecnologia de apenas de elevada disponibilidade empregada. Mecanismos, como em direto de migração de VM e SANs não são necessários. Se esses mecanismos são utilizados em conjunto com o Service Fabric, eles _reduzir_ de disponibilidade de aplicações e a confiabilidade. O motivo é que eles introduzem complexidade adicional, adicionar fontes centralizadas de falha e utilizam estratégias de fiabilidade e disponibilidade que entram em conflito com as pessoas de Service Fabric. 
>
>

No seguinte gráfico, podemos todas as entidades que contribuem para domínios de falha e listam todos os domínios de falha diferentes que resultam de cores. Neste exemplo, temos de centros de dados ("DC"), racks ("R") e os painéis ("B"). Se cada painel contém mais do que uma máquina virtual, poderá haver outra camada na hierarquia de domínio de falhas.

<center>

![Nós organizados por meio de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Gestor de recursos de Cluster do Service Fabric considera os domínios de falha no cluster e planos de layouts. As réplicas com monitoração de estado ou instâncias sem monitoração de estado para um serviço são distribuídas para que estejam em domínios de falha. Distribui o serviço em domínios de falha, garante que a disponibilidade do serviço não é comprometida quando a falha de um domínio de falha em qualquer nível da hierarquia.

Gestor de recursos de cluster não importa quantas camadas existem na hierarquia de domínio de falhas. Ele tenta Certifique-se de que a perda de qualquer um parte da hierarquia não afeta a serviços em execução no mesmo. 

É melhor se o mesmo número de nós é em cada nível de profundidade na hierarquia de domínio de falhas. Se o "árvore" de domínios de falha é desequilibrado no seu cluster, é mais difícil para o Resource Manager de Cluster para descobrir a melhor alocação dos serviços. Layouts de domínio de falhas desequilibrado significam que a perda de alguns domínios afeta a disponibilidade dos serviços mais do que outros domínios. Como resultado, o Gestor de recursos do Cluster é interrompida entre dois objetivos: 

* Quer utilize as máquinas no domínio "pesado", ao disponibilizar serviços nos mesmos. 
* Quer colocar os serviços em outros domínios, para que a perda de um domínio não causem problemas. 

O que o aspeto de domínios desequilibrados? O diagrama seguinte mostra dois layouts de cluster diferente. No primeiro exemplo, os nós são distribuídos uniformemente por domínios de falha. No segundo exemplo, um domínio de falha tem muitos mais nós que os outros domínios de falha. 

<center>

![Dois layouts de cluster diferente][Image2]
</center>

No Azure, a escolha de qual falha domínio contém um nó é gerida por si. Mas, dependendo do número de nós que Aprovisiona, ainda pode acabar com domínios de falha que tem mais nós nos mesmos do que em outros. 

Por exemplo, digamos que tem cinco domínios de falha no cluster, mas aprovisionar sete nós para um tipo de nó (**NodeType**). Neste caso, os primeiro dois domínios de falha acabam com mais nós. Se continuar a implementar mais **NodeType** instâncias com apenas algumas instâncias, o problema fica ainda pior. Por esse motivo, recomendamos que o número de nós em cada tipo de nó é um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização
Domínios de atualização são outro recurso que ajuda a Service Fabric Cluster Resource Manager entender o layout do cluster. Domínios de atualização definem conjuntos de nós que são atualizadas ao mesmo tempo. Domínios de atualização ajudam a compreender e orquestrar operações de gestão, como atualizações de Gestor de recursos do Cluster.

Domínios de atualização são muito como domínios de falha, mas com algumas diferenças fundamentais. Em primeiro lugar, áreas de falhas de hardware coordenada definem domínios de falha. Domínios de atualização, por outro lado, são definidos pela política. Tem de decidir quantos desejar, em vez de permitir que o ambiente de ditar o número. Pode ter tantos domínios de atualização de nós. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, eles são mais parecido com uma marca simple. 

O diagrama seguinte mostra três domínios de atualização repartidos em três domínios de falha. Ela também mostra uma possível colocação para três réplicas diferentes de um serviço com estado, onde cada acaba em domínios de atualização e de falha diferente. Esse posicionamento permite que a perda de um domínio de falha no meio de uma atualização de serviço e ainda terá uma cópia do código e dados.  

<center>

![Domínios com a colocação e de falha de atualização][Image3]
</center>

Há prós e contras em ter um grande número de domínios de atualização. Domínios de atualização mais significa que cada passo da atualização é mais granular e afeta um número menor de nós ou serviços. Serviços menos tem de mover cada vez, apresentando o menor volume de alterações no sistema. Isso tende a melhorar a fiabilidade, porque o menor do serviço é afetado por qualquer problema que introduziu durante a atualização. Mais domínios de atualização também significam que terá de memória intermédia menos disponível nos outros nós para lidar com o impacto da atualização. 

Por exemplo, se tiver cinco domínios de atualização, os nós em cada estiver lidando com aproximadamente 20 por cento do tráfego. Se tiver de desativar esse domínio de atualização para uma atualização, essa carga, normalmente, tem de ir a alguma lugar. Uma vez que tenha quatro restantes domínios de atualização, cada um tem de ter espaço para cerca de 5% do tráfego total. Domínios de atualização mais significam que precisa de menos memória intermédia em nós do cluster. 

Considere se tiver 10 domínios de atualização em vez disso. Nesse caso, cada domínio de atualização manipula apenas cerca de 10% do tráfego total. Quando uma atualização passos através do cluster, cada domínio precisaria ter espaço para apenas cerca de 1.1% do tráfego total. Mais domínios de atualização geralmente permitem executar os nós numa utilização mais elevada, porque tem capacidade de reserva menos. O mesmo é verdadeiro para domínios de falha.  

A desvantagem de ter vários domínios de atualização é que as atualizações tendem a demorar mais tempo. Service Fabric aguarda um curto período de tempo depois de um domínio de atualização concluído e realiza verificações antes de iniciar a atualização seguinte. Esses atrasos ativar detectando problemas introduzidos pela atualização antes de continua a atualização. O compromisso é aceitável, pois evita que alterações ruins afetem muito do serviço de cada vez.

A presença de domínios de atualização muito poucos tem muitos efeitos de lado negativos. Enquanto cada domínio de atualização está pendente e a ser atualizado, não uma grande parte da sua capacidade geral está disponível. Por exemplo, se tiver apenas três domínios de atualização, está fazendo para baixo de aproximadamente um terço do seu serviço geral ou a capacidade do cluster ao mesmo tempo. Ter grande parte do seu serviço para baixo ao mesmo tempo não é desejável porque é necessário capacidade suficiente no resto do seu cluster para processar a carga de trabalho. Manter que esse buffer significa que, durante o funcionamento normal, esses nós são que menos carregado do que seria, caso contrário. Isso aumenta o custo de executar seu serviço.

Não existe nenhum limite real para o número total de domínios de falha ou atualização num ambiente ou restrições sobre como que se sobreponham. No entanto, existem padrões comuns:

- Domínios de falha e domínios de atualização mapeado 1:1
- Um domínio de atualização por nó (instância de sistema operacional física ou virtual)
- Um modelo de "repartido" ou "matrix" em que os domínios de falha e domínios de atualização formam uma matriz com máquinas normalmente em execução para baixo as diagonals

<center>

![Layouts de domínios de falha e atualização][Image4]
</center>

Existe uma resposta melhor para escolher que esquema. Cada uma tem prós e contras. Por exemplo, o modelo de 1FD:1UD é fácil de configurar. O modelo de um domínio de atualização por modelo de nó é mais como o que as pessoas são utilizados para. Durante as atualizações, cada nó é atualizada de forma independente. Isso é semelhante a como pequenos conjuntos de máquinas foram atualizados manualmente no passado.

O modelo mais comuns é a matriz de FD/UD, onde os domínios de falha e domínios de atualização formam uma tabela e nós são colocados iniciar ao longo do diagonal. Esse é o modelo utilizado por predefinição em clusters do Service Fabric no Azure. Para clusters com vários nós, tudo o que acaba se parecendo com um padrão de matriz densa.

> [!NOTE]
> Clusters do Service Fabric alojados no Azure não suportam a alteração a estratégia de predefinição. Apenas os clusters autónomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de atualização e de falha e o comportamento resultante
### <a name="default-approach"></a>Abordagem de predefinição
Por predefinição, o Gestor de recursos do Cluster mantém Balanceados entre domínios de falha e atualização de serviços. Isso é modelado como um [restrição](service-fabric-cluster-resource-manager-management-integration.md). A restrição de Estados de domínios de atualização e de falha: "Para uma partição de determinado serviço há nunca deve ser uma diferença maior que um no número de objetos do serviço (instâncias de serviço sem estado ou réplicas de serviço com estado) entre quaisquer dois domínios no mesmo nível de hierarquia."

Digamos que esta restrição fornece uma garantia de "máximo diferença". A restrição de domínios de falha e atualização impede que certos movimentos ou disposições que violam a regra.

Por exemplo, vamos supor que temos um cluster connosco seis, configurada com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos supor que a criação de um serviço com uma **TargetReplicaSetSize** (ou, para um serviço sem estado, **InstanceCount**) valor de cinco. N1 N5 direcionado para as réplicas. Na verdade, N6 nunca é usada, independentemente de quantos serviços assim que cria. Mas por quê? Vamos ver a diferença entre o layout atual e o que aconteceria se N6 for escolhida.

Este é o número total de réplicas por domínio de atualização e de falha e o esquema, temos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este esquema é balanceada em termos de nós por domínio de falha e o domínio de atualização. Ele também é balanceado em termos de número de réplicas por domínio de atualização e de falha. Cada domínio tem o mesmo número de nós e o mesmo número de réplicas.

Agora, vejamos o que aconteceria se tivéssemos usado N6 em vez de N2. Como seriam as réplicas distribuídas, em seguida?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Este esquema viola a nossa definição da garantia de "máximo diferença" para a restrição de domínio de falhas. FD0 tem duas réplicas, ao passo que FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, que é maior do que a diferença de máxima de um. Porque a restrição seja violada, Gestor de recursos de Cluster não permite esse esquema. Da mesma forma, se escolhemos N2 e N6 (em vez de N1 e N2), Obteremos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este esquema é balanceada em termos de domínios de falha. Mas agora ele está violação a restrição de domínio de atualização, porque UD0 tem zero réplicas e UD1 tem dois. Este esquema também é inválida e não ser escolhida pelo Gestor de recursos do Cluster.

Essa abordagem para a distribuição de réplicas com monitoração de estado ou instâncias sem monitoração de estado fornece a tolerância de falhas possível melhor. Se um domínio ficar inativo, o número mínimo de réplicas/instâncias é perdido. 

Por outro lado, essa abordagem pode ser demasiado restritivo e permite ao cluster utilizar todos os recursos. Para determinadas configurações de cluster, não não possível utilizar alguns nós. Isto pode fazer com que o Service Fabric não colocar seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns de nós do cluster não podem ser utilizado (N6 no exemplo). Mesmo que adicionou nós a esse cluster (N7 N10), instâncias/réplicas seria colocadas apenas em N1 – N5 devido a restrições nos domínios de falha e atualização. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Abordagem alternativa

Gestor de recursos de cluster suporta a outra versão da restrição para domínios de falha e atualização. Ele permite que o posicionamento ao mesmo tempo garantindo ainda um nível mínimo de segurança. A restrição alternativa pode ser declarada da seguinte maneira: "Para uma partição de determinado serviço, distribuição de réplica em vários domínios deve garantir que a partição não sofrem uma perda de quórum." Digamos que esta restrição fornece uma garantia de "quórum seguro". 

> [!NOTE]
> Para um serviço com estado, definimos *perda de quórum* numa situação quando a maioria das réplicas de partição estão indisponíveis, ao mesmo tempo. Por exemplo, se **TargetReplicaSetSize** é cinco, um conjunto de qualquer três réplicas representa o quórum. Da mesma forma, se **TargetReplicaSetSize** é seis, quatro réplicas são necessárias para o quórum. Em ambos os casos, não mais do que duas réplicas podem ficar indisponíveis ao mesmo tempo se a partição que deseja continuar a funcionar normalmente. 
>
> Para um serviço sem estado, não há nenhum algo como *perda de quórum*. Serviços sem estado continuam a funcionar, normalmente, mesmo que a maioria das instâncias ficam inativos, ao mesmo tempo. Então, vamos nos concentrar em serviços com estado no resto deste artigo.
>

Vamos voltar para o exemplo anterior. Com a versão de "quórum seguro" da restrição, todos os três layouts seria válidos. Mesmo que FD0 falhou no layout da segunda ou UD1 falhou no layout da terceira, a partição ainda teria o quórum. (A maioria das réplicas ainda seria cópia de segurança.) Com esta versão da restrição, N6 quase sempre pode ser utilizado.

A abordagem de "quórum seguro" fornece mais flexibilidade do que a abordagem de "máximo diferença". O motivo é que ele é mais fácil encontrar distribuições de réplica que são válidas em quase qualquer topologia do cluster. No entanto, essa abordagem não pode garantir a falha melhores características de tolerância porque algumas falhas são pior do que outras pessoas. 

Na pior das hipóteses, a maioria das réplicas pode ser perdida com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de três falhas necessárias perder o quórum com cinco réplicas ou instâncias, pode agora perder uma maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>Abordagem adaptável
Uma vez que ambas as abordagens têm vantagens e desvantagens, introduzimos uma abordagem adaptável, que combina essas duas estratégias.

> [!NOTE]
> Este é o comportamento padrão a partir do Service Fabric versão 6.2. 
> 
> A abordagem adaptável usa a lógica de "máximo diferença" por predefinição e muda para a lógica de "quórum seguro" apenas quando necessário. Gestor de recursos de cluster detecta automaticamente qual estratégia é necessária examinar como é que o cluster e os serviços são configurados.
> 
> Gestor de recursos de cluster deve usar a lógica de "quórum com base" para um serviço ambas estas condições forem verdadeiras:
>
> * **TargetReplicaSetSize** para o serviço é uniformemente divisível pelo número de domínios de falha e o número de domínios de atualização.
> * O número de nós é menor ou igual ao número de domínios de falha, multiplicados pelo número de domínios de atualização.
>
> Lembre-se de que o Resource Manager de Cluster irá utilizar esta abordagem para serviços com e sem monitoração de estado, mesmo que a perda de quórum não é relevante para serviços sem estado.

Voltemos ao exemplo anterior e partem do princípio de que um cluster agora tem oito nós. O cluster ainda está configurado com cinco domínios de falha e cinco domínios de atualização e o **TargetReplicaSetSize** valor de um serviço alojado nesse cluster permanece cinco. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Uma vez que todas as condições necessárias são cumpridas, Gestor de recursos do Cluster irá utilizar a lógica de "o quórum com base em" o serviço de distribuição. Isto permite a utilização de N6 N8. Uma distribuição de serviço possíveis em vez disso pode ser assim:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se o serviço **TargetReplicaSetSize** valor é reduzido para quatro (por exemplo), Gestor de recursos de Cluster vai reparar que essa alteração. Será retomada usando a lógica de "máximo diferença", porque **TargetReplicaSetSize** não deixa de poder é dividable pelo número de domínios de falha e domínios de atualização. Como resultado, irão ocorrer em certos movimentos de réplica distribuir as réplicas de quatro restantes em nós N1 N5. Dessa forma, a versão de "máximo diferença" da lógica de domínio de falha e atualização do domínio não é violada. 

No layout anterior, se o **TargetReplicaSetSize** valor é cinco e N1 é removida do cluster, o número de domínios de atualização torna-se igual para quatro. Novamente, o Gestor de recursos de Cluster é iniciado com a lógica de "máximo diferença", porque o número de domínios de atualização não divide o serviço de maneira uniforme **TargetReplicaSetSize** mais valor. Como resultado, réplica R1, quando criada novamente, tem de parar em N4 para que a restrição para o domínio de atualização e de falha não é violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/D |N/D |N/D |N/D |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurar domínios de falha e atualização
Nas implementações do Service Fabric alojado no Azure, domínios de falha e domínios de atualização são definidos automaticamente. Service Fabric seleciona e utiliza as informações do ambiente do Azure.

Se estiver a criar seu próprio cluster (ou pretende executar uma topologia específica no desenvolvimento), pode fornecer o domínio de falhas e informações de domínio de atualização por conta própria. Neste exemplo, definimos um cluster de desenvolvimento local do nó de nove que abrange três datacenters (cada um com três racks). Este cluster tem também três domínios de atualização repartidos por essas três datacenters. Eis um exemplo da configuração no ClusterManifest.xml:

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

Este exemplo usa ClusterConfig.json para das implementações autónomas:

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
> Durante a definição de clusters através do Azure Resource Manager, o Azure atribui domínios de falha e domínios de atualização. Para que a definição dos seus tipos de nós e o dimensionamento de máquinas virtuais define modelo do Azure Resource Manager não inclui informações sobre o domínio de falha ou domínio de atualização.
>

## <a name="node-properties-and-placement-constraints"></a>Propriedades de nó e restrições de posicionamento
Às vezes (na verdade, na maioria das vezes) vai querer garantir que determinadas cargas de trabalho são executados apenas em determinados tipos de nós do cluster. Por exemplo, algumas cargas de trabalho poderão necessitar de GPUs ou SSDs e outras empresas poderão não. 

Um ótimo exemplo de filtragem de hardware para cargas de trabalho específicas é quase todos os arquitetura de n camadas. Determinados computadores servem como o front-end ou do lado do serviço de API do aplicativo e são expostos a clientes ou à internet. Computadores diferentes, muitas vezes, com recursos de hardware diferente, lidar com o trabalho de camadas de computação ou de armazenamento. Normalmente, são _não_ expostos diretamente a clientes ou à internet. 

Service Fabric espera que, em alguns casos, determinadas cargas de trabalho poderão ter de executar em configurações de hardware específico. Por exemplo:

* Uma aplicação de n camadas existente foi "elevadas e mudou" num ambiente do Service Fabric.
* Uma carga de trabalho tem de ser executada no hardware específico, por motivos de isolamento de desempenho, dimensionamento ou segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de consumo de política ou recurso.

Para oferecer suporte a esses tipos de configurações, o Service Fabric inclui marcas que pode aplicar a nós. Essas marcas são chamadas *propriedades de nó*. *Restrições de posicionamento* são as declarações anexadas aos serviços individuais que selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem em que os serviços devem ser executados. O conjunto de restrições é extensível. Qualquer par de chave/valor pode trabalhar. 

<center>

![Diferentes cargas de trabalho para um esquema de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriedades de nó incorporado
Service Fabric define algumas propriedades de nó predefinidos que podem ser utilizadas automaticamente para que não precisa defini-las. As propriedades predefinidas definidas em cada nó são **NodeType** e **NodeName**. 

Por exemplo, pode escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. **O NodeType** é uma propriedade comumente utilizada. É útil porque ele corresponde 1:1 com um tipo de uma máquina. Cada tipo de máquina corresponde a um tipo de carga de trabalho num aplicativo de n camadas tradicional.

<center>

![Restrições de posicionamento e propriedades de nó][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de posicionamento e a sintaxe de propriedade de nó 
O valor especificado na propriedade de nó pode ser uma cadeia de caracteres, booleanos, ou assinado há muito tempo. A instrução no serviço é chamada um posicionamento *restrição* pois isso restringe as onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booleana que opera nas propriedades de nó do cluster. Os seletores válidos nestas instruções booleanos são:

* Condicional verifica-se para a criação de instruções específicas:

  | Instrução | Sintaxe |
  | --- |:---:|
  | "igual a" | "==" |
  | "não igual a" | "!=" |
  | "maior que" | ">" |
  | "maior que ou igual a" | ">=" |
  | "menor que" | "<" |
  | "menor que ou igual a" | "<=" |

* Booleanas instruções para operações de agrupamentos e lógicas:

  | Instrução | Sintaxe |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "grupo como a única instrução" | "()" |

Aqui estão alguns exemplos de declarações de restrição básica:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Apenas nós em que a instrução de restrição de posicionamento global avalia como "True" podem ter o serviço colocado na mesma. Nós que não tem uma propriedade definida não correspondem a qualquer restrição de posicionamento que contém a propriedade.

Digamos que as seguintes propriedades de nó foram definidas para um tipo de nó ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

O exemplo seguinte mostra as propriedades de nó definidas por meio de ClusterConfig.json para das implementações autónomas ou Template para clusters alojado no Azure. 

> [!NOTE]
> No modelo do Azure Resource Manager, o tipo de nó é normalmente parametrizado. Ela ficaria `"[parameters('vmNodeType1Name')]"` em vez de NodeType01.
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

É possível criar o posicionamento do serviço *restrições* para um serviço da seguinte forma:

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

Se todos os nós do NodeType01 são válidos, também pode selecionar esse tipo de nó com a restrição `"(NodeType == NodeType01)"`.

Restrições de posicionamento de um serviço podem ser atualizadas dinamicamente durante o tempo de execução. Se for necessário, pode mover um serviço no cluster, adicionar e remover requisitos e assim por diante. Service Fabric garante que o serviço permaneça ativos e disponíveis, mesmo quando esses tipos de alterações são feitos.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições de posicionamento são especificadas para cada instância de serviço com nome. Atualizações sempre tomar o lugar de (substituir) o que foi anteriormente especificado.

A definição do cluster define as propriedades num nó. Alterar as propriedades de um nó exige uma atualização para a configuração do cluster. Atualizar propriedades de um nó requer que cada nó afetado para reiniciar para comunicar as respetivas propriedades de novo. O Service Fabric gere estas atualizações sem interrupção.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerir recursos de cluster
Uma das tarefas mais importantes de qualquer orchestrator é ajudar a gerir o consumo de recursos do cluster. Gerir recursos de cluster, pode significar duas coisas diferentes. 

Em primeiro lugar, aí é garantir que as máquinas não estão sobrecarregadas. Isso significa certificar-se de que as máquinas não estão em execução mais serviços que eles possam tratar. 

Em segundo lugar, há balanceamento e otimização, que são críticos para executar os serviços de forma eficiente. Ofertas de serviço económica ou sensíveis a desempenho, não é possível que alguns nós ser frequente, enquanto outras estão frios. Nós frequente levam à contenção de recursos e fraco desempenho. Nós frios representam desperdício de recursos e os custos de aumento. 

O Service Fabric representa os recursos conforme *métricas*. As métricas são qualquer recurso lógico ou físico que pretende descrever para o Service Fabric. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb." Para obter informações sobre os recursos físicos que podem reger o Service Fabric em nós, consulte [governação de recursos](service-fabric-resource-governance.md). Para obter informações sobre como configurar métricas personalizadas e respetivas utilizações, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de posicionamento e propriedades de nó. Propriedades de nó são estáticos descritores dos próprios nós. Métricas descrevem os recursos que têm de nós e que serviços consumam quando executados num nó. Uma propriedade de nó pode ser **HasSSD** e que pode ser definido como VERDADEIRO ou FALSO. A quantidade de espaço disponível nesse SSD e o quanto é consumida pelos serviços seria uma métrica como "DriveSpaceInMb." 

Assim como para restrições de posicionamento e propriedades de nó, Gestor de recursos de Cluster do Service Fabric não entende que os nomes da média de métricas. Nomes de métricas são apenas cadeias de caracteres. É uma boa prática para declarar unidades como parte dos nomes de métrica que criou quando podem ser ambíguos.

## <a name="capacity"></a>Capacidade
Se desativado todos os recursos *balanceamento*, Gestor de recursos de Cluster do Service Fabric ainda a garantir que nenhum nó passa pela sua capacidade. É possível gerir estouros de capacidade, a menos que o cluster é demasiado completo ou a carga de trabalho é maior do que qualquer nó. Capacidade é outra *restrição* que utiliza o Resource Manager de Cluster para compreender qual a quantidade de um recurso de um nó tem. Capacidade restante também é controlado para o cluster como um todo. 

A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um nó pode ter uma capacidade para "ClientConnections" de 32,768. Outros nós podem ter outros limites. Um serviço em execução nesse nó pode dizer que está atualmente a consumir 32,256 da métrica "ClientConnections."

Durante o tempo de execução, o Gestor de recursos de Cluster controla capacidade restante no cluster e em nós. Para controlar a capacidade, o Gestor de recursos de Cluster subtrai utilização de cada serviço da capacidade de um nó em que o serviço é executado. Com essas informações, o Gestor de recursos do Cluster pode descobrir onde colocar ou mover réplicas para que nós não passam pela capacidade.

<center>

![Nós de cluster e a capacidade][Image7]
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

Pode ver as capacidades definidas no manifesto do cluster. Eis um exemplo para ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Eis um exemplo de capacidades definida por meio de ClusterConfig.json para das implementações autónomas ou Template, para clusters alojado no Azure: 

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

Um serviço carregar dinamicamente, muitas vezes, as alterações. Digamos que o carregamento de uma réplica de "ClientConnections" foi alterado de 1.024 para 2.048. O nó que estava em execução no, em seguida, tinha uma capacidade de 512 apenas restante para essa métrica. Agora essa réplica ou de posicionamento da instância é inválido, porque não existe espaço suficiente nesse nó. Gestor de recursos do cluster tem de obter o nó de volta abaixo da capacidade. Ele reduz a carga no nó que está acima da capacidade ao mover um ou mais das réplicas ou instâncias de nesse nó para outros nós. 

Gestor de recursos de cluster tenta minimizar o custo da movimentação de réplicas. Pode saber mais sobre [custo de movimento](service-fabric-cluster-resource-manager-movement-cost.md) e sobre [reequilibrar estratégias e regras](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade do cluster
Como é que o Gestor de recursos de Cluster do Service Fabric manter o cluster geral do que está a ser demasiado completo? Com a carga dinâmico, não há muita pode fazer. Serviços podem ter o pico de carga independentemente das ações que usa o Resource Manager de Cluster. Como resultado, o cluster com bastante espaço hoje pode ser inferior se houver um pico de amanhã. 

Os controlos no Gestor de recursos do Cluster ajudam a evitar problemas. A primeira coisa que pode fazer é impedir que a criação de novas cargas de trabalho que faria com que o cluster para ficar cheio.

Digamos que criar um serviço sem estado e tem alguns carga associada a ele. O serviço se preocupa a métrica "DiskSpaceInMb". O serviço irá consumir cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Pretende criar três instâncias do serviço. Isso significa que terá de 15 unidades de "DiskSpaceInMb" estar presente no cluster que pode até mesmo criar estas instâncias de serviço.

Gestor de recursos de cluster continuamente calcula a capacidade e o consumo de cada métrica para que ele possa determinar a capacidade restante do cluster. Se não existir espaço suficiente, o Gestor de recursos de Cluster rejeita a chamada para criar um serviço.

Como o requisito é apenas que 15 unidades estará disponíveis, é possível atribuir este espaço de muitas formas diferentes. Por exemplo, poderá haver uma unidade restante da capacidade em 15 nós diferentes, ou três unidades restantes da capacidade em cinco nós diferentes. Se o Gestor de recursos do Cluster pode reorganizar as coisas, portanto, existem cinco unidades disponíveis em três nós, ele coloca o serviço. Reorganizar o cluster é possível que, normalmente, a menos que o cluster está quase cheia ou os serviços existentes não podem ser consolidados por algum motivo.

## <a name="buffered-capacity"></a>Capacidade em buffer
A capacidade em buffer é outro recurso do Gestor de recursos do Cluster. Ele permite que a reserva de uma parte da capacidade global do nó. Esse buffer de capacidade é utilizado apenas para colocar serviços durante as atualizações e falhas de nó. 

Capacidade em buffer é especificada globalmente por métrica para todos os nós. O valor que escolher para a capacidade de reserva é uma função do número de domínios de falha de atualização que tiver no cluster. Domínios de atualização e de falha mais significam que pode escolher um número mais baixo para a sua capacidade em buffer. Se tiver mais de domínios, pode esperar quantidades menores do seu cluster estar indisponível durante as atualizações. Especificar a capacidade em buffer faz sentido apenas se especificou também a capacidade de nós para uma métrica.

Eis um exemplo de como especificar a capacidade em buffer na ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Eis um exemplo de como especificar a capacidade em buffer via ClusterConfig.json para das implementações autónomas ou Template para clusters alojado no Azure:

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

A criação de novos serviços falha quando o cluster sem capacidade em buffer para uma métrica. Impedir a criação de novos serviços para preservar a memória intermédia garante que as atualizações e falhas não causam nós fiquem acima da capacidade. Capacidade em buffer é opcional, mas recomendamos em qualquer cluster que define uma capacidade para uma métrica.

Gestor de recursos de cluster mostra essas informações de carga. Para cada uma, estas informações incluem: 
- As definições de capacidade em buffer.
- A capacidade total.
- O consumo atual.
- Se é considerada cada uma com o balanceamento de ou não.
- Estatísticas sobre o desvio-padrão.
- Os nós que têm o maior e menor carga.  
  
O código seguinte mostra um exemplo dessa saída:

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

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações sobre o fluxo de arquitetura e as informações no Gestor de recursos do Cluster, consulte [descrição geral da arquitetura do Resource Manager de Cluster](service-fabric-cluster-resource-manager-architecture.md).
* A definição de métricas de desfragmentação é uma forma de consolidar a carga em nós em vez de dispersão-lo. Para saber como configurar a desfragmentação, veja [desfragmentação de métricas e de carga no Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Para saber como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, veja [balanceamento de cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
