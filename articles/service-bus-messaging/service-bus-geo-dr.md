---
title: Azure Service Bus Geo-disaster recovery | Microsoft Docs
description: Como utilizar as regiões geográficas para falhar e recuperar desastres no Azure Service Bus
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 3e8050cdaaae7e16a0f5125292df4b89b3690ed3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035399"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperação de geo-desastre de autocarro de serviço Azure

A resiliência contra falhas desastrosas dos recursos de processamento de dados é um requisito para muitas empresas e, em alguns casos, mesmo exigida pelos regulamentos do sector. 

A Azure Service Bus já espalha o risco de falhas catastróficas de máquinas individuais ou mesmo de racks completos em clusters que abrangem múltiplos domínios de falha dentro de um datacenter e implementa mecanismos transparentes de deteção de falhas e falhas de falha, de modo a que o serviço continue a funcionar dentro dos níveis de serviço garantidos e tipicamente sem interrupções visíveis quando tais falhas ocorrerem. Se um espaço de nomes de Service Bus tiver sido criado com a opção ativa para [zonas de disponibilidade,](../availability-zones/az-overview.md)o risco é que o risco de paragem seja ainda mais distribuído por três instalações fisicamente separadas, e o serviço tem reservas de capacidade suficientes para lidar instantaneamente com a perda completa e catastrófica de toda a instalação. 

O modelo de cluster Azure Service Bus totalmente ativo com suporte de zona de disponibilidade é superior a qualquer produto corretor de mensagens no local em termos de resiliência contra falhas graves de hardware e até perda catastrófica de instalações inteiras do datacenter. Ainda assim, pode haver situações graves com destruição física generalizada que nem essas medidas podem defender-se o suficiente. 

A funcionalidade de recuperação de geo-desastres de autocarro de serviço foi concebida para facilitar a recuperação de um desastre desta magnitude e abandonar uma região de Azure falhada para sempre e sem ter de alterar as configurações da sua aplicação. O abandono de uma região do Azure irá normalmente envolver vários serviços e esta funcionalidade visa principalmente ajudar a preservar a integridade da configuração da aplicação composta. A funcionalidade está globalmente disponível para o Service Bus Premium SKU. 

A funcionalidade de recuperação Geo-Disaster garante que toda a configuração de um espaço de nome (Filas, Tópicos, Subscrições, Filtros) é continuamente replicada de um espaço de nome primário para um espaço de nome secundário quando emparelhado, e permite iniciar uma passagem de falha única do primário para o secundário a qualquer momento. O movimento de failover irá repontá-lo o nome do pseudónimo escolhido para o espaço de nome para o espaço de nome secundário e, em seguida, quebrar o emparelhamento. O fracasso é quase instantâneo uma vez iniciado. 

> [!IMPORTANT]
> A funcionalidade permite a continuidade instantânea das operações com a mesma configuração, mas **não replica as mensagens mantidas em filas ou subscrições de tópicos ou filas de cartas mortas.** Para preservar a semântica da fila, tal replicação exigirá não só a replicação de dados de mensagens, mas de todas as mudanças de estado no corretor. Para a maioria dos espaços de nomes de Service Bus, o tráfego de replicação necessário ultrapassaria em muito o tráfego de aplicações e com filas de alta produção, a maioria das mensagens continuaria a replicar-se para o secundário enquanto já estão a ser apagadas das primárias, causando tráfego excessivamente desperdiçado. Para as rotas de replicação de alta latência, que se aplicam a muitos emparelhamentos que escolheria para a recuperação de geo-desastres, também poderia ser impossível para o tráfego de replicação acompanhar de forma sustentável o tráfego de aplicações devido aos efeitos de estrangulamento induzidos pela latência.
 
