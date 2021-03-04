---
title: Recuperação de geo-desastres - Azure Event Hubs| Microsoft Docs
description: Como utilizar as regiões geográficas para falhar e realizar a recuperação de desastres nos Hubs de Eventos do Azure
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: eb6ef1a7536b819d1bc973740a0da6fdf3d756d5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042373"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hubs de Eventos Azure - Recuperação de geo-desastres 

A resiliência contra falhas desastrosas dos recursos de processamento de dados é um requisito para muitas empresas e, em alguns casos, mesmo exigida pelos regulamentos do sector. 

O Azure Event Hubs já espalha o risco de falhas catastróficas de máquinas individuais ou mesmo de racks completos em clusters que abrangem múltiplos domínios de falha dentro de um datacenter e implementa mecanismos transparentes de deteção de falhas e falhas de forma a que o serviço continue a funcionar dentro dos níveis de serviço garantidos e tipicamente sem interrupções visíveis em caso de tais falhas. Se um espaço de nomes do Event Hubs tiver sido criado com a opção ativa para [zonas de disponibilidade,](../availability-zones/az-overview.md)o risco de paragem é ainda mais distribuído por três instalações fisicamente separadas, e o serviço tem reservas de capacidade suficientes para lidar instantaneamente com a perda completa e catastrófica de toda a instalação. 

O modelo de cluster Azure Event Hubs, com suporte para zonas de disponibilidade, proporciona resiliência contra falhas graves de hardware e até mesmo perda catastrófica de instalações inteiras do datacenter. Ainda assim, pode haver situações graves com destruição física generalizada que nem essas medidas podem defender-se suficientemente. 

A funcionalidade de recuperação de geo-desastres do Event Hubs foi concebida para facilitar a recuperação de um desastre desta magnitude e abandonar uma região de Azure falhada para sempre e sem ter de alterar as configurações da sua aplicação. O abandono de uma região do Azure irá normalmente envolver vários serviços e esta funcionalidade visa principalmente ajudar a preservar a integridade da configuração da aplicação composta.  

A funcionalidade de recuperação Geo-Disaster garante que toda a configuração de um espaço de nome (Event Hubs, Consumer Groups e configurações) é continuamente replicada de um espaço de nome primário para um espaço de nome secundário quando emparelhado, e permite-lhe iniciar uma passagem de falha única do primário para o secundário a qualquer momento. O movimento de failover irá re-apontar o nome do pseudónimo escolhido para o espaço de nome para o espaço de nome secundário e, em seguida, quebrar o emparelhamento. O fracasso é quase instantâneo uma vez iniciado. 

