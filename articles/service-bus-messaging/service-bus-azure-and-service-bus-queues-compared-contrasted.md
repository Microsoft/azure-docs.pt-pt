---
title: Filas do armazenamento do Azure e filas do barramento de serviço comparadas e contrastadas | Microsoft Docs
description: Analisa diferenças e semelhanças entre dois tipos de filas oferecidos pelo Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 32c903e5d469a9a3e7b98bd406b5512d752bb210
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69017799"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas de armazenamento e filas do barramento de serviço – comparações e contrastes
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidos pelo Microsoft Azure hoje: Filas de armazenamento e filas do barramento de serviço. A utilização destas informações permite-lhe comparar e contrastar as respetivas tecnologias, e tomar uma decisão mais informada quanto à solução que melhor responde às suas necessidades.

## <a name="introduction"></a>Introdução
O Azure dá suporte a dois tipos de mecanismos de fila: Filas de **armazenamento** e **filas do barramento de serviço**.

As **filas de armazenamento**, que fazem parte da infraestrutura de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) , apresentam uma interface simples get/Put/Peek baseada em REST, fornecendo mensagens confiáveis e persistentes dentro e entre serviços.

As **filas do barramento de serviço** fazem parte de uma infraestrutura mais ampla de [mensagens do Azure](https://azure.microsoft.com/services/service-bus/) que dá suporte ao enfileiramento, bem como publicação/assinatura e padrões de integração mais avançados. Para obter mais informações sobre filas/tópicos/assinaturas do barramento de serviço, consulte a [visão geral do barramento de serviço](service-bus-messaging-overview.md).

Embora ambas as tecnologias de enfileiramento existam simultaneamente, as filas de armazenamento foram introduzidas primeiro, como um mecanismo de armazenamento de fila dedicado criado com base nos serviços de armazenamento do Azure. As filas do barramento de serviço são criadas com base na infraestrutura de mensagens mais ampla projetada para integrar aplicativos ou componentes de aplicativos que podem abranger vários protocolos de comunicação, contratos de dados, domínios de confiança e/ou ambientes de rede.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia
As filas de armazenamento e as filas do barramento de serviço são implementações do serviço de enfileiramento de mensagens atualmente oferecido pelo Microsoft Azure. Cada um tem um conjunto de recursos um pouco diferente, o que significa que você pode escolher um ou outro, ou usar ambos, dependendo das necessidades de sua solução específica ou do problema comercial/técnico que está resolvendo.

Ao determinar qual tecnologia de enfileiramento se adapta à finalidade de uma determinada solução, os arquitetos e desenvolvedores de soluções devem considerar essas recomendações. Para obter mais detalhes, consulte a próxima seção.

Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso de filas de armazenamento** quando:

* Seu aplicativo deve armazenar mais de 80 GB de mensagens em uma fila.
* Seu aplicativo deseja acompanhar o progresso para processar uma mensagem dentro da fila. Isso será útil se o trabalho que está processando uma mensagem falhar. Um trabalho subsequente pode então usar essas informações para continuar de onde o trabalho anterior parou.
* Você precisa de logs do lado do servidor de todas as transações executadas em suas filas.

Como arquiteto/desenvolvedor de soluções, **você deve considerar o uso de filas do barramento de serviço** quando:

* Sua solução deve ser capaz de receber mensagens sem a necessidade de sondar a fila. Com o barramento de serviço, isso pode ser obtido por meio do uso da operação de recebimento de sondagem longa usando protocolos baseados em TCP aos quais o barramento de serviço dá suporte.
* Sua solução requer que a fila forneça uma entrega ordenada de FIFO (primeiro a entrar, primeiro a sair).
* Sua solução deve ser capaz de dar suporte à detecção automática de duplicidades.
* Você deseja que seu aplicativo processe mensagens como fluxos paralelos de execução longa (as mensagens são associadas a um fluxo usando a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) na mensagem). Nesse modelo, cada nó no aplicativo de consumo compete por fluxos, em oposição às mensagens. Quando um fluxo é fornecido a um nó de consumo, o nó pode examinar o estado do estado de fluxo do aplicativo usando transações.
* Sua solução requer comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.
* Seu aplicativo lida com mensagens que podem exceder 64 KB, mas provavelmente não abordará o limite de 256 KB.
* Você lida com um requisito para fornecer um modelo de acesso baseado em função às filas e direitos/permissões diferentes para remetentes e destinatários. Para obter mais informações, consulte [Active Directory controle de acesso baseado em função (versão prévia)](service-bus-role-based-access-control.md)
* O tamanho da fila não será maior que 80 GB.
* Você deseja usar o protocolo de mensagens com base em padrões AMQP 1,0. Para obter mais informações sobre AMQP, consulte [visão geral do AMQP do barramento de serviço](service-bus-amqp-overview.md).
* Você pode prever uma eventual migração da comunicação ponto a ponto baseada em fila para um padrão de troca de mensagens que permite a integração direta de receptores adicionais (assinantes), cada um dos quais recebe cópias independentes de alguns ou todos mensagens enviadas para a fila. O último se refere ao recurso de publicação/assinatura fornecido nativamente pelo barramento de serviço.
* Sua solução de mensagens deve ser capaz de dar suporte à garantia de entrega "no máximo uma vez" sem a necessidade de você criar os componentes de infraestrutura adicionais.
* Você gostaria de poder publicar e consumir lotes de mensagens.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparando filas de armazenamento e filas do barramento de serviço
As tabelas nas seções a seguir fornecem um agrupamento lógico de recursos de fila e permitem que você compare rapidamente os recursos disponíveis nas filas do armazenamento do Azure e nas filas do barramento de serviço.

