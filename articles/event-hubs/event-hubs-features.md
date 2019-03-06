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
ms.openlocfilehash: a54c01385b06aeaf4c894677ea95262ab2135893
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456147"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funcionalidades e a terminologia nos Hubs de eventos do Azure

Os Hubs de eventos do Azure é uma serviço que ingere e processa grandes volumes de eventos e dados, com baixa latência e alta fiabilidade de processamento de eventos escalável. Ver [o que é o Hubs de eventos?](event-hubs-what-is-event-hubs.md) para uma visão geral.

Este artigo baseia-se nas informações dos [artigo de descrição geral](event-hubs-what-is-event-hubs.md)e fornece detalhes técnicos e de implementação sobre componentes de Hubs de eventos e recursos.

## <a name="namespace"></a>Espaço de Nomes
Um espaço de nomes de Hubs de eventos fornece um contentor de âmbito exclusivo, referenciado pela respetiva [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), em que cria um ou mais dos hubs de eventos ou tópicos do Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Hubs de Eventos para o Apache Kafka

[Esta funcionalidade](event-hubs-for-kafka-ecosystem-overview.md) fornece um ponto final que permite que os clientes comunicar com os Hubs de eventos utilizando o protocolo de Kafka. Esta integração fornece aos clientes um ponto de extremidade do Kafka. Isto permite aos clientes configurar seus aplicativos existentes do Kafka para comunicar com os Hubs de eventos, fornecendo uma alternativa à execução de seus próprios clusters do Kafka. Os Hubs de eventos para o Apache Kafka suporta os protocolos de Kafka 1.0 e posterior. 

Com esta integração, não precisa executar clusters de Kafka ou geri-las com Zookeeper. Isso também permite que trabalhe com alguns dos recursos mais exigentes de Hubs de eventos, como a capturar, a ampliação automática de mensagens em fila e a recuperação após desastre geográfico.

Esta integração também permite que as aplicações, como criador de espelho ou estrutura como o Kafka ligar trabalhar clusterless apenas alterações de configuração. 

## <a name="event-publishers"></a>Publicadores de eventos

Qualquer entidade que envie dados para um hub de eventos é um produtor de eventos, ou *publicador de eventos*. Os publicadores de eventos podem publicar eventos através de HTTPS ou AMQP 1.0 ou Kafka 1.0 e posterior. Os publicadores de eventos utilizam um token de Assinatura de Acesso Partilhado (SAS) para se identificarem a si próprios perante um hub de eventos e podem ter uma identidade exclusiva ou utilizar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento

Pode publicar um evento através de AMQP 1.0, Kafka 1.0 (e posterior) ou HTTPS. Os Event Hubs fornecem [bibliotecas de cliente e classes](event-hubs-dotnet-framework-api-overview.md) para publicar eventos para um hub de eventos a partir de clientes .NET. Para outros tempos de execução e plataformas, pode utilizar qualquer cliente AMQP 1.0, como o [Apache Qpid](http://qpid.apache.org/). Pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados de eventos) tem um limite de 1 MB, independentemente de ser um evento simples ou um lote. Publicar eventos maiores do que este resultados de limiar num erro. É preferível os publicadores não terem conhecimento das partições dentro do hub de eventos e apenas especificarem uma *chave de partição* (apresentada na secção seguinte) ou a sua identidade através do respetivo token SAS.

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

[Captura de Hubs de eventos](event-hubs-capture-overview.md) permite-lhe capturar os dados de transmissão em fluxo nos Hubs de eventos e guarde-o à sua escolha de uma conta de armazenamento de BLOBs ou uma conta do Azure Data Lake Service automaticamente. Pode ativar a captura do portal do Azure e especifique um tamanho mínimo e a janela de tempo para executar a captura. Utilizar a captura de Hubs de eventos, especificar sua própria conta de armazenamento de Blobs do Azure e um contentor ou uma conta de serviço do Azure Data Lake, um dos quais é utilizado para armazenar os dados capturados. Dados capturados são escritos no formato Apache Avro.

## <a name="partitions"></a>Partições

O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Hubs de Eventos](./media/event-hubs-features/partition.png)

Os Hubs de eventos retém os dados durante um período de retenção configurado aplicado a todas as partições de evento hub. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes.

![Hubs de Eventos](./media/event-hubs-features/multiple_partitions.png)

O número de partições é especificado durante a criação e deve ser entre 2 e 32. O número de partições não é alterável, pelo que deve considerar uma escala a longo prazo quando definir o número de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Pode aumentar o número de partições além de 32 ao contactar a equipa do Event Hubs.

