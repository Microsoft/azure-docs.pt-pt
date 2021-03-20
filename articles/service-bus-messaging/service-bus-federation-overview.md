---
title: Replicação de mensagens e federação inter-região - Azure Service Bus | Microsoft Docs
description: Este artigo fornece uma visão geral da replicação de eventos e federação inter-região com a Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880832"
---
# <a name="message-replication-and-cross-region-federation"></a>Replicação de mensagens e federação entre regiões

Dentro dos espaços de nome, a Azure Service Bus suporta [a criação de topologias de filas acorrentadas e subscrições de tópicos utilizando o autoforwarding](service-bus-auto-forwarding.md) para permitir a implementação de vários padrões de encaminhamento. Por exemplo, pode fornecer aos parceiros filas dedicadas às quais enviaram ou recebem permissões e que podem ser temporariamente suspensas se necessário, e ligá-las de forma flexível a outras entidades privadas à aplicação. Também pode criar topologias complexas de encaminhamento em várias fases, ou pode criar filas de carteiro, que drenam as subscrições de tópicos semelhantes à fila e permitem uma maior capacidade de armazenamento por subscritor. 

Muitas soluções sofisticadas também requerem que as mensagens sejam replicadas através dos limites do espaço de nome, de modo a implementar estes e outros padrões. As mensagens podem ter de fluir entre espaços de nome associados a múltiplos, diferentes inquilinos de aplicação, ou em várias regiões Azure diferentes. 