## <a name="foundational-capabilities"></a>Recursos fundamentais
Esta seção compara alguns dos recursos fundamentais de enfileiramento fornecidos pelas filas de armazenamento e filas do barramento de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Garantia de pedidos |**Não** <br/><br>Para obter mais informações, consulte a primeira observação na seção "informações adicionais".</br> |**Sim, primeiro a entrar, primeiro a sair (FIFO)**<br/><br>(com o uso de sessões de mensagens) |
| Garantia de entrega |**Pelo menos uma vez** |**Pelo menos uma vez**<br/><br/>**No máximo uma vez** |
| Suporte a operações atômicas |**Não** |**Sim**<br/><br/> |
| Comportamento de recebimento |**Sem bloqueio**<br/><br/>(será concluído imediatamente se nenhuma mensagem nova for encontrada) |**Bloqueio com/sem tempo limite**<br/><br/>(oferece sondagem longa ou a ["técnica de Comet"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sem bloqueio**<br/><br/>(somente com o uso da API gerenciada do .NET) |
| API de estilo push |**Não** |**Sim**<br/><br/>[](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) API do .NET e sessões onMessage. |
| Modo de recebimento |**Espiar & concessão** |**Bloqueio de & de inspeção**<br/><br/>**Receber & Excluir** |
| Modo de acesso exclusivo |**Baseado em concessão** |**Baseado em bloqueio** |
| Duração da concessão/bloqueio |**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Você pode renovar ou liberar uma concessão de mensagem usando a API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) .) |**60 segundos (padrão)**<br/><br/>Você pode renovar um bloqueio de mensagem usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) . |
| Precisão da concessão/bloqueio |**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferente, que você pode atualizar conforme necessário durante o processamento da mensagem, usando a API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) ) |**Nível da fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas você pode renovar o bloqueio usando a API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .) |
| Recebimento em lote |**Sim**<br/><br/>(especificando explicitamente a contagem de mensagens ao recuperar mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(habilitando implicitamente uma propriedade de pré-busca ou explicitamente por meio do uso de transações) |
| Envio em lote |**Não** |**Sim**<br/><br/>(com o uso de transações ou envio em lote no lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* As mensagens nas filas de armazenamento são normalmente o primeiro a entrar, mas, às vezes, podem estar fora de ordem; por exemplo, quando a duração do tempo limite de visibilidade de uma mensagem expirar (por exemplo, como resultado de um aplicativo cliente falhar durante o processamento). Quando o tempo limite de visibilidade expira, a mensagem se torna visível novamente na fila de outro operador para remover a fila. Nesse ponto, a mensagem recentemente visível pode ser colocada na fila (para ser removida da fila novamente) após uma mensagem que foi originalmente enfileirada depois dela.
* O padrão de FIFO garantido nas filas do barramento de serviço requer o uso de sessões de mensagens. Caso o aplicativo falhe durante o processamento de uma mensagem recebida no modo de **bloqueio de & de Peek** , na próxima vez que um destinatário da fila aceitar uma sessão de mensagens, ele começará com a mensagem com falha após seu período de tempo de vida (TTL) expirar.
* As filas de armazenamento são projetadas para oferecer suporte a cenários de enfileiramento padrão, como dissociar componentes de aplicativos para aumentar a escalabilidade e a tolerância para falhas, nivelamento de carga e criação de fluxos de trabalho de processo.
* As filas do barramento de serviço dão suporte à garantia de entrega *pelo menos uma vez* . 
* Inconsistências em relação à manipulação de mensagens no contexto de sessões do barramento de serviço podem ser evitadas usando o estado de sessão para armazenar o estado do aplicativo em relação ao andamento da manipulação da sequência de mensagens da sessão e ao usar transações liquidar mensagens recebidas e atualizar o estado da sessão. Esse tipo de recurso de consistência, às vezes, é rotulado como *processamento exatamente uma vez* nos produtos de outros fornecedores, mas as falhas de transação certamente farão com que as mensagens sejam entregues novamente e, portanto, o termo não seja exatamente adequado.
* As filas de armazenamento fornecem um modelo de programação uniforme e consistente entre filas, tabelas e BLOBs – para desenvolvedores e para equipes de operações.
* As filas do barramento de serviço fornecem suporte para transações locais no contexto de uma única fila.
* O modo de **recebimento e exclusão** com suporte do barramento de serviço fornece a capacidade de reduzir a contagem de operações de mensagens (e o custo associado) no Exchange para garantir a garantia de entrega reduzida.
* As filas de armazenamento fornecem concessões com a capacidade de estender as concessões para mensagens. Isso permite que os trabalhadores mantenham concessões curtas em mensagens. Portanto, se um trabalho falhar, a mensagem poderá ser processada rapidamente novamente por outro operador. Além disso, um trabalhador pode estender a concessão em uma mensagem se precisar processá-la por mais tempo do que a hora de concessão atual.
* As filas de armazenamento oferecem um tempo limite de visibilidade que pode ser definido no enfileiramento ou na remoção da enfileiramento de uma mensagem. Além disso, você pode atualizar uma mensagem com valores de concessão diferentes em tempo de execução e atualizar valores diferentes entre as mensagens na mesma fila. Tempos limite de bloqueio do barramento de serviço são definidos nos metadados da fila; no entanto, você pode renovar o bloqueio chamando o método [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* O tempo limite máximo para uma operação de recebimento de bloqueio nas filas do barramento de serviço é de 24 dias. No entanto, os tempos limite baseados em REST têm um valor máximo de 55 segundos.
* O envio em lote do lado do cliente fornecido pelo barramento de serviço permite que um cliente de fila envie várias mensagens em lote em uma única operação de envio. O envio em lote só está disponível para operações de envio assíncronas.
* Recursos como o teto de 200 TB de filas de armazenamento (mais quando você virtualiza contas) e filas ilimitadas o tornam uma plataforma ideal para provedores de SaaS.
* As filas de armazenamento fornecem um mecanismo de controle de acesso delegado e flexível e de alto desempenho.

## <a name="advanced-capabilities"></a>Recursos avançados
Esta seção compara os recursos avançados fornecidos pelas filas de armazenamento e filas do barramento de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Mensagens mortas automáticas |**Não** |**Sim** |
| Aumento do valor de vida útil da fila |**Sim**<br/><br/>(por meio da atualização in-loco do tempo limite de visibilidade) |**Sim**<br/><br/>(fornecido por meio de uma função de API dedicada) |
| Suporte a mensagens suspeitas |**Sim** |**Sim** |
| Atualização in-loco |**Sim** |**Sim** |
| Log de transações do lado do servidor |**Sim** |**Não** |
| Métricas de armazenamento |**Sim**<br/><br/>**Métricas de minutos**: fornece métricas em tempo real para disponibilidade, TPS, contagens de chamada de API, contagens de erros e muito mais, tudo em tempo real (agregado por minuto e relatado em poucos minutos a partir do que acabou de acontecer na produção. Para obter mais informações, consulte [sobre métricas de análise de armazenamento](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Sim**<br/><br/>(consultas em massa chamando [](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)getqueues) |
| Gestão de estados |**Não** |**Sim**<br/><br/>[Microsoft. ServiceBus. Messaging. as. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. as. Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. as. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [ Microsoft. ServiceBus. Messaging. as. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Encaminhamento automático de mensagem |**Não** |**Sim** |
| Função limpar fila |**Sim** |**Não** |
| Grupos de mensagens |**Não** |**Sim**<br/><br/>(com o uso de sessões de mensagens) |
| Estado do aplicativo por grupo de mensagens |**Não** |**Sim** |
| Deteção de duplicados |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Procurando grupos de mensagens |**Não** |**Sim** |
| Buscando sessões de mensagens por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de enfileiramento permitem que uma mensagem seja agendada para entrega em um momento posterior.
* O encaminhamento automático da fila permite que milhares de filas encaminhem automaticamente suas mensagens para uma única fila, da qual o aplicativo de recebimento consome a mensagem. Você pode usar esse mecanismo para obter segurança, controlar o fluxo e isolar o armazenamento entre cada editor de mensagem.
* As filas de armazenamento fornecem suporte para atualizar o conteúdo da mensagem. Você pode usar essa funcionalidade para manter informações de estado persistentes e atualizações de progresso incremental na mensagem para que ela possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com as filas do barramento de serviço, você pode habilitar o mesmo cenário por meio do uso de sessões de mensagens. As sessões permitem salvar e recuperar o estado de processamento do aplicativo (usando [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* As mensagens [mortas](service-bus-dead-letter-queues.md), que só têm suporte nas filas do barramento de serviço, podem ser úteis para isolar os emails que não podem ser processados com êxito pelo aplicativo de recebimento ou quando as mensagens não podem alcançar seu destino devido a um tempo de vida (TTL) expirado Propriedade. O valor TTL especifica quanto tempo uma mensagem permanece na fila. Com o barramento de serviço, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período de TTL expirar.
* Para localizar mensagens "suspeitas" em filas de armazenamento, ao remover uma mensagem da fila, o aplicativo examina a propriedade [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) da mensagem. Se **DequeueCount** for maior que um determinado limite, o aplicativo moverá a mensagem para uma fila de "mensagens mortas" definida pelo aplicativo.
* As filas de armazenamento permitem que você obtenha um log detalhado de todas as transações executadas na fila, bem como métricas agregadas. Essas duas opções são úteis para depurar e entender como seu aplicativo usa filas de armazenamento. Eles também são úteis para o ajuste de desempenho de seu aplicativo e a redução dos custos de uso de filas.
* O conceito de "sessões de mensagens" com suporte no barramento de serviço permite que as mensagens que pertencem a um determinado grupo lógico sejam associadas a um determinado destinatário, que, por sua vez, cria uma afinidade de sessão entre as mensagens e seus respectivos destinatários. Você pode habilitar essa funcionalidade avançada no barramento de serviço definindo a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) em uma mensagem. Os receptores podem escutar em uma ID de sessão específica e receber mensagens que compartilham o identificador de sessão especificado.
* A funcionalidade de detecção de duplicação com suporte das filas do barramento de serviço remove automaticamente as mensagens duplicadas enviadas a uma fila ou tópico, com base no valor da propriedade [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Capacidade e cotas
Esta seção compara as filas de armazenamento e as filas do barramento de serviço da perspectiva de [capacidade e cotas](service-bus-quotas.md) que podem ser aplicáveis.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a uma [única capacidade de conta de armazenamento](../storage/common/storage-introduction.md#queue-storage)) |**1 GB a 80 GB**<br/><br/>(definido na criação de uma fila e [habilitando o particionamento](service-bus-partitioning.md) – consulte a seção "informações adicionais") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao usar a codificação **Base64** )<br/><br/>O Azure dá suporte a mensagens grandes combinando filas e blobs – nesse ponto, você pode enfileirar até 200 GB para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo cabeçalho e corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende da [camada de serviço](service-bus-premium-messaging.md). |
| TTL máximo da mensagem |**Infinito** (a partir da versão de API 2017-07-27) |**TimeSpan.Max** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por namespace de serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**Ilimitado**<br/><br/>(100 limite de conexão simultânea só se aplica à comunicação baseada em protocolo TCP) |

### <a name="additional-information"></a>Informações adicionais
* O barramento de serviço impõe limites de tamanho de fila. O tamanho máximo da fila é especificado na criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor do tamanho da fila definido na criação da fila for atingido, mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para obter mais informações sobre cotas no barramento de serviço, consulte cotas do [barramento de serviço](service-bus-quotas.md).
* O particionamento não tem suporte na [camada Premium](service-bus-premium-messaging.md). Na camada Standard, você pode criar filas do barramento de serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Na camada Standard, com o particionamento habilitado (que é o padrão), o barramento de serviço cria 16 partições para cada GB que você especificar. Assim, se você criar uma fila com 5 GB de tamanho, com 16 partições, o tamanho máximo da fila se tornará (5 * 16) = 80 GB. Você pode ver o tamanho máximo da fila ou do tópico particionado examinando sua entrada na [portal do Azure][Azure portal].
* Com as filas de armazenamento, se o conteúdo da mensagem não for XML seguro, ele deverá ser codificado em **Base64** . Se você codificar a mensagem em **Base64**, a carga do usuário poderá ser de até 48 KB, em vez de 64 KB.
* Com as filas do barramento de serviço, cada mensagem armazenada em uma fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo de mensagem suportado pela camada de serviço.
* Quando os clientes se comunicam com filas do barramento de serviço por meio do protocolo TCP, o número máximo de conexões simultâneas com uma única fila do barramento de serviço é limitado a 100. Esse número é compartilhado entre remetentes e destinatários. Se essa cota for atingida, as solicitações subsequentes para conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Esse limite não é imposto em clientes que se conectam às filas usando a API baseada em REST.
* Se você precisar de mais de 10.000 filas em um único namespace do barramento de serviço, poderá entrar em contato com a equipe de suporte do Azure e solicitar um aumento. Para dimensionar além de 10.000 filas com o barramento de serviço, você também pode criar namespaces adicionais usando o [portal do Azure][Azure portal].

## <a name="management-and-operations"></a>Gerenciamento e operações
Esta seção compara os recursos de gerenciamento fornecidos pelas filas de armazenamento e filas do barramento de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Protocolo de gerenciamento |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS** |
| Protocolo de tempo de execução |**REST sobre HTTP/HTTPS** |**REST sobre HTTPS**<br/><br/>**AMQP 1,0 padrão (TCP com TLS)** |
| API .NET |**Sim**<br/><br/>(API do cliente de armazenamento do .NET) |**Sim**<br/><br/>(API do barramento de serviço do .NET) |
| FormaC++ |**Sim** |**Sim** |
| API de Java |**Sim** |**Sim** |
| API DO PHP |**Sim** |**Sim** |
| API do node. js |**Sim** |**Sim** |
| Suporte a metadados arbitrários |**Sim** |**Não** |
| Regras de nomenclatura de fila |**Até 63 caracteres de comprimento**<br/><br/>(As letras em um nome de fila devem estar em minúsculas.) |**Até 260 caracteres de comprimento**<br/><br/>(Os nomes e caminhos de fila não diferenciam maiúsculas de minúsculas.) |
| Função obter comprimento da fila |**Sim**<br/><br/>(Valor aproximado se as mensagens expirarem além da TTL sem serem excluídas.) |**Sim**<br/><br/>(Valor exato, pontual). |
| Função Peek |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* As filas de armazenamento fornecem suporte para atributos arbitrários que podem ser aplicados à descrição da fila, na forma de pares de nome/valor.
* Ambas as tecnologias de fila oferecem a capacidade de inspecionar uma mensagem sem precisar bloqueá-la, o que pode ser útil ao implementar uma ferramenta de Gerenciador/navegador de filas.
* As APIs de mensagens orientadas do .NET do barramento de serviço utilizam conexões TCP Full duplex para melhorar o desempenho em comparação com REST sobre HTTP e dão suporte ao protocolo padrão AMQP 1,0.
* Os nomes das filas de armazenamento podem ter de 3-63 a caracteres de comprimento, podem conter letras minúsculas, números e hifens. Para obter mais informações, consulte [nomeando filas e metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Os nomes de fila do barramento de serviço podem ter até 260 caracteres de comprimento e ter regras de nomenclatura menos restritivas. Os nomes de fila do barramento de serviço podem conter letras, números, pontos, hifens e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta seção aborda os recursos de autenticação e autorização com suporte nas filas de armazenamento e filas do barramento de serviço.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Authentication |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado por meio de tokens SAS. |RÍGIDO |
| Federação do provedor de identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Cada solicitação para uma das tecnologias de enfileiramento deve ser autenticada. Não há suporte para filas públicas com acesso anônimo. Usando a [SAS](service-bus-sas.md), você pode resolver esse cenário publicando uma SAS somente gravação, SAS somente leitura ou até mesmo uma SAS de acesso completo.
* O esquema de autenticação fornecido pelas filas de armazenamento envolve o uso de uma chave simétrica, que é um HMAC (Message Authentication Code baseado em hash), calculado com o algoritmo SHA-256 e codificado como uma cadeia de caracteres **Base64** . Para obter mais informações sobre o respectivo protocolo, consulte [autenticação para os serviços de armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). As filas do barramento de serviço dão suporte a um modelo semelhante usando chaves simétricas. Para obter mais informações, consulte [autenticação de assinatura de acesso compartilhado com o barramento de serviço](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Ao obter uma compreensão mais profunda das duas tecnologias, você poderá tomar uma decisão mais informada sobre qual tecnologia de fila usar e quando. A decisão sobre quando usar filas de armazenamento ou filas do barramento de serviço depende claramente de vários fatores. Esses fatores podem depender muito das necessidades individuais do seu aplicativo e de sua arquitetura. Se o seu aplicativo já usa os principais recursos do Microsoft Azure, talvez você prefira escolher filas de armazenamento, especialmente se precisar de comunicação básica e mensagens entre serviços ou filas que precisem de mais de 80 GB de tamanho.

Como as filas do barramento de serviço fornecem uma série de recursos avançados, como sessões, transações, detecção de duplicidades, mensagens mortas automáticas e recursos de publicação/assinatura duráveis, eles podem ser uma opção preferida se você estiver criando um híbrido ou, caso contrário, o aplicativo exige esses recursos.

## <a name="next-steps"></a>Passos seguintes
Os artigos a seguir fornecem mais orientações e informações sobre como usar filas de armazenamento ou filas do barramento de serviço.

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como usar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens orientadas do barramento de serviço](service-bus-performance-improvements.md)
* [Introdução a filas e tópicos no barramento de serviço do Azure (postagem de blog)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [O guia do desenvolvedor para o barramento de serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Usando o serviço de enfileiramento no Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

