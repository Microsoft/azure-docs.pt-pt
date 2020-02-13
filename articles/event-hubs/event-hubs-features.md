---
title: Descrição geral das funcionalidades - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece detalhes sobre os recursos e terminologia dos Hubs de eventos do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157181"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funcionalidades e a terminologia nos Hubs de eventos do Azure

Os Hubs de eventos do Azure é uma serviço que ingere e processa grandes volumes de eventos e dados, com baixa latência e alta fiabilidade de processamento de eventos escalável. Vê [o que é O Event Hubs](event-hubs-what-is-event-hubs.md) para uma visão geral de alto nível.

Este artigo baseia-se na informação no artigo de [visão geral,](event-hubs-what-is-event-hubs.md)e fornece detalhes técnicos e de implementação sobre componentes e funcionalidades do Event Hubs.

## <a name="namespace"></a>Espaço de nomes
Um espaço de nome de Event Hubs fornece um recipiente de digitalização único, referenciado pelo seu nome de [domínio totalmente qualificado,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)no qual cria um ou mais centros de eventos ou tópicos de Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Hubs de Eventos para o Apache Kafka

[Esta funcionalidade](event-hubs-for-kafka-ecosystem-overview.md) fornece um ponto final que permite aos clientes falar com os Event Hubs usando o protocolo Kafka. Esta integração fornece aos clientes um ponto de extremidade do Kafka. Isto permite aos clientes configurar seus aplicativos existentes do Kafka para comunicar com os Hubs de eventos, fornecendo uma alternativa à execução de seus próprios clusters do Kafka. Os Hubs de eventos para o Apache Kafka suporta os protocolos de Kafka 1.0 e posterior. 

Com esta integração, você não precisa executar aglomerados kafka ou geri-los com Zookeeper. Isso também permite que trabalhe com alguns dos recursos mais exigentes de Hubs de eventos, como a capturar, a ampliação automática de mensagens em fila e a recuperação após desastre geográfico.

Esta integração também permite que as aplicações, como criador de espelho ou estrutura como o Kafka ligar trabalhar clusterless apenas alterações de configuração. 

## <a name="event-publishers"></a>Publicadores de eventos

Qualquer entidade que envie dados para um centro de eventos é um produtor de eventos, ou editor de *eventos.* Os publicadores de eventos podem publicar eventos através de HTTPS ou AMQP 1.0 ou Kafka 1.0 e posterior. Os publicadores de eventos utilizam um token de Assinatura de Acesso Partilhado (SAS) para se identificarem a si próprios perante um hub de eventos e podem ter uma identidade exclusiva ou utilizar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento

