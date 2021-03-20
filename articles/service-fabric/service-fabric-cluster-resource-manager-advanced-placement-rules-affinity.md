---
title: Gestor de Recursos do Cluster de Tecido de Serviço - Affinity
description: Visão geral da afinidade do serviço para os serviços do Azure Service Fabric e orientação sobre a configuração da afinidade do serviço.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005739"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar e utilizar afinidade de serviço no Tecido de Serviço
A affinity é um controlo que é fornecido principalmente para ajudar a facilitar a transição de aplicações monolíticas maiores para o mundo da nuvem e microserviços. Também é usado como uma otimização para melhorar o desempenho dos serviços, embora isso possa ter efeitos colaterais.

Digamos que está a trazer uma app maior, ou que não foi desenhada com microserviços em mente, para o Service Fabric (ou qualquer ambiente distribuído). Este tipo de transição é comum. Começa-se por elevar toda a aplicação para o ambiente, embalar e certificar-se de que está a funcionar sem problemas. Depois começas a dividi-lo em diferentes serviços menores que falam uns com os outros.

Eventualmente poderá descobrir que a aplicação está a ter alguns problemas. As questões geralmente se enquadram numa destas categorias:

1. Alguns componentes X na aplicação monolítica tinham uma dependência indocumentada do componente Y, e você acabou de transformar esses componentes em serviços separados. Como estes serviços estão agora a funcionar em diferentes nós no aglomerado, estão avariados.
2. Estes componentes comunicam através (tubos de nome local | ficheiros de memória partilhada | no disco) e precisam realmente de ser capazes de escrever a um recurso local partilhado por razões de desempenho neste momento. Essa dependência difícil é removida mais tarde, talvez.
3. Está tudo bem, mas acontece que estes dois componentes são na verdade sensíveis ao chato/desempenho. Quando os mudaram para serviços separados, o desempenho global da aplicação afundou ou a latência aumentou. Como resultado, a aplicação global não está a corresponder às expectativas.

Nestes casos, não queremos perder o nosso trabalho de refactor, e não queremos voltar ao monólito. A última condição pode até ser desejável como uma simples otimização. No entanto, até que possamos redesenhar os componentes para funcionar naturalmente como serviços (ou até que possamos resolver as expectativas de desempenho de outra forma) vamos precisar de algum sentido de localidade.

O que fazer? Bem, podes tentar ligar a afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar afinidade
Para estabelecer afinidade, define-se uma relação de afinidade entre dois serviços diferentes. Pode pensar na afinidade como "apontar" um serviço para outro e dizer "Este serviço só pode funcionar onde esse serviço está a funcionar." Por vezes, referimo-nos à afinidade como uma relação pai/filho (onde se aponta a criança para o progenitor). A Affinity garante que as réplicas ou instâncias de um serviço são colocadas nos mesmos nós que as de outro serviço.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Um serviço de criança só pode participar numa única relação de afinidade. Se queria que a criança fosse afinada a dois serviços parentais ao mesmo tempo, tem algumas opções:
> - Inverter as relações (ter parentService1 e parentService2 ponto no serviço infantil atual), ou
> - Designe um dos pais como um hub por convenção e tenha todos os serviços apontados para esse serviço. 
>
> O comportamento de colocação resultante no cluster deve ser o mesmo.
>

## <a name="different-affinity-options"></a>Diferentes opções de afinidade
A afinidade é representada através de um de vários esquemas de correlação, e tem dois modos diferentes. O modo de afinidade mais comum é o que chamamos de NãoAlignedAffinity. Na NonAlignedAffinity, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. O outro modo é AlignedAffinity. A Affinity alinhada só é útil com serviços estatais. Configurar dois serviços estatais para ter afinidade alinhada garante que as primárias desses serviços são colocadas nos mesmos nós que os outros. Também faz com que cada par de secundários para esses serviços sejam colocados nos mesmos nós. Também é possível (embora menos comum) configurar a NãoAlignedAffinity para serviços estatais. Para nonAlignedAffinity, as diferentes réplicas dos dois serviços estatais funcionariam nos mesmos nós, mas as suas primárias poderiam acabar em diferentes nós.