A sua solução manterá vários espaços de nomes de Service Bus em diferentes regiões e replicará mensagens entre filas e tópicos, e/ou que trocará mensagens com fontes e alvos como [Azure Event Hubs,](../event-hubs/event-hubs-about.md) [Azure IoT Hub,](../iot-fundamentals/iot-introduction.md)ou [Apache Kafka.](https://kafka.apache.org) 

Estes cenários são o foco deste artigo. 

## <a name="federation-patterns"></a>Padrões da Federação

Existem inúmeras motivações potenciais para o porquê de querer mover mensagens entre entidades de Service Bus, como filas ou tópicos, ou entre o Service Bus e outras fontes e alvos. 

Em comparação com o conjunto semelhante de padrões para Os Centros de Eventos , a federação para [entidades](../service-bus-messaging/service-bus-federation-overview.md)semelhantes a filas é mais complexa porque as filas de mensagens prometem aos seus consumidores propriedade exclusiva sobre qualquer mensagem, espera-se que preservem a ordem de chegada na entrega de mensagens, e para que o corretor coordene uma distribuição justa de mensagens entre [consumidores concorrentes.](/azure/architecture/patterns/competing-consumers) 

Existem impedimentos práticos, incluindo os constrangimentos do [teorema](https://en.wikipedia.org/wiki/CAP_theorem)da PAC, que dificultam a visão unificada de uma fila que está simultaneamente disponível em várias regiões e que permite que [os consumidores](/azure/architecture/patterns/competing-consumers) concorrentes, distribuídos regionalmente, tomem posse exclusiva de mensagens. Tal fila geo-distribuída exigiria uma replicação totalmente consistente não só de mensagens, mas também do estado de entrega de cada mensagem antes de as mensagens poderem ser disponibilizadas aos consumidores. Um objetivo de total consistência para uma fila hipotética e distribuída regionalmente está em conflito direto com o objetivo-chave que praticamente todos os clientes da Azure Service Bus têm ao considerar cenários da federação: Máxima disponibilidade e fiabilidade para as suas soluções. 

Os padrões aqui apresentados focam-se, portanto, na disponibilidade e fiabilidade, ao mesmo tempo que visam evitar melhor a perda de informação e o tratamento duplicado de mensagens. 

### <a name="resiliency-against-regional-availability-events"></a>Resiliência contra eventos de disponibilidade regional 

Embora a máxima disponibilidade e fiabilidade sejam as principais prioridades operacionais para a Service Bus, existem, no entanto, muitas formas pelas quais um produtor ou consumidor pode ser impedido de falar com o seu "primário" Service Bus devido a problemas de networking ou resolução de nomes, ou em que a entidade Service Bus pode, de facto, não responder ou devolver erros. O processador de mensagens designado também pode ficar indisponível.

Tais condições não são "desastrosas" de tal forma que você vai querer abandonar completamente a implantação regional como você poderia fazer em uma situação de recuperação de desastres, mas o cenário de negócio de algumas aplicações pode já ser impactado por eventos de disponibilidade que não duram mais do que alguns minutos ou mesmo segundos. O Azure Service Bus é frequentemente utilizado em ambientes de nuvem híbrida e com clientes que residem na borda da rede, por exemplo em lojas de retalho, restaurantes, agências bancárias, instalações de fabrico, instalações logísticas e aeroportos. É possível que um problema de encaminhamento de rede ou congestionamento tenha impacto na capacidade de qualquer site chegar ao ponto final do Service Bus atribuído, enquanto um ponto final secundário numa região diferente pode ser alcançável. Ao mesmo tempo, os sistemas de processamento de mensagens que chegam destes sites podem ainda ter acesso sem entraves aos pontos finais primários e secundários do Service Bus. 

Existem muitos exemplos práticos de tais aplicações híbridas de nuvem e borda com uma baixa tolerância de negócio para o impacto de problemas de encaminhamento de rede ou de problemas de disponibilidade transitório de uma entidade de Service Bus. Estes incluem o processamento de pagamentos em sites de retalho, embarque nos portões do aeroporto, e pedidos de telemóvel em restaurantes, todos eles a um instante, e paralisação completa sempre que a via de comunicação fiável não estiver disponível.

Nesta categoria, discutimos três padrões distribuídos distintos: replicação "totalmente ativa", replicação "activa-passiva" e replicação "spillover". 

#### <a name="all-active-replication"></a>Replicação All-Active

O padrão de replicação "totalmente ativo" permite que uma réplica ativa do mesmo tópico lógico (ou fila) esteja disponível em múltiplos espaços de nome (e regiões), e para que todas as mensagens fiquem disponíveis em todas as réplicas, independentemente do local onde tenham sido ensacados. O padrão geralmente preserva a ordem das mensagens em relação a qualquer editor. 

![Todo o padrão ativo](media/service-bus-federation-overview/mirrored-topics.jpg)

Como mostrado na ilustração, o padrão geralmente se apoia em Tópicos de Autocarro de Serviço. Um tópico para cada espaço de nome que participará no esquema de replicação. Cada um destes tópicos tem uma "subscrição de replicação" para qualquer um dos outros tópicos a que as mensagens devem ser replicadas. Na ilustração acima, temos simplesmente um par de tópicos e, portanto, uma única subscrição de replicação para o outro tópico. Num cenário com três espaços de nome *{n1, n2, n3}*, um tópico no namespace *n1* teria duas subscrições de replicação, uma para o tópico correspondente em *n2* e outra para o tópico correspondente em *n3*. 

Cada subscrição de replicação tem uma regra que combina uma expressão de filtro SQL `replicated <> 1` () e uma ação SQL `set replicated = 1` (). O filtro da regra garante que apenas as mensagens em que a propriedade personalizada `replication` não é definida ou não tem o valor `1` tornam-se elegíveis para esta subscrição, e a ação define essa propriedade exata para o valor `1` em cada mensagem selecionada logo a seguir. O efeito é que quando a mensagem é copiada para o tópico correspondente, já não é elegível para replicação na direção oposta e, portanto, evitamos que as mensagens saltem entre réplicas.

Uma subscrição com uma regra respetiva pode ser facilmente adicionada a qualquer tópico usando o CLI Azure como este.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Para modelar uma fila, cada tópico é restrito a apenas uma subscrição regular (que não as subscrições de replicação) que todos os consumidores partilham.

> O modelo de replicação totalmente ativo coloca uma cópia de cada mensagem enviada em qualquer um dos tópicos em cada um dos tópicos. Isto significa que o seu código de aplicação em cada região verá e processará todas as mensagens.
> Este padrão é adequado para cenários em que os dados são partilhados em várias regiões ou se o processamento redundante é geralmente desejado. Se precisar processar cada mensagem apenas uma vez, como numa fila regular, tem de considerar um dos dois padrões seguintes.  

#### <a name="active-passive-replication"></a>Replicação Active-Passive

O padrão de replicação "active-passivo" é a variação do padrão anterior onde apenas um dos tópicos (o "primário") é ativamente utilizado pela aplicação de envio e receção de mensagens e mensagens são replicados num tópico secundário para o caso em que o tópico primário pode tornar-se indisponível ou inacessível. 

![Padrão passivo ativo](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

A principal diferença entre este padrão e o padrão anterior é que a replicação é unidirecional do tópico primário para o tópico secundário. O tema secundário nunca se torna o principal, mas é uma opção de reserva para quando o tópico principal é temporariamente inutilizável. 

O lado positivo da utilização deste padrão é que tenta ajudar a minimizar o processamento duplicado. Durante a replicação, a propriedade da `TimeToLive` mensagem é definida para uma duração nas mensagens replicadas que reflete o tempo esperado durante o qual uma falha da primária levará a uma falha. Por exemplo, se o seu cenário de caso de uso requer uma transição do consumidor para o secundário no máximo 1 minuto de quando a recuperação de mensagens do início primário mostrando problemas, o secundário deve idealmente ter todas as mensagens disponíveis que não poderia aceder na primária, mas um número mínimo de mensagens que já tinha processado a partir das primárias antes de os problemas aparecerem. Se definirmos o `TimeToLive` período para o dobro desse período, 2 minutos, durante a replicação `set sys.TimeToLive = '0:2:0'` (na ação de regras), o secundário apenas retém mensagens durante 2 minutos e descartará as mais antigas. Isto significa que quando o recetor muda para o secundário, pode ler e descartar mensagens mais antigas do que a última que processou e, em seguida, processar a partir da primeira mensagem que ainda não viu. A duração real da retenção dependerá da caixa de utilização específica e da rapidez com que deseja e pode passar para a secundária da sua aplicação. A `TimeToLive` definição é honrada no intervalo de alguns segundos a dias. 

Enquanto a aplicação está a usar o secundário, também pode publicar diretamente para o tópico secundário, que então age como qualquer tópico regular. Após a transição para o secundário, o consumidor verá, por isso, uma mistura de mensagens e mensagens replicadas publicadas diretamente no secundário. Por conseguinte, a aplicação deve, por conseguinte, voltar a ser publicada para a primária e continuar a permitir a drenagem das mensagens publicadas localmente antes de voltar a mudar o consumidor para o secundário. Devido ao reinício da replicação automaticamente uma vez que o primário está novamente disponível, o consumidor também receberia novas mensagens publicadas nas primárias durante esse período, embora com uma latência um pouco mais elevada. 

> Este padrão é adequado para cenários em que as mensagens devem ser processadas apenas uma vez. A aplicação precisa de cooperar para manter o registo das mensagens que processou a partir das primárias, pois irá encontrar duplicados durante a duração da janela de failover no secundário, e voltará a encontrar duplicados enquanto religa.
> O critério de des duplicação deve ser melhor um pedido fornecido `MessageId` . O `EnqueuedTimeUtc` valor também é adequado como indicador de marca de água, mas a aplicação precisa de permitir alguma quantidade de deriva do relógio (vários segundos) entre o primário e o secundário como em qualquer sistema distribuído.


#### <a name="spillover-replication"></a>Replicação do derrame

O padrão de replicação "spillover" permite o uso ativo/ativo de várias entidades de Service Bus em várias regiões para lidar com o cenário em que o Service Bus é saudável, mas o *consumidor* fica sobrecarregado com o número de mensagens pendentes ou está totalmente indisponível. Uma razão para isso pode ser que uma base de dados que apoie o processo de consumo possa ser lenta ou indisponível. Este padrão funciona com filas simples e com subscrições de tópicos.  

![Replicação do derrame](media/service-bus-federation-overview/spillover.jpg)  

Como mostrado na ilustração, o padrão de replicação do derrame replica mensagens da fila ou da [fila de letras mortas](service-bus-dead-letter-queues.md) associadas a uma fila ou tópico emparelhado em um espaço de nome diferente. 

Sem que haja uma situação de falha, os dois espaços de nome são utilizados em paralelo, cada um recebendo algum subconjunto do tráfego geral de mensagens e os seus consumidores associados que manuseiam esse subconjunto. Uma vez que um dos consumidores começa a apresentar altas taxas de insucesso ou para de imediato, as respetivas mensagens acabarão na fila da letra morta, quer excedendo a contagem de entrega, quer porque expira. As tarefas de replicação irão então buscá-las e re-encacaná-las na fila emparelhada, onde são depois apresentadas ao consumidor presumivelmente saudável. 

Se o processamento tiver de ocorrer dentro de um determinado prazo, `TimeToLive` deve ser definido o período de espera e/ou as mensagens de modo a que o processamento possa continuar a ocorrer a tempo pelo derrame secundário, podendo, por exemplo, ser definido para metade do tempo `TimeToLive` permitido.

Tal como acontece com o [padrão all-active,](#all-active-replication)a aplicação pode adicionar um indicador à mensagem se a mensagem já foi replicada uma vez que não saltam entre o par de filas, mas são bastante postadas numa fila auxiliar que funciona como a fila de letras mortas para o padrão composto.

> Este padrão é adequado para cenários em que a principal preocupação é defender-se contra questões de disponibilidade nos consumidores ou recursos em que os consumidores confiam, bem como redistribuir picos de tráfego numa das filas emparelhadas. Também fornece proteção contra um dos espaços de nome que ficam indisponíveis se os consumidores lerem de ambas as filas, mas o atraso de replicação imposto pela `TimeToLive` expiração pode fazer com que as mensagens dentro dessa janela de tempo fiquem presas no espaço de nome indisponível. 

### <a name="latency-optimization"></a>Otimização da latência 

Os tópicos são usados para distribuir informação a vários consumidores. Em alguns casos, especialmente os consumidores com uma ampla distribuição geográfica, poderia ser benéfico replicar mensagens de um tópico para um tema num espaço de nome secundário mais próximo dos consumidores.

![Otimização da Latência](media/service-bus-federation-overview/latency-optimization.jpg)  

Por exemplo, ao partilhar dados entre centros regionais e continentais, é mais eficiente transferir informação apenas uma vez entre os hubs e fazer com que os consumidores obtenham a sua cópia dos dados desses centros. 

As transferências de replicação podem ser feitas em lotes, que os consumidores obtêm e resolvem mensagens uma a uma. Com uma latência de rede base de 100 ms entre, digamos, América do Norte e Europa, cada mensagem demora mais 200 ms a processar as duas viagens de ida e volta a uma entidade remota para adquirir e acomodar as mensagens, em comparação com uma entidade da mesma região. 

### <a name="validation-reduction-and-enrichment"></a>Validação, redução e enriquecimento

As mensagens podem ser enviadas para uma fila de Autocarros de Serviço ou tópico por clientes externos à sua própria solução.

![Validação, redução, enriquecimento](media/service-bus-federation-overview/validation.jpg)  

Tais mensagens podem exigir a verificação do cumprimento de um determinado esquema e de que as mensagens não conformes sejam retiradas ou enviadas com letras mortas. Algumas mensagens podem ter de ser reduzidas em complexidade, omitindo dados e algumas podem ter de ser enriquecidas adicionando dados com base em análises de dados de referência. As operações podem ser realizadas com funcionalidades personalizadas na tarefa de replicação. 

### <a name="stream-to-queue-replication"></a>Fluxo para a replicação da fila

O Azure Event Hubs é uma solução ideal para lidar com volumes extremos de eventos que chegam. Mas nem os Centros de Eventos nem motores semelhantes como o Apache Kafka fornecem um modelo [de consumidor concorrente](/azure/architecture/patterns/competing-consumers) gerido por serviços, onde vários consumidores podem lidar com mensagens da mesma fonte em simultâneo sem correr o risco de processar duplicado, e finalmente resolver essas mensagens depois de serem processadas. 

![Integração](media/service-bus-federation-overview/hub-to-queue.jpg)

Um fluxo para a replicação da fila transfere o conteúdo de uma única partição do Event Hub ou o conteúdo de um Centro de Eventos completo para uma fila de Service Bus, de onde as mensagens podem ser processadas de forma segura, transacional e com consumidores concorrentes. Esta replicação também permite usar todas as outras capacidades do Service Bus para essas mensagens, incluindo encaminhamento com tópicos e demultiplexing baseado em sessão. 

### <a name="consolidation-and-normalization"></a>Consolidação e normalização 

As soluções globais são muitas vezes compostas por pegadas regionais que são largamente independentes, incluindo ter as suas próprias capacidades de processamento, mas as perspetivas supranais e globais exigirão a integração de dados e, portanto, uma consolidação central dos mesmos dados de mensagens que são avaliados nas respetivas pegadas regionais para a perspetiva local. 

![Consolidação](media/service-bus-federation-overview/merge.jpg)

A normalização é um sabor do cenário de consolidação, pelo que duas ou mais sequências de mensagens recebidas transportam o mesmo tipo de informação, mas com diferentes estruturas ou diferentes codificações, e as mensagens devem ser transcodificadas ou transformadas antes de poderem ser consumidas. 

A normalização também pode incluir trabalhos criptográficos, tais como desencriptação de cargas encriptadas de ponta a ponta e reencrimar com diferentes teclas e algoritmos para o público do consumidor a jusante. 

### <a name="splitting-and-routing"></a>Divisão e encaminhamento

Os tópicos do Service Bus e as suas regras de subscrição são frequentemente usados para filtrar um fluxo de mensagens para um público em particular, e esse público obteve então o conjunto filtrado a partir de uma subscrição. 

![A dividir](media/service-bus-federation-overview/split.jpg)

Num sistema global onde o público para essas mensagens é distribuído globalmente ou pertence a diferentes aplicações, a replicação pode ser usada para transferir mensagens de tal subscrição para uma fila ou tópico num espaço de nome diferente do local onde são consumidas.

### <a name="replication-applications-in-azure-functions"></a>Aplicações de replicação em Funções Azure

A implementação dos padrões acima requer um ambiente de execução escalável e fiável para as tarefas de replicação que pretende configurar e executar. Em Azure, o ambiente de tempo de execução mais adequado para tarefas apátridas é [Azure Functions](../azure-functions/functions-overview.md). 

As Funções Azure podem funcionar sob uma [identidade gerida pelo Azure](../active-directory/managed-identities-azure-resources/overview.md) de modo a que as tarefas de replicação possam integrar-se com as regras de controlo de acesso baseadas em funções dos serviços de origem e alvo sem ter de gerir segredos ao longo do caminho de replicação. Para fontes de replicação e alvos que requerem credenciais explícitas, as Funções Azure podem manter os valores de configuração para essas credenciais em armazenamento controlado de acesso apertado dentro do Cofre da [Chave Azure](../key-vault/general/overview.md).

Além disso, as Funções Azure permitem que as tarefas de replicação se integrem diretamente com redes virtuais Azure e [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço para todos os serviços de mensagens Azure, e é facilmente integrado com [o Azure Monitor.](../azure-monitor/overview.md)

Mais importante ainda, a Azure Functions tem gatilhos pré-construídos, escaláveis e encadernações de saída para [Azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), [Azure IoT Hub,](../azure-functions/functions-bindings-event-iot.md) [Azure Service Bus,](../azure-functions/functions-bindings-service-bus.md) [Azure Event Grid,](../azure-functions/functions-bindings-event-grid.md)e [Azure Queue Storage,](../azure-functions/functions-bindings-storage-queue.md)extensões personalizadas para [RabbitMQ,](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka.](https://github.com/azure/azure-functions-kafka-extension) A maioria dos gatilhos adaptar-se-á dinamicamente às necessidades de produção, escalando o número de instâncias de execução simultânea para cima e para baixo com base em métricas documentadas. 

Com o plano de consumo de Funções Azure, os gatilhos pré-construídos podem até zero enquanto não há mensagens disponíveis para replicação, o que significa que não incorre em custos para manter a configuração pronta a escalar. A principal desvantagem da utilização do plano de consumo é que a latência para tarefas de replicação "acordar" deste Estado é significativamente maior do que com os planos de hospedagem onde a infraestrutura é mantida em funcionamento.  

Em contraste com tudo isto, os motores de replicação mais comuns para mensagens e eventos, como [o MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) da Apache Kafka, exigem que você forneça um ambiente de hospedagem e escala o próprio motor de replicação. Isto inclui configurar e integrar as funcionalidades de segurança e networking e facilitar o fluxo de dados de monitorização, e então ainda não tem a oportunidade de injetar tarefas de replicação personalizadas no fluxo. 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, exploramos uma série de padrões de federação e explicamos o papel das Funções Azure como o evento e o tempo de replicação de mensagens em Azure. 

Em seguida, você pode querer ler como configurar uma aplicação de replicador com Funções Azure e, em seguida, como replicar fluxos de eventos entre Os Centros de Eventos e vários outros sistemas de eventos e mensagens:

- [Aplicações de replicação em Funções Azure](service-bus-federation-replicator-functions.md)
- [Replicação de eventos entre entidades de Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Eventos de encaminhamento para Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Adquirir eventos a partir de Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif