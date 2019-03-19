---
title: Gestor de recursos do Service Fabric Cluster - afinidade | Documentos da Microsoft
description: Descrição geral de configuração de afinidade para serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9c4af55a5ddb05335f8acfdd23711df2290e217b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085699"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar e utilizar as afinidades de serviço no Service Fabric
Afinidade é um controle que é fornecido principalmente para ajudar a facilitar a transição de aplicativos monolíticos maiores no mundo de nuvem e de microsserviços. Também é utilizado como uma otimização para melhorar o desempenho de serviços, embora se o fizer, pode ter efeitos colaterais.

Digamos que trazem uma aplicação maior ou que simplesmente não foi desenvolvido com microsserviços em mente, no Service Fabric (ou qualquer ambiente distribuído). Este tipo de transição é comum. Comece por elevação de todo o aplicativo para o ambiente, empacotamento-lo e garantir que ele esteja funcionando corretamente. Em seguida, começa a segmentar a Modelagem em diferentes serviços menores que todos conversar entre si.

Eventualmente, pode achar que a aplicação está a ter alguns problemas. Os problemas geralmente se encaixam em uma dessas categorias:

1. Algum componente X na aplicação monolítica tinha uma dependência de não-documentada no componente Y, e passou desses componentes em serviços separados. Uma vez que estes serviços estão agora em execução em diferentes nós do cluster, está desfeitos.
2. Esses componentes comunicam através de (local pipes nomeado | memória compartilhada | ficheiros no disco) e que realmente precisam de ser possível escrever um recurso local compartilhado por motivos de desempenho neste momento. Essa dependência difícil é removida mais tarde, talvez.
3. Está tudo certo, mas acontece que esses dois componentes são, na verdade, chatty/desempenho confidencial. Quando movidas-los em serviços separados geral do desempenho de aplicações tanked ou latência aumentada. Como resultado, o aplicativo inteiro não está atendendo às expectativas.

Nestes casos, nós não quer perder nosso trabalho de refatoração e não quiser voltar para a solução. A última condição pode até ser desejável como um recurso de otimização sem formatação. No entanto, até que podemos pode reestruturar os componentes para trabalhar naturalmente como serviços (ou até que nós podemos resolver as expectativas de desempenho alguma forma), vamos precisar de alguma forma de localidade.

O que fazer? Bem, pode tentar ativar a afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar a afinidade de
Para configurar a afinidade, define uma relação de afinidade entre dois serviços diferentes. Pode pensar afinidade como "apontando" um serviço em outro e dizendo que "este serviço só pode ser executado em que se o serviço está em execução." Por vezes, nos Referimos afinidade como uma relação de pai/filho (onde apontar o filho para o elemento principal). Afinidade garante que as réplicas ou instâncias de um serviço são colocadas em nós do mesmo, como as de outro serviço.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Um serviço de subordinado só pode participar de uma relação de afinidade único. Se quisesse que o menor possível agrupar, ao mesmo tempo, dois serviços de principal tem duas opções:
> - Inverter as relações (ter parentService1 e parentService2 apontar para o serviço de filho atual), ou
> - Designar um dos pais como um concentrador por convenção e têm todos os serviços do ponto em que o serviço. 
>
> O comportamento de colocação resultante do cluster deve ser o mesmo.
>

## <a name="different-affinity-options"></a>Opções de afinidade diferentes
Afinidade é representada através de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos NonAlignedAffinity. NonAlignedAffinity, as réplicas ou instâncias dos serviços diferentes são colocadas em nós do mesmo. O outro modo é AlignedAffinity. Afinidade alinhada é útil apenas com os serviços com estado. Configurar serviços com monitoração de estado para alinhado afinidade garante que as cores primárias desses serviços são colocadas em nós do mesmo como entre si. Também faz com que cada par de bases de dados secundárias para os serviços sejam colocados em nós do mesmo. Também é possível (embora menos comum) configurar NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity, as réplicas diferentes dos dois serviços com estado seriam executado em nós do mesmo, mas suas cores primárias poderiam acabar em nós diferentes.

