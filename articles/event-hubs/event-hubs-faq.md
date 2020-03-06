---
title: Perguntas mais frequentes sobre - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece uma lista de perguntas mais frequentes (FAQ) para os Hubs de eventos do Azure e suas respostas.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 3b46c574ea47622ec97e70c0d2f2cdc3aa54ec0d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393655"
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas mais frequentes sobre os Hubs de eventos

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um espaço de nomes de Hubs de eventos?
Um espaço de nomes é um contentor de âmbito para tópicos do Event Hub/Kafka. Dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Um espaço de nomes serve como um contêiner de aplicativo que pode alojar vários tópicos do Event Hub/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando é que crio um novo espaço de nome vs. usar um espaço de nome existente?
As dotações de capacidade[(unidades de entrada (UsT)](#throughput-units)são faturadas ao nível do espaço de nome. Um espaço de nome também está associado a uma região.

Pode querer criar um novo espaço de nome em vez de utilizar um existente num dos seguintes cenários: 

- Precisa de um Centro de Eventos associado a uma nova região.
- Você precisa de um Hub de evento associado a uma subscrição diferente.
- Você precisa de um Hub de Evento sita com uma alocação de capacidade distinta (isto é, a necessidade de capacidade para o espaço de nome com o hub de evento adicionado excederia o limiar de 40 TU e você não quer ir para o cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>O que é a diferença entre os escalões Standard e Hubs de eventos básico?

O escalão Standard dos Hubs de eventos do Azure fornece funcionalidades para além das que estão disponíveis no escalão básico. As seguintes funcionalidades estão incluídas com o padrão:

* Retenção de eventos maior
* Ligações mediadas adicionais, com uma taxa de excesso para obter mais informações que o número incluído
* Mais do que um [único grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Captura](event-hubs-capture-overview.md)
* [Integração kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre os níveis de preços, incluindo os Hubs de Eventodedicados, consulte os detalhes de preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="where-is-azure-event-hubs-available"></a>Onde os Hubs de eventos do Azure está disponível?

Os Hubs de eventos do Azure está disponível em todas as regiões do Azure suportadas. Para obter uma lista, visite a página das [regiões de Azure.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Pode utilizar uma única ligação AMQP para enviar e receber vários dos hubs de eventos?

Sim, desde que todos os hubs de eventos estão no mesmo espaço de nomes.

### <a name="what-is-the-maximum-retention-period-for-events"></a>O que é o período de retenção máximo para eventos?

Escalão Standard dos Hubs de eventos, atualmente, suporta um período de retenção máximo de sete dias. Os centros de eventos não se destinam a uma loja de dados permanente. Os períodos de retenção superiores a 24 horas destinam-se a cenários em que seja conveniente reproduzir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizagem automática nos dados existentes. Se precisar de retenção de mensagens para além de sete dias, permitindo que os Hubs de [Eventos Capture](event-hubs-capture-overview.md) no seu centro de eventos puxe os dados do seu centro de eventos para a conta de Armazenamento ou a conta azure Data Lake Service à sua escolha. Habilitar a captura implica uma cobrança com base em unidades de débito adquiridas.

Pode configurar o período de retenção dos dados capturados na sua conta de armazenamento. A funcionalidade de **gestão** do ciclo de vida do Armazenamento Azure oferece uma política rica e baseada em regras para fins gerais v2 e contas de armazenamento blob. Utilize a política para transitar os seus dados para os níveis de acesso adequados ou expirar no final do ciclo de vida dos dados. Para mais informações, consulte Gerir o ciclo de [vida de armazenamento de Blob Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como posso monitorizar o meu Hubs de eventos?
Os Centros de Eventos emitem métricas exaustivas que fornecem o estado dos seus recursos ao [Monitor Azure.](../azure-monitor/overview.md) Eles também permitem avaliar o estado de funcionamento geral do serviço Hubs de eventos não apenas ao nível do espaço de nomes, mas também no nível de entidade. Saiba qual é a monitorização oferecida para [o Azure Event Hubs.](event-hubs-metrics-azure-monitor.md)

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso abrir na firewall? 
Pode utilizar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Protocolo avançado de fila de mensagens (AMQP)
- HTTP
- Apache Kafka

Consulte a tabela seguinte para as portas de saída que precisa de abrir para utilizar estes protocolos para comunicar com o Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [o guia de protocolo amqp](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Ver [Use Event Hubs a partir de aplicações kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Que endereços IP preciso para whitelist?
Para localizar os endereços IP corretos para a lista de permissões de suas conexões, siga estas etapas:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer`. A única altura em que mudaria é se restaurar o espaço de nome num cluster diferente.

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

## <a name="apache-kafka-integration"></a>Integração do Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como posso integrar o meu aplicativo existente do Kafka com os Hubs de eventos?
Os Hubs de eventos fornece um ponto de extremidade do Kafka que pode ser utilizado por seus aplicativos existentes do Apache Kafka com base. Uma alteração de configuração é tudo o que é necessária para ter a experiência de PaaS Kafka. Ele fornece uma alternativa à execução de seu próprio cluster do Kafka. Os Hubs de eventos suporta Apache Kafka 1.0 e as versões mais recentes do cliente e funciona com seus existente Kafka aplicativos, ferramentas e estruturas. Para mais informações, consulte O Repo do [Event Hubs para Kafka.](https://github.com/Azure/azure-event-hubs-for-kafka)

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>O que as alterações de configuração precisam ser feito para a minha aplicação existente comunicar com os Hubs de eventos?
Para ligar a um Hub de eventos habilitados no Kafka, terá de atualizar as configurações de cliente do Kafka. É feito criando um espaço de nome de Event Hubs e obtendo a cadeia de [ligação](event-hubs-get-connection-string.md). Altere o bootstrap.servers para apontar o FQDN de Hubs de eventos e a porta para 9093. Atualize o sasl.jaas.config para direcionar o cliente Kafka para o seu ponto final de Hubs de Eventos ativado por Kafka (que é a cadeia de ligação que obteve), com a autenticação correta, como mostrado abaixo:

bootstrap.Servers={your. EVENTHUBS. FQDN}: 9093 request.timeout.ms=60000 o security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule necessário o nome de utilizador = a palavra-passe de "$ConnectionString" = "{seu. EVENTHUBS. LIGAÇÃO. CADEIA DE CARACTERES} ";

Exemplo:

bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule necessário o nome de utilizador = "$ ConnectionString"password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Nota: Se sasl.jaas.config não for uma configuração suportada na sua estrutura, encontre as configurações que são usadas para definir o nome de utilizador e a palavra-passe SASL e utilizá-las. Defina o nome de utilizador para $ConnectionString e a palavra-passe para a cadeia de ligação dos Hubs de eventos.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>O que é o tamanho de mensagens/eventos para Hubs de eventos de Kafka-ativado?
O tamanho máximo da mensagem permitido para os Centros de Eventos ativados por Kafka é de 1 MB.

## <a name="throughput-units"></a>Unidades de débito

### <a name="what-are-event-hubs-throughput-units"></a>Quais são as unidades de débito dos Hubs de eventos?
Débito em Hubs de eventos define a quantidade de dados em megabytes de bytes ou o número (em milhares) de eventos de 1 KB, essa entrada e de saída através dos Hubs de eventos. Essa taxa de transferência é medida em unidades de débito (n). Comprar TUs antes de começar a utilizar o serviço de Hubs de eventos. Pode selecionar explicitamente TUs de Hubs de eventos utilizando o portal ou de modelos do Gestor de recursos de Hubs de eventos. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Unidades de débito são aplicáveis a todos os hubs de eventos num espaço de nomes?
Sim, o (n) de unidades de débito aplicam-se a todos os hubs de eventos num espaço de nomes de Hubs de eventos. Significa que compra TUs ao nível do espaço de nomes e compartilhados entre os hubs de eventos nesse espaço de nomes. Cada TU autoriza o espaço de nomes as seguintes capacidades:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um hub de eventos), mas não mais de 1000 eventos de entrada, operações de gestão ou controlo de chamadas de API por segundo.
- Até 2 MB por segundo de eventos de saída (eventos consumidos a partir de um hub de eventos), mas não mais de 4096 eventos de saída.
- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção de 24 horas predefinido).

### <a name="how-are-throughput-units-billed"></a>Como são cobradas as unidades de débito?
Unidades de débito (n) são cobradas à hora. A faturação baseia-se no número máximo de unidades que foi selecionado durante a hora especificada. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar a utilização nas minhas unidades de débito?
Pode começar tão baixo como uma unidade de produção (TU) e ligar [automaticamente](event-hubs-auto-inflate.md). A ampliação automática funcionalidade permite-lhe aumentar sua TUs à medida que aumenta o tráfego de/payload. Também pode definir um limite superior no número de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona a funcionalidade de ampliação automática dos Hubs de eventos?
O a ampliação automática permite a funcionalidade que aumenta verticalmente as unidades de débito (n). Isso significa que pode iniciar com a compra TUs baixas e ampliação automática escalas de segurança de sua TUs à medida que aumenta a sua entrada. Ele fornece uma opção rentável e o controle total do número de TUs para gerir. Esta funcionalidade é apenas uma funcionalidade de **escala,** e pode controlar completamente a escala para baixo do número de TUs atualizando-a. 

Pode querer começar com baixa unidades de débito (n), por exemplo, 2 TUs. Se prever que o tráfego pode aumentar de 15 TUs, ativar à ampliação automática funcionalidade no seu espaço de nomes e defina o limite máximo de 15 TUs. Agora pode aumentar sua TUs automaticamente à medida que o seu tráfego aumenta.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Existe um custo associado quando posso ativar a funcionalidade de ampliação automática?
Não há **nenhum custo** associado a esta funcionalidade. 

### <a name="how-are-throughput-limits-enforced"></a>Como os limites de débito são impostas?
Se o débito de entrada total ou a taxa de eventos de entrada total em todos os hubs de eventos num espaço de nomes exceder as permissões da unidade de débito agregada, os remetentes são limitados e recebem erros que indicam que a quota de entrada foi excedida.

Se o débito de saída total ou a taxa de saída do total de eventos em todos os hubs de eventos num espaço de nomes exceder as permissões da unidade de débito agregada, os recetores limitados e recebem erros que indicam que a quota de saída foi excedida. Quotas de entrada e de saída são impostas separadamente, para que nenhum remetente possa causar o abrandamento do consumo de eventos, nem pode um recetor impedir o envio de para um hub de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Existe um limite no número de unidades de débito (n) que podem ser reservados/selecionado?
Na oferta do multi-inquilino, unidades de débito podem crescer até 40 TUs (pode selecionar até 20 TUs no portal e emitir um pedido de suporte para atualizá-lo com 40 TUs no mesmo espaço de nomes). Além de 40 TUs, o Event Hubs oferece o modelo baseado em recursos/capacidade chamado **clusters dedicados**do Event Hubs . Clusters dedicados são vendidos em unidades de capacidade (CUs).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>Quais são os clusters de Hubs de eventos dedicados?
Clusters de dedicado de Hubs de eventos oferecem implementações de inquilino único para os clientes com requisitos mais exigentes. Esta oferta baseia-se um cluster baseado em capacidades que não está vinculado pelas unidades de débito. Significa que pode usar o cluster para ingerir e transmitir os seus dados conforme ditado pelo CPU e pelo uso da memória do cluster. Para mais informações, consulte [Clusters dedicados ao Event Hubs.](event-hubs-dedicated-overview.md)

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Quanto uma unidade de capacidade único permite-me obter?
Para um cluster dedicado, o quanto pode ingerir e transmitir depende de vários fatores como os seus produtores, consumidores, a taxa a que está a ingerir e processar, e muito mais. 

Tabela a seguir mostra os resultados do benchmark que obtivemos durante os nossos testes:

| Forma de payload | Recetores | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400k mensagens/seg | 800 MB/seg | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6k mensagens/seg | 1.33 GB/seg | 133k mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34k mensagens / seg | 1,05 GB/seg | 34k mensagens/seg | 1000 TUs | 250 TUs |

No teste, utilizou-se os seguintes critérios:

- Um cluster de Hubs de eventos dedicado com quatro unidades de capacidade (CUs) foi utilizado. 
- O hub de eventos utilizado para ingestão tinha 200 partições. 
- Os dados que foi ingeridos recebeu dois aplicativos de recetor recebimento de todas as partições.

Os resultados dão-lhe uma ideia do que pode ser conseguido com um cluster de Hubs de eventos dedicado. Além disso, um cluster de dedicate vem com a captura de Hubs de eventos ativado para os seus cenários de retenção de micro-lotes e de longa duração.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como posso criar um cluster de Hubs de eventos dedicados?
Cria um cluster dedicado ao Event Hubs, submetendo um pedido de apoio de aumento de [quota](https://portal.azure.com/#create/Microsoft.Support) ou contactando a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com) Normalmente, demora cerca de duas semanas para obter o cluster implementado e passada para ser utilizado por si. Este processo é temporário até que um self-serve completo seja disponibilizado através do portal Azure ou modelos do Gestor de Recursos Azure, que demoram cerca de duas horas a implantar o cluster.

## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>O número de partições é necessário?
O número de partições é especificado durante a criação e deve ser entre 2 e 32. A contagem de divisórias não é mutável, por isso deve considerar a escala a longo prazo ao definir a contagem de divisórias. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Para obter mais informações sobre divisórias, consulte [Divisórias](event-hubs-features.md#partitions).

Talvez queira defini-lo como o valor mais alto possível, que é 32, no momento da criação. Lembre-se que ter mais do que uma partição resultará em eventos enviados para várias divisórias sem reter a ordem, a menos que configure os remetentes para enviar apenas para uma única divisória das 32 deixando as restantes 31 divisórias redundantes. No primeiro caso, terá que ler eventos em todas as 32 divisórias. Neste último caso, não há custos adicionais óbvios para além da configuração extra que você tem que fazer no Anfitrião do Processador de Eventos.

Os Hubs de eventos foi concebido para permitir que um leitor de partição única por grupo de consumidores. Na maioria dos casos de utilização, a predefinição de quatro partições é suficiente. Se procura escalar o processamento do seu evento, talvez deseja considerar adicionar divisórias adicionais. Não existe um limite específico de entrada numa divisória, no entanto a entrada agregada no seu espaço de nome é limitada pelo número de unidades de produção. À medida que aumenta o número de unidades de débito no seu espaço de nomes, pode desejar partições adicionais para permitir que os leitores simultâneos alcançar um débito máximo seus próprios.

No entanto, se tiver um modelo no qual a sua aplicação tem uma afinidade com uma partição específica, aumento do número de partições pode não ser de qualquer vantagem para si. Para mais informações, consulte [disponibilidade e consistência.](event-hubs-availability-and-consistency.md)

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre os preços?

Para obter informações completas sobre os preços dos Hubs de Eventos, consulte os detalhes de preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe uma cobrança de retenção de eventos de Hubs de eventos para mais de 24 horas?

O escalão Standard dos Hubs de eventos permitem a retenção de mensagens períodos mais de 24 horas, para um máximo de sete dias. Se o tamanho do número total de eventos armazenados ultrapassar a concessão de armazenamento para o número de unidades de débito selecionadas (84 GB por unidade de débito), o tamanho que ultrapassar esse montante é cobrado à tarifa de armazenamento de Blobs do Azure publicada. A concessão de armazenamento em cada unidade de débito abrange todos os custos de armazenamento para períodos de retenção de 24 horas (predefinição), mesmo que a unidade de débito é utilizada a cópia de segurança para a concessão máxima de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é que o tamanho de armazenamento dos Hubs de eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo qualquer overhead interno para cabeçalhos de eventos ou estruturas de armazenamento de disco em todos os hubs de eventos, é medido durante o dia. No final do dia, é calculado o tamanho de armazenamento máximo. A concessão de armazenamento diário é calculada com base no número mínimo de unidades de débito que foram selecionadas durante o dia (cada unidade de débito fornece uma concessão de 84 GB). Se o tamanho total exceder o subsídio de armazenamento diário calculado, o armazenamento em excesso é faturado utilizando taxas de armazenamento De Blob Azure (à taxa de **armazenamento localmente redundante).**

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados os eventos de entrada dos Hubs de eventos?

Cada evento enviado para um hub de eventos é contabilizado como uma mensagem a cobrar. Um *evento de ingresso* é definido como uma unidade de dados que é inferior ou igual a 64 KB. Qualquer evento que é menor ou igual a 64 KB de tamanho é considerado um evento a cobrar. Se o evento for superior a 64 KB, o número de eventos a cobrar é calculado de acordo com o tamanho de evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o hub de eventos é faturado como um evento, mas uma mensagem de 96 KB enviada para o hub de eventos é faturada como dois eventos.

Eventos consumidos a partir de um hub de eventos, bem como operações de gestão e chamadas de controlo, como pontos de verificação, não são contabilizados como eventos de entrada a cobrar, mas acrescem até o montante da unidade de débito.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Custos de ligações mediadas são aplicáveis aos Hubs de eventos?

Custos de ligação aplicam-se apenas quando é utilizado o protocolo AMQP. Não há custos de ligação para o envio de eventos por HTTP, independentemente do número de sistemas ou dispositivos de envio. Se planeia utilizar a AMQP (por exemplo, para obter um streaming de eventos mais eficiente ou para permitir uma comunicação bidirecional em cenários de comando e controlo IoT), consulte a página de informação de preços do [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas ligações estão incluídas em cada nível de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é faturada a Captura dos Hubs de Eventos?

Captura é ativada quando qualquer hub de eventos no espaço de nomes tem a opção de captura ativada. Captura de Hubs de eventos é cobrada por hora por unidade de débito comprada. Como a contagem de unidades de débito aumenta ou diminui, a faturação da captura de Hubs de eventos reflete estas alterações em incrementos de hora completa. Para mais informações sobre a faturação de Hubs de Eventos, consulte as informações sobre preços do [Event Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>É-me faturada para a conta de armazenamento que eu selecionar para a captura de Hubs de eventos?

Captura utiliza uma conta de armazenamento fornecida por si quando ativada num hub de eventos. Como é sua conta de armazenamento, todas as alterações para esta configuração são faturadas à sua subscrição do Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existem quotas associados com os Hubs de eventos?

Para obter uma lista de todas as quotas do Event Hubs, consulte [quotas.](event-hubs-quotas.md)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não sou capaz de criar um espaço de nome depois de aapagar de outra subscrição? 
Quando eliminar um espaço de nome de uma subscrição, aguarde 4 horas antes de o recriar com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelo Hubs de eventos e respetivas ações sugeridas?

Para obter uma lista de possíveis exceções do Event Hubs, consulte a visão geral das [exceções.](event-hubs-messaging-exceptions.md)

### <a name="diagnostic-logs"></a>Registos de diagnósticos

O Event Hubs suporta dois tipos de [registos](event-hubs-diagnostic-logs.md) de diagnóstico - registos de erros de captura e registos operacionais - ambos representados em json e podem ser ligados através do portal Azure.

### <a name="support-and-sla"></a>Suporte e SLA

O suporte técnico para Centros de Eventos está disponível através dos [fóruns comunitários.](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus) O suporte de gestão da faturação e subscrição é fornecido sem custos.

Para saber mais sobre o nosso SLA, consulte a página de Acordos de [Nível de Serviço.](https://azure.microsoft.com/support/legal/sla/)

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Hubs de eventos auto-inflação](event-hubs-auto-inflate.md)
