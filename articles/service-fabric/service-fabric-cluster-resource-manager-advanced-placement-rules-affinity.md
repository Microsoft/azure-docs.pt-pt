---
title: Gestor de recursos de cluster de tecido de serviço - Affinity
description: Visão geral da afinidade do serviço para os serviços azure service Fabric e orientação sobre a configuração da afinidade do serviço.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551748"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar e usar afinidade de serviço em Tecido de Serviço
A affinity é um controlo que é fornecido principalmente para ajudar a facilitar a transição de aplicações monolíticas maiores para o mundo das nuvens e microserviços. Também é usado como uma otimização para melhorar o desempenho dos serviços, embora fazê-lo possa ter efeitos colaterais.

Digamos que está a trazer uma aplicação maior, ou uma que não foi concebida com microserviços em mente, para o Service Fabric (ou qualquer ambiente distribuído). Este tipo de transição é comum. Começa-se por levantar toda a aplicação para o ambiente, embalá-la e certificar-se de que está a funcionar sem problemas. Depois começas a decompor-te em diferentes serviços menores que todos falam uns com os outros.

Eventualmente, poderá descobrir que a aplicação está a passar por alguns problemas. As questões geralmente enquadram-se numa destas categorias:

1. Alguns componentes X na aplicação monolítica tinham uma dependência indocumentada do componente Y, e você acabou de transformar esses componentes em serviços separados. Como estes serviços estão agora a funcionar em nós diferentes no aglomerado, estão avariados.
2. Estes componentes comunicam via (tubos de nome local ] memória partilhada [ ficheiros no disco) e precisam realmente de ser capazes de escrever a um recurso local partilhado por razões de desempenho neste momento. Essa dependência dura é removida mais tarde, talvez.
3. Está tudo bem, mas acontece que estes dois componentes são realmente sensíveis à conversa/desempenho. Quando os transferiram para serviços separados, o desempenho global da aplicação afundou ou a latência aumentou. Como resultado, a aplicação global não está a corresponder às expectativas.

Nestes casos, não queremos perder o nosso trabalho de refactoring, e não queremos voltar ao monólito. A última condição pode até ser desejável como uma simples otimização. No entanto, até que possamos redesenhar os componentes para funcionar naturalmente como serviços (ou até que possamos resolver as expectativas de desempenho de outra forma) vamos precisar de algum sentido de localidade.

O que fazer? Bem, podias tentar ligar a afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar a finidade
Para criar afinidade, define-se uma relação de afinidade entre dois serviços diferentes. Pode pensar na afinidade como "apontar" um serviço para outro e dizer "Este serviço só pode funcionar onde esse serviço está a funcionar." Às vezes referimo-nos à afinidade como uma relação pai/filho (onde aponta a criança ao progenitor). A Affinity garante que as réplicas ou instâncias de um serviço são colocadas nos mesmos nódosos que os de outro serviço.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Um serviço infantil só pode participar numa única relação de afinidade. Se queria que a criança fosse afinada com dois serviços parentais ao mesmo tempo, tem algumas opções:
> - Reverter as relações (ter paisService1 e parentService2 ponto no serviço infantil atual), ou
> - Designe um dos pais como um hub por convenção e tenha todos os serviços nesse serviço. 
>
> O comportamento de colocação resultante no cluster deve ser o mesmo.
>

## <a name="different-affinity-options"></a>Diferentes opções de afinidade
A affinity é representada através de um de vários esquemas de correlação, e tem dois modos diferentes. O modo de afinidade mais comum é o que chamamos de Affinity Não Alinhado. Na Affinity Não Alinhada, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. O outro modo está alinhadoAffinity. A Affinity Alinhada é útil apenas com serviços estatais. Configurar dois serviços estatais para ter afinidade alinhada garante que as primárias desses serviços são colocadas nos mesmos nós que os outros. Também faz com que cada par de secundários para que esses serviços sejam colocados nos mesmos nós. Também é possível (embora menos comum) configurar a Affinity Não Alinhada para serviços estatais. Para a Affinity Não Alinhada, as diferentes réplicas dos dois serviços estatais seriam executadas nos mesmos nós, mas as suas primárias poderiam acabar em nós diferentes.