<center>

![Modos de Afinidade e Seus Efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Melhor esforço desejado estado
Uma relação de afinidade é o melhor esforço. Não fornece as mesmas garantias de colocação ou fiabilidade que o funcionamento no mesmo processo executável. Os serviços numa relação de afinidade são entidades fundamentalmente diferentes que podem falhar e ser movidas de forma independente. Uma relação de afinidade também pode quebrar, embora estas pausas sejam temporárias. Por exemplo, limitações de capacidade podem significar que apenas alguns dos objetos de serviço na relação de afinidade podem caber num dado nó. Nestes casos, mesmo que haja uma relação de afinidade no lugar, não pode ser aplicada devido aos outros constrangimentos. Se for possível fazê-lo, a violação será corrigida automaticamente mais tarde.

### <a name="chains-vs-stars"></a>Correntes vs estrelas
Hoje, o Cluster Resource Manager não é capaz de modelar cadeias de relações de afinidade. O que isto significa é que um serviço que é uma criança numa relação de afinidade não pode ser pai em outra relação de afinidade. Se queres modelar este tipo de relacionamento, tens de o modelar como uma estrela, em vez de uma corrente. Para passar de uma corrente para uma estrela, a criança mais de baixo seria parental para o pai do primeiro filho. Dependendo do arranjo dos seus serviços, poderá ter de o fazer várias vezes. Se não houver um serviço de pais naturais, talvez tenha que criar um que sirva como espaço reservado. Dependendo dos seus requisitos, também poderá querer ver [os Grupos de Aplicações.](service-fabric-cluster-resource-manager-application-groups.md)

<center>

![Correntes vs Estrelas no Contexto das Relações de Afinidade][Image2]
</center>

Outra coisa a notar sobre as relações de afinidade hoje em dia é que eles são direcionais por defeito. Isto significa que a regra da afinidade apenas impõe que a criança colocada com o progenitor. Não garante que o progenitor esteja localizado com a criança. Portanto, se houver uma violação de afinidade e corrigir a violação por alguma razão não é viável mover a criança para o nó do progenitor, então - mesmo que mover o progenitor para o nó da criança teria corrigido a violação - o progenitor não será movido para o nó da criança. Definir o config [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) para verdadeiro removeria a direccionalidade. Também é importante notar que a relação de afinidade não pode ser perfeita ou aplicada instantaneamente, uma vez que diferentes serviços têm com diferentes ciclos de vida e podem falhar e mover-se independentemente. Por exemplo, digamos que o pai de repente falha noutro nó porque se despenhou. O Cluster Resource Manager e o Failover Manager lidam primeiro com o failover, uma vez que manter os serviços em alta, consistente e disponível é a prioridade. Uma vez concluída a falha, a relação de afinidade é quebrada, mas o Cluster Resource Manager acha que está tudo bem até perceber que a criança não está localizada com o progenitor. Este tipo de controlos são efetuados periodicamente. Mais informações sobre como o Cluster Resource Manager avalia os constrangimentos estão disponíveis [neste artigo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), e [este](service-fabric-cluster-resource-manager-balancing.md) fala mais sobre como configurar a cadência em que estes constrangimentos são avaliados.   


### <a name="partitioning-support"></a>Suporte de partilha
A última coisa a notar sobre afinidade é que as relações de afinidade não são apoiadas onde o progenitor é dividido. Os serviços parentais divididos podem eventualmente ser suportados, mas hoje não é permitido.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre serviços de configuração, [Saiba mais sobre a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para limitar os serviços a um pequeno conjunto de máquinas ou agregar a carga de serviços, utilize grupos de [aplicações](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png