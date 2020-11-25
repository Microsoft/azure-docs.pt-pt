---
title: Visão geral das funcionalidades - Azure Event Hubs Microsoft Docs
description: Este artigo fornece detalhes sobre funcionalidades e terminologia dos Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: db7c0244fd4e9e04f9cfbcbba8748ec8190fc5c5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96007443"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funcionalidades e terminologia nos Hubs de Eventos do Azure

O Azure Event Hubs é um serviço de processamento de eventos escalável que ingere e processa grandes volumes de eventos e dados, com baixa latência e elevada fiabilidade. Veja [o que é o Event Hubs para](./event-hubs-about.md) uma visão geral de alto nível.

Este artigo baseia-se na informação no [artigo geral,](./event-hubs-about.md)e fornece detalhes técnicos e de implementação sobre componentes e funcionalidades do Event Hubs.

## <a name="namespace"></a>Espaço de Nomes
Um espaço de nomes de Event Hubs fornece um recipiente de scoping único, referenciado pelo seu [nome de domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)no qual você cria um ou mais centros de eventos ou tópicos Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Hubs de Eventos para o Apache Kafka

[Esta funcionalidade](event-hubs-for-kafka-ecosystem-overview.md) fornece um ponto final que permite aos clientes falar com os Centros de Eventos utilizando o protocolo Kafka. Esta integração proporciona aos clientes um ponto final kafka. Isto permite que os clientes configurem as suas aplicações kafka existentes para falar com os Event Hubs, dando uma alternativa à gestão dos seus próprios clusters Kafka. Os Centros de Eventos para Apache Kafka suportam o protocolo 1.0 e mais tarde. 

Com esta integração, você não precisa executar clusters Kafka ou geri-los com Zookeeper. Isto também permite trabalhar com algumas das características mais exigentes de Centros de Eventos como Capture, Auto-inflate e Geo-disaster Recovery.

Esta integração também permite que aplicações como o Mirror Maker ou uma estrutura como o Kafka Connect funcionem sem cluster com apenas alterações de configuração. 

## <a name="event-publishers"></a>Publicadores de eventos

Qualquer entidade que envie dados para um centro de eventos é um produtor de eventos, ou *editor de eventos.* Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1.0 ou Kafka 1.0 e mais tarde. Os publicadores de eventos utilizam um token de Assinatura de Acesso Partilhado (SAS) para se identificarem a si próprios perante um hub de eventos e podem ter uma identidade exclusiva ou utilizar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento

Pode publicar um evento via AMQP 1.0, Kafka 1.0 (e mais tarde) ou HTTPS. O serviço Event Hubs fornece bibliotecas de clientes [REST API](https://docs.microsoft.com/rest/api/eventhub/) e [.NET,](event-hubs-dotnet-standard-getstarted-send.md) [Java,](event-hubs-java-get-started-send.md) [Python,](event-hubs-python-get-started-send.md) [JavaScript](event-hubs-node-get-started-send.md)e [Go](event-hubs-go-get-started-send.md) para a publicação de eventos num centro de eventos. Para outros tempos de execução e plataformas, pode utilizar qualquer cliente AMQP 1.0, como o [Apache Qpid](https://qpid.apache.org/). 

Pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados de eventos) tem um limite de 1 MB, independentemente de se trate de um único evento ou de um lote. Publicar eventos maiores do que este limiar resulta num erro. É preferível os publicadores não terem conhecimento das partições dentro do hub de eventos e apenas especificarem uma *chave de partição* (apresentada na secção seguinte) ou a sua identidade através do respetivo token SAS.

A opção para utilizar AMQP ou HTTPS é específica do cenário de utilização. O AMQP requer o estabelecimento de um socket bidirecional persistente, para além da segurança de nível do transporte (TLS) ou SSL/TLS. A AMQP tem custos de rede mais elevados ao rubricar a sessão, no entanto HTTPS requer despesa adicional de TLS para cada pedido. O AMQP tem um desempenho superior para publicadores frequentes.

![Chaves de partição](./media/event-hubs-features/partition_keys.png)