Pode publicar um evento através de AMQP 1.0, Kafka 1.0 (e posterior) ou HTTPS. O Event Hubs fornece [bibliotecas e aulas](event-hubs-dotnet-framework-api-overview.md) de clientes para a publicação de eventos a um centro de eventos de clientes .NET. Para outros tempos de execução e plataformas, pode utilizar qualquer cliente AMQP 1.0, como o [Apache Qpid](https://qpid.apache.org/). Pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados de eventos) tem um limite de 1 MB, independentemente de ser um evento simples ou um lote. Publicar eventos maiores do que este resultados de limiar num erro. É preferível os publicadores não terem conhecimento das partições dentro do hub de eventos e apenas especificarem uma *chave de partição* (apresentada na secção seguinte) ou a sua identidade através do respetivo token SAS.

A opção para utilizar AMQP ou HTTPS é específica do cenário de utilização. O AMQP requer o estabelecimento de um socket bidirecional persistente, para além da segurança de nível do transporte (TLS) ou SSL/TLS. O AMQP tem custos de rede superiores ao inicializar a sessão. No entanto, o HTTPS requer sobrecarga SSL adicional para cada pedido. O AMQP tem um desempenho superior para publicadores frequentes.

![Hubs de Eventos](./media/event-hubs-features/partition_keys.png)

Os Hubs de Eventos garantem que todos os eventos que partilham o valor de chave de partição sejam entregues por ordem e para a mesma partição. Se as chaves de partição forem utilizadas com as políticas do publicador, a identidade do publicador e o valor da chave de partição devem coincidir. Caso contrário, ocorrerá um erro.

### <a name="publisher-policy"></a>Política do publicador

Os Event Hubs permitem um controlo granular sobre os publicadores de eventos através de *políticas do publicador*. As políticas do publicador são funcionalidades de tempo de execução concebidas para facilitar um elevado número de publicadores de eventos independentes. Com as políticas do publicador, cada publicador utiliza o seu próprio identificador exclusivo quando publica eventos num hub de eventos, utilizando o mecanismo seguinte:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Não precisa de criar os nomes dos publicadores com antecedência, mas devem corresponder ao token SAS utilizado ao publicar um evento, para garantir identidades do publicador independentes. Ao utilizar as políticas do publicador, o valor **PartitionKey** é definido para o nome do publicador. Para que funcionem corretamente, estes valores têm de corresponder.

## <a name="capture"></a>Captura

A Captura de Hubs de [Eventos](event-hubs-capture-overview.md) permite-lhe capturar automaticamente os dados de streaming em Event Hubs e guardá-los à sua escolha de uma conta de armazenamento Blob ou de uma conta azure Data Lake Service. Pode ativar a captura do portal do Azure e especifique um tamanho mínimo e a janela de tempo para executar a captura. Utilizar a captura de Hubs de eventos, especificar sua própria conta de armazenamento de Blobs do Azure e um contentor ou uma conta de serviço do Azure Data Lake, um dos quais é utilizado para armazenar os dados capturados. Dados capturados são escritos no formato Apache Avro.

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokens SAS

Os Hubs de Eventos utilizam *Assinaturas de Acesso Partilhado*, que estão disponíveis no espaço de nomes e ao nível do hub de eventos. Um token SAS é gerado a partir de uma chave SAS e é um hash SHA de um URL, codificado num formato específico. Através do nome da chave (política) e do token, o Event Hubs pode regenerar o hash e, assim, autenticar o remetente. Normalmente, os tokens SAS de publicadores de eventos são criados apenas com privilégios de **envio** num hub de eventos específico. Este mecanismo de URLs do token SAS é a base para a identificação do publicador apresentada na política do publicador. Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de eventos

Qualquer entidade que leia os dados de eventos de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos ligam-se através da sessão AMQP 1.0 e os eventos são entregues durante a sessão à medida que ficam disponíveis. O cliente não necessita de consultar a disponibilidade dos dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/subscrição de Hubs de Eventos é ativado através de *grupos de consumidores*. Um grupo de consumidores é uma vista (estado, posição ou desvio) de um hub de eventos completo. Os grupos de consumidores ativam várias aplicações de consumo e cada uma tem uma vista separada do fluxo de eventos e lê o fluxo de forma independente ao seu próprio ritmo e com os seus próprios desvios.

Na arquitetura de processamento de transmissão, cada aplicação a jusante equaciona um grupo de consumidores. Se pretender escrever dados de eventos para armazenamento de longa duração, essa aplicação de escrita de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode ser efetuado por outro grupo de consumidores, em separado. Só pode aceder a partições através de um grupo de consumidores. Há sempre um grupo de consumidores predefinido num hub de eventos e pode criar até 20 grupos de consumidores para um hub de eventos de camada Standard.

Pode haver no máximo 5 leitores simultâneos numa partilha por grupo de consumidores; no **entanto, recomenda-se que exista apenas um recetor ativo numa partilha por grupo de consumidores.** Dentro de uma única partição, cada leitor recebe todas as mensagens. Se tiver vários leitores na mesma partição, em seguida, processa mensagens duplicadas. Terá de lidar com isso em seu código, que pode não ser trivial. No entanto, é uma abordagem válida em alguns cenários.


Apresentamos a seguir exemplos da convenção de URI do grupo de consumidores:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A imagem seguinte mostra a arquitetura do processamento de fluxos dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Desvios de fluxo

Um *desvio* é a posição de um evento numa partição. Pode considerar um desvio igual a um cursor do lado do cliente. O desvio é uma numeração de bytes do evento. Este desvio permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual pretende começar a ler eventos. Pode especificar o desvio como um carimbo de data/hora ou como um valor de desvio. Os consumidores são responsáveis por armazenarem os seus próprios valores de desvio fora do serviço dos Event Hubs. Dentro de uma partição, cada evento inclui um desvio.

![Hubs de Eventos](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ponto de verificação

O *ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam a respetiva posição dentro de uma sequência de eventos da partição. O ponto de verificação é da responsabilidade do consumidor e ocorre numa base por partição dentro de um grupo de consumidores. Esta responsabilidade significa que para cada grupo de consumidores, cada leitor da partição tem de manter um controlo da respetiva posição atual no fluxo de eventos e pode informar o serviço quando considera o fluxo de dados completo.

Se um leitor for desligado de uma partição, quando voltar a ser ligado, começa a leitura no ponto de verificação que foi previamente submetido pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor é novamente ligado, ele passa o deslocamento para o hub de eventos para especificar a localização na qual deve começar a ler. Desta forma, pode utilizar o ponto de verificação para marcar os eventos como “concluídos” pelas aplicações a jusante e para fornecer resiliência se ocorrer uma ativação pós-falha entre os leitores em execução em computadores diferentes. É possível devolver dados mais antigos ao especificar um desvio inferior a partir deste processo de ponto de verificação. Através deste mecanismo, o ponto de verificação ativa a resiliência pós-falha e a repetição do fluxo de eventos.

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor

Ligue todos os consumidores de Hubs de eventos por meio de uma sessão do AMQP 1.0, um canal de comunicação bidirecional com deteção de estado. Cada partição tem uma sessão do AMQP 1.0 que facilita o transporte de eventos segregados pela partição.

#### <a name="connect-to-a-partition"></a>Ligar a uma partição

Quando ligar a partições, é prática comum utilizar um mecanismo de locação para coordenar as ligações do leitor com partições específicas. Desta forma, é possível para cada partição num grupo de consumidores ter apenas um leitor ativo. O ponto de verificação, a locação e a gestão de leitores são simplificados através da classe [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) para clientes .NET. O Anfitrião do Processador de Eventos é um agente de consumidor inteligente.

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
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Guia de programação dos Event Hubs](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Amostras de Hubs de Eventos][]

[Amostras de Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