> [!TIP]
> Para replicar o conteúdo de filas e subscrições de tópicos e operar espaços de nome correspondentes em configurações ativas/ativas para lidar com interrupções e desastres, não se apoie neste conjunto de recursos de recuperação de geo-desastres, mas siga a [orientação de replicação](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante notar a distinção entre "interrupções" e "desastres". 

Uma *paralisação* é a indisponibilidade temporária da Azure Service Bus, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, depois de resolvido o problema, a Service Bus volta a estar disponível. Normalmente, uma paragem não causa a perda de mensagens ou outros dados. Um exemplo de tal falha pode ser uma falha de energia no datacenter. Algumas interrupções são apenas perdas de ligação curtas devido a problemas transitórios ou de rede. 

Um *desastre* é definido como a perda permanente ou a longo prazo de um cluster de ônibus de serviço, região de Azure, ou datacenter. A região ou o datacenter podem ou não voltar a estar disponíveis, ou podem ficar em baixo durante horas ou dias. Exemplos de tais desastres são incêndios, inundações ou terramotos. Um desastre que se torna permanente pode causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos não deve haver perda de dados e as mensagens podem ser recuperadas uma vez que o centro de dados está de volta.

A funcionalidade de recuperação de geo-desastres da Azure Service Bus é uma solução de recuperação de desastres. Os conceitos e fluxos de trabalho descritos neste artigo aplicam-se a cenários de catástrofe, e não a interrupções transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

A funcionalidade de recuperação de desastres implementa a recuperação de desastres de metadados e baseia-se em espaços de nomes de recuperação de desastres primários e secundários. A funcionalidade de recuperação de geo-desastres está disponível apenas para o [SKU Premium.](service-bus-premium-messaging.md) Não é necessário fazer alterações nas cordas de ligação, uma vez que a ligação é feita através de um pseudónimo.

São utilizados neste artigo os seguintes termos:

-  *Pseudónimo :* O nome para uma configuração de recuperação de desastres que configura. O pseudónimo fornece uma única cadeia estável de nome de domínio totalmente qualificado (FQDN). As aplicações utilizam este fio de ligação de pseudónimo para ligar a um espaço de nome. A utilização de um pseudónimo garante que a cadeia de ligação se mantém inalterada quando a falha é acionada.

-  *Espaço de nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nome primário é "ativo" e recebe mensagens (este pode ser um espaço de nome existente ou novo). O espaço de nome secundário é "passivo" e não recebe mensagens. Os metadados entre ambos estão sincronizados, pelo que ambos podem aceitar mensagens sem qualquer código de aplicação ou alterações nas cordas de ligação. Para garantir que apenas o espaço de nome ativo recebe mensagens, deve utilizar o pseudónimo. 
-  *Metadados*: Entidades como filas, tópicos e subscrições; e as suas propriedades do serviço que estão associados ao espaço de nome. Apenas as entidades e as suas configurações são replicadas automaticamente. As mensagens não são replicadas.
-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="setup"></a>Configuração

A seguinte secção é uma visão geral para configurar o emparelhamento entre os espaços de nome.

![1][]

Primeiro cria-se ou usa-se um espaço de nome primário existente, e um novo espaço de nome secundário, em seguida, emparelha os dois. Este emparelhamento dá-lhe um pseudónimo que pode usar para ligar. Porque usas um pseudónimo, não tens de mudar as cordas de ligação. Apenas novos espaços de nome podem ser adicionados ao seu emparelhamento failover. 

1. Crie o espaço de nome primário.
1. Crie o espaço de nome secundário numa região diferente. Este passo é opcional. Pode criar o espaço de nome secundário enquanto cria o emparelhamento no passo seguinte. 
1. No portal Azure, navegue para o seu espaço de nome principal.
1. Selecione **Geo-recuperação** no menu esquerdo e selecione Iniciar o **emparelhamento** na barra de ferramentas. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Iniciar o emparelhamento a partir do espaço de nome primário":::    
1. Na página **de emparelhamento Iniciar,** siga estes passos:
    1. Selecione um espaço de nome secundário existente ou crie um em uma região diferente. Neste exemplo, um espaço de nome existente é usado como espaço de nome secundário.  
    1. Para **Alias, insira** um pseudónimo para o emparelhamento geo-dr. 
    1. Em seguida, selecione **Criar**. 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="Selecione o espaço de nome secundário":::        
1. Deve ver a página **de Alias De Serviço Geo-DR Como** mostrado na imagem seguinte. Também pode navegar para a página **De Alias Geo-DR** a partir da página do espaço de nome primário selecionando a **Geo-recuperação** no menu esquerdo. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Página de Alias Geo-DR de ônibus de serviço":::
1. Na página **De Alias Geo-DR,** selecione políticas de **acesso partilhado** no menu esquerdo para aceder à cadeia de ligação primária para o pseudónimo. Utilize esta cadeia de ligação em vez de utilizar diretamente a cadeia de ligação ao espaço de nome primário/secundário. Inicialmente, o pseudónimo aponta para o espaço de nome primário.
1. Mude para a página **'Vista Geral'.** Pode fazer as seguintes ações: 
    1. Quebre o emparelhamento entre espaços de nome primário e secundário. **Selecione Break pairing** na barra de ferramentas. 
    1. Falha manualmente no espaço de nome secundário. 
        1. Selecione **Failover** na barra de ferramentas. 
        1. Confirme que deseja falhar no espaço de nome secundário digitando o seu pseudónimo. 
        1. Ligue a opção **Safe Failover** para falhar com segurança no espaço de nome secundário. Esta função garante que as replicações Geo-DR pendentes sejam concluídas antes de passarem para o secundário. 
        1. Em seguida, selecione **Failover**. 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > Falhando por cima irá ativar o espaço de nome secundário e remover o espaço de nome primário do emparelhamento Geo-Disaster Recuperação. Crie outro espaço de nome para ter um novo par de recuperação de geo-desastres. 

1. Por último, deve adicionar alguma monitorização para detetar se é necessário um failover. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, pelo que as falhas automáticas raramente são possíveis, uma vez que muitas vezes as falhas devem ser realizadas em sincronização com o subsistema ou infraestrutura restantes.

### <a name="service-bus-standard-to-premium"></a>Ônibus de serviço padrão para premium
Se [emigrou o seu espaço de nome Azure Service Standard para Azure Service Bus Premium,](service-bus-migrate-standard-premium.md)então deve utilizar o pseudónimo pré-existente (isto é, a sua cadeia de conexão de espaço de identificação Service Bus Standard) para criar a configuração de recuperação de desastres através da **API PS/CLI** ou **REST**.

É porque, durante a migração, o seu nome de conexão Azure Service Bus Standard/DNS em si torna-se um pseudónimo para o seu espaço de nomeS Azure Service Bus Premium.

As aplicações do seu cliente devem utilizar este pseudónimo (isto é, a cadeia de conexão namespace Standard do Azure Service Standard) para ligar ao espaço de nome Premium onde foi criado o emparelhamento de recuperação de desastres.

Se utilizar o Portal para configurar a configuração de recuperação de desastres, então o portal irá abstrair esta ressalva de si.


## <a name="failover-flow"></a>Fluxo de failover

Uma falha é desencadeada manualmente pelo cliente (quer explicitamente através de um comando, quer através de uma lógica de negócio de propriedade do cliente que desencadeia o comando) e nunca pela Azure. Dá ao cliente total propriedade e visibilidade para resolução de paralisação na espinha dorsal da Azure.

![4][]

Depois que o failover é desencadeado -

1. A cadeia de ligação ***alias*** é atualizada para apontar para o espaço de nomes Secondary Premium.

2. Os clientes (remetentes e recetores) ligam-se automaticamente ao espaço de nome secundário.

3. O emparelhamento existente entre o espaço de nome primário e secundário premium está quebrado.

Uma vez iniciada a falha -

1. Se ocorrer outra paralisação, pretende ser capaz de falhar novamente. Então, crie outro espaço de nome passivo e atualize o emparelhamento. 

2. Puxe mensagens do antigo espaço de nome primário uma vez que esteja disponível novamente. Depois disso, utilize esse espaço de nome para mensagens regulares fora da sua configuração de geo-recuperação ou elimine o antigo espaço de nome primário.

> [!NOTE]
> Só as semânticas dianteiras falhadas são apoiadas. Neste cenário, falha-se e depois volta a emparelhar com um novo espaço de nome. Falhar não é suportado; por exemplo, num aglomerado SQL. 

Pode automatizar falhas quer com sistemas de monitorização, quer com soluções de monitorização personalizadas. No entanto, tal automatização requer planeamento e trabalho extra, que está fora do âmbito deste artigo.

![2][]

## <a name="management"></a>Gestão

Se cometeu um erro; por exemplo, emparelhaste as regiões erradas durante a configuração inicial, podes quebrar o emparelhamento dos dois espaços de nome a qualquer momento. Se quiser utilizar os espaços de nome emparelhados como espaços de nome regulares, elimine o pseudónimo.

## <a name="use-existing-namespace-as-alias"></a>Use o espaço de nome existente como pseudónimo

Se tiver um cenário em que não possa alterar as ligações de produtores e consumidores, pode reutilizar o nome do seu nome como nome de pseudónimo. Consulte [o código de amostra no GitHub aqui.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)

## <a name="samples"></a>Amostras

As [amostras no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostram como configurar e iniciar uma falha. Estas amostras demonstram os seguintes conceitos:

- Uma amostra .NET e configurações que são necessárias no Azure Ative Directory para usar o Azure Resource Manager com Service Bus, para configurar e permitir a recuperação de geo-desastres.
- Passos necessários para executar o código de amostra.
- Como usar um espaço de nome existente como pseudónimo.
- Passos para permitir a recuperação de geo-desastres via PowerShell ou CLI.
- [Enviar e receber](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) do atual espaço de nome primário ou secundário utilizando o pseudónimo.

## <a name="considerations"></a>Considerações

Note as seguintes considerações a ter em conta com esta versão:

1. No seu planeamento de falhas, também deve considerar o fator tempo. Por exemplo, se perder conectividade por mais de 15 a 20 minutos, poderá decidir iniciar a falha.

2. O facto de não se replicarem dados significa que as sessões ativas não são replicadas. Além disso, a deteção duplicada e as mensagens programadas podem não funcionar. Novas sessões, novas mensagens agendadas e novos duplicados funcionarão. 

3. A falha de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez.

4. As entidades sincronizadas podem demorar algum tempo, aproximadamente 50-100 entidades por minuto. As assinaturas e as regras também contam como entidades.

### <a name="availability-zones"></a>Zonas de Disponibilidade

O Service Bus Premium SKU suporta [Zonas de Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas na mesma região de Azure. A Service Bus gere três cópias da loja de mensagens (1 primária e 2 secundária). A Service Bus mantém as três cópias sincronizadas para operações de dados e gestão. Se a cópia primária falhar, uma das cópias secundárias é promovida para primária sem tempo de inatividade percebido. Se as aplicações virem desconexões transitórias do Service Bus, a lógica de repetição no SDK reconectará-se automaticamente ao Service Bus. 

Quando utiliza zonas de disponibilidade, tanto os metadados como os dados (mensagens) são replicados em centros de dados na zona de disponibilidade. 

> [!NOTE]
> O suporte das Zonas disponibilidade para o Azure Service Bus Premium só está disponível nas [regiões de Azure](../availability-zones/az-region.md) onde existem zonas de disponibilidade.

Pode ativar zonas de disponibilidade apenas em novos espaços de nome, utilizando o portal Azure. A Service Bus não apoia a migração de espaços de nome existentes. Não é possível desativar a redundância da zona depois de a permitir no seu espaço de nome.

![3][]

## <a name="private-endpoints"></a>Pontos finais privados
Esta secção fornece mais considerações ao utilizar a recuperação de geo-desastres com espaços de nome que usam pontos finais privados. Para saber mais sobre a utilização de pontos finais privados com o Service Bus em geral, consulte [Integrate Azure Service Bus com Azure Private Link](private-link-service.md).

### <a name="new-pairings"></a>Novos pares
Se tentar criar um emparelhamento entre um espaço de nome primário com um ponto final privado e um espaço de nome secundário sem um ponto final privado, o emparelhamento falhará. O emparelhamento só terá sucesso se os espaços de nome primário e secundário tiverem pontos finais privados. Recomendamos que utilize as mesmas configurações nos espaços de nome primário e secundário e em redes virtuais nas quais são criados pontos finais privados. 

> [!NOTE]
> Quando tenta emparelhar o espaço de nome primário com um ponto final privado e o espaço de nome secundário, o processo de validação apenas verifica se existe um ponto final privado no espaço de nome secundário. Não verifica se o ponto final funciona ou funcionará após o fracasso. É sua responsabilidade garantir que o espaço de nome secundário com ponto final privado funcionará como esperado após o failover.
>
> Para testar se as configurações do ponto final privado são as mesmas, envie um pedido [de filas](/rest/api/servicebus/stable/queues/get) para o espaço de nome secundário a partir da rede virtual e verifique se recebe uma mensagem de erro do serviço.

### <a name="existing-pairings"></a>Emparelhamentos existentes
Se já existir uma combinação entre o espaço de nome primário e secundário, a criação de ponto final privado no espaço principal de nome falhará. Para resolver, crie um ponto final privado no espaço de nome secundário primeiro e, em seguida, crie um para o espaço de nome primário.

> [!NOTE]
> Embora permitamos o acesso apenas à leitura do espaço de nome secundário, são permitidas atualizações para as configurações de ponto final privado. 

### <a name="recommended-configuration"></a>Configuração recomendada
Ao criar uma configuração de recuperação de desastres para a sua aplicação e Service Bus, tem de criar pontos finais privados para espaços de nomes de autocarros de serviço primários e secundários contra redes virtuais que hospedam instâncias primárias e secundárias da sua aplicação.

Digamos que tem duas redes virtuais: VNET-1, VNET-2 e estes espaços primários e de segundo nome: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-Secondary. Tem de fazer os seguintes passos: 

- No ServiceBus-Namespace1-Primary, crie dois pontos finais privados que utilizem sub-redes de VNET-1 e VNET-2
- No ServiceBus-Namespace2-Secondary, crie dois pontos finais privados que utilizem as mesmas sub-redes de VNET-1 e VNET-2 

![Pontos finais privados e redes virtuais](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


A vantagem desta abordagem é que o failover pode acontecer na camada de aplicação independente do espaço de nomes do Service Bus. Pondere os seguintes cenários: 

**Falha apenas na aplicação:** Aqui, a aplicação não existirá no VNET-1, mas passará para vNET-2. Uma vez que ambos os pontos finais privados estão configurados tanto no VNET-1 como no VNET-2 para espaços de nome primário e secundário, a aplicação apenas funcionará. 

**Serviço Bus só falha no espaço**: Aqui novamente, uma vez que ambos os pontos finais privados estão configurados em redes virtuais tanto para espaços de nome primário como secundário, a aplicação apenas funcionará. 

> [!NOTE]
> Para obter orientações sobre a recuperação de geo-desastres de uma rede virtual, consulte [Rede Virtual - Continuidade do Negócio](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Passos seguintes

- Consulte aqui a [referência da API de](/rest/api/servicebus/stable/disasterrecoveryconfigs)recuperação de geo-desastres .
- Executar a amostra de recuperação de geo-desastres [no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)
- Consulte a amostra de recuperação de geo-desastres [que envia mensagens para um pseudónimo.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes artigos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Repouso API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
