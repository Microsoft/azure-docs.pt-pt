---
title: Compare as filas de armazenamento azure e as filas de ônibus de serviço
description: Analisa diferenças e semelhanças entre dois tipos de filas oferecidas pelo Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ffa98e511053edc75fd0e6f25f7b0e21ee9ddda0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414536"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas de armazenamento e filas de ônibus de serviço - comparadoe e contrastado
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidas hoje pelo Microsoft Azure: filas de armazenamento e filas de ônibus de serviço. Ao utilizar esta informação, pode comparar e comparar as respetivas tecnologias e ser capaz de tomar uma decisão mais informada sobre qual a solução que melhor satisfaz as suas necessidades.

## <a name="introduction"></a>Introdução
O Azure suporta dois tipos de mecanismos de fila: filas de **armazenamento** e filas de **autocarros de serviço.**

**As filas**de armazenamento, que fazem parte da infraestrutura de [armazenamento Azure,](https://azure.microsoft.com/services/storage/) dispõem de uma interface simples de GET/PUT/PEEK baseada em REST, fornecendo mensagens fiáveis e persistentes dentro e entre serviços.

**As filas** de ônibus de serviço fazem parte de uma infraestrutura de [mensagens Azure](https://azure.microsoft.com/services/service-bus/) mais ampla que suporta a fila, bem como publicar/subscrever, e padrões de integração mais avançados. Para mais informações sobre as filas/tópicos/subscrições do Service Bus, consulte a [visão geral do Service Bus](service-bus-messaging-overview.md).

Embora ambas as tecnologias de fila existam simultaneamente, as filas de armazenamento foram introduzidas primeiro, como um mecanismo dedicado de armazenamento de fila construído em cima dos serviços de Armazenamento Azure. As filas de ônibus de serviço são construídas em cima da infraestrutura de mensagens mais ampla projetada para integrar aplicações ou componentes de aplicações que podem abranger vários protocolos de comunicação, contratos de dados, domínios de confiança e/ou ambientes de rede.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia
Tanto as filas de armazenamento como as filas de ônibus de serviço são implementações do serviço de fila de mensagens atualmente oferecido pelo Microsoft Azure. Cada um tem um conjunto de funcionalidades ligeiramente diferente, o que significa que pode escolher um ou outro, ou usar ambos, dependendo das necessidades da sua solução específica ou problema negócio/técnico que está a resolver.

Ao determinar qual a tecnologia de fila que se adequa ao propósito de uma determinada solução, os arquitetos e desenvolvedores de soluções devem considerar estas recomendações. Para mais detalhes, consulte a próxima secção.

Como arquiteto/desenvolvedor de **soluções, deve considerar a utilização** de filas de armazenamento quando:

* A sua aplicação deve armazenar mais de 80 GB de mensagens numa fila.
* A sua aplicação quer acompanhar o progresso para processar uma mensagem dentro da fila. Isto é útil se o trabalhador processar uma mensagem falhar. Um trabalhador subsequente pode então utilizar essa informação para continuar a partir do local onde o trabalhador anterior deixou de lado.
* Você precisa de registos laterais do servidor de todas as transações executadas contra as suas filas.

Como arquiteto/desenvolvedor de **soluções, deve considerar a utilização** de filas de ônibus de serviço quando:

* A sua solução deve ser capaz de receber mensagens sem ter de fazer sondagens na fila. Com o Service Bus, isso pode ser conseguido através da utilização da operação de longa votação através dos protocolos baseados na TCP que o Bus de Serviço suporta.
* A sua solução requer que a fila forneça uma entrega garantida de primeira em primeiro lugar (FIFO).
* A sua solução deve ser capaz de suportar a deteção automática de duplicados.
* Pretende que a sua aplicação processe mensagens como streams paralelos de longa duração (as mensagens estão associadas a um fluxo utilizando a propriedade [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) na mensagem). Neste modelo, cada nó na aplicação consumista compete por streams, ao contrário das mensagens. Quando um fluxo é dado a um nó consumista, o nó pode examinar o estado do estado de fluxo de aplicação usando transações.
* A sua solução requer comportamento transacional e atómico ao enviar ou receber várias mensagens de uma fila.
* A sua aplicação lida com mensagens que podem exceder 64 KB, mas que provavelmente não se aproximarão do limite de 256 KB.
* Você lida com a exigência de fornecer um modelo de acesso baseado em papéis às filas, e diferentes direitos/permissões para remetentes e recetores. Para obter mais informações, veja os artigos seguintes:
    - [Autenticar com identidades geridas](service-bus-managed-service-identity.md)
    - [Autenticar a partir de uma aplicação](authenticate-application.md)
* O tamanho da fila não irá ultrapassar os 80 GB.
* Você quer usar o protocolo de mensagens baseado em padrões AMQP 1.0. Para mais informações sobre a AMQP, consulte a [visão geral do Bus de Serviço AMQP](service-bus-amqp-overview.md).
* Pode prever uma eventual migração da comunicação ponto-a-ponto baseada na fila para um padrão de troca de mensagens que permite a integração perfeita de recetores adicionais (assinantes), cada um dos quais recebe cópias independentes de algumas ou de todas as mensagens enviadas para a fila. Este último refere-se à capacidade de publicação/subscrição nativamente fornecida pela Service Bus.
* A sua solução de mensagens deve ser capaz de suportar a garantia de entrega "At-Most-Once" sem a necessidade de construir os componentes adicionais da infraestrutura.
* Quer publicar e consumir lotes de mensagens.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparar filas de armazenamento e filas de ônibus de serviço
As tabelas nas seguintes secções proporcionam um agrupamento lógico de funcionalidades de fila e permitem comparar, num ápice, as capacidades disponíveis tanto nas filas de armazenamento do Azure como nas filas de ônibus de serviço.

## <a name="foundational-capabilities"></a>Capacidades fundamentais
Esta secção compara algumas das capacidades fundamentais de fila fornecidas pelas filas de armazenamento e pelas filas de ônibus de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Garantia de encomenda |**Não** <br/><br>Para mais informações, consulte a primeira nota na secção "Informações Adicionais".</br> |**Sim - Primeiro-em-Primeiro-Out (FIFO)**<br/><br>(através da utilização de sessões de mensagens) |
| Garantia de entrega |**Pelo menos uma vez** |**Pelo menos uma vez** (utilizando o modo de receção PeekLock - este é o padrão) <br/><br/>**No mais uma vez** (usando o modo receiveanddelete) <br/> <br/> Saiba mais sobre [vários modos Receber](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Suporte à operação atómica |**Não** |**Sim**<br/><br/> |
| Receber comportamento |**Não bloqueio**<br/><br/>(completa imediatamente se não for encontrada nova mensagem) |**Bloqueio com/sem tempo**<br/><br/>(oferece sondagens longas, ou a ["técnica do Cometa"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Não bloqueio**<br/><br/>(através da utilização de .NET gerido apenas API) |
| API estilo push |**Não** |**Sim**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) sessions .NET API. |
| Receber modo |**Peek & Arrendamento** |**Espreitar & Fechadura**<br/><br/>**Receber & Excluir** |
| Modo de acesso exclusivo |**Baseado em locação** |**Baseado em bloqueio** |
| Duração do arrendamento/bloqueio |**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Pode renovar ou lançar um contrato de locação de mensagens utilizando o [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 segundos (padrão)**<br/><br/>Pode renovar um bloqueio de mensagem utilizando o [API Renovador.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Precisão de arrendamento/bloqueio |**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo diferente, que pode então atualizar conforme necessário durante o processamento da mensagem, utilizando a [API UpdateMessage)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) |**Nível de fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas pode renovar o bloqueio utilizando o [API RenewLock.)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Receção lotada |**Sim**<br/><br/>(especificando explicitamente a contagem de mensagens na recuperação de mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(implicitamente permitindo um imóvel pré-busca ou explicitamente através da utilização de transações) |
| Envio em lotado |**Não** |**Sim**<br/><br/>(através da utilização de transações ou de lotação do lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* As mensagens nas filas de armazenamento são tipicamente a primeira a sair, mas às vezes podem estar fora de ordem; por exemplo, quando a duração do tempo de validade da mensagem expira (por exemplo, como resultado de uma falha na aplicação do cliente durante o processamento). Quando o tempo de validade da visibilidade expira, a mensagem torna-se visível novamente na fila para que outro trabalhador a desfaça. Nessa altura, a mensagem recém-visível pode ser colocada na fila (para ser despacuada novamente) depois de uma mensagem que foi originalmente enquecida depois dela.
* O padrão FIFO garantido nas filas de ônibus de serviço requer o uso de sessões de mensagens. No caso de a aplicação se falhar durante o processamento de uma mensagem recebida no modo **Peek & Lock,** da próxima vez que um recetor de fila aceitar uma sessão de mensagens, começará com a mensagem falhada após o termo do prazo de vida (TTL).
* As filas de armazenamento são projetadas para suportar cenários de fila padrão, tais como dissociar componentes de aplicação para aumentar a escalabilidade e tolerância para falhas, nivelamento de carga e fluxos de trabalho de processo de construção.
* As inconsistências no que diz respeito ao tratamento de mensagens no contexto das sessões de ônibus de serviço podem ser evitadas usando o estado de sessão para armazenar o estado da aplicação em relação ao progresso do manuseamento da sequência de mensagens da sessão, e usando transações em torno de regularizar mensagens recebidas e atualizar o estado da sessão. Este tipo de funcionalidade de consistência é por vezes rotulado *exactamente-uma vez processamento* em outros produtos do fornecedor, mas falhas de transação obviamente fazem com que as mensagens sejam reentregues e, portanto, o termo não é exatamente adequado.
* As filas de armazenamento proporcionam um modelo de programação uniforme e consistente através de filas, mesas e BLOBs – tanto para desenvolvedores como para equipas de operações.
* As filas de ônibus de serviço fornecem suporte para transações locais no contexto de uma única fila.
* O modo **Receber e Eliminar** suportado pelo Service Bus fornece a capacidade de reduzir a contagem de operações de mensagens (e custo associado) em troca de uma garantia de entrega reduzida.
* As filas de armazenamento proporcionam arrendamentos com a capacidade de estender os contratos de arrendamento para mensagens. Isto permite que os trabalhadores mantenham arrendamentos curtos em mensagens. Assim, se um trabalhador se despenhar, a mensagem pode ser rapidamente processada novamente por outro trabalhador. Além disso, um trabalhador pode estender o contrato de arrendamento por mensagem se precisar de processá-lo mais tempo do que o tempo de arrendamento atual.
* As filas de armazenamento oferecem um tempo de visibilidade que você pode definir sobre a enqueuing ou dequeuing de uma mensagem. Além disso, pode atualizar uma mensagem com diferentes valores de arrendamento no tempo de execução e atualizar diferentes valores através de mensagens na mesma fila. Os tempos de bloqueio do autocarro de serviço são definidos nos metadados da fila; no entanto, pode renovar o bloqueio chamando o método [RenewLock.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)
* O prazo máximo para um bloqueio recebe a operação nas filas de autocarros de serviço é de 24 dias. No entanto, os tempos de repouso têm um valor máximo de 55 segundos.
* O lote do lado do cliente fornecido pela Service Bus permite que um cliente de fila embote várias mensagens numa única operação de envio. O lote só está disponível para operações de envio assíncronos.
* Funcionalidades como o teto de 200 TB das filas de armazenamento (mais quando virtualiza contas) e filas ilimitadas fazem dela uma plataforma ideal para os fornecedores saaS.
* As filas de armazenamento fornecem um mecanismo flexível e performante de controlo de acesso delegado.

## <a name="advanced-capabilities"></a>Capacidades avançadas
Esta secção compara as capacidades avançadas fornecidas pelas filas de armazenamento e pelas filas de ônibus de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Letras automáticas mortas |**Não** |**Sim** |
| Aumento do valor tempo-a-viver da fila |**Sim**<br/><br/>(através da atualização do tempo de tempo de visibilidade) |**Sim**<br/><br/>(fornecido através de uma função API dedicada) |
| Suporte de mensagem venenosa |**Sim** |**Sim** |
| Atualização no local |**Sim** |**Sim** |
| Registo de transações do lado do servidor |**Sim** |**Não** |
| Métricas de armazenamento |**Sim**<br/><br/>**Métricas minúsculas**: fornece métricas em tempo real para disponibilidade, TPS, contagens de chamadas API, contagens de erros, e muito mais, tudo em tempo real (agregado por minuto e relatado dentro de poucos minutos do que acabou de acontecer na produção. Para mais informações, consulte sobre as [métricas de análise](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)de armazenamento . |**Sim**<br/><br/>(consultas a granel, chamando [GetQueues)](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) |
| Gestão de estados |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Ative](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Reencaminhamento automático de mensagens |**Não** |**Sim** |
| Função de fila de purga |**Sim** |**Não** |
| Grupos de mensagens |**Não** |**Sim**<br/><br/>(através da utilização de sessões de mensagens) |
| Estado de aplicação por grupo de mensagens |**Não** |**Sim** |
| Deteção de duplicados |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Grupos de mensagens de navegação |**Não** |**Sim** |
| Sessões de mensagens por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de fila permitem que uma mensagem seja agendada para entrega mais tarde.
* O reencaminhamento automático da fila permite que milhares de filas reencaminhom automaticamente as suas mensagens para uma única fila, a partir da qual a aplicação recetora consome a mensagem. Pode utilizar este mecanismo para alcançar a segurança, controlar o fluxo e isolar o armazenamento entre cada editor de mensagens.
* As filas de armazenamento fornecem suporte para atualizar o conteúdo da mensagem. Pode utilizar esta funcionalidade para persistir informações do Estado e atualizações incrementais de progresso na mensagem para que possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com as filas de ônibus de serviço, pode ativar o mesmo cenário através da utilização de sessões de mensagens. As sessões permitem-lhe guardar e recuperar o estado de processamento de aplicações (utilizando [o SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [o GetState).](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)
* [As letras mortas](service-bus-dead-letter-queues.md), que só são suportadas por filas de autocarros de serviço, podem ser úteis para isolar mensagens que não podem ser processadas com sucesso pela aplicação recetora ou quando as mensagens não podem chegar ao seu destino devido a uma propriedade expirada no tempo de vida (TTL). O valor TTL especifica quanto tempo uma mensagem permanece na fila. Com o Service Bus, a mensagem será transferida para uma fila especial chamada $DeadLetterQueue quando o período TTL expirar.
* Para encontrar mensagens "venenosas" nas filas de armazenamento, ao desafogar uma mensagem, a aplicação examina a propriedade [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) da mensagem. Se o **DequeueCount** for superior a um determinado limiar, a aplicação move a mensagem para uma fila de "letra morta" definida pela aplicação.
* As filas de armazenamento permitem-lhe obter um registo detalhado de todas as transações executadas contra a fila, bem como métricas agregadas. Ambas as opções são úteis para depurar e entender como a sua aplicação utiliza as filas de armazenamento. Também são úteis para afinar o desempenho da sua aplicação e reduzir os custos de utilização de filas.
* O conceito de "sessões de mensagens" suportadas pelo Service Bus permite que mensagens que pertencem a um determinado grupo lógico sejam associadas a um determinado recetor, o que, por sua vez, cria uma afinidade semelhante a uma sessão entre mensagens e respetivos recetores. Pode ativar esta funcionalidade avançada no Service Bus, definindo a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) numa mensagem. Os recetores podem então ouvir um ID de sessão específico e receber mensagens que partilhem o identificador de sessão especificado.
* A funcionalidade de deteção de duplicação suportada pelas filas de ônibus de serviço remove automaticamente mensagens duplicadas enviadas para uma fila ou tópico, com base no valor da propriedade [MessageId.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)

## <a name="capacity-and-quotas"></a>Capacidade e quotas
Esta secção compara as filas de armazenamento e as filas de ônibus de serviço na perspetiva da [capacidade e das quotas](service-bus-quotas.md) que podem ser aplicadas.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a uma única capacidade de conta de [armazenagem)](../storage/common/storage-introduction.md#queue-storage) |**1 GB a 80 GB**<br/><br/>(definida aquando da criação de uma fila e [da partilha de vias](service-bus-partitioning.md) – ver secção "Informação Adicional") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao utilizar a codificação **base64)**<br/><br/>O Azure suporta grandes mensagens combinando filas e bolhas – altura em que pode enverar até 200 GB para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo o cabeçalho e o corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende do [nível](service-bus-premium-messaging.md)de serviço. |
| Mensagem máxima TTL |**Infinito** (a partir da api-versão 2017-07-27) |**Timespan.Max** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por espaço de nome de serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**Ilimitado**<br/><br/>(100 limite de ligação simultâneo apenas se aplica à comunicação protocolar tCP) |

### <a name="additional-information"></a>Informações adicionais
* O ônibus de serviço impõe limites de tamanho da fila. O tamanho máximo da fila é especificado após a criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor do tamanho da fila definido na criação da fila for atingido, as mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para mais informações sobre quotas em Service Bus, consulte Quotas de [Ônibus de Serviço.](service-bus-quotas.md)
* A partilha não é suportada no [nível Premium](service-bus-premium-messaging.md). No nível Standard, pode criar filas de ônibus de serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é de 1 GB). No nível Standard, com a partilha ativada (que é o padrão), o Service Bus cria 16 divisórias para cada GB que especifica. Como tal, se criar uma fila de 5 GB de tamanho, com 16 divisórias o tamanho máximo da fila torna-se (5 * 16) = 80 GB. Pode ver o tamanho máximo da sua fila ou tópico dividido, olhando para a sua entrada no [portal Azure][Azure portal].
* Com as filas de armazenamento, se o conteúdo da mensagem não for seguro XML, então deve ser codificado pelo **Base64.** Se o **Base64**codificar a mensagem, a carga útil do utilizador pode chegar a 48 KB, em vez de 64 KB.
* Com as filas de ônibus de serviço, cada mensagem armazenada numa fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo da mensagem suportado pelo nível de serviço.
* Quando os clientes comunicam com as filas de ônibus de serviço sobre o protocolo TCP, o número máximo de ligações simultâneas a uma única fila de ônibus de serviço é limitado a 100. Este número é partilhado entre remetentes e recetores. Se esta quota for atingida, os pedidos subsequentes de ligações adicionais serão rejeitados e uma exceção será recebida pelo código de chamada. Este limite não é imposto aos clientes que se ligam às filas utilizando API baseado em REST.
* Se necessitar de mais de 10.000 filas num único espaço de nome do Ônibus de Serviço, pode contactar a equipa de suporte do Azure e solicitar um aumento. Para escalar para além de 10.000 filas com o Service Bus, também pode criar espaços de nome adicionais usando o [portal Azure][Azure portal].

## <a name="management-and-operations"></a>Gestão e operações
Esta secção compara as funcionalidades de gestão fornecidas pelas filas de armazenamento e pelas filas de ônibus de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Protocolo de gestão |**DESCANSE em HTTP/HTTPS** |**DESCANSE em HTTPS** |
| Protocolo de tempo de execução |**DESCANSE em HTTP/HTTPS** |**DESCANSE em HTTPS**<br/><br/>**Norma AMQP 1.0 (TCP com TLS)** |
| API .NET |**Sim**<br/><br/>(.NET Storage Client API) |**Sim**<br/><br/>(.NET Service Bus API) |
| C++ nativo |**Sim** |**Sim** |
| API de Java |**Sim** |**Sim** |
| PHP API |**Sim** |**Sim** |
| Node.js API |**Sim** |**Sim** |
| Suporte arbitrário de metadados |**Sim** |**Não** |
| Regras de nomeação de fila |**Até 63 caracteres de comprimento**<br/><br/>(As letras num nome de fila devem ser minúsculas.) |**Até 260 caracteres de comprimento**<br/><br/>(Os caminhos de fila e os nomes são insensíveis aos casos.) |
| Obtenha função de comprimento da fila |**Sim**<br/><br/>(Valor aproximado se as mensagens expirarem para além do TTL sem serem eliminadas.) |**Sim**<br/><br/>(Valor exato, pontual.) |
| Função peek |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* As filas de armazenamento fornecem suporte para atributos arbitrários que podem ser aplicados à descrição da fila, sob a forma de pares de nome/valor.
* Ambas as tecnologias de fila oferecem a capacidade de espreitar uma mensagem sem ter que bloqueá-la, o que pode ser útil na implementação de uma ferramenta de explorador/navegador de fila.
* O Bus de Serviço .NET intermediou as APIs de mensagens com capacidade suportantes para melhorar o desempenho em comparação com o REST over HTTP, e suportam o protocolo padrão AMQP 1.0.
* Os nomes das filas de armazenamento podem ter 3-63 caracteres de comprimento, podem conter letras minúsculas, números e hífenes. Para mais informações, consulte [Filas de Nomeação e Metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Os nomes da fila de ônibus de serviço podem ter até 260 caracteres de comprimento e têm regras de nomeação menos restritivas. Os nomes da fila de ônibus de serviço podem conter letras, números, períodos, hífenes e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta secção discute as funcionalidades de autenticação e autorização suportadas por filas de armazenamento e filas de ônibus de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Autenticação |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado através de tokens SAS. |SAS |
| Federação de Fornecedores de Identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Todos os pedidos a qualquer uma das tecnologias de fila devem ser autenticados. As filas públicas com acesso anónimo não são suportadas. Utilizando [o SAS,](service-bus-sas.md)pode abordar este cenário publicando um SAS apenas por escrito, sas de leitura ou até mesmo um SAS de acesso completo.
* O esquema de autenticação fornecido pelas filas de armazenamento envolve o uso de uma chave simétrica, que é um Código de Autenticação de Mensagens (HMAC) baseado em hash, computado com o algoritmo SHA-256 e codificado como uma cadeia **Base64.** Para mais informações sobre o respetivo protocolo, consulte [autenticação para os Serviços de Armazenamento Azure.](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services) As filas de ônibus de serviço suportam um modelo semelhante usando chaves simétricas. Para mais informações, consulte autenticação de assinatura de [acesso partilhado com ônibus de serviço](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Ao obter uma compreensão mais profunda das duas tecnologias, poderá tomar uma decisão mais informada sobre qual a tecnologia da fila a utilizar e quando. A decisão sobre quando usar as filas de armazenamento ou as filas de ônibus de serviço depende claramente de uma série de fatores. Estes fatores podem depender fortemente das necessidades individuais da sua aplicação e da sua arquitetura. Se a sua aplicação já utilizar as capacidades centrais do Microsoft Azure, poderá preferir escolher as filas de Armazenamento, especialmente se necessitar de comunicação básica e mensagens entre serviços ou precisar de filas superiores a 80 GB de tamanho.

Uma vez que as filas de ônibus de serviço fornecem uma série de funcionalidades avançadas, tais como sessões, transações, deteção duplicada, letras automáticas mortas e capacidades de publicação/subscrição duráveis, podem ser uma escolha preferida se estiver a construir uma aplicação híbrida ou se a sua aplicação de outra forma necessitar destas funcionalidades.

## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos fornecem mais orientação e informações sobre a utilização de filas de armazenamento ou filas de ônibus de serviço.

* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Boas práticas para melhorias de desempenho usando mensagens mediadas service Bus](service-bus-performance-improvements.md)
* [Introdução de Filas e Tópicos no Ônibus de Serviço Azure (post de blog)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [O Guia do Desenvolvedor para o Ônibus de Serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Utilização do Serviço de Fila em Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