<center>

![Modos de afinidade e seus efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Melhor estado de esforço pretendido
Uma relação de afinidade é melhor esforço. Ele não fornece as garantias mesmo de colocalização ou confiabilidade que em execução no mesmo processo executável faz. Os serviços numa relação de afinidade são fundamentalmente diferentes entidades que podem efetuar a ativação e ser movidas de forma independente. Também poderá interromper uma relação de afinidade, embora essas quebras são temporárias. Por exemplo, as limitações de capacidade podem significar que apenas alguns dos objetos de serviço na relação de afinidade se encaixa num determinado nó. Nestes casos, apesar de não existe uma relação de afinidade no lugar, ele não pode ser imposto devido a outras restrições. Se for possível fazer isso, a violação é automaticamente corrigida mais tarde.

### <a name="chains-vs-stars"></a>Cadeias versus estrelas
Hoje mesmo o Gestor de recursos de Cluster não é capaz de cadeias de modelo de relações de afinidade. O que significa que um serviço que é um filho numa relação de afinidade não pode ser um encarregado de educação na outra relação de afinidade. Se deseja modelar este tipo de relação, terá efetivamente modelá-lo como uma estrela, em vez de uma cadeia. Para mudar de uma cadeia para uma estrela, o subordinado na extremidade inferior seria ser pai de principal do primeiro subordinado em vez disso. Consoante a disposição dos seus serviços, poderá ter de fazer isso várias vezes. Se não houver nenhum serviço principal natural, poderá ter de criar um que serve como um marcador de posição. Dependendo dos requisitos, talvez também queira examinar [grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Vs de cadeias. Estrelas no contexto de relações de afinidade][Image2]
</center>

Outro ponto importante sobre as relações de afinidade hoje é que eles são direcionais por predefinição. Isso significa que a regra de afinidade só irá impor que o filho colocadas com o elemento principal. Não garante que o elemento principal é localizado com o filho. Por conseguinte, se existir uma violação de afinidade e para corrigir a violação por algum motivo, não é viável para mover o filho para o nó do encarregado de educação, em seguida, – mesmo que mover o elemento principal para o nó do seu filho seria ter corrigido a violação – o elemento principal não será movido para th nó do menor e. Definir a configuração [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) para true removeria a direcionalidade. Também é importante observar que a relação de afinidade não pode ser perfeito ou instantaneamente imposta uma vez que os diferentes serviços têm com diferentes ciclos de vida e podem falhar e mover de forma independente. Por exemplo, digamos que o elemento principal, de repente, efetua a ativação pós-falha para outro nó porque ele falhou. Processam, o Gestor de recursos de Cluster e o Gestor de ativação pós-falha, a ativação pós-falha em primeiro lugar, desde a manter os serviços, consistente, e disponível é a prioridade. Uma vez concluída a ativação pós-falha, a relação de afinidade é interrompida, mas o Gestor de recursos de Cluster achar que está tudo certo até que ele observar que o elemento subordinado não é localizado com o elemento principal. Esses tipos de verificações são executados periodicamente. Obter mais informações sobre como o Gestor de recursos de Cluster avalia as restrições estão disponíveis no [este artigo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), e [esse](service-fabric-cluster-resource-manager-balancing.md) fala mais sobre como configurar a cadência da sobre quais são essas restrições avaliada.   


### <a name="partitioning-support"></a>Suporte de criação de partições
A última coisa a observar sobre a afinidade é esse afinidade as relações não são suportadas em que o elemento principal está particionado. Serviços principal particionada podem ser suportados, eventualmente, mas atualmente não é permitido.

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para limitar os serviços a um pequeno conjunto de máquinas ou agregar a carga de serviços, utilize [grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png