> [!IMPORTANT]
> A funcionalidade permite a continuidade instantânea das operações com a mesma configuração, mas **não replica os dados do evento**. A menos que o desastre tenha causado a perda de todas as zonas, os dados do evento que são preservados no Principal Event Hub após a falha serão recuperáveis e os eventos históricos podem ser obtidos a partir daí uma vez que o acesso seja restaurado. Para replicar dados de eventos e operar espaços de nome correspondentes em configurações ativas/ativas para lidar com interrupções e desastres, não se apoie neste conjunto de recursos de recuperação de geo-desastres, mas siga a [orientação de replicação](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante notar a distinção entre "interrupções" e "desastres". Uma **paralisação** é a indisponibilidade temporária dos Azure Event Hubs, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, depois de o problema ser corrigido, o Event Hubs volta a estar disponível. Normalmente, uma paragem não causa a perda de mensagens ou outros dados. Um exemplo de tal falha pode ser uma falha de energia no datacenter. Algumas interrupções são apenas perdas de ligação curtas devido a problemas transitórios ou de rede. 

Uma *catástrofe* é definida como a perda permanente ou a longo prazo de um cluster de Centros de Eventos, região de Azure ou datacenter. A região ou o datacenter podem ou não voltar a estar disponíveis, ou podem ficar em baixo durante horas ou dias. Exemplos de tais desastres são incêndios, inundações ou terramotos. Um desastre que se torna permanente pode causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos não deve haver perda de dados e as mensagens podem ser recuperadas uma vez que o centro de dados está de volta.

A funcionalidade de recuperação de geo-desastres dos Hubs de Eventos Azure é uma solução de recuperação de desastres. Os conceitos e fluxos de trabalho descritos neste artigo aplicam-se a cenários de catástrofe, e não a interrupções transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

A funcionalidade de recuperação de desastres implementa a recuperação de desastres de metadados e baseia-se em espaços de nomes de recuperação de desastres primários e secundários. 

A funcionalidade de recuperação de geo-desastres está disponível apenas para os [SKUs padrão e dedicados.](https://azure.microsoft.com/pricing/details/event-hubs/) Não é necessário fazer alterações nas cordas de ligação, uma vez que a ligação é feita através de um pseudónimo.

São utilizados neste artigo os seguintes termos:

-  *Pseudónimo :* O nome para uma configuração de recuperação de desastres que configura. O pseudónimo fornece uma única cadeia estável de nome de domínio totalmente qualificado (FQDN). As aplicações utilizam este fio de ligação de pseudónimo para ligar a um espaço de nome. 

-  *Espaço de nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nome primário é "ativo" e recebe mensagens (pode ser um espaço de nome existente ou novo). O espaço de nome secundário é "passivo" e não recebe mensagens. Os metadados entre ambos estão sincronizados, pelo que ambos podem aceitar mensagens sem qualquer código de aplicação ou alterações nas cordas de ligação. Para garantir que apenas o espaço de nome ativo recebe mensagens, deve utilizar o pseudónimo.
-  *Metadados*: Entidades como centros de eventos e grupos de consumidores; e as suas propriedades do serviço que estão associados ao espaço de nome. Apenas as entidades e as suas configurações são replicadas automaticamente. Mensagens e eventos não são replicados. 
-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="supported-namespace-pairs"></a>Pares de espaços com nome suportado
São suportadas as seguintes combinações de espaços de nome primário e secundário:  

| Espaço de nome primário | Espaço de nome secundário | Suportado | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sim | 
| Standard | Dedicada | Sim | 
| Dedicada | Dedicada | Sim | 
| Dedicada | Standard | Não | 

> [!NOTE]
> Não se pode emparelhar espaços de nomes que estão no mesmo aglomerado dedicado. Pode emparelhar espaços de nomes que estão em aglomerados separados. 

## <a name="setup-and-failover-flow"></a>Configuração e fluxo de failover

A seguinte secção é uma visão geral do processo de failover, e explica como configurar o failover inicial. 

![1][]

### <a name="setup"></a>Configuração

Primeiro cria-se ou usa-se um espaço de nome primário existente, e um novo espaço de nome secundário, em seguida, emparelha os dois. Este emparelhamento dá-lhe um pseudónimo que pode usar para ligar. Porque usas um pseudónimo, não tens de mudar as cordas de ligação. Apenas novos espaços de nome podem ser adicionados ao seu emparelhamento failover. 

1. Crie o espaço de nome primário.
1. Crie o espaço de nome secundário numa região diferente. Este passo é opcional. Pode criar o espaço de nome secundário enquanto cria o emparelhamento no passo seguinte. 
1. No portal Azure, navegue para o seu espaço de nome principal.
1. Selecione **Geo-recuperação** no menu esquerdo e selecione Iniciar o **emparelhamento** na barra de ferramentas. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Iniciar o emparelhamento a partir do espaço de nome primário":::    
1. Na página **de emparelhamento Iniciar,** siga estes passos:
    1. Selecione um espaço de nome secundário existente ou crie um em uma região diferente. Neste exemplo, é selecionado um espaço de nome existente.  
    1. Para **Alias, insira** um pseudónimo para o emparelhamento geo-dr. 
    1. Em seguida, selecione **Criar**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Selecione o espaço de nome secundário":::        
1. Devia ver a página do **Geo-DR Alias.** Também pode navegar para esta página a partir do espaço de nome primário selecionando **Geo-recuperação** no menu esquerdo.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Página de pseudónimos Geo-DR":::    
1. Na página **De Alias Geo-DR,** selecione políticas de **acesso partilhado** no menu esquerdo para aceder à cadeia de ligação primária para o pseudónimo. Utilize esta cadeia de ligação em vez de utilizar diretamente a cadeia de ligação ao espaço de nome primário/secundário. 
1. Nesta página **geral,** pode fazer as seguintes ações: 
    1. Quebre o emparelhamento entre espaços de nome primário e secundário. **Selecione Break pairing** na barra de ferramentas. 
    1. Falha manual no espaço de nome secundário. Selecione **Failover** na barra de ferramentas. 
    
        > [!WARNING]
        > Falhando por cima irá ativar o espaço de nome secundário e remover o espaço de nome primário do emparelhamento Geo-Disaster Recuperação. Crie outro espaço de nome para ter um novo par de recuperação de geo-desastres. 

Por último, deve adicionar alguma monitorização para detetar se é necessário um failover. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, pelo que as falhas automáticas raramente são possíveis, uma vez que muitas vezes as falhas devem ser realizadas em sincronização com o subsistema ou infraestrutura restantes.

### <a name="example"></a>Exemplo

Num exemplo deste cenário, considere uma solução de Ponto de Venda (POS) que emite mensagens ou eventos. O Event Hubs passa esses eventos para alguma solução de mapeamento ou reformatação, que depois encaminha dados mapeados para outro sistema para posterior processamento. Nessa altura, todos estes sistemas poderão estar alojados na mesma região de Azure. A decisão de quando e de que parte falhar depende do fluxo de dados na sua infraestrutura. 

Pode automatizar falhas quer com sistemas de monitorização, quer com soluções de monitorização personalizadas. No entanto, tal automatização requer planeamento e trabalho extra, que está fora do âmbito deste artigo.

### <a name="failover-flow"></a>Fluxo de failover

Se iniciar a falha, são necessários dois passos:

1. Se ocorrer outra paralisação, pretende ser capaz de falhar novamente. Portanto, crie outro espaço de nome passivo e atualize o emparelhamento. 

2. Puxe mensagens do antigo espaço de nome primário uma vez que esteja disponível novamente. Depois disso, utilize esse espaço de nome para mensagens regulares fora da sua configuração de geo-recuperação ou elimine o antigo espaço de nome primário.

> [!NOTE]
> Só as semânticas dianteiras falhadas são apoiadas. Neste cenário, falha-se e depois volta a emparelhar com um novo espaço de nome. Falhar não é suportado; por exemplo, num aglomerado SQL. 

![2][]

## <a name="management"></a>Gestão

Se cometeu um erro; por exemplo, emparelhaste as regiões erradas durante a configuração inicial, podes quebrar o emparelhamento dos dois espaços de nome a qualquer momento. Se quiser utilizar os espaços de nome emparelhados como espaços de nome regulares, elimine o pseudónimo.

## <a name="samples"></a>Amostras

A [amostra no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) mostra como configurar e iniciar uma falha. Esta amostra demonstra os seguintes conceitos:

- Definições necessárias no Azure Ative Directory para utilizar o Azure Resource Manager com Centros de Eventos. 
- Passos necessários para executar o código de amostra. 
- Enviar e receber do atual espaço de nome primário. 

## <a name="considerations"></a>Considerações

Note as seguintes considerações a ter em conta:

1. Por design, a recuperação de geo-desastres do Event Hubs não replica dados, pelo que não é possível reutilizar o valor de compensação antigo do seu centro de eventos primário no seu centro de eventos secundário. Recomendamos reiniciar o recetor do evento com um dos seguintes métodos:

   - *EventPosition.FromStart()* - Se desejar ler todos os dados no seu centro de eventos secundário.
   - *EventPosition.FromEnd()* - Se desejar ler todos os novos dados a partir do momento da ligação ao seu centro de eventos secundário.
   - *EventPosition.FromEnqueuedTime (dataTime)* - Se desejar ler todos os dados recebidos no seu centro de eventos secundários a partir de uma determinada data e hora.

2. No seu planeamento de falhas, também deve considerar o fator tempo. Por exemplo, se perder conectividade por mais de 15 a 20 minutos, poderá decidir iniciar a falha. 
 
3. O facto de não se replicarem dados significa que as sessões ativas atuais não são replicadas. Além disso, a deteção duplicada e as mensagens programadas podem não funcionar. Novas sessões, mensagens agendadas e novos duplicados funcionarão. 

4. A falha de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez. 

5. As entidades sincronizadas podem demorar algum tempo, aproximadamente 50-100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidade 

O Event Hubs Standard SKU suporta [Zonas de Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro de uma região de Azure. 

> [!NOTE]
> O suporte de Zonas de Disponibilidade para Azure Event Hubs Standard só está disponível nas [regiões de Azure](../availability-zones/az-region.md) onde existem zonas de disponibilidade.

Pode ativar zonas de disponibilidade apenas em novos espaços de nome, utilizando o portal Azure. Os Centros de Eventos não apoiam a migração de espaços de nome existentes. Não pode desativar a redundância da zona depois de o permitir no seu espaço de nome.

Quando utiliza zonas de disponibilidade, tanto os metadados como os dados (eventos) são replicados em centros de dados na zona de disponibilidade. 

![3][]

## <a name="private-endpoints"></a>Pontos finais privados
Esta secção fornece mais considerações ao utilizar a recuperação de geo-desastres com espaços de nome que usam pontos finais privados. Para aprender a utilizar pontos finais privados com os Centros de Eventos em geral, consulte os [pontos finais privados Configure](private-link-service.md).

### <a name="new-pairings"></a>Novos pares
Se tentar criar um emparelhamento entre um espaço de nome primário com um ponto final privado e um espaço de nome secundário sem um ponto final privado, o emparelhamento falhará. O emparelhamento só terá sucesso se os espaços de nome primário e secundário tiverem pontos finais privados. Recomendamos que utilize as mesmas configurações nos espaços de nome primário e secundário e em redes virtuais nas quais são criados pontos finais privados.  

> [!NOTE]
> Quando tenta emparelhar o espaço de nome primário com o ponto final privado e um espaço de nome secundário, o processo de validação apenas verifica se existe um ponto final privado no espaço de nome secundário. Não verifica se o ponto final funciona ou funcionará após o fracasso. É sua responsabilidade garantir que o espaço de nome secundário com ponto final privado funcionará como esperado após o failover.
>
> Para testar que as configurações de ponto final privado são as mesmas nos espaços de nome primário e secundário, envie um pedido de leitura (por exemplo: [Obter o Event Hub)](/rest/api/eventhub/get-event-hub)para o espaço de nome secundário a partir da rede virtual e verifique se recebe uma mensagem de erro do serviço.

### <a name="existing-pairings"></a>Emparelhamentos existentes
Se já existir uma combinação entre o espaço de nome primário e secundário, a criação de ponto final privado no espaço principal de nome falhará. Para resolver, crie um ponto final privado no espaço de nome secundário primeiro e, em seguida, crie um para o espaço de nome primário.

> [!NOTE]
> Embora permitamos o acesso apenas à leitura do espaço de nome secundário, são permitidas atualizações para as configurações de ponto final privado. 

### <a name="recommended-configuration"></a>Configuração recomendada
Ao criar uma configuração de recuperação de desastres para a sua aplicação e espaços de nomes de Event Hubs, deve criar pontos finais privados para os espaços de nomes de Centros de Eventos primários e secundários contra redes virtuais que hospedam instâncias primárias e secundárias da sua aplicação. 

Digamos que tem duas redes virtuais: VNET-1, VNET-2 e estes espaços de nome primário e secundário: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Tem de fazer os seguintes passos: 

- No EventHubs-Namespace1-Primary, crie dois pontos finais privados que utilizem sub-redes de VNET-1 e VNET-2
- No EventHubs-Namespace2-Secondary, crie dois pontos finais privados que utilizem as mesmas sub-redes de VNET-1 e VNET-2 

![Pontos finais privados e redes virtuais](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

A vantagem desta abordagem é que o failover pode acontecer na camada de aplicação independente do espaço de nomes do Event Hubs. Pondere os seguintes cenários: 

**Falha apenas na aplicação:** Aqui, a aplicação não existirá no VNET-1, mas passará para vNET-2. Uma vez que ambos os pontos finais privados estão configurados tanto no VNET-1 como no VNET-2 para espaços de nome primário e secundário, a aplicação apenas funcionará. 

**Falha apenas** no espaço de nome do Event Hubs : Aqui novamente, uma vez que ambos os pontos finais privados estão configurados em redes virtuais tanto para espaços de nome primário como secundário, a aplicação apenas funcionará. 

> [!NOTE]
> Para obter orientações sobre a recuperação de geo-desastres de uma rede virtual, consulte [Rede Virtual - Continuidade do Negócio](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Passos seguintes

* A [amostra no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) percorre um simples fluxo de trabalho que cria um geo-emparelhamento e inicia uma falha para um cenário de recuperação de desastres.
* A [referência REST API](/rest/api/eventhub/) descreve APIs para a realização da configuração de recuperação de geo-desastres.

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Introdução ao Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
