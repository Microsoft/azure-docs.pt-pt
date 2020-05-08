---
title: Recuperação geo-desastre - Hubs de eventos do Azure Microsoft Docs
description: Como utilizar as regiões geográficas para falhar e realizar a recuperação de desastres em Hubs de Eventos Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2c42637dda9d1a413c0521ea2d7565a63ca58e81
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858296"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - Recuperação geo-desastre 

Quando regiões ou centros de dados inteiros do Azure (se não forem [utilizadas zonas](../availability-zones/az-overview.md) de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a operar numa região ou datacenter diferente. Como tal, *a recuperação geo-desastre* e *a geo-replicação* são características importantes para qualquer empresa. O Azure Event Hubs apoia a recuperação geo-desastre e a geo-replicação, ao nível do espaço de nome. 

> [!NOTE]
> A funcionalidade de recuperação de geo-desastres só está disponível para as [SKUs padrão e dedicadas.](https://azure.microsoft.com/pricing/details/event-hubs/)  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante notar a distinção entre "interrupções" e "desastres". Uma *paralisação* é a indisponibilidade temporária de Hubs de Eventos Azure, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, após a resolução do problema, os Centros de Eventos voltam a estar disponíveis. Normalmente, uma interrupção não causa a perda de mensagens ou outros dados. Um exemplo de tal falha pode ser uma falha de energia no datacenter. Algumas interrupções são apenas perdas de ligação curtas devido a problemas transitórios ou de rede. 

Um *desastre* é definido como a perda permanente, ou a longo prazo de um cluster de Hubs de Eventos, região de Azure ou datacenter. A região ou datacenter pode ou não voltar a estar disponível, ou pode ficar parada durante horas ou dias. Exemplos de tais desastres são incêndios, inundações ou terramotos. Um desastre que se torna permanente pode causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos não deve haver perda de dados e as mensagens podem ser recuperadas uma vez que o centro de dados está de volta.

A funcionalidade de recuperação geo-desastre do Azure Event Hubs é uma solução de recuperação de desastres. Os conceitos e fluxos de trabalho descritos neste artigo aplicam-se a cenários de catástrofe, e não a interrupções transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

O recurso de recuperação de desastres implementa a recuperação de desastres de metadados e baseia-se em espaços de recuperação de desastres primários e secundários. 

A funcionalidade de recuperação de geo-desastres está disponível apenas para as [SKUs padrão e dedicadas.](https://azure.microsoft.com/pricing/details/event-hubs/) Não é necessário efazer alterações nas cordas de ligação, uma vez que a ligação é feita através de um pseudónimo.

Neste artigo são utilizados os seguintes termos:

-  *Pseudónimo*: O nome para uma configuração de recuperação de desastres que configura. O pseudónimo fornece uma única linha de ligação estável de nome de domínio totalmente qualificado (FQDN). As aplicações utilizam esta cadeia de ligação de pseudónimo para se ligar a um espaço de nome. 

-  Espaço de *nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nome principal é "ativo" e recebe mensagens (este pode ser um espaço de nome existente ou novo). O espaço de nome secundário é "passivo" e não recebe mensagens. Os metadados entre ambos estão sincronizados, para que ambos possam aceitar sem problemas sem qualquer código de aplicação ou alterações nas cordas de ligação. Para garantir que apenas o espaço de nome ativo recebe mensagens, deve utilizar o pseudónimo. 

-  *Metadados*: Entidades como centros de eventos e grupos de consumidores; e as suas propriedades do serviço que estão associados ao espaço de nome. Note que apenas as entidades e as suas definições são replicadas automaticamente. Mensagens e eventos não são replicados. 

-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="supported-namespace-pairs"></a>Pares de espaço de nome suportados
As seguintes combinações de espaços de nomes primários e secundários são suportadas:  

| Espaço de nome primário | Espaço de nome secundário | Suportado | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sim | 
| Standard | Dedicada | Sim | 
| Dedicada | Dedicada | Sim | 
| Dedicada | Standard | No | 

> [!NOTE]
> Não se pode emparelhar espaços de nomes que estão no mesmo aglomerado dedicado. Pode emparelhar espaços de nome sinuosos em aglomerados separados. 

## <a name="setup-and-failover-flow"></a>Fluxo de configuração e failover

A secção seguinte é uma visão geral do processo de failover, e explica como configurar a falha inicial. 

![1][]

### <a name="setup"></a>Configuração

Primeiro cria-se ou usas um espaço de nome primário existente, e um novo espaço de nome secundário, depois emparelhas os dois. Este emparelhamento dá-lhe um pseudónimo que pode usar para ligar. Como usas um pseudónimo, não tens de mudar as cordas de ligação. Apenas novos espaços de nome podem ser adicionados ao seu emparelhamento failover. Por fim, deverá adicionar alguma monitorização para detetar se é necessária uma falha. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, pelo que as falhas automáticas raramente são possíveis, uma vez que muitas vezes as falhas devem ser realizadas em sintonia com o subsistema ou infraestrutura restantes.

### <a name="example"></a>Exemplo

Num exemplo deste cenário, considere uma solução de Ponto de Venda (POS) que emita mensagens ou eventos. O Event Hubs passa esses eventos para alguma solução de mapeamento ou reformatação, que depois reencaminha dados mapeados para outro sistema para posterior processamento. Nessa altura, todos estes sistemas poderão estar alojados na mesma região de Azure. A decisão sobre quando e que parte a falhar depende do fluxo de dados na sua infraestrutura. 

Pode automatizar a falha, quer com sistemas de monitorização, quer com soluções de monitorização personalizadas. No entanto, essa automatização requer planeamento e trabalho extra, o que está fora do âmbito deste artigo.

### <a name="failover-flow"></a>Fluxo de failover

Se iniciar a falha, são necessários dois passos:

1. Se ocorrer outra paragem, poderá falhar novamente. Por isso, configurar outro espaço de nome passivo e atualizar o emparelhamento. 

2. Retire as mensagens do antigo espaço de nome primário uma vez que esteja disponível novamente. Depois disso, utilize esse espaço de nome para mensagens regulares fora da sua configuração de geo-recuperação ou elimine o antigo espaço de nome primário.

> [!NOTE]
> Só a semântica falhada para a frente é apoiada. Neste cenário, falha-se e depois reemparelha-se com um novo espaço de nome. A reprovação não é suportada; por exemplo, num cluster SQL. 

![2][]

## <a name="management"></a>Gestão

Se cometeu um erro; por exemplo, emparelhou as regiões erradas durante a configuração inicial, pode quebrar o emparelhamento dos dois espaços de nome a qualquer momento. Se pretender utilizar os espaços de nome emparelhados como espaços de nome regulares, elimine o pseudónimo.

## <a name="samples"></a>Amostras

A [amostra no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) mostra como configurar e iniciar uma falha. Esta amostra demonstra os seguintes conceitos:

- Definições necessárias no Diretório Ativo Azure para utilizar o Gestor de Recursos Azure com Hubs de Eventos. 
- Passos necessários para executar o código da amostra. 
- Envie e receba do atual espaço de nome primário. 

## <a name="considerations"></a>Considerações

Note as seguintes considerações a ter em conta com esta libertação:

1. Por design, a recuperação geo-desastre do Event Hubs não replica dados, pelo que não pode reutilizar o antigo valor de compensação do seu centro de eventos primários no seu centro de eventos secundários. Recomendamos reiniciar o recetor do evento com um dos seguintes:

- *EventPosition.FromStart()* - Se desejar ler todos os dados do seu centro de eventos secundários.
- *EventPosition.FromEnd()* - Se desejar ler todos os novos dados a partir do momento da ligação ao seu centro de eventos secundários.
- *EventPosition.FromEnqueuedTime (dataTime)* - Se desejar ler todos os dados recebidos no seu centro de eventos secundários a partir de uma determinada data e hora.

2. No seu planeamento falhado, também deve considerar o fator tempo. Por exemplo, se perder conectividade por mais de 15 a 20 minutos, poderá decidir iniciar a falha. 
 
3. O facto de não serem replicados dados significa que as sessões atualmente ativas não são replicadas. Além disso, a deteção duplicada e as mensagens programadas podem não funcionar. Novas sessões, mensagens programadas e novos duplicados funcionarão. 

4. A falta de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez. 

5. Entidades sincronizadoras podem demorar algum tempo, aproximadamente 50-100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidade 

O Evento Hubs Standard SKU suporta Zonas de [Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro de uma região do Azure. 

> [!NOTE]
> O suporte das Zonas de Disponibilidade para o Azure Event Hubs Standard só está disponível nas [regiões do Azure](../availability-zones/az-region.md) onde existem zonas de disponibilidade.

Só pode ativar Zonas de Disponibilidade em novos espaços de nome, utilizando o portal Azure. Os Centros de Eventos não suportam a migração de espaços de nome existentes. Não é possível desativar a redundância da zona depois de a ter habilitado para o seu espaço de nome.

![3][]

## <a name="next-steps"></a>Passos seguintes

* A [amostra no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) atravessa um simples fluxo de trabalho que cria um geo-emparelhamento e inicia uma falha para um cenário de recuperação de desastres.
* A [referência REST API](/rest/api/eventhub/) descreve APIs para executar a configuração de recuperação de geo-desastres.

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
