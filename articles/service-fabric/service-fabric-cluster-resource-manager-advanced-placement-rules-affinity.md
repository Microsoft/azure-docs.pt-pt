---
title: Gerenciador de recursos de Cluster Service Fabric-afinidade
description: Visão geral da afinidade de serviço para serviços de Service Fabric do Azure e orientação sobre a configuração de afinidade de serviço.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551748"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurando e usando a afinidade de serviço no Service Fabric
A afinidade é um controle que é fornecido principalmente para ajudar a facilitar a transição de aplicativos monolíticos maiores para o mundo de microserviços e nuvem. Ele também é usado como uma otimização para melhorar o desempenho dos serviços, embora isso possa ter efeitos colaterais.

Digamos que você esteja trazendo um aplicativo maior, ou um que não tenha sido projetado com os microserviços em mente, para Service Fabric (ou qualquer ambiente distribuído). Esse tipo de transição é comum. Você começa levantando todo o aplicativo no ambiente, empacotando-o e verificando se ele está funcionando sem problemas. Em seguida, você começa a dividi-lo em serviços menores diferentes que se comunicam entre si.

Eventualmente, você pode descobrir que o aplicativo está enfrentando alguns problemas. Os problemas geralmente se enquadram em uma destas categorias:

1. Algum componente X no aplicativo monolítico tinha uma dependência não documentada no componente Y, e você acabou de transformar esses componentes em serviços separados. Como esses serviços agora estão sendo executados em nós diferentes no cluster, eles são desfeitos.
2. Esses componentes se comunicam por meio de (pipes nomeados locais | memória compartilhada | arquivos em disco) e eles realmente precisam ser capazes de gravar em um recurso local compartilhado por motivos de desempenho no momento. Essa dependência rígida é removida posteriormente, talvez.
3. Tudo está bem, mas acontece que esses dois componentes são, na verdade, diferenciados/de desempenho. Quando elas foram movidas para serviços separados, o desempenho geral do aplicativo foi aumentado ou a latência aumentou. Como resultado, o aplicativo geral não atende às expectativas.

Nesses casos, não queremos perder nosso trabalho de refatoração e não queremos voltar para o monolítico. A última condição pode até ser desejável como uma otimização simples. No entanto, até que possamos recriar os componentes para trabalhar naturalmente como serviços (ou até que possamos resolver as expectativas de desempenho de alguma outra maneira), vamos precisar de alguma noção de localidade.

O que fazer? Bem, você pode tentar ativar a afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar a afinidade
Para configurar a afinidade, você define uma relação de afinidade entre dois serviços diferentes. Você pode considerar a afinidade como "apontar" um serviço em outro e dizer "este serviço pode ser executado somente onde o serviço está em execução". Às vezes, nos referimos à afinidade como uma relação pai/filho (onde você aponta o filho no pai). A afinidade garante que as réplicas ou instâncias de um serviço sejam colocadas nos mesmos nós que os de outro serviço.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Um serviço filho só pode participar de uma única relação de afinidade. Se você quisesse que o filho fosse relacionados a dois serviços pai de uma vez, você tem duas opções:
> - Inverta as relações (tenha parentService1 e parentService2 ponto no serviço filho atual) ou
> - Designar um dos pais como um hub por convenção e fazer com que todos os serviços apontem para esse serviço. 
>
> O comportamento de posicionamento resultante no cluster deve ser o mesmo.
>

## <a name="different-affinity-options"></a>Diferentes opções de afinidade
A afinidade é representada por meio de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos de NonAlignedAffinity. No NonAlignedAffinity, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. O outro modo é AlignedAffinity. A afinidade alinhada é útil somente com serviços com estado. Configurar dois serviços com estado para ter a afinidade alinhada garante que os primários desses serviços sejam colocados nos mesmos nós um do outro. Ele também faz com que cada par de secundários para esses serviços seja colocado nos mesmos nós. Também é possível (embora menos comum) configurar o NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity, as diferentes réplicas dos dois serviços com estado seriam executadas nos mesmos nós, mas seus primários poderiam acabar em nós diferentes.

<center>

![modos de afinidade e seus efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Estado desejado do melhor esforço
Uma relação de afinidade é o melhor esforço. Ele não fornece as mesmas garantias de colocação ou confiabilidade que são executadas no mesmo processo executável. Os serviços em uma relação de afinidade são entidades basicamente diferentes que podem falhar e serem movidas de forma independente. Uma relação de afinidade também pode ser interrompida, embora essas interrupções sejam temporárias. Por exemplo, limitações de capacidade podem significar que apenas alguns dos objetos de serviço na relação de afinidade podem caber em um determinado nó. Nesses casos, embora haja uma relação de afinidade em vigor, ela não pode ser imposta devido a outras restrições. Se for possível fazer isso, a violação será corrigida automaticamente mais tarde.

### <a name="chains-vs-stars"></a>Cadeias vs. estrelas
Hoje, o Gerenciador de recursos de cluster não é capaz de modelar cadeias de relações de afinidade. Isso significa que um serviço que é um filho em uma relação de afinidade não pode ser pai em outra relação de afinidade. Se você quiser modelar esse tipo de relação, terá de modelar efetivamente como uma estrela, em vez de uma cadeia. Para mover de uma cadeia para uma estrela, o filho na extremidade inferior seria pai para o pai do primeiro filho. Dependendo da organização de seus serviços, talvez você precise fazer isso várias vezes. Se não houver nenhum serviço pai natural, talvez você precise criar um que sirva como um espaço reservado. Dependendo dos seus requisitos, talvez você também queira examinar os grupos de [aplicativos](service-fabric-cluster-resource-manager-application-groups.md).

<center>

Cadeias de ![vs. estrelas no contexto de relações de afinidade][Image2]
</center>

Outra coisa a observar sobre as relações de afinidade hoje é que elas são direcionais por padrão. Isso significa que a regra de afinidade impõe apenas que o filho colocado com o pai. Ele não garante que o pai esteja localizado com o filho. Portanto, se houver uma violação de afinidade e para corrigir a violação por algum motivo, não é possível mover o filho para o nó do pai. em seguida, mesmo que mover o pai para o nó do filho tenha corrigido a violação – o pai não será movido para th nó do filho e. Definir a configuração [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) como true removeria a direcionalidade. Também é importante observar que a relação de afinidade não pode ser perfeita ou imposta instantaneamente, já que serviços diferentes têm ciclos de vida diferentes e podem falhar e mover de forma independente. Por exemplo, digamos que o pai falhe repentinamente em outro nó porque falhou. O Gerenciador de recursos de cluster e Gerenciador de Failover lidam com o failover primeiro, pois manter os serviços, consistentes e disponíveis é a prioridade. Após a conclusão do failover, a relação de afinidade é interrompida, mas o Gerenciador de recursos de cluster pensa que tudo está bem até observar que o filho não está localizado com o pai. Esses tipos de verificações são executados periodicamente. Mais informações sobre como o Gerenciador de recursos de cluster avalia as restrições estão disponíveis neste [artigo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), e [este](service-fabric-cluster-resource-manager-balancing.md) fala mais sobre como configurar a cadência na qual essas restrições são avaliadas.   


### <a name="partitioning-support"></a>Suporte de criação de partições
A coisa final a ser observada sobre a afinidade é que as relações de afinidade não têm suporte onde o pai é particionado. Os serviços pai particionados podem ter suporte eventualmente, mas hoje não são permitidos.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar serviços, [saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para limitar os serviços a um pequeno conjunto de computadores ou agregar a carga de serviços, use [grupos de aplicativos](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png