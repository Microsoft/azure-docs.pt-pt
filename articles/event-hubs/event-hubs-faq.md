---
title: Perguntas frequentes - Azure Event Hubs [ Azure Event Hubs ] Microsoft Docs
description: Este artigo fornece uma lista de perguntas frequentes (FAQ) para os Hubs de Eventos Azure e suas respostas.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 0ff1f19a30be8c4ca40a980459901fd9224a6626
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996623"
---
# <a name="event-hubs-frequently-asked-questions"></a>Centros de Eventos frequentemente fazem perguntas

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um espaço de nome de Event Hubs?
Um espaço de nome é um recipiente de deteção para tópicos de Hub de Evento/Kafka. Dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Um espaço de nome serve como um recipiente de aplicação que pode alojar vários tópicos de Evento Hub/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando é que crio um novo espaço de nome vs. usar um espaço de nome existente?
As dotações de capacidade[(unidades de entrada (UsT)](#throughput-units)são faturadas ao nível do espaço de nome. Um espaço de nome também está associado a uma região.

Pode querer criar um novo espaço de nome em vez de utilizar um existente num dos seguintes cenários: 

- Precisa de um Centro de Eventos associado a uma nova região.
- Você precisa de um Hub de evento associado a uma subscrição diferente.
- Você precisa de um Hub de Evento sita com uma alocação de capacidade distinta (isto é, a necessidade de capacidade para o espaço de nome com o hub de evento adicionado excederia o limiar de 40 TU e você não quer ir para o cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre os níveis Básico e Standard do Event Hubs?

O nível Standard de Hubs de Eventos Azure fornece funcionalidades para além do que está disponível no nível Básico. As seguintes funcionalidades estão incluídas com standard:

* Retenção de eventos mais longo
* Ligações intermediadas adicionais, com uma taxa de sobrecarga por mais do que o número incluído
* Mais do que um [único grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Captura](event-hubs-capture-overview.md)
* [Integração kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre os níveis de preços, incluindo os Hubs de Eventodedicados, consulte os detalhes de preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="where-is-azure-event-hubs-available"></a>Onde está disponível o Azure Event Hubs?

O Azure Event Hubs está disponível em todas as regiões apoiadas do Azure. Para obter uma lista, visite a página das [regiões de Azure.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso usar uma única ligação AMQP para enviar e receber de vários centros de eventos?

Sim, desde que todos os centros de eventos estejam no mesmo espaço de nome.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período máximo de retenção para eventos?

O nível Standard do Event Hubs suporta atualmente um período máximo de retenção de sete dias. Os centros de eventos não se destinam a uma loja de dados permanente. Os períodos de retenção superiores a 24 horas destinam-se a cenários em que seja conveniente reproduzir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizagem automática nos dados existentes. Se precisar de retenção de mensagens para além de sete dias, permitindo que os Hubs de [Eventos Capture](event-hubs-capture-overview.md) no seu centro de eventos puxe os dados do seu centro de eventos para a conta de Armazenamento ou a conta azure Data Lake Service à sua escolha. Ativar a Captura incorre numa carga baseada nas suas unidades de entrada adquiridas.

Pode configurar o período de retenção dos dados capturados na sua conta de armazenamento. A funcionalidade de **gestão** do ciclo de vida do Armazenamento Azure oferece uma política rica e baseada em regras para fins gerais v2 e contas de armazenamento blob. Utilize a política para transitar os seus dados para os níveis de acesso adequados ou expirar no final do ciclo de vida dos dados. Para mais informações, consulte Gerir o ciclo de [vida de armazenamento de Blob Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como posso monitorizar os meus Centros de Eventos?
Os Centros de Eventos emitem métricas exaustivas que fornecem o estado dos seus recursos ao [Monitor Azure.](../azure-monitor/overview.md) Também lhe permitem avaliar a saúde geral do serviço Event Hubs não só ao nível do espaço de nome, mas também ao nível da entidade. Saiba qual é a monitorização oferecida para [o Azure Event Hubs.](event-hubs-metrics-azure-monitor.md)

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso abrir na firewall? 
Pode utilizar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Consulte a tabela seguinte para as portas de saída que precisa de abrir para utilizar estes protocolos para comunicar com o Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [o guia de protocolo amqp](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Ver [Use Event Hubs a partir de aplicações kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Que endereços IP preciso para whitelist?
Para encontrar os endereços IP certos para a lista branca para as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido `Non-authoritative answer` em . A única altura em que mudaria é se restaurar o espaço de nome num cluster diferente.

Se utilizar a redundância da zona para o seu espaço de nome, precisa de fazer alguns passos adicionais: 

1. Primeiro, verificas o espaço do nome.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note o nome na secção de resposta não **autorizada,** que se encontra num dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Executar nslookup para cada um com sufixos sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade, 

### <a name="where-can-i-find-client-ip-sending-or-receiving-msgs-to-my-namespace"></a>Onde posso encontrar ip cliente enviando ou recebendo msgs para o meu espaço de nome?
Em primeiro lugar, ative a [filtragem ip](event-hubs-ip-filtering.md) no espaço de nome. 

Em seguida, ative os registos de diagnóstico para [eventos de ligação virtual](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) de Rede De Event Hubs seguindo instruções nos [registos de diagnóstico Enable](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Verá o endereço IP para o qual a ligação é negada.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="apache-kafka-integration"></a>Integração Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como integro a minha aplicação kafka existente com centros de eventos?
O Event Hubs fornece um ponto final de Kafka que pode ser usado pelas suas aplicações baseadas em Apache Kafka existentes. Uma mudança de configuração é tudo o que é necessário para ter a experiência PaaS Kafka. Fornece uma alternativa para gerir o seu próprio aglomerado kafka. O Event Hubs suporta as versões de clientes Apache Kafka 1.0 e mais recentes e trabalha com as suas aplicações, ferramentas e quadros kafka existentes. Para mais informações, consulte O Repo do [Event Hubs para Kafka.](https://github.com/Azure/azure-event-hubs-for-kafka)

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Que mudanças de configuração têm de ser feitas para que a minha aplicação existente fale com os Centros de Eventos?
Para se ligar a um centro de eventos, terá de atualizar os configs do cliente Kafka. É feito criando um espaço de nome de Event Hubs e obtendo a cadeia de [ligação](event-hubs-get-connection-string.md). Mude as botastrap.servers para indicar o Event Hubs FQDN e a porta para 9093. Atualize o sasl.jaas.config para direcionar o cliente Kafka para o seu ponto final do Event Hubs (que é a cadeia de ligação que obteve), com a autenticação correta, como mostrado abaixo:

bootstrap.servers={YOUR. EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR. EVENTHUBS. A LIGAÇÃO. STRING}";

Exemplo:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.net. SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Nota: Se sasl.jaas.config não for uma configuração suportada na sua estrutura, encontre as configurações que são usadas para definir o nome de utilizador e a palavra-passe SASL e utilizá-las. Detete o nome de utilizador para $ConnectionString e a palavra-passe para a sua linha de ligação Event Hubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Qual é o tamanho da mensagem/evento para Os Centros de Eventos?
O tamanho máximo da mensagem permitido para Os Centros de Eventos é de 1 MB.

## <a name="throughput-units"></a>Unidades de débito

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produção de Centros de Eventos?
A entrada em Event Hubs define a quantidade de dados em mega bytes ou o número (em milhares) de eventos de 1-KB que ingressam e se esgresatravés dos Centros de Eventos. Esta entrada é medida em unidades de entrada (TUs). Compre TUs antes de começar a utilizar o serviço De eventos Hubs. Pode selecionar explicitamente as TUs do Event Hubs, utilizando modelos de gestor de recursos de portal ou de eventos hubs. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>As unidades de produção aplicam-se a todos os centros de eventos num espaço de nome?
Sim, as unidades de produção (TUs) aplicam-se a todos os centros de eventos num espaço de nome de Event Hubs. Significa que você compra TUs ao nível do espaço de nome e é partilhado entre os centros de eventos sob esse espaço de nome. Cada TU dá o nome ao espaço de nome para as seguintes capacidades:

- Até 1 MB por segundo de eventos de ingresso (eventos enviados para um centro de eventos), mas não mais de 1000 eventos de ingresso, operações de gestão ou chamadas de Controlo da API por segundo.
- Até 2 MB por segundo de eventos de egress (eventos consumidos a partir de um centro de eventos), mas não mais de 4096 eventos de egress.
- Até 84 GB de armazenamento de eventos (o suficiente para o período de retenção padrão 24 horas).

### <a name="how-are-throughput-units-billed"></a>Como são cobradas unidades de entrada?
As unidades de entrada (TUs) são faturadas de hora em hora. A faturação baseia-se no número máximo de unidades selecionadas durante a hora dada. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar o uso das minhas unidades de entrada?
Pode começar tão baixo como uma unidade de produção (TU) e ligar [automaticamente](event-hubs-auto-inflate.md). A função de auto-insuflar permite-lhe crescer as suas TUs à medida que o seu tráfego/carga útil aumenta. Também pode estabelecer um limite superior no número de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona a funcionalidade de auto-inflação dos Centros de Eventos?
A função de insuflar automaticamente permite-lhe aumentar as suas unidades de produção (TUs). Significa que pode começar por comprar TUs baixos e inflação automática aumenta as suas TiCà à medida que a sua entrada aumenta. Dá-lhe uma opção rentável e controlo total do número de TUs para gerir. Esta funcionalidade é apenas uma funcionalidade de **escala,** e pode controlar completamente a escala para baixo do número de TUs atualizando-a. 

Pode querer começar com unidades de baixa suposição (TUs), por exemplo, 2 TUs. Se prever que o seu tráfego pode crescer para 15 TUs, ligue a função de insuflado automático no seu espaço de nome e defina o limite máximo para 15 TUs. Agora pode cultivar automaticamente as suas Tiques à medida que o seu tráfego cresce.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Há algum custo associado quando ligo a função de auto-inflação?
Não há **nenhum custo** associado a esta funcionalidade. 

### <a name="how-are-throughput-limits-enforced"></a>Como são aplicados os limites de entrada?
Se a entrada total de **ingressos** ou a taxa total de eventos de ingresso em todos os centros de eventos num espaço de nome exceder as licenças unitárias de produção agregados, os remetentes são estrangulados e recebem erros que indicam que a quota de ingresso foi ultrapassada.

Se a produção total de **saída** ou a taxa total de egress do evento em todos os centros de eventos num espaço de nome exceder as licenças unitárias de produção agregadas, os recetores são estrangulados, mas não são gerados erros de estrangulamento. 

As quotas de ingresso e de saída são aplicadas separadamente, de modo que nenhum remetente pode fazer com que o consumo do evento abrande, nem um recetor pode impedir que os eventos sejam enviados para um centro de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Existe um limite no número de unidades de entrada (Ti) que podem ser reservadas/selecionadas?
Numa oferta multi-arrendatária, as unidades de entrada podem crescer até 40 TUs (pode selecionar até 20 TUs no portal, e levantar um bilhete de apoio para elevar para 40 TUs no mesmo espaço de nome). Além de 40 TUs, o Event Hubs oferece o modelo baseado em recursos/capacidade chamado **clusters dedicados**do Event Hubs . Os clusters dedicados são vendidos em Unidades de Capacidade (CUs).

## <a name="dedicated-clusters"></a>Aglomerados dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>O que são os clusters dos Hubs de Eventos Dedicados?
Os clusters dedicados do Event Hubs oferecem implementações de inquilinos únicos para clientes com requisitos mais exigentes. Esta oferta constrói um cluster baseado na capacidade que não está ligado por unidades de entrada. Significa que pode usar o cluster para ingerir e transmitir os seus dados conforme ditado pelo CPU e pelo uso da memória do cluster. Para mais informações, consulte [Clusters dedicados ao Event Hubs.](event-hubs-dedicated-overview.md)

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>O que é que uma única unidade de capacidade me permite alcançar?
Para um cluster dedicado, o quanto pode ingerir e transmitir depende de vários fatores como os seus produtores, consumidores, a taxa a que está a ingerir e processar, e muito mais. 

A tabela seguinte mostra os resultados de referência que alcançámos durante os nossos testes:

| Forma de carga útil | Recetores | Largura de banda de ingresso| Mensagens de ingresso | Largura de banda de Egress | Mensagens de egress | Tosta total | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400k mensagens/seg | 800 MB/seg | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6k mensagens/seg | 1.33 GB/seg | 133k mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34k mensagens / seg | 1,05 GB/seg | 34k mensagens/seg | 1000 TUs | 250 TUs |

Nos ensaios, foram utilizados os seguintes critérios:

- Foi utilizado um cluster dedicado de Hubs de Eventos com quatro unidades de capacidade (CUs). 
- O centro de eventos usado para ingestão tinha 200 divisórias. 
- Os dados que foram ingeridos foram recebidos por duas aplicações recetoras que receberam de todas as divisórias.

Os resultados dão-lhe uma ideia do que pode ser alcançado com um cluster dedicado de Hubs de Eventos. Além disso, um cluster dedicado vem com a Captura de Hubs de Eventos habilitada para os seus cenários de microlote e retenção a longo prazo.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como posso criar um cluster dedicado aos Centros de Eventos?
Cria um cluster dedicado ao Event Hubs, submetendo um pedido de apoio de aumento de [quota](https://portal.azure.com/#create/Microsoft.Support) ou contactando a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com) Normalmente demora cerca de duas semanas para que o cluster seja implantado e entregue para ser usado por si. Este processo é temporário até que um autosserviço completo seja disponibilizado através do portal Azure.

## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>Quantas partições são necessárias?
O número de partições é especificado durante a criação e deve ser entre 2 e 32. A contagem de divisórias não é mutável, por isso deve considerar a escala a longo prazo ao definir a contagem de divisórias. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Para obter mais informações sobre divisórias, consulte [Divisórias](event-hubs-features.md#partitions).

Talvez queira defini-lo como o valor mais alto possível, que é 32, no momento da criação. Lembre-se que ter mais do que uma partição resultará em eventos enviados para várias divisórias sem reter a ordem, a menos que configure os remetentes para enviar apenas para uma única divisória das 32 deixando as restantes 31 divisórias redundantes. No primeiro caso, terá que ler eventos em todas as 32 divisórias. Neste último caso, não há custos adicionais óbvios para além da configuração extra que você tem que fazer no Anfitrião do Processador de Eventos.

O Event Hubs foi concebido para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de utilização, a definição padrão de quatro divisórias é suficiente. Se procura escalar o processamento do seu evento, talvez deseja considerar adicionar divisórias adicionais. Não existe um limite específico de entrada numa divisória, no entanto a entrada agregada no seu espaço de nome é limitada pelo número de unidades de produção. À medida que aumenta o número de unidades de entrada no seu espaço de nome, pode querer divisórias adicionais para permitir que os leitores simultâneos atinjam a sua própria potência máxima.

No entanto, se tiver um modelo em que a sua aplicação tenha uma afinidade com uma determinada partição, o aumento do número de divisórias pode não ser benéfico para si. Para mais informações, consulte [disponibilidade e consistência.](event-hubs-availability-and-consistency.md)

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?

Para obter informações completas sobre os preços dos Hubs de Eventos, consulte os detalhes de preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há alguma taxa para reter eventos de Event Hubs por mais de 24 horas?

O nível Standard Do Evento Hubs permite períodos de retenção de mensagens superiores a 24 horas, por um período máximo de sete dias. Se o tamanho do número total de eventos armazenados exceder o subsídio de armazenamento para o número de unidades de produção selecionadas (84 GB por unidade de produção), o tamanho que excede o subsídio é cobrado à taxa de armazenamento de Azure Blob publicada. O subsídio de armazenamento em cada unidade de entrada cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de entrada seja utilizada até ao subsídio máximo de ingresso.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é calculado e cobrado o tamanho do armazenamento do Event Hubs?

O tamanho total de todos os eventos armazenados, incluindo qualquer sobrecarga interna para cabeçalhos de eventos ou em estruturas de armazenamento de discos em todos os centros de eventos, é medido ao longo do dia. No final do dia, o tamanho máximo do armazenamento é calculado. O subsídio de armazenamento diário é calculado com base no número mínimo de unidades de entrada selecionadas durante o dia (cada unidade de entrada fornece um subsídio de 84 GB). Se o tamanho total exceder o subsídio de armazenamento diário calculado, o armazenamento em excesso é faturado utilizando taxas de armazenamento De Blob Azure (à taxa de **armazenamento localmente redundante).**

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados os eventos de ingressos do Event Hubs?

Cada evento enviado para um centro de eventos conta como uma mensagem de faturação. Um *evento de ingresso* é definido como uma unidade de dados que é inferior ou igual a 64 KB. Qualquer evento inferior ou igual a 64 KB em tamanho é considerado um evento de faturação. Se o evento for superior a 64 KB, o número de eventos faturados é calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o centro do evento é anunciado como um evento, mas uma mensagem de 96 KB enviada para o centro do evento é anunciada como dois eventos.

Os eventos consumidos a partir de um centro de eventos, bem como operações de gestão e chamadas de controlo, tais como postos de controlo, não são contabilizados como eventos de ingresso faturados, mas acumulam-se até ao subsídio unitário de produção.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>As tarifas de ligação intermediadas aplicam-se aos Centros de Eventos?

Os encargos de ligação só se aplicam quando o protocolo AMQP é utilizado. Não existem taxas de ligação para o envio de eventos usando HTTP, independentemente do número de sistemas de envio ou dispositivos. Se planeia utilizar a AMQP (por exemplo, para obter um streaming de eventos mais eficiente ou para permitir uma comunicação bidirecional em cenários de comando e controlo IoT), consulte a página de informação de preços do [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas ligações estão incluídas em cada nível de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é que os Centros de Eventos captam?

A captura é ativada quando qualquer centro de eventos no espaço de nome tem a opção Captura ativada. O Event Hubs Capture é faturado de hora em hora por unidade de produção adquirida. À medida que a contagem da unidade de produção é aumentada ou diminuída, a faturação de captura de Hubs de Eventos reflete estas alterações em incrementos de hora inteira. Para mais informações sobre a faturação de Hubs de Eventos, consulte as informações sobre preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Será que sou cobrado pela conta de armazenamento que seleciono para a Captura de Centros de Eventos?

A captura utiliza uma conta de armazenamento que fornece quando ativada num centro de eventos. Como é a sua conta de armazenamento, quaisquer alterações para esta configuração são faturadas para a sua subscrição Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Há alguma quota associada aos Centros de Eventos?

Para obter uma lista de todas as quotas do Event Hubs, consulte [quotas.](event-hubs-quotas.md)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não sou capaz de criar um espaço de nome depois de aapagar de outra subscrição? 
Quando eliminar um espaço de nome de uma subscrição, aguarde 4 horas antes de o recriar com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos Centros de Eventos e as suas ações sugeridas?

Para obter uma lista de possíveis exceções do Event Hubs, consulte a visão geral das [exceções.](event-hubs-messaging-exceptions.md)

### <a name="diagnostic-logs"></a>Registos de diagnósticos

O Event Hubs suporta dois tipos de [registos](event-hubs-diagnostic-logs.md) de diagnóstico - registos de erros de captura e registos operacionais - ambos representados em json e podem ser ligados através do portal Azure.

### <a name="support-and-sla"></a>Apoio e SLA

O suporte técnico para Os Hubs de Eventos está disponível através do [Microsoft Q&Uma página de perguntas para o Azure Service Bus](https://docs.microsoft.com/answers/topics/azure-service-bus.html). O apoio à gestão de faturação e subscrição não é fornecido a qualquer custo.

Para saber mais sobre o nosso SLA, consulte a página de Acordos de [Nível de Serviço.](https://azure.microsoft.com/support/legal/sla/)

## <a name="next-steps"></a>Próximos passos

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Hubs de eventos auto-inflação](event-hubs-auto-inflate.md)