<center>

![Modos de afinidade e seus efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Melhor esforço desejado estado
Uma relação de afinidade é o melhor esforço. Não fornece as mesmas garantias de collocalização ou fiabilidade que o funcionamento no mesmo processo executável. Os serviços numa relação de afinidade são entidades fundamentalmente diferentes que podem falhar e ser movidas de forma independente. Uma relação de afinidade também pode quebrar, embora estas pausas sejam temporárias. Por exemplo, as limitações de capacidade podem significar que apenas alguns dos objetos de serviço na relação de afinidade podem caber num dado nó. Nestes casos, mesmo que haja uma relação de afinidade no lugar, não pode ser aplicada devido aos outros constrangimentos. Se for possível fazê-lo, a violação é corrigida automaticamente mais tarde.

### <a name="chains-vs-stars"></a>Correntes vs. estrelas
Hoje o Cluster Resource Manager não é capaz de modelar cadeias de relacionamentos de afinidade. O que isto significa é que um serviço que é uma criança numa relação de afinidade não pode ser pai em outra relação de afinidade. Se queres modelar este tipo de relacionamento, tens de a modelar como uma estrela, em vez de uma corrente. Para passar de uma corrente para uma estrela, a criança mais fundo seria, em vez disso, pai do progenitor do primeiro filho. Dependendo do arranjo dos seus serviços, poderá ter de o fazer várias vezes. Se não houver um serviço natural de pais, pode ter de criar um que sirva como espaço reservado. Dependendo dos seus requisitos, também poderá querer analisar [os Grupos](service-fabric-cluster-resource-manager-application-groups.md)de Aplicação .

<center>

![Cadeias vs. Estrelas no Contexto das Relações de Afinidade][Image2]
</center>

Outra coisa a notar sobre as relações de afinidade hoje em dia é que são direcionais por defeito. Isto significa que a regra da afinidade apenas impõe que a criança colocada com o progenitor. Não garante que o progenitor esteja localizado com a criança. Portanto, se houver uma violação de afinidade e corrigir a violação por alguma razão não é viável mover a criança para o nó dos pais, então, mesmo que mover o progenitor para o nó da criança teria corrigido a violação, o progenitor não será transferido para o nó da criança. Definir a config [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) true removeria a direccionalidade. Também é importante notar que a relação de afinidade não pode ser perfeita ou instantaneamente aplicada, uma vez que diferentes serviços têm com diferentes ciclos de vida e podem falhar e mover-se independentemente. Por exemplo, digamos que o pai de repente falha em outro nó porque se despenhou. O Cluster Resource Manager e o Failover Manager lidam primeiro com a falha, uma vez que manter os serviços em alta, consistente e disponível é a prioridade. Uma vez que a falha termine, a relação de afinidade é quebrada, mas o Gestor de Recursos do Cluster acha que está tudo bem até notar que a criança não está localizada com o progenitor. Este tipo de verificações são realizados periodicamente. Mais informações sobre a forma como o Cluster Resource Manager avalia os constrangimentos está disponível [neste artigo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), e [este](service-fabric-cluster-resource-manager-balancing.md) fala mais sobre como configurar a cadência em que estes constrangimentos são avaliados.   


### <a name="partitioning-support"></a>Apoio à partilha
A última coisa a notar sobre afinidade é que as relações de afinidade não são apoiadas onde o pai é dividido. Os serviços de pais divididos podem eventualmente ser apoiados, mas hoje não é permitido.

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre a configuração de serviços, [Saiba sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para limitar os serviços a um pequeno conjunto de máquinas ou agregar a carga de serviços, utilize grupos de [aplicação](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png