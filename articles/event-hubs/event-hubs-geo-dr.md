---
title: Recuperação geo-desastre - Hubs de eventos do Azure Microsoft Docs
description: Como usar as regiões geográficas para falhar e realizar a recuperação de desastres em Hubs de Eventos Azure
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
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 47e3a27ba9c0b7995f45f38ae4e19941cb4f8c01
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659708"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - Recuperação geo-desastre 
Quando regiões ou datacenters inteiros do Azure (se não forem [utilizadas zonas](../availability-zones/az-overview.md) de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a operar numa região ou datacenter diferente. Como tal, *a recuperação geo-desastre* e *a geo-replicação* são características importantes para qualquer empresa. O Azure Event Hubs apoia a recuperação geo-desastre e a geo-replicação, ao nível do espaço de nome. 

> [!NOTE]
> A funcionalidade de recuperação de geo-desastres só está disponível para as [SKUs padrão e dedicadas.](https://azure.microsoft.com/pricing/details/event-hubs/)  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante notar a distinção entre "interrupções" e "desastres". Uma **paralisação** é a indisponibilidade temporária de Hubs de Eventos Azure, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, após a resolução do problema, os Centros de Eventos voltam a estar disponíveis. Normalmente, uma interrupção não causa a perda de mensagens ou outros dados. Um exemplo de tal falha pode ser uma falha de energia no datacenter. Algumas interrupções são apenas perdas de ligação curtas devido a problemas transitórios ou de rede. 

Um *desastre* é definido como a perda permanente, ou a longo prazo de um cluster de Hubs de Eventos, região de Azure ou datacenter. A região ou datacenter pode ou não voltar a estar disponível, ou pode ficar parada durante horas ou dias. Exemplos de tais desastres são incêndios, inundações ou terramotos. Um desastre que se torna permanente pode causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos não deve haver perda de dados e as mensagens podem ser recuperadas uma vez que o centro de dados está de volta.

A funcionalidade de recuperação geo-desastre do Azure Event Hubs é uma solução de recuperação de desastres. Os conceitos e fluxos de trabalho descritos neste artigo aplicam-se a cenários de catástrofe, e não a interrupções transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

O recurso de recuperação de desastres implementa a recuperação de desastres de metadados e baseia-se em espaços de recuperação de desastres primários e secundários. 

A funcionalidade de recuperação de geo-desastres está disponível apenas para as [SKUs padrão e dedicadas.](https://azure.microsoft.com/pricing/details/event-hubs/) Não é necessário fazer alterações nas cordas de ligação, uma vez que a ligação é feita através de um pseudónimo.

Neste artigo são utilizados os seguintes termos:

-  *Pseudónimo*: O nome para uma configuração de recuperação de desastres que configura. O pseudónimo fornece uma única linha de ligação estável de nome de domínio totalmente qualificado (FQDN). As aplicações utilizam esta cadeia de ligação de pseudónimo para se ligar a um espaço de nome. 

-  Espaço de *nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nome principal é "ativo" e recebe mensagens (pode ser um espaço de nome existente ou novo). O espaço de nome secundário é "passivo" e não recebe mensagens. Os metadados entre ambos estão sincronizados, para que ambos possam aceitar sem problemas sem qualquer código de aplicação ou alterações nas cordas de ligação. Para garantir que apenas o espaço de nome ativo recebe mensagens, deve utilizar o pseudónimo. 

-  *Metadados*: Entidades como centros de eventos e grupos de consumidores; e as suas propriedades do serviço que estão associados ao espaço de nome. Apenas as entidades e as suas definições são replicadas automaticamente. Mensagens e eventos não são replicados. 

-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="supported-namespace-pairs"></a>Pares de espaço de nome suportados
As seguintes combinações de espaços de nomes primários e secundários são suportadas:  

| Espaço de nome primário | Espaço de nome secundário | Suportado | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sim | 
| Standard | Dedicada | Sim | 
| Dedicada | Dedicada | Sim | 
| Dedicada | Standard | Não | 

> [!NOTE]
> Não se pode emparelhar espaços de nomes que estão no mesmo aglomerado dedicado. Pode emparelhar espaços de nome sinuosos em aglomerados separados. 

## <a name="setup-and-failover-flow"></a>Fluxo de configuração e failover

A secção seguinte é uma visão geral do processo de failover, e explica como configurar a falha inicial. 

![1][]

### <a name="setup"></a>Configuração

Primeiro cria-se ou usas um espaço de nome primário existente, e um novo espaço de nome secundário, depois emparelhas os dois. Este emparelhamento dá-lhe um pseudónimo que pode usar para ligar. Porque usas um pseudónimo, não tens de mudar as cordas de ligação. Apenas novos espaços de nome podem ser adicionados ao seu emparelhamento failover. Por fim, deverá adicionar alguma monitorização para detetar se é necessária uma falha. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, pelo que as falhas automáticas raramente são possíveis, uma vez que muitas vezes as falhas devem ser realizadas em sintonia com o subsistema ou infraestrutura restantes.

### <a name="example"></a>Exemplo

Num exemplo deste cenário, considere uma solução de Ponto de Venda (POS) que emita mensagens ou eventos. O Event Hubs passa esses eventos para alguma solução de mapeamento ou reformatação, que depois reencaminha dados mapeados para outro sistema para posterior processamento. Nessa altura, todos estes sistemas poderão estar alojados na mesma região de Azure. A decisão sobre quando e que parte a falhar depende do fluxo de dados na sua infraestrutura. 

Pode automatizar a falha, quer com sistemas de monitorização, quer com soluções de monitorização personalizadas. No entanto, essa automatização requer planeamento e trabalho extra, o que está fora do âmbito deste artigo.

### <a name="failover-flow"></a>Fluxo de failover

Se iniciar a falha, são necessários dois passos:

1. Se ocorrer outra paragem, poderá falhar novamente. Por isso, configurar outro espaço de nome passivo e atualizar o emparelhamento. 

2. Puxe mensagens do antigo espaço de nome primário assim que estiver disponível novamente. Depois disso, utilize esse espaço de nome para mensagens regulares fora da sua configuração de geo-recuperação ou elimine o antigo espaço de nome primário.

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

1. Por design, a recuperação geo-desastre do Event Hubs não replica dados, pelo que não pode reutilizar o antigo valor de compensação do seu centro de eventos primários no seu centro de eventos secundários. Recomendamos reiniciar o recetor do evento com um dos seguintes métodos:

- *EventPosition.FromStart()* - Se desejar ler todos os dados do seu centro de eventos secundários.
- *EventPosition.FromEnd()* - Se desejar ler todos os novos dados a partir do momento da ligação ao seu centro de eventos secundários.
- *EventPosition.FromEnqueuedTime (dataTime)* - Se desejar ler todos os dados recebidos no seu centro de eventos secundários a partir de uma determinada data e hora.

2. No seu planeamento falhado, também deve considerar o fator tempo. Por exemplo, se perder conectividade por mais de 15 a 20 minutos, poderá decidir iniciar a falha. 
 
3. O facto de não se replicarem dados significa que as sessões atualmente ativas não são replicadas. Além disso, a deteção duplicada e as mensagens programadas podem não funcionar. Novas sessões, mensagens programadas e novos duplicados funcionarão. 

4. A falta de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez. 

5. Entidades sincronizadoras podem demorar algum tempo, aproximadamente 50-100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidade 

O Evento Hubs Standard SKU suporta Zonas de [Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro de uma região do Azure. 

> [!NOTE]
> O suporte das Zonas de Disponibilidade para o Azure Event Hubs Standard só está disponível nas [regiões do Azure](../availability-zones/az-region.md) onde existem zonas de disponibilidade.

Só pode ativar Zonas de Disponibilidade em novos espaços de nome, utilizando o portal Azure. O Event Hubs não suporta a migração de espaços de nomeexistentes. Não pode desativar a redundância da zona depois de permitir no seu espaço de nome.

![3][]

## <a name="private-endpoints"></a>Pontos finais privados
Esta secção fornece considerações adicionais ao utilizar a recuperação geo-desastre com espaços de nome que utilizam pontos finais privados. Para aprender sobre a utilização de pontos finais privados com Hubs de Eventos em geral, consulte [configurar pontos finais privados](private-link-service.md).

### <a name="new-pairings"></a>Novos pares
Se tentar criar um emparelhamento entre um espaço de nome primário com um ponto final privado e um espaço de nome secundário sem um ponto final privado, o emparelhamento falhará. O emparelhamento só terá sucesso se os espaços de nomes primários e secundários tiverem pontos finais privados. Recomendamos que utilize as mesmas configurações nos espaços de nomes primários e secundários e em redes virtuais nas quais são criados pontos finais privados.  

> [!NOTE]
> Quando se tenta emparelhar o espaço de nome primário com um ponto final privado e um espaço de nome secundário, o processo de validação verifica apenas se existe um ponto final privado no espaço de nome secundário. Não verifica se o ponto final funciona ou funcionará após o fracasso. É da sua responsabilidade garantir que o espaço de nome secundário com ponto final privado funcionará como esperado após o fracasso.
>
> Para testar que as configurações de pontofinal privado são as mesmas em espaços de nomeprimárioe secundário, envie um pedido de leitura (por exemplo: [Obter O Hub](/rest/api/eventhub/get-event-hub)de Eventos) para o espaço de nome secundário de fora da rede virtual, e verifique se recebe uma mensagem de erro do serviço.

### <a name="existing-pairings"></a>Pares existentes
Se o emparelhamento entre o espaço de nomes primário e secundário já existir, a criação de pontos finais privados no espaço de nome primário falhará. Para resolver, crie primeiro um ponto final privado no espaço de nome secundário e, em seguida, crie um para o espaço de nome principal.

> [!NOTE]
> Embora permitam-se apenas acesso a leitura ao espaço de nome secundário, são permitidas atualizações às configurações de pontofinal privado. 

### <a name="recommended-configuration"></a>Configuração recomendada
Ao criar uma configuração de recuperação de desastres para a sua aplicação e espaços de nome sinuosos de eventos, deve criar pontos finais privados para centros de nome sinuosos primários e secundários contra redes virtuais que acolhem instâncias primárias e secundárias da sua aplicação. 

Digamos que tem duas redes virtuais: VNET-1, VNET-2 e estes espaços de nome primário e secundário: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Tem de fazer os seguintes passos: 

- Em EventHubs-Namespace1-Primary, crie dois pontos finais privados que usam subredes de VNET-1 e VNET-2
- No EventHubs-Namespace2-Secondary, crie dois pontos finais privados que usam as mesmas subredes de VNET-1 e VNET-2 

![Pontos finais privados e redes virtuais](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

A vantagem desta abordagem é que o failover pode acontecer na camada de aplicação independente do espaço de nome do Event Hubs. Pondere os seguintes cenários: 

**Falha na aplicação:** Aqui, a aplicação não existirá no VNET-1, mas passará para VNET-2. Como ambos os pontos finais privados estão configurados tanto em VNET-1 como VNET-2 para espaços de nome primário e secundário, a aplicação funcionará apenas. 

Falha no **espaço do Event Hubs**: Aqui novamente, uma vez que ambos os pontos finais privados estão configurados em ambas as redes virtuais para espaços de nomes primários e secundários, a aplicação funcionará apenas. 

> [!NOTE]
> Para obter orientações sobre a recuperação geo-desastre de uma rede virtual, consulte [Rede Virtual - Continuidade de Negócios](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Passos seguintes

* A [amostra no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) atravessa um simples fluxo de trabalho que cria um geo-emparelhamento e inicia uma falha para um cenário de recuperação de desastres.
* A [referência REST API](/rest/api/eventhub/) descreve APIs para executar a configuração de recuperação de geo-desastres.

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Pitão](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