Os Hubs de Eventos garantem que todos os eventos que partilham o valor de chave de partição sejam entregues por ordem e para a mesma partição. Se as chaves de partição forem utilizadas com as políticas do publicador, a identidade do publicador e o valor da chave de partição devem coincidir. Caso contrário, ocorrerá um erro.

### <a name="publisher-policy"></a>Política do publicador

Os Event Hubs permitem um controlo granular sobre os publicadores de eventos através de *políticas do publicador*. As políticas do publicador são funcionalidades de tempo de execução concebidas para facilitar um elevado número de publicadores de eventos independentes. Com as políticas do publicador, cada publicador utiliza o seu próprio identificador exclusivo quando publica eventos num hub de eventos, utilizando o mecanismo seguinte:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Não precisa de criar os nomes dos publicadores com antecedência, mas devem corresponder ao token SAS utilizado ao publicar um evento, para garantir identidades do publicador independentes. Ao utilizar as políticas do publicador, o valor **PartitionKey** é definido para o nome do publicador. Para que funcionem corretamente, estes valores têm de corresponder.

## <a name="capture"></a>Recolha

[O Event Hubs Capture](event-hubs-capture-overview.md) permite-lhe capturar automaticamente os dados de streaming nos Centros de Eventos e guardá-los para a sua escolha de uma conta de armazenamento Blob ou de uma conta Azure Data Lake Service. Pode ativar a Captura a partir do portal Azure e especificar um tamanho e janela de tempo mínimos para realizar a captura. Utilizando a Captura de Centros de Eventos, especifica a sua própria conta e contentor Azure Blob, ou conta Azure Data Lake Service, uma das quais é usada para armazenar os dados capturados. Os dados capturados estão escritos no formato Apache Avro.

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokens SAS

Os Hubs de Eventos utilizam *Assinaturas de Acesso Partilhado*, que estão disponíveis no espaço de nomes e ao nível do hub de eventos. Um token SAS é gerado a partir de uma chave SAS e é um hash SHA de um URL, codificado num formato específico. Através do nome da chave (política) e do token, o Event Hubs pode regenerar o hash e, assim, autenticar o remetente. Normalmente, os tokens SAS de publicadores de eventos são criados apenas com privilégios de **envio** num hub de eventos específico. Este mecanismo de URLs do token SAS é a base para a identificação do publicador apresentada na política do publicador. Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de eventos

Qualquer entidade que leia os dados de eventos de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos ligam-se através da sessão AMQP 1.0 e os eventos são entregues durante a sessão à medida que ficam disponíveis. O cliente não necessita de consultar a disponibilidade dos dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/subscrição de Hubs de Eventos é ativado através de *grupos de consumidores*. Um grupo de consumidores é uma vista (estado, posição ou desvio) de um hub de eventos completo. Os grupos de consumidores ativam várias aplicações de consumo e cada uma tem uma vista separada do fluxo de eventos e lê o fluxo de forma independente ao seu próprio ritmo e com os seus próprios desvios.

Na arquitetura de processamento de transmissão, cada aplicação a jusante equaciona um grupo de consumidores. Se pretender escrever dados de eventos para armazenamento de longa duração, essa aplicação de escrita de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode ser efetuado por outro grupo de consumidores, em separado. Só pode aceder a partições através de um grupo de consumidores. Há sempre um grupo de consumidores predefinido num hub de eventos e pode criar até 20 grupos de consumidores para um hub de eventos de camada Standard.

Pode haver, no máximo, 5 leitores simultâneos numa partição por grupo de consumidores; no **entanto, recomenda-se que exista apenas um recetor ativo numa partição por grupo de consumidores.** Dentro de uma única divisória, cada leitor recebe todas as mensagens. Se tiver vários leitores na mesma partição, então processa mensagens duplicadas. Tens de lidar com isto no teu código, o que pode não ser trivial. No entanto, é uma abordagem válida em alguns cenários.

