---
title: Perguntas frequentes - Azure Event Hubs Microsoft Docs
description: Este artigo fornece uma lista de perguntas frequentes (FAQ) para Azure Event Hubs e suas respostas.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 511706e0de2737feb259c0ff9529373ab8b6d026
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495229"
---
# <a name="event-hubs-frequently-asked-questions"></a>Os Centros de Eventos fazem perguntas frequentes

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um espaço de nomes de Event Hubs?
Um espaço de nome é um recipiente de deteção para tópicos de evento/kafka. Dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Um espaço de nome serve como um recipiente de aplicação que pode alojar vários tópicos de Evento Hub/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando é que eu crio um novo espaço de nome vs. usar um espaço de nome existente?
As alocações de capacidade[(unidades de produção (TUs)](#throughput-units)são faturadas ao nível do espaço de identificação. Um espaço de nome também está associado a uma região.

Pode querer criar um novo espaço de nome em vez de utilizar um existente num dos seguintes cenários: 

- Precisa de um Centro de Eventos associado a uma nova região.
- Precisa de um Centro de Eventos associado a uma subscrição diferente.
- Você precisa de um Event Hub com uma alocação de capacidade distinta (isto é, a necessidade de capacidade para o espaço de nome com o centro de eventos adicionado ultrapassaria o limiar de 40 TU e você não quer ir para o cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre os níveis básicos e standard do Event Hubs?

O nível standard de Azure Event Hubs fornece funcionalidades para além do que está disponível no nível Básico. As seguintes funcionalidades estão incluídas com a Standard:

* Retenção de eventos mais longos
* Ligações intermediadas adicionais, com uma taxa de sobrecarga para mais do que o número incluído
* Mais do que um único [grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Recolha](event-hubs-capture-overview.md)
* [Integração kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre os níveis de preços, incluindo os Event Hubs Dedicados, consulte os detalhes dos preços dos [Centros de Eventos.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="where-is-azure-event-hubs-available"></a>Onde está disponível o Azure Event Hubs?

O Azure Event Hubs está disponível em todas as regiões de Azure apoiadas. Para obter uma lista, visite a página das [regiões de Azure.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso utilizar uma única ligação AMQP para enviar e receber de vários centros de eventos?

Sim, desde que todos os centros de eventos estejam no mesmo espaço de nome.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período máximo de retenção para eventos?

O nível Standard do Event Hubs suporta atualmente um período máximo de retenção de sete dias. Os centros de eventos não se destinam a uma loja de dados permanente. Períodos de retenção superiores a 24 horas destinam-se a cenários em que é conveniente reproduzir um fluxo de eventos para os mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizagem automática sobre os dados existentes. Se necessitar de retenção de mensagens para além de sete dias, permitir que o [Event Hubs Capture](event-hubs-capture-overview.md) no seu centro de eventos puxe os dados do seu centro de eventos para a conta de armazenamento ou para a conta Azure Data Lake Service à sua escolha. Ativar a Captura incorre numa carga baseada nas suas unidades de produção adquiridas.

Pode configurar o período de retenção para os dados capturados na sua conta de armazenamento. A funcionalidade de **gestão** do ciclo de vida do Azure Storage oferece uma política rica e baseada em regras para fins gerais v2 e contas de armazenamento de bolhas. Utilize a política para transitar os seus dados para os níveis de acesso apropriados ou expire no final do ciclo de vida dos dados. Para obter mais informações, consulte [gerir o ciclo de vida de armazenamento Azure Blob](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como monitorizo os meus Centros de Eventos?
O Event Hubs emite métricas exaustivas que fornecem o estado dos seus recursos ao [Azure Monitor.](../azure-monitor/overview.md) Também permitem avaliar a saúde geral do serviço Event Hubs não só ao nível do espaço de nome, mas também ao nível da entidade. Saiba qual é a monitorização oferecida para [o Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Onde é que o Azure Event Hubs armazena os dados dos clientes?
O Azure Event Hubs armazena dados do cliente. Estes dados são automaticamente armazenados pelos Event Hubs numa única região, pelo que este serviço satisfaz automaticamente os requisitos de residência de dados da região, incluindo os especificados no [Trust Center.](https://azuredatacentermap.azurewebsites.net/)

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso para abrir na firewall? 
Pode utilizar os seguintes protocolos com a Azure Service Bus para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Consulte a tabela seguinte para as portas de saída que precisa de abrir para utilizar estes protocolos para comunicar com os Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [guia de protocolo amQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Ver [Use Event Hubs a partir de aplicações kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Que endereços IP preciso de permitir?
Para encontrar os endereços IP certos para adicionar à lista permitida para as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer` . 

Se utilizar a **redundância** da zona para o seu espaço de nome, tem de fazer alguns passos adicionais: 

1. Primeiro, corres nslookup no espaço de nomes.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note o nome na secção **de resposta não autorizada,** que se encontra num dos seguintes formatos: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Executar nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade, 

    > [!NOTE]
    > O endereço IP devolvido pelo `nslookup` comando não é um endereço IP estático. No entanto, permanece constante até que a implementação subjacente seja eliminada ou transferida para um cluster diferente.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Onde posso encontrar o IP do cliente a enviar ou a receber mensagens para o meu espaço de nome?
Em primeiro lugar, ative [a filtragem IP](event-hubs-ip-filtering.md) no espaço de nomes. 

Em seguida, Ative os registos de diagnóstico para [eventos de ligação de rede virtual do Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) seguindo instruções nos [registos de diagnóstico Ativar](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Verá o endereço IP para o qual a ligação é negada.

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

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como posso integrar a minha aplicação kafka existente com os Centros de Eventos?
O Event Hubs fornece um ponto final kafka que pode ser usado pelas aplicações existentes baseadas em Apache Kafka. Uma mudança de configuração é tudo o que é necessário para ter a experiência PaaS Kafka. Ele fornece uma alternativa para gerir o seu próprio cluster Kafka. O Event Hubs suporta o Apache Kafka 1.0 e as versões de clientes mais recentes e trabalha com as aplicações, ferramentas e estruturas da Kafka existentes. Para mais informações, consulte [os Centros de Eventos para Kafka repo.](https://github.com/Azure/azure-event-hubs-for-kafka)

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Que alterações de configuração precisam de ser feitas para que a minha aplicação existente fale com os Centros de Eventos?
Para se conectar a um centro de eventos, você precisará atualizar os configs do cliente Kafka. É feito criando um espaço de nomes de Event Hubs e obtendo a [cadeia de conexão.](event-hubs-get-connection-string.md) Mude o bootstrap.servers para apontar o Event Hubs FQDN e a porta para 9093. Atualize o sasl.jaas.config para direcionar o cliente Kafka para o seu ponto final do Event Hubs (que é a cadeia de ligação que obteve), com autenticação correta como mostrado abaixo:

bootstrap.servers={YOUR. OS EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR. OS EVENTHUBS. LIGAÇÃO. STRING}";

Exemplo:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.Plain.PlainLoginModule requirou username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

Nota: Se sasl.jaas.config não for uma configuração suportada no seu quadro, encontre as configurações que são usadas para definir o nome de utilizador e palavra-passe SASL e use-as em vez disso. Desa estama de utilizador para $ConnectionString e a palavra-passe na sua cadeia de ligação 'Centro de Eventos'.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Qual é o tamanho da mensagem/evento para os Centros de Eventos?
O tamanho máximo de mensagem permitido para Os Centros de Eventos é de 1 MB.

## <a name="throughput-units"></a>Unidades de débito

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produção do Event Hubs?
A produção em Event Hubs define a quantidade de dados em mega bytes ou o número (em milhares) de eventos de 1-KB que entra e entra através de Event Hubs. Esta produção é medida em unidades de produção (TUs). Compre TUs antes de começar a usar o serviço Event Hubs. Pode selecionar explicitamente as TUs do Event Hubs, utilizando modelos de gestores de recursos do portal ou de centros de eventos. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>As unidades de produção aplicam-se a todos os centros de eventos num espaço de nome?
Sim, as unidades de produção (TUs) aplicam-se a todos os centros de eventos num espaço de nomes do Event Hubs. Significa que você compra TUs ao nível do espaço de nome e são partilhados entre os centros de eventos sob esse espaço de nome. Cada TU dá direito ao espaço de nome às seguintes capacidades:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um centro de eventos), mas não mais de 1000 eventos ingress, operações de gestão ou controlar chamadas de API por segundo.
- Até 2 MB por segundo de eventos de egress (eventos consumidos a partir de um centro de eventos), mas não mais de 4096 eventos de esrupção.
- Até 84 GB de armazenamento de eventos (o suficiente para o período de retenção padrão de 24 horas).

### <a name="how-are-throughput-units-billed"></a>Como são cobradas as unidades de produção?
As unidades de produção (TUs) são faturadas de hora a hora. A faturação baseia-se no número máximo de unidades selecionadas durante a hora dada. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar o uso das minhas unidades de produção?
Pode iniciar tão baixo como uma unidade de produção (TU) e ligar [automaticamente](event-hubs-auto-inflate.md). A função de insuflado automático permite-lhe aumentar as suas TUs à medida que o seu tráfego/carga útil aumenta. Também pode definir um limite superior no número de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona a função de auto-insuflado dos Centros de Eventos?
A função de insuflado automático permite-lhe escalar as suas unidades de produção (TUs). Significa que pode começar por comprar TUs baixas e aumentar as escalas automáticas dos seus TUs à medida que a sua entrada aumenta. Dá-lhe uma opção rentável e controlo total do número de TUs para gerir. Esta funcionalidade é apenas uma funcionalidade **de escala,** e é possível controlar completamente a escala do número de TUs atualizando-a. 

Pode querer começar com unidades de produção baixa (TUs), por exemplo, 2 TUs. Se previr que o seu tráfego pode aumentar para 15 TUs, ligue a função de insuflado automático no seu espaço de nome e deseja o limite máximo para 15 TUs. Agora pode cultivar os seus TUs automaticamente à medida que o seu tráfego aumenta.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Há algum custo associado quando eu ligar a função de auto-insuflação?
Não **há nenhum custo** associado a esta funcionalidade. 

### <a name="how-are-throughput-limits-enforced"></a>Como são aplicados os limites de produção?
Se a produção total **de entradas** ou a taxa total de eventos de entrada em todos os centros de eventos num espaço de nome exceder os subsídios agregados de unidade de produção, os remetentes são estrangulados e recebem erros que indicam que a quota de entrada foi excedida.

Se o rendimento total da **saída** ou a taxa total de saída de eventos em todos os centros de eventos num espaço de nome exceder as licenças de unidade de produção agregada, os recetores são estrangulados, mas não são gerados erros de estrangulamento. 

As quotas de entrada e saída são aplicadas separadamente, de modo que nenhum remetente pode fazer com que o consumo de eventos abrande, nem um recetor pode impedir que os eventos sejam enviados para um centro de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Existe um limite para o número de unidades de produção (TUs) que podem ser reservadas/selecionadas?
Numa oferta multi-arrendatário, as unidades de produção podem crescer até 40 TUs (você pode selecionar até 20 TUs no portal, e levantar um bilhete de apoio para elevá-lo a 40 TUs no mesmo espaço de nome). Além de 40 TUs, o Event Hubs oferece o modelo baseado em recursos/capacidade chamado **clusters dedicados ao Event Hubs.** Os clusters dedicados são vendidos em Unidades de Capacidade (CUs).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>O que são os clusters dos Hubs de Eventos Dedicados?
Clusters dedicados ao Evento Hubs oferecem implantações de inquilinos únicos para clientes com requisitos mais exigentes. Esta oferta constrói um cluster baseado na capacidade que não está ligado por unidades de produção. Significa que pode usar o cluster para ingerir e transmitir os seus dados como ditado pelo CPU e pelo uso da memória do cluster. Para mais informações, consulte [clusters dedicados do Event Hubs.](event-hubs-dedicated-overview.md)

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como posso criar um cluster dedicado ao Event Hubs?
Para obter instruções passo a passo e mais informações sobre a criação de um cluster dedicado ao Event Hubs, consulte o [Quickstart: Crie um cluster dedicado de Centros de Eventos utilizando o portal Azure.](event-hubs-dedicated-cluster-create-portal.md) 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>Quantas partições são necessárias?
O número de divisórias é especificado na criação e deve estar entre 1 e 32. A contagem de divisórias não é mutável, por isso deve considerar a escala a longo prazo ao definir a contagem de divisórias. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Para obter mais informações sobre divisórias, consulte [As Partições.](event-hubs-features.md#partitions)

Talvez queira defini-lo como o valor mais alto possível, que é 32, no momento da criação. Lembre-se que ter mais de uma partição resultará em eventos enviados para múltiplas divisórias sem reter a ordem, a menos que você configuure os remetentes para enviar apenas para uma única partição das 32 deixando as restantes 31 divisórias redundantes. No primeiro caso, terás de ler eventos em todas as 32 divisórias. Neste último caso, não há um custo adicional óbvio para além da configuração extra que tem de fazer no Host do Processador de Eventos.

O Event Hubs foi concebido para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de utilização, a definição padrão de quatro divisórias é suficiente. Se procura escalar o processamento do seu evento, talvez deva considerar adicionar divisórias adicionais. Não há um limite específico de produção numa partição, no entanto, a produção agregada no seu espaço de nome é limitada pelo número de unidades de produção. À medida que aumenta o número de unidades de produção no seu espaço de nome, pode querer divisórias adicionais para permitir que os leitores simultâneos atinjam o seu próprio rendimento máximo.

No entanto, se tiver um modelo em que a sua aplicação tenha uma afinidade com uma determinada partição, aumentar o número de divisórias pode não ser benéfico para si. Para mais informações, consulte [disponibilidade e consistência.](event-hubs-availability-and-consistency.md)

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?

Para obter informações completas sobre os preços dos Event Hubs, consulte os detalhes de preços do [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há alguma acusação por manter eventos de Event Hubs por mais de 24 horas?

O nível Standard Event Hubs permite períodos de retenção de mensagens superiores a 24 horas, durante um máximo de sete dias. Se a dimensão do número total de eventos armazenados exceder o subsídio de armazenamento para o número de unidades de produção selecionadas (84 GB por unidade de produção), o tamanho que excede o subsídio é cobrado na taxa de armazenamento Azure Blob publicada. O subsídio de armazenagem em cada unidade de produção cobre todos os custos de armazenagem para períodos de retenção de 24 horas (o incumprimento) mesmo que a unidade de produção seja utilizada até ao subsídio máximo de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é calculado e cobrado o tamanho de armazenamento do Event Hubs?

O tamanho total de todos os eventos armazenados, incluindo qualquer sobrecarga interna para cabeçalhos de eventos ou em estruturas de armazenamento de discos em todos os centros de eventos, é medido ao longo do dia. No final do dia, o tamanho máximo de armazenamento é calculado. O subsídio de armazenamento diário é calculado com base no número mínimo de unidades de produção selecionadas durante o dia (cada unidade de produção fornece um subsídio de 84 GB). Se o tamanho total exceder o subsídio de armazenamento diário calculado, o armazenamento em excesso é faturado usando as taxas de armazenamento Azure Blob (à taxa **de armazenamento localmente redundante).**

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados os eventos de ingresss do Event Hubs?

Cada evento enviado para um centro de eventos conta como uma mensagem faturada. Um *evento de entrada* é definido como uma unidade de dados que é inferior ou igual a 64 KB. Qualquer evento com menos ou igual a 64 KB de tamanho é considerado um evento faturalável. Se o evento for superior a 64 KB, o número de eventos faturantes é calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8-KB enviado para o centro de eventos é anunciado como um evento, mas uma mensagem de 96 KB enviada para o centro de eventos é faturada como dois eventos.

Os eventos consumidos a partir de um centro de eventos, bem como operações de gestão e chamadas de controlo, como postos de controlo, não são contabilizados como eventos de entrada faturada, mas acumulam-se até ao subsídio de unidade de produção.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>As taxas de ligação intermediadas aplicam-se aos Centros de Eventos?

As taxas de ligação só se aplicam quando o protocolo AMQP é utilizado. Não existem taxas de ligação para envio de eventos utilizando HTTP, independentemente do número de sistemas ou dispositivos de envio. Se planeia utilizar amQP (por exemplo, para obter um streaming de eventos mais eficiente ou para permitir a comunicação bidis em cenários de comando e controlo IoT), consulte a página de informações de preços do [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas ligações estão incluídas em cada nível de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é que o Event Hubs Capture é faturado?

A captura é ativada quando qualquer centro de eventos no espaço de nomes tem a opção Captura ativada. Event Hubs Capture é faturado de hora a hora por unidade de produção comprada. À medida que a contagem de unidades de produção é aumentada ou diminuída, a faturação de captura de Centros de Eventos reflete estas mudanças em incrementos de horas inteiras. Para obter mais informações sobre a faturação do Event Hubs Capture, consulte [as informações sobre preços do Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Será que sou cobrado para a conta de armazenamento que seleciono para a Captura de Centros de Eventos?

A Captura utiliza uma conta de armazenamento que fornece quando ativada num centro de eventos. Como é a sua conta de armazenamento, quaisquer alterações para esta configuração são faturadas na sua subscrição Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existem quotas associadas aos Centros de Eventos?

Para obter uma lista de todas as quotas do Event Hubs, consulte [as quotas.](event-hubs-quotas.md)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não posso criar um espaço de nome depois de o eliminar de outra subscrição? 
Quando eliminar um espaço de nome de uma subscrição, aguarde 4 horas antes de recriá-lo com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos Event Hubs e as suas ações sugeridas?

Para obter uma lista de possíveis exceções do Event Hubs, consulte [a visão geral das exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Registos de diagnósticos

O Event Hubs suporta dois tipos de [registos](event-hubs-diagnostic-logs.md) de diagnóstico - Captar registos de erros e registos operacionais - ambos representados em json e podem ser ligados através do portal Azure.

### <a name="support-and-sla"></a>Apoio e SLA

O suporte técnico para o Event Hubs está disponível através da [página de perguntas do Microsoft Q&A para o Azure Service Bus](/answers/topics/azure-service-bus.html). O suporte de gestão de faturação e subscrição é fornecido sem custos.

Para saber mais sobre o nosso SLA, consulte a página [De Acordos de Nível de Serviço.](https://azure.microsoft.com/support/legal/sla/)

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Como posso direcionar uma versão específica do Azure Storage SDK ao usar o Azure Blob Storage como uma loja de checkpoint?
Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blog Storage como uma loja de checkpoint, verifique a [versão API suportada do Azure Storage para a sua construção do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão no seu código. 

Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão API de armazenamento específica, consulte as seguintes amostras para C#, Java, Python e JavaScript/TypeScript.  

Para um exemplo sobre como direcionar uma versão API de armazenamento específica do seu código, consulte as seguintes amostras no GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python - [Sincronizado,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) [Assíncronos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](./event-hubs-about.md)
* [Criar um Centro de Eventos](event-hubs-create.md)
* [Hubs de eventos Auto-insuflado](event-hubs-auto-inflate.md)
