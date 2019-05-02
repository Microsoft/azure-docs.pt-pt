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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ce9c6a83d664bc9ad1798792f7762556c9a0d541
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690278"
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas mais frequentes sobre os Hubs de eventos

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um espaço de nomes de Hubs de eventos?
Um espaço de nomes é um contentor de âmbito para tópicos do Event Hub/Kafka. Ele fornece um exclusivo [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Um espaço de nomes serve como um contêiner de aplicativo que pode alojar vários tópicos do Event Hub/Kafka. 

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>O que é a diferença entre os escalões Standard e Hubs de eventos básico?

O escalão Standard dos Hubs de eventos do Azure fornece funcionalidades para além das que estão disponíveis no escalão básico. As seguintes funcionalidades estão incluídas com o padrão:

* Retenção de eventos maior
* Ligações mediadas adicionais, com uma taxa de excesso para obter mais informações que o número incluído
* Mais do que um único [grupo de consumidores](event-hubs-features.md#consumer-groups)
* [captura](event-hubs-capture-overview.md)
* [Integração do Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre os preços escalões, incluindo os Hubs de eventos dedicados, consulte a [os detalhes dos preços dos Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Onde os Hubs de eventos do Azure está disponível?

Os Hubs de eventos do Azure está disponível em todas as regiões do Azure suportadas. Para obter uma lista, visite o [regiões do Azure](https://azure.microsoft.com/regions/) página.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Pode utilizar uma única ligação AMQP para enviar e receber vários dos hubs de eventos?

Sim, desde que todos os hubs de eventos estão no mesmo espaço de nomes.

### <a name="what-is-the-maximum-retention-period-for-events"></a>O que é o período de retenção máximo para eventos?

Escalão Standard dos Hubs de eventos, atualmente, suporta um período de retenção máximo de sete dias. Os hubs de eventos não se destinam como um arquivo de dados permanente. Períodos de retenção superiores a 24 horas destinam-se a cenários em que é conveniente reproduzir um fluxo de eventos nos mesmos sistemas; Por exemplo, programar ou verificar um novo modelo de aprendizagem automática nos dados existentes. Se precisar de retenção de mensagens para além dos sete dias, permitindo [captura de Hubs de eventos](event-hubs-capture-overview.md) em seu evento hub extrai os dados do seu hub de eventos para a conta de armazenamento ou a conta de serviço do Azure Data Lake à sua escolha. Habilitar a captura implica uma cobrança com base em unidades de débito adquiridas.

### <a name="how-do-i-monitor-my-event-hubs"></a>Como posso monitorizar o meu Hubs de eventos?
Os Hubs de eventos emite métricas exaustivas que fornecem o estado dos seus recursos para [do Azure Monitor](../azure-monitor/overview.md). Eles também permitem avaliar o estado de funcionamento geral do serviço Hubs de eventos não apenas ao nível do espaço de nomes, mas também no nível de entidade. Saiba mais sobre o que monitorizar é oferecido para [os Hubs de eventos do Azure](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso abrir na firewall? 
Pode utilizar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Veja a seguinte tabela para as portas de saída que é necessário abrir para utilizar estes protocolos para comunicar com os Hubs de eventos do Azure. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [Guia do protocolo AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9092 | Consulte [dos Hubs de eventos de utilização de aplicações do Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Que endereços IP é necessário à lista de permissões?
Para localizar os endereços IP corretos para a lista de permissões para as suas ligações, siga estes passos:

1. Execute o seguinte comando numa linha de comandos: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP devolvido na `Non-authoritative answer`. Este endereço IP é estático. O único ponto no tempo que ele seria alterado é se restaurar o espaço de nomes para um cluster diferente.

Se utilizar a redundância de zona para o espaço de nomes, precisa fazer algumas etapas adicionais: 

1. Em primeiro lugar, execute nslookup no espaço de nomes.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na **resposta não autoritativa** seção, que se encontra dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP de todos os três instâncias em execução em três zonas de disponibilidade, 

## <a name="apache-kafka-integration"></a>Integração do Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como posso integrar o meu aplicativo existente do Kafka com os Hubs de eventos?
Os Hubs de eventos fornece um ponto de extremidade do Kafka que pode ser utilizado por seus aplicativos existentes do Apache Kafka com base. Uma alteração de configuração é tudo o que é necessária para ter a experiência de PaaS Kafka. Ele fornece uma alternativa à execução de seu próprio cluster do Kafka. Os Hubs de eventos suporta Apache Kafka 1.0 e as versões mais recentes do cliente e funciona com seus existente Kafka aplicativos, ferramentas e estruturas. Para obter mais informações, consulte [Hubs de eventos para o repositório de Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>O que as alterações de configuração precisam ser feito para a minha aplicação existente comunicar com os Hubs de eventos?
Para ligar a um Hub de eventos habilitados no Kafka, terá de atualizar as configurações de cliente do Kafka. Isso é feito ao criar um espaço de nomes de Hubs de eventos e obter o [cadeia de ligação](event-hubs-get-connection-string.md). Altere o bootstrap.servers para apontar o FQDN de Hubs de eventos e a porta para 9093. Atualizar o sasl.jaas.config para direcionar o cliente do Kafka para o ponto final de Hubs de eventos de Kafka-ativado (que é a cadeia de ligação que obteve), com corrija autenticação conforme mostrado abaixo:

bootstrap.Servers={your. EVENTHUBS. FQDN}: 9093 request.timeout.ms=60000 o security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule necessário o nome de utilizador = a palavra-passe de "$ConnectionString" = "{seu. EVENTHUBS. LIGAÇÃO. CADEIA DE CARACTERES} ";

Exemplo:

bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule necessário o nome de utilizador = "$ ConnectionString"password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Nota: Se sasl.jaas.config não é uma configuração suportada no sua estrutura, encontre as configurações que são utilizadas para definir o nome de utilizador do SASL e a palavra-passe e utilizá-los em vez disso. Defina o nome de utilizador para $ConnectionString e a palavra-passe para a cadeia de ligação dos Hubs de eventos.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>O que é o tamanho de mensagens/eventos para Hubs de eventos de Kafka-ativado?
O tamanho máximo permitido para os Hubs de eventos habilitado para o Kafka é 1MB.

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
Pode iniciar como tão baixo como uma unidade de débito (TU) e ative [ampliação automática](event-hubs-auto-inflate.md). A ampliação automática funcionalidade permite-lhe aumentar sua TUs à medida que aumenta o tráfego de/payload. Também pode definir um limite superior no número de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona a funcionalidade de ampliação automática dos Hubs de eventos?
O a ampliação automática permite a funcionalidade que aumenta verticalmente as unidades de débito (n). Isso significa que pode iniciar com a compra TUs baixas e ampliação automática escalas de segurança de sua TUs à medida que aumenta a sua entrada. Ele fornece uma opção rentável e o controle total do número de TUs para gerir. Esta funcionalidade é uma **vertical apenas** funcionalidade e pode controlar completamente a reduzir verticalmente o número de TUs ao atualizá-los. 

Pode querer começar com baixa unidades de débito (n), por exemplo, 2 TUs. Se prever que o tráfego pode aumentar de 15 TUs, ativar à ampliação automática funcionalidade no seu espaço de nomes e defina o limite máximo de 15 TUs. Agora pode aumentar sua TUs automaticamente à medida que o seu tráfego aumenta.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Existe um custo associado quando posso ativar a funcionalidade de ampliação automática?
Há **sem custos** associados a esta funcionalidade. 

### <a name="how-are-throughput-limits-enforced"></a>Como os limites de débito são impostas?
Se o débito de entrada total ou a taxa de eventos de entrada total em todos os hubs de eventos num espaço de nomes exceder as permissões da unidade de débito agregada, os remetentes são limitados e recebem erros que indicam que a quota de entrada foi excedida.

Se o débito de saída total ou a taxa de saída do total de eventos em todos os hubs de eventos num espaço de nomes exceder as permissões da unidade de débito agregada, os recetores limitados e recebem erros que indicam que a quota de saída foi excedida. Quotas de entrada e de saída são impostas separadamente, para que nenhum remetente possa causar o abrandamento do consumo de eventos, nem pode um recetor impedir o envio de para um hub de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Existe um limite no número de unidades de débito (n) que podem ser reservados/selecionado?
Na oferta do multi-inquilino, unidades de débito podem crescer até 40 TUs (pode selecionar até 20 TUs no portal e emitir um pedido de suporte para atualizá-lo com 40 TUs no mesmo espaço de nomes). Para além dos 40 TUs, os Hubs de eventos oferece o modelo baseado em recursos/capacidade chamado os **clusters de Hubs de eventos dedicados**. Clusters dedicados são vendidos em unidades de capacidade (CUs).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>Quais são os clusters de Hubs de eventos dedicados?
Clusters de dedicado de Hubs de eventos oferecem implementações de inquilino único para os clientes com requisitos mais exigentes. Esta oferta baseia-se um cluster baseado em capacidades que não está vinculado pelas unidades de débito. Isso significa que poderia utilizar o cluster para ingerir e transmitir os seus dados conforme ditado pela utilização de CPU e memória do cluster. Para obter mais informações, consulte [clusters de Hubs de eventos dedicados](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Quanto uma unidade de capacidade único permite-me obter?
Para um cluster dedicado, quanto pode ingerir e transmitir em fluxo depende de vários fatores, tais como os produtores, os consumidores, a taxa a que são a ingerir e processar e muito mais. 

Tabela a seguir mostra os resultados do benchmark que obtivemos durante os nossos testes:

| Forma de payload | Recetores | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400 mil msgs/seg | 800 MB/seg | 800 k msgs/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6 k msgs/seg | 1.33 GB/seg | 133 k msgs/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34 k msgs / seg | 1,05 GB/seg | 34 k msgs/seg | 1000 TUs | 250 TUs |

No teste, utilizou-se os seguintes critérios:

- Um cluster de Hubs de eventos dedicado com quatro unidades de capacidade (CUs) foi utilizado. 
- O hub de eventos utilizado para ingestão tinha 200 partições. 
- Os dados que foi ingeridos recebeu dois aplicativos de recetor recebimento de todas as partições.

Os resultados dão-lhe uma ideia do que pode ser conseguido com um cluster de Hubs de eventos dedicado. Além disso, um cluster de dedicate vem com a captura de Hubs de eventos ativado para os seus cenários de retenção de micro-lotes e de longa duração.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como posso criar um cluster de Hubs de eventos dedicados?
Criar um cluster dedicado de Hubs de eventos ao enviar um [pedido de suporte de aumento de quota](https://portal.azure.com/#create/Microsoft.Support) ou contacte o [equipa dos Hubs de eventos](mailto:askeventhubs@microsoft.com). Normalmente, demora cerca de duas semanas para obter o cluster implementado e passada para ser utilizado por si. Este processo é temporário até uma completa personalizada é disponibilizado através do portal do Azure ou de modelos do Azure Resource Manager, que demora cerca de duas horas a implementar o cluster.

## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>O número de partições é necessário?

O número de partições num hub de eventos não pode ser modificado após a configuração. Com isso em mente, é importante pensar sobre quantas partições terá antes de começar. 

Os Hubs de eventos foi concebido para permitir que um leitor de partição única por grupo de consumidores. Na maioria dos casos de utilização, a predefinição de quatro partições é suficiente. Se estiver à procura de dimensionar o processamento de eventos, poderá querer considerar a adição de partições adicionais. Não existe nenhum limite de taxa de transferência específico numa partição, no entanto, o débito agregado em seu espaço de nomes é limitado pelo número de unidades de débito. À medida que aumenta o número de unidades de débito no seu espaço de nomes, pode desejar partições adicionais para permitir que os leitores simultâneos alcançar um débito máximo seus próprios.

No entanto, se tiver um modelo no qual a sua aplicação tem uma afinidade com uma partição específica, aumento do número de partições pode não ser de qualquer vantagem para si. Para obter mais informações, consulte [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre os preços?

Para obter informações completas sobre os preços de Hubs de eventos, consulte a [os detalhes dos preços dos Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe uma cobrança de retenção de eventos de Hubs de eventos para mais de 24 horas?

O escalão Standard dos Hubs de eventos permitem a retenção de mensagens períodos mais de 24 horas, para um máximo de sete dias. Se o tamanho do número total de eventos armazenados ultrapassar a concessão de armazenamento para o número de unidades de débito selecionadas (84 GB por unidade de débito), o tamanho que ultrapassar esse montante é cobrado à tarifa de armazenamento de Blobs do Azure publicada. A concessão de armazenamento em cada unidade de débito abrange todos os custos de armazenamento para períodos de retenção de 24 horas (predefinição), mesmo que a unidade de débito é utilizada a cópia de segurança para a concessão máxima de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é que o tamanho de armazenamento dos Hubs de eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo qualquer overhead interno para cabeçalhos de eventos ou estruturas de armazenamento de disco em todos os hubs de eventos, é medido durante o dia. No final do dia, é calculado o tamanho de armazenamento máximo. A concessão de armazenamento diário é calculada com base no número mínimo de unidades de débito que foram selecionadas durante o dia (cada unidade de débito fornece uma concessão de 84 GB). Se o tamanho total exceder o montante do armazenamento diário calculado, o armazenamento em excesso é cobrado utilizando as taxas de armazenamento de Blobs do Azure (com o **armazenamento localmente redundante** taxa).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados os eventos de entrada dos Hubs de eventos?

Cada evento enviado para um hub de eventos é contabilizado como uma mensagem a cobrar. Uma *evento de entrada* é definido como uma unidade de dados que é menor ou igual a 64 KB. Qualquer evento que é menor ou igual a 64 KB de tamanho é considerado um evento a cobrar. Se o evento for superior a 64 KB, o número de eventos a cobrar é calculado de acordo com o tamanho de evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o hub de eventos é faturado como um evento, mas uma mensagem de 96 KB enviada para o hub de eventos é faturada como dois eventos.

Eventos consumidos a partir de um hub de eventos, bem como operações de gestão e chamadas de controlo, como pontos de verificação, não são contabilizados como eventos de entrada a cobrar, mas acrescem até o montante da unidade de débito.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Custos de ligações mediadas são aplicáveis aos Hubs de eventos?

Custos de ligação aplicam-se apenas quando é utilizado o protocolo AMQP. Não há custos de ligação para o envio de eventos por HTTP, independentemente do número de sistemas ou dispositivos de envio. Se planeia utilizar o AMQP (por exemplo, para obter uma transmissão de eventos mais eficiente ou para permitir a comunicação bidirecional no comando de IoT e cenários de controle), consulte a [obter informações sobre preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/) página para obter detalhes sobre quantos as ligações são incluídas em cada escalão de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é faturada a Captura dos Hubs de Eventos?

Captura é ativada quando qualquer hub de eventos no espaço de nomes tem a opção de captura ativada. Captura de Hubs de eventos é cobrada por hora por unidade de débito comprada. Como a contagem de unidades de débito aumenta ou diminui, a faturação da captura de Hubs de eventos reflete estas alterações em incrementos de hora completa. Para obter mais informações sobre a faturação da captura dos Hubs de eventos, consulte [obter informações sobre preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>É-me faturada para a conta de armazenamento que eu selecionar para a captura de Hubs de eventos?

Captura utiliza uma conta de armazenamento fornecida por si quando ativada num hub de eventos. Como é sua conta de armazenamento, todas as alterações para esta configuração são faturadas à sua subscrição do Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existem quotas associados com os Hubs de eventos?

Para obter uma lista de todas as quotas de Hubs de eventos, consulte [quotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que motivo não consigo criar um espaço de nomes depois excluí-lo a partir de outra subscrição? 
Quando elimina um espaço de nomes de uma subscrição, aguarde até quatro horas antes de recriá-lo com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelo Hubs de eventos e respetivas ações sugeridas?

Para obter uma lista de exceções de Hubs de eventos possíveis, consulte [descrição geral de exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Os Hubs de eventos suportam dois tipos de [registos de diagnóstico](event-hubs-diagnostic-logs.md) -capturar os registos de erros e registos operacionais - que são representados em json e podem ser ativados através do portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA

Disponibilizamos suporte técnico para os Hubs de eventos está disponível através da [fóruns da Comunidade](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). O suporte de gestão da faturação e subscrição é fornecido sem custos.

Para saber mais sobre o nosso SLA, consulte a [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/) página.

## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Event Hubs a ampliação automática](event-hubs-auto-inflate.md)