Alguns clientes oferecidos pelos Azure SDKs são agentes de consumo inteligentes que gerem automaticamente os detalhes de garantir que cada partição tem um único leitor e que todas as divisórias para um centro de eventos estão sendo lidas. Isto permite que o seu código se concentre no processamento dos eventos que estão a ser lidos a partir do centro de eventos para que possa ignorar muitos dos detalhes das divisórias. Para obter mais informações, consulte [Connect to a partition](#connect-to-a-partition).

Os exemplos que se seguem mostram a convenção URI do grupo de consumidores:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

A imagem seguinte mostra a arquitetura do processamento de fluxos dos Hubs de Eventos:

![Arquitetura de Centros de Eventos](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Desvios de fluxo

Um *desvio* é a posição de um evento numa partição. Pode considerar um desvio igual a um cursor do lado do cliente. O desvio é uma numeração de bytes do evento. Este desvio permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual pretende começar a ler eventos. Pode especificar o desvio como um carimbo de data/hora ou como um valor de desvio. Os consumidores são responsáveis por armazenarem os seus próprios valores de desvio fora do serviço dos Event Hubs. Dentro de uma partição, cada evento inclui um desvio.

![Compensação de partição](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Pontos de verificação

O *ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam a respetiva posição dentro de uma sequência de eventos da partição. O ponto de verificação é da responsabilidade do consumidor e ocorre numa base por partição dentro de um grupo de consumidores. Esta responsabilidade significa que para cada grupo de consumidores, cada leitor da partição tem de manter um controlo da respetiva posição atual no fluxo de eventos e pode informar o serviço quando considera o fluxo de dados completo.

Se um leitor for desligado de uma partição, quando voltar a ser ligado, começa a leitura no ponto de verificação que foi previamente submetido pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor se conecta, passa a offset para o centro do evento para especificar o local onde começar a ler. Desta forma, pode utilizar o ponto de verificação para marcar os eventos como “concluídos” pelas aplicações a jusante e para fornecer resiliência se ocorrer uma ativação pós-falha entre os leitores em execução em computadores diferentes. É possível devolver dados mais antigos ao especificar um desvio inferior a partir deste processo de ponto de verificação. Através deste mecanismo, o ponto de verificação ativa a resiliência pós-falha e a repetição do fluxo de eventos.

> [!NOTE]
> Se estiver a utilizar o Azure Blob Storage como loja de checkpoint num ambiente que suporta uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure, terá de utilizar código para alterar a versão API do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se estiver a executar [Os Centros de Eventos numa versão Azure Stack Hub 2002](/azure-stack/user/event-hubs-overview), a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, é necessário utilizar o código para direcionar a versão API do serviço de armazenamento para 2017-11-09. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte estas amostras no GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) ou  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor

Todos os consumidores de Centros de Eventos conectam-se através de uma sessão AMQP 1.0, um canal de comunicação bidirecional consciente do estado. Cada partição tem uma sessão do AMQP 1.0 que facilita o transporte de eventos segregados pela partição.

#### <a name="connect-to-a-partition"></a>Ligar a uma partição

Ao ligar-se às divisórias, é prática comum usar um mecanismo de locação para coordenar as ligações dos leitores a divisórias específicas. Desta forma, é possível que cada divisória de um grupo de consumidores tenha apenas um leitor ativo. A verificação, o leasing e a gestão dos leitores são simplificados utilizando os clientes dentro dos SDKs do Event Hubs, que funcionam como agentes de consumo inteligentes. Esses avisos são:

- O [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) para .NET
- O [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) para Java
- [O EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) para Python
- O [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) para JavaScript/TypeScript

#### <a name="read-events"></a>Ler eventos

Depois de abrir uma sessão AMQP 1.0 e uma ligação para uma partição específica, os eventos são entregues para o cliente AMQP 1.0 pelo serviço de Event Hubs. Este mecanismo de entrega permite um maior débito e uma latência inferior do que os mecanismos baseados na solicitação como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados de eventos contém metadados importantes, como o número de sequência e desvio que são utilizados para facilitar o ponto de verificação numa sequência de eventos.

Dados do evento:
* Desvio
* Número de sequência
* Corpo
* Propriedades do utilizador
* Propriedades do sistema

É da sua responsabilidade gerir o desvio.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Introdução ao Event Hubs
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Amostras de Centros de Eventos](event-hubs-samples.md)
