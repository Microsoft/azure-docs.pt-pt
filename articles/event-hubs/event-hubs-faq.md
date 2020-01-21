---
title: Perguntas frequentes – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma lista de perguntas frequentes (FAQ) para os hubs de eventos do Azure e suas respostas.
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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706385"
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas frequentes sobre hubs de eventos

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um namespace de hubs de eventos?
Um namespace é um contêiner de escopo para tópicos de Hub de eventos/Kafka. Ele fornece um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)exclusivo. Um namespace serve como um contêiner de aplicativo que pode alojar vários tópicos de Hub de eventos/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando crio um novo namespace versus uso um namespace existente?
As alocações de capacidade ([Tus)](#throughput-units)são cobradas no nível do namespace. Um namespace também está associado a uma região.

Talvez você queira criar um novo namespace em vez de usar um existente em um dos seguintes cenários: 

- Você precisa de um hub de eventos associado a uma nova região.
- Você precisa de um hub de eventos associado a uma assinatura diferente.
- Você precisa de um hub de eventos com uma alocação de capacidade distinta (ou seja, a capacidade necessária para o namespace com o Hub de eventos adicionado excederia o limite de 40 TU e você não quer ir para o cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre as camadas básica e Standard dos hubs de eventos?

A camada Standard dos hubs de eventos do Azure fornece recursos além do que está disponível na camada básica. Os recursos a seguir estão incluídos no padrão:

* Retenção de eventos mais longa
* Conexões orientadas adicionais, com um encargo excedente para mais do que o número incluído
* Mais de um único [grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Captura](event-hubs-capture-overview.md)
* [Integração do Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre tipos de preço, incluindo Hubs de Eventos Dedicados, consulte os [detalhes de preços dos hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Onde estão disponíveis os hubs de eventos do Azure?

Os hubs de eventos do Azure estão disponíveis em todas as regiões do Azure com suporte. Para obter uma lista, visite a página [regiões do Azure](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso usar uma única conexão AMQP para enviar e receber de vários hubs de eventos?

Sim, desde que todos os hubs de eventos estejam no mesmo namespace.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período de retenção máximo para eventos?

A camada Standard dos hubs de eventos atualmente dá suporte a um período de retenção máximo de sete dias. Os hubs de eventos não se destinam a um armazenamento de dados permanente. Períodos de retenção superiores a 24 horas são destinados a cenários em que é conveniente reproduzir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina nos dados existentes. Se você precisar de retenção de mensagens além de sete dias, habilitar a [captura de hubs de eventos](event-hubs-capture-overview.md) em seu hub de eventos efetua pull dos dados do hub de eventos para a conta de armazenamento ou Azure data Lake conta de serviço de sua escolha. Habilitar a captura gera uma cobrança com base em suas unidades de produtividade adquiridas.

Você pode configurar o período de retenção para os dados capturados em sua conta de armazenamento. O recurso **Gerenciamento de ciclo de vida** do armazenamento do Azure oferece uma política avançada baseada em regras para contas de armazenamento de BLOBs V2 e de uso geral. Use a política para fazer a transição dos dados para as camadas de acesso apropriadas ou expirar no final do ciclo de vida dos dados. Para obter mais informações, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como fazer monitorar meus hubs de eventos?
Os hubs de eventos emitem as métricas exaustivas que fornecem o estado de seus recursos para [Azure monitor](../azure-monitor/overview.md). Eles também permitem que você avalie a integridade geral do serviço de hubs de eventos não apenas no nível de namespace, mas também no nível de entidade. Saiba mais sobre o que o monitoramento é oferecido para os [hubs de eventos do Azure](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quais portas eu preciso abrir no firewall? 
Você pode usar os seguintes protocolos com o barramento de serviço do Azure para enviar e receber mensagens:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Consulte a tabela a seguir para as portas de saída que você precisa abrir para usar esses protocolos para se comunicar com os hubs de eventos do Azure. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [Guia do protocolo AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Consulte [usar hubs de eventos de aplicativos Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quais endereços IP preciso para a lista de permissões?
Para localizar os endereços IP corretos para a lista de permissões de suas conexões, siga estas etapas:

1. Execute o seguinte comando em um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP retornado em `Non-authoritative answer`. A única vez que mudaria é se você restaurasse o namespace em um cluster diferente.

Se você usar a redundância de zona para seu namespace, precisará executar algumas etapas adicionais: 

1. Primeiro, execute nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção de **resposta não autoritativa** , que está em um dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos S1, S2 e S3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 

## <a name="apache-kafka-integration"></a>Integração do Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como fazer integrar meu aplicativo Kafka existente aos hubs de eventos?
Os hubs de eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos existentes baseados em Apache Kafka. Uma alteração de configuração é tudo o que é necessário para ter a experiência de Kafka de PaaS. Ele fornece uma alternativa para executar seu próprio cluster Kafka. Os hubs de eventos dão suporte às versões de cliente Apache Kafka 1,0 e mais recentes e funcionam com seus aplicativos, ferramentas e estruturas Kafka existentes. Para obter mais informações, consulte [hubs de eventos para o repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Quais alterações de configuração precisam ser feitas para que meu aplicativo existente se comunique com os hubs de eventos?
Para se conectar a um hub de eventos habilitado para Kafka, você precisará atualizar as configurações do cliente Kafka. Isso é feito criando um namespace de hubs de eventos e obtendo a [cadeia de conexão](event-hubs-get-connection-string.md). Altere o bootstrap. Servers para apontar o FQDN dos hubs de eventos e a porta para 9093. Atualize o SASL. JAAS. config para direcionar o cliente Kafka para o ponto de extremidade dos hubs de eventos habilitados para Kafka (que é a cadeia de conexão que você obteve), com a autenticação correta, conforme mostrado abaixo:

Bootstrap. Servers = {seu. EVENTHUBS. FQDN}: 9093 solicitação. Timeout. ms = 60000 Security. Protocol = SASL_SSL SASL. mecanismo = PLAIN SASL. JAAS. config = org. Apache. Kafka. Common. Security. Plain. PlainLoginModule obrigatório nome de usuário = "$ConnectionString" senha = "{YOUR. EVENTHUBS. Conexão. Cadeia de caracteres} ";

Exemplo:

Bootstrap. Servers = dummynamespace. ServiceBus. Windows. net: 9093 solicitação. Timeout. ms = 60000 Security. Protocol = SASL_SSL SASL. mecanismo = PLAIN SASL. JAAS. config = org. Apache. Kafka. Common. Security. Plains. PlainLoginModule Required username = "$ConnectionString" password = "Endpoint = SB://dummynamespace.ServiceBus.Windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Observação: se SASL. JAAS. config não for uma configuração com suporte em sua estrutura, localize as configurações usadas para definir o nome de usuário e a senha SASL e use-os em vez disso. Defina o nome de usuário como $ConnectionString e a senha para a cadeia de conexão dos hubs de eventos.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Qual é o tamanho da mensagem/evento para os hubs de eventos habilitados para Kafka?
O tamanho máximo de mensagem permitido para hubs de eventos habilitados para Kafka é de 1 MB.

## <a name="throughput-units"></a>Unidades de débito

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produtividade dos hubs de eventos?
A taxa de transferência nos hubs de eventos define a quantidade de dados em mega bytes ou o número (em milhares) de eventos de 1 KB que ingressou e saída por meio de hubs de eventos. Essa taxa de transferência é medida em unidades de taxa de transferência (TUs). Compre TUs antes de começar a usar o serviço de hubs de eventos. Você pode selecionar explicitamente os hubs de eventos TUs usando modelos do portal ou do Resource Manager dos hubs de eventos. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>As unidades de produtividade se aplicam a todos os hubs de eventos em um namespace?
Sim, as unidades de taxa de transferência (TUs) se aplicam a todos os hubs de eventos em um namespace de hubs de eventos. Isso significa que você adquire TUs no nível de namespace e é compartilhado entre os hubs de eventos sob esse namespace. Cada TU concede o namespace aos seguintes recursos:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um hub de eventos), mas não mais do que 1000 eventos de entrada, operações de gerenciamento ou chamadas de API de controle por segundo.
- Até 2 MB por segundo de eventos de saída (eventos consumidos de um hub de eventos), mas não mais do que 4096 eventos de saída.
- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção de 24 horas padrão).

### <a name="how-are-throughput-units-billed"></a>Como as unidades de produtividade são cobradas?
As unidades de taxa de transferência (TUs) são cobradas por hora. A cobrança é baseada no número máximo de unidades que foi selecionado durante a determinada hora. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar o uso em minhas unidades de produtividade?
Você pode iniciar como uma unidade de taxa de transferência mínima (TU) e ativar o [inflar automaticamente](event-hubs-auto-inflate.md). O recurso de inflar automaticamente permite que você aumente seu TUs à medida que seu tráfego/carga aumenta. Você também pode definir um limite superior no número de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona o recurso de inflar automaticamente dos hubs de eventos?
O recurso inflar automaticamente permite escalar verticalmente suas unidades de taxa de transferência (TUs). Isso significa que você pode começar comprando o Low TUs e o inflar automaticamente dimensiona seu TUs à medida que sua entrada aumenta. Ele oferece uma opção econômica e controle total do número de TUs para gerenciar. Esse recurso é um recurso **somente de expansão** e você pode controlar completamente o dimensionamento do número de Tus atualizando-o. 

Talvez você queira começar com TUs (unidades de baixa taxa de transferência), por exemplo, 2 TUs. Se você prever que seu tráfego pode crescer para 15 TUs, ative o recurso de inflar automaticamente em seu namespace e defina o limite máximo como 15 TUs. Agora você pode aumentar seu TUs automaticamente à medida que seu tráfego cresce.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Há um custo associado ao ativar o recurso de inflar automaticamente?
Não há **nenhum custo** associado a esse recurso. 

### <a name="how-are-throughput-limits-enforced"></a>Como os limites de taxa de transferência são aplicados?
Se a produtividade de entrada total ou a taxa total de eventos de entrada em todos os hubs de eventos em um namespace exceder as provisões da unidade de produtividade agregada, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de egresso total ou a taxa total de egresso de eventos em todos os hubs de eventos em um namespace exceder as provisões da unidade de produtividade agregada, os receptores serão limitados e receberão erros indicando que a cota de saída foi excedida. As cotas de entrada e saída são impostas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos fique lento, nem um receptor impeça que os eventos sejam enviados para um hub de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Há um limite no número de unidades de taxa de transferência (TUs) que podem ser reservadas/selecionadas?
Em uma oferta multilocatário, as unidades de produtividade podem crescer até 40 TUs (você pode selecionar até 20 TUs no portal e gerar um tíquete de suporte para aumentá-lo para 40 TUs no mesmo namespace). Além de 40 TUs, os hubs de eventos oferecem o modelo baseado em recurso/capacidade chamado **clusters de hubs de eventos dedicados**. Os clusters dedicados são vendidos em unidades de capacidade (CUs).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>O que são clusters Hubs de Eventos Dedicados?
Os clusters Hubs de Eventos Dedicados oferecem implantações de locatário único para clientes com requisitos mais exigentes. Essa oferta cria um cluster baseado em capacidade que não está associado por unidades de produtividade. Isso significa que você pode usar o cluster para ingerir e transmitir seus dados conforme determinado pelo uso de CPU e memória do cluster. Para obter mais informações, consulte [clusters hubs de eventos dedicados](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Quanto uma unidade de capacidade única me permite conseguir?
Para um cluster dedicado, quanto você pode ingerir e transmitir depende de vários fatores, como seus produtores, consumidores, a taxa na qual você está ingerindo e processando e muito mais. 

A tabela a seguir mostra os resultados de benchmark que obtivemos durante nosso teste:

| Forma de conteúdo | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | mensagens 400 mil/s | 800 MB/s | mensagens 800K/s | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | mensagens de 66.6 k/s | 1,33 GB/s | mensagens 133k/s | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1, 5 GB/s | mensagens 34k/s | 1, 5 GB/s | mensagens 34k/s | 1000 TUs | 250 TUs |

No teste, os seguintes critérios foram usados:

- Foi usado um cluster de hubs de eventos dedicado com quatro unidades de capacidade (CUs). 
- O Hub de eventos usado para a ingestão tinha 200 partições. 
- Os dados que foram ingeridos foram recebidos por dois aplicativos receptores recebendo de todas as partições.

Os resultados dão uma ideia do que pode ser obtido com um cluster de hubs de eventos dedicado. Além disso, um cluster dedicado vem com a captura de hubs de eventos habilitada para seus cenários de retenção de micro lote e de longo prazo.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como fazer criar um cluster Hubs de Eventos Dedicados?
Você cria um cluster de hubs de eventos dedicado enviando uma [solicitação de suporte de aumento de cota](https://portal.azure.com/#create/Microsoft.Support) ou contatando a [equipe de hubs de eventos](mailto:askeventhubs@microsoft.com). Normalmente leva cerca de duas semanas para que o cluster seja implantado e transmitido para ser usado por você. Esse processo é temporário até que um autoatendimento completo seja disponibilizado por meio dos modelos portal do Azure ou Azure Resource Manager, que levam cerca de duas horas para implantar o cluster.

## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>De quantas partições eu preciso?
O número de partições é especificado durante a criação e deve ser entre 2 e 32. A contagem de partições não pode ser alterada, portanto, você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Para obter mais informações sobre partições, consulte [partições](event-hubs-features.md#partitions).

Talvez você queira defini-lo para ser o maior valor possível, que é 32, no momento da criação. Lembre-se de que ter mais de uma partição resultará em eventos enviados a várias partições sem reter o pedido, a menos que você configure os remetentes para enviar somente para uma única partição fora do 32 deixando as 31 restantes de partições redundantes. No primeiro caso, você terá que ler eventos em todas as partições 32. No último caso, não há nenhum custo adicional óbvio da configuração extra que você precisa fazer no host do processador de eventos.

Os hubs de eventos são projetados para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de uso, a configuração padrão de quatro partições é suficiente. Se você pretende dimensionar seu processamento de eventos, convém considerar a adição de partições adicionais. Não há nenhum limite de taxa de transferência específico em uma partição, no entanto, a taxa de transferência agregada no namespace é limitada pelo número de unidades de produtividade. Ao aumentar o número de unidades de produtividade em seu namespace, talvez você queira partições adicionais para permitir que os leitores simultâneos obtenham sua própria taxa de transferência máxima.

No entanto, se você tiver um modelo no qual seu aplicativo tem uma afinidade com uma partição específica, aumentar o número de partições poderá não ser de qualquer benefício para você. Para obter mais informações, consulte [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?

Para obter informações completas sobre os preços dos hubs de eventos, consulte os [detalhes de preços dos hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há um encargo para reter eventos de hubs de eventos por mais de 24 horas?

A camada Standard dos hubs de eventos permite períodos de retenção de mensagens com mais de 24 horas, por um máximo de sete dias. Se o tamanho do número total de eventos armazenados exceder a concessão de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que excede a concessão será cobrado na taxa de armazenamento de BLOBs do Azure publicada. A concessão de armazenamento em cada unidade de produtividade abrange todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão), mesmo se a unidade de produtividade for usada até a concessão máxima de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como o tamanho de armazenamento dos hubs de eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo qualquer sobrecarga interna para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os hubs de eventos, é medido ao longo do dia. No final do dia, é calculado o tamanho de armazenamento máximo. A concessão de armazenamento diário é calculada com base no número mínimo de unidades de débito que foram selecionadas durante o dia (cada unidade de débito fornece uma concessão de 84 GB). Se o tamanho total exceder a concessão de armazenamento diária calculada, o armazenamento em excesso será cobrado usando as taxas de armazenamento de BLOBs do Azure (na taxa de **armazenamento com redundância local** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como os eventos de entrada dos hubs de eventos são calculados?

Cada evento enviado a um hub de eventos conta como uma mensagem cobrável. Um *evento de entrada* é definido como uma unidade de dados que é menor ou igual a 64 KB. Qualquer evento que seja menor ou igual a 64 KB de tamanho é considerado um evento cobrável. Se o evento for maior que 64 KB, o número de eventos faturáveis será calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o Hub de eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada para o Hub de eventos é cobrada como dois eventos.

Os eventos consumidos de um hub de eventos, bem como operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contados como eventos de entrada faturáveis, mas acumulam até a concessão de unidade de produtividade.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>As tarifas de conexão orientadas se aplicam aos hubs de eventos?

As tarifas de conexão se aplicam somente quando o protocolo AMQP é usado. Não há custos de ligação para o envio de eventos por HTTP, independentemente do número de sistemas ou dispositivos de envio. Se você planeja usar o AMQP (por exemplo, para obter um streaming de eventos mais eficiente ou para habilitar a comunicação bidirecional em cenários de comando e controle de IoT), consulte a página [informações de preços dos hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas conexões estão incluídas em cada camada de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é faturada a Captura dos Hubs de Eventos?

A captura será habilitada quando qualquer Hub de eventos no namespace tiver a opção de captura habilitada. A captura de hubs de eventos é cobrada por hora por unidade de produtividade adquirida. À medida que a contagem de unidades de produtividade aumenta ou diminui, a cobrança de captura de hubs de eventos reflete essas alterações em incrementos de hora inteira. Para obter mais informações sobre cobrança de captura de hubs de eventos, consulte [informações de preços de hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Sou cobrado pela conta de armazenamento que seleciono para captura de hubs de eventos?

A captura usa uma conta de armazenamento que você fornece quando habilitada em um hub de eventos. Como sua conta de armazenamento, todas as alterações para essa configuração são cobradas em sua assinatura do Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Há alguma cota associada aos hubs de eventos?

Para obter uma lista de todas as cotas de hubs de eventos, consulte [cotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não consigo criar um namespace depois de excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, você pode receber a seguinte mensagem de erro: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos hubs de eventos e suas ações sugeridas?

Para obter uma lista de possíveis exceções de hubs de eventos, consulte [visão geral de exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Os hubs de eventos dão suporte a dois tipos de [logs de diagnóstico](event-hubs-diagnostic-logs.md) -capturar logs de erro e logs operacionais – ambos são representados em JSON e podem ser ativados por meio do portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA

O suporte técnico para hubs de eventos está disponível por meio dos [fóruns da Comunidade](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). O suporte de gestão da faturação e subscrição é fornecido sem custos.

Para saber mais sobre nosso SLA, consulte a página [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Inflar automaticamente os hubs de eventos](event-hubs-auto-inflate.md)