Enquanto as partições serem identificáveis e poderem ser enviadas diretamente, enviando diretamente para uma partição não é recomendado. Em vez disso, pode utilizar construções de nível superior, apresentadas nas secções [Publicador de eventos](#event-publishers) e [Capacidade](#capacity). 

As partições são preenchidas com uma seqüência de dados de eventos que contêm o corpo do evento, uma matriz de propriedades definida pelo utilizador e metadados, como o desvio na partição e o respetivo número na sequência de transmissão.

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Chave de partição

Pode utilizar uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.

## <a name="sas-tokens"></a>Tokens SAS

Os Hubs de Eventos utilizam *Assinaturas de Acesso Partilhado*, que estão disponíveis no espaço de nomes e ao nível do hub de eventos. Um token SAS é gerado a partir de uma chave SAS e é um hash SHA de um URL, codificado num formato específico. Através do nome da chave (política) e do token, o Event Hubs pode regenerar o hash e, assim, autenticar o remetente. Normalmente, os tokens SAS de publicadores de eventos são criados apenas com privilégios de **envio** num hub de eventos específico. Este mecanismo de URLs do token SAS é a base para a identificação do publicador apresentada na política do publicador. Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de eventos

Qualquer entidade que leia os dados de eventos de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos ligam-se através da sessão AMQP 1.0 e os eventos são entregues durante a sessão à medida que ficam disponíveis. O cliente não necessita de consultar a disponibilidade dos dados.

### <a name="consumer-groups"></a>Grupos de consumidores

O mecanismo de publicação/subscrição de Hubs de Eventos é ativado através de *grupos de consumidores*. Um grupo de consumidores é uma vista (estado, posição ou desvio) de um hub de eventos completo. Os grupos de consumidores ativam várias aplicações de consumo e cada uma tem uma vista separada do fluxo de eventos e lê o fluxo de forma independente ao seu próprio ritmo e com os seus próprios desvios.

Na arquitetura de processamento de transmissão, cada aplicação a jusante equaciona um grupo de consumidores. Se pretender escrever dados de eventos para armazenamento de longa duração, essa aplicação de escrita de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode ser efetuado por outro grupo de consumidores, em separado. Só pode aceder a partições através de um grupo de consumidores. Há sempre um grupo de consumidores predefinido num hub de eventos e pode criar até 20 grupos de consumidores para um hub de eventos de camada Standard.

Pode haver no máximo 5 leitores simultâneos numa partição por grupo de consumidores No entanto **recomenda-se que existe apenas um recetor ativo numa partição por grupo de consumidores**. Dentro de uma única partição, cada leitor recebe todas as mensagens. Se tiver vários leitores na mesma partição, em seguida, processa mensagens duplicadas. Terá de lidar com isso em seu código, que pode não ser trivial. No entanto, é uma abordagem válida em alguns cenários.


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

## <a name="capacity"></a>Capacidade

O Event Hubs tem uma arquitetura paralela altamente escalável e existem vários fatores principais a considerar no tamanho e dimensionamento.

### <a name="throughput-units"></a>Unidades de débito

A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma única unidade de débito inclui a seguinte capacidade:

* Entrada: Até 1 MB por segundo ou 1000 eventos por segundo (o que acontecer primeiro).
* Saída: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir unidades de débito no **escala** painel dos espaços de nomes no [portal do Azure](https://portal.azure.com). Também pode gerir unidades de débito programaticamente usando a [APIs de Hubs de eventos](event-hubs-api-overview.md).

Unidades de débito são previamente adquiridas e são faturadas por hora. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Débito até 20 unidades podem ser adquiridas para um espaço de nomes de Hubs de eventos e são partilhadas entre todos os hubs de eventos nesse espaço de nomes.

Pode comprar mais unidades de débito em blocos de 20, até 100 unidades de débito, ao contactar o suporte do Azure. Além do limite, pode comprar blocos de 100 unidades de débito.

Recomendamos que equilibre as unidades de débito e as partições para alcançar a escala ideal. Uma única partição tem um mínimo de dimensionamento de uma unidade de débito. O número de unidades de débito deve ser inferior ou igual ao número de partições num hub de eventos.

Para obter informações detalhadas sobre os preços dos Hubs de Eventos, veja [Preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Hubs de Eventos][Event Hubs tutorial]
* [Guia de programação dos Event Hubs](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de eventos][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Exemplos de Hubs de eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
