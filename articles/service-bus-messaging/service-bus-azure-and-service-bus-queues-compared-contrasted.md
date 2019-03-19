---
title: Filas de armazenamento do Azure e filas do Service Bus comparação e contraste | Documentos da Microsoft
description: Analisa as diferenças e semelhanças entre dois tipos de filas disponibilizadas pelo Azure.
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
ms.openlocfilehash: 74525b42445d87923b0bad7a522456257e651d00
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856027"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas de armazenamento e filas do Service Bus - comparação e contraste
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas disponibilizadas atualmente pelo Microsoft Azure: Filas de armazenamento e filas do Service Bus. A utilização destas informações permite-lhe comparar e contrastar as respetivas tecnologias, e tomar uma decisão mais informada quanto à solução que melhor responde às suas necessidades.

## <a name="introduction"></a>Introdução
O Azure suporta dois tipos de mecanismos de fila: **Filas de armazenamento** e **filas do Service Bus**.

**Filas de armazenamento**, que fazem parte dos [o armazenamento do Azure](https://azure.microsoft.com/services/storage/) infraestrutura, funcionalidade uma interface baseada em REST GET/PUT/PEEK simples, fornecendo mensagens confiáveis e persistentes dentro e entre serviços.

**Filas do Service Bus** fazem parte de uma mais ampla [mensagens Azure](https://azure.microsoft.com/services/service-bus/) infraestrutura que suporta a colocação em fila, bem como publicação/subscrição e muito mais avançados de padrões de integração. Para obter mais informações sobre as filas/tópicos/subscrições do Service Bus, consulte a [descrição geral do Service Bus](service-bus-messaging-overview.md).

Embora ambas as tecnologias de colocação em fila existirem em simultâneo, as filas de armazenamento foram introduzidas em primeiro lugar, como um mecanismo de armazenamento de fila dedicada criado sobre os serviços de armazenamento do Azure. Filas do Service Bus são criadas sobre a infraestrutura de mensagens mais ampla, projetada para integrar aplicativos ou componentes da aplicação que podem abranger vários protocolos de comunicação, contratos de dados, os domínios de confiança, e/ou ambientes de rede.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia
Filas de armazenamento e filas do Service Bus são implementações da mensagem de colocação em fila de serviço atualmente oferecido pelo Microsoft Azure. Cada um tem um conjunto de funcionalidades de um pouco diferente, o que significa que pode escolher um ou outro, ou utilizar ambos, dependendo das necessidades da sua solução específica ou um problema de negócios/técnico que está a resolver.

Ao determinar qual tecnologia de colocação em fila se encaixa o propósito de uma determinada solução, os programadores e arquitetos de soluções devem considerar estas recomendações. Para obter mais detalhes, consulte a secção seguinte.

Como um arquiteto de solução/programador, **deve considerar a utilização de filas de armazenamento** quando:

* A aplicação tem de armazenar mais de 80 GB de mensagens numa fila.
* Seu aplicativo deseja controlar o progresso para processar uma mensagem dentro da fila. Isto é útil no caso de falha da função de trabalho de processamento de uma mensagem. Uma função de trabalho subsequente, em seguida, pode utilizar essas informações para continuar a partir de onde parou a função de trabalho anterior.
* Precisa de registos de lado do servidor de todas as transações executadas com base em suas filas.

Como um arquiteto de solução/programador, **deve considerar a utilização de filas do Service Bus** quando:

* Sua solução tem de ser capaz de receber mensagens sem ter de consultar a fila. Com o Service Bus, isso pode ser conseguido através da utilização da consulta longa de receber a operação utilizando protocolos baseados em TCP que o Service Bus suporta.
* Sua solução requer a fila para fornecer um garantida first-in-first-out (FIFO) ordenada de entrega.
* Sua solução tem de ser capaz de dar suporte a deteção automática de duplicados.
* Pretende que a aplicação para processar mensagens como fluxos de longa execução paralelas (as mensagens são associadas a um fluxo com o [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) propriedade da mensagem). Nesse modelo, cada nó da aplicação de consumo competindo para fluxos, em vez de mensagens. Quando recebe um fluxo para um nó de consumo, o nó pode examinar o estado do Estado de fluxo do aplicativo usando transações.
* A solução requer comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.
* Seu aplicativo manipula mensagens que podem exceder 64 KB, mas iremos limitar as abordagem não é provável que a 256 KB.
* Lida com um requisito para fornecer um modelo de acesso baseado em funções para as filas e os direitos/permissões diferentes de remetentes e recetores.
* O tamanho da fila não irá aumentar mais de 80 GB.
* Que pretende utilizar o protocolo de mensagens do baseada em padrões de AMQP 1.0. Para obter mais informações sobre o AMQP, consulte [visão geral do Service Bus AMQP](service-bus-amqp-overview.md).
* Pode imaginar uma eventual migração da comunicação ponto a ponto com base em fila para um padrão de troca de mensagens que permite a integração transparente de recetores adicionais (assinantes), cada uma delas recebe cópias independentes de alguns ou todos mensagens enviadas para a fila. O último refere-se para a capacidade de publicação/subscrição de mensagens em fila nativamente fornecida pelo Service Bus.
* Sua solução de mensagens deve ser capaz de dar suporte a garantia de entrega de "No-mais-uma vez" sem a necessidade de criar os componentes de infraestrutura adicional.
* Gostaria de poder publicar e consumir os lotes de mensagens.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparar filas do Service Bus e filas de armazenamento
As tabelas nas secções seguintes fornecem um agrupamento lógico de recursos da fila e permitem-lhe comparar, rapidamente, as capacidades disponíveis no filas de armazenamento do Azure e filas do Service Bus.

## <a name="foundational-capabilities"></a>Capacidades básicas
Esta seção compara alguns dos recursos fundamentais colocação em fila fornecidos pelo filas de armazenamento e filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Garantia de ordenação |**Não** <br/><br>Para obter mais informações, consulte a nota primeiro na secção "Additional Information".</br> |**Sim - First-In-First-Out (FIFO)**<br/><br>(com o uso de sessões de mensagens) |
| Garantia de entrega |**At-Least-Once** |**At-Least-Once**<br/><br/>**Na maioria-única** |
| Suporte de operação atômica |**Não** |**Sim**<br/><br/> |
| Comportamento de recebimento |**Não limitativos**<br/><br/>(é concluída imediatamente não se for encontrada nenhuma mensagem nova) |**Bloqueio com/sem tempo limite**<br/><br/>(oferece consulta longa, ou o ["Técnica Comet"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Não limitativos**<br/><br/>(através da utilização de .NET API gerenciada apenas) |
| API em estilo de push |**Não** |**Sim**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e **OnMessage** sessões .NET API. |
| Receber modo |**Pré-visualização & da concessão** |**Pré-visualização e de bloqueio**<br/><br/>**Receber e eliminar** |
| Modo de acesso exclusivo |**Com base na concessão** |**Com base em bloqueio** |
| Duração de concessão/bloqueio |**30 segundos (predefinição)**<br/><br/>**7 dias (máximo)** (pode renovar ou libertar uma concessão de mensagem utilizando o [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API.) |**60 segundos (predefinição)**<br/><br/>Pode renovar um bloqueio de mensagem através da [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| Concessão/bloqueio de precisão |**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferentes, que, em seguida, pode atualizar conforme necessário durante o processamento da mensagem, ao utilizar o [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API) |**Nível de fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas pode renovar o bloqueio utilizando o [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Colocar em lote receber |**Sim**<br/><br/>(especificar explicitamente o número de mensagens na obtenção de mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(implicitamente ativar uma propriedade de obtenção prévia ou explicitamente através da utilização de transações) |
| Envio em lote |**Não** |**Sim**<br/><br/>(por meio do uso de transações ou criação de batches de lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* Mensagens em filas de armazenamento são, normalmente, first-in-first-out, mas, às vezes, podem estar fora de ordem; Por exemplo, quando duração do tempo limite de visibilidade de uma mensagem expira (por exemplo, como resultado de uma aplicação de cliente falhar durante o processamento). Quando o tempo limite de visibilidade expira, a mensagem se torna visível novamente na fila para outra função de trabalho da fila-lo. Nesse ponto, a mensagem recentemente visível pode ser colocada na fila (para ser removidos da fila novamente) após uma mensagem que foi originalmente colocado em fila depois dela.
* O padrão de FIFO garantido em filas do Service Bus requer o uso de sessões de mensagens. No caso de falha da aplicação ao processar uma mensagem é recebida na **observar & Bloquear** modo, na próxima vez que um recetor de fila aceita uma sessão de mensagens, irá começar com a mensagem de falha após a respetiva time-to-live (TTL) período de expirar.
* Filas de armazenamento foram concebidas para suportar cenários de colocação em fila padrão, como desacoplando componentes da aplicação para aumentar a escalabilidade e tolerância a falhas, carregar nivelamento e a criação de fluxos de trabalho do processo.
* Suporte de filas do Service Bus a *pelo-menos-uma vez* garantia de entrega. 
* Inconsistências em relação ao processamento de mensagens no contexto de sessões de barramento de serviço podem ser evitadas ao utilizar o estado da sessão para armazenar o estado do aplicativo em relação ao progresso de manipulação de sequência de mensagem da sessão e com transações em torno de liquidar recebeu mensagens e a atualizar o estado da sessão. Esse tipo de recurso de consistência, às vezes, é rotulado *exatamente-processamento uma vez* de outro fabricante produtos, mas a transação falhas, obviamente, fará com que as mensagens sejam redeliveried e, portanto, o termo adequado não exatamente.
* Filas de armazenamento fornecem um modelo de programação uniforme e consistente em filas, tabelas e BLOBs – para os desenvolvedores e para as equipas de operações.
* Filas do Service Bus fornecem suporte para transações locais no contexto de uma única fila.
* O **receber e eliminar** modo suportado pelo Service Bus fornece a capacidade de reduzir o contador de operações de mensagens (e o custo associado), em troca de garantia de entrega de baixa expectativa com.
* Filas de armazenamento fornecem concessões a capacidade de estender as concessões para mensagens. Isso permite que os operadores de concessões curtas nas mensagens. Portanto, no caso de falha de uma função de trabalho, a mensagem pode ser rapidamente processada novamente por outra função de trabalho. Além disso, uma função de trabalho pode alargar a concessão numa mensagem, se precisar de processá-lo mais do que o tempo de locação financeira atual.
* Filas de armazenamento oferecem um tempo limite de visibilidade que pode definir após o enfileiramento ou remoção da fila de uma mensagem. Além disso, pode atualizar uma mensagem com valores de concessão diferentes em tempo de execução e atualizar valores diferentes em mensagens na mesma fila. Tempos limite de bloqueio do Service Bus é definido nos metadados da fila; No entanto, pode renovar o bloqueio chamando o [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) método.
* O tempo limite máximo para um bloqueio de receber a operação em filas do Service Bus é de 24 dias. No entanto, tempos limite baseado em REST tem um valor máximo de 55 segundos.
* Criação de batches de lado do cliente fornecidos pelo Service Bus permite que um cliente de fila para o lote várias mensagens numa operação única enviar. Criação de batches só está disponível para operações de envio assíncrono.
* Recursos como o limite de 200 TB de filas de armazenamento (mais informações quando é virtualizar contas) e filas ilimitadas tornam uma plataforma ideal para fornecedores SaaS.
* Filas de armazenamento proporcionam um flexível e de elevado desempenho delegada mecanismo de controlo de acesso.

## <a name="advanced-capabilities"></a>Capacidades avançadas
Esta seção compara as capacidades avançadas fornecidas pelo filas de armazenamento e filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Automática mensagens não entregues |**Não** |**Sim** |
| Aumentando o valor de tempo de vida de fila |**Sim**<br/><br/>(por meio de atualização no local de tempo limite de visibilidade) |**Sim**<br/><br/>(fornecido através de uma função de API dedicada) |
| Suporte de mensagem de mensagens suspeitas |**Sim** |**Sim** |
| Atualização no local |**Sim** |**Sim** |
| Log de transação do lado do servidor |**Sim** |**Não** |
| Métricas de armazenamento |**Sim**<br/><br/>**Métricas de minuto**: fornece métricas em tempo real para a API de disponibilidade, TPS, chamam contagens, contagens de erros e muito mais, tudo em tempo real (agregados por minuto e comunicadas em poucos minutos do que acaba de acontecer na produção. Para obter mais informações, consulte [sobre métricas da análise de armazenamento](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Sim**<br/><br/>(em massa consultas chamando [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gestão de estados |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Mensagens de reencaminhamento automático |**Não** |**Sim** |
| Remover função de fila |**Sim** |**Não** |
| Grupos de mensagem |**Não** |**Sim**<br/><br/>(com o uso de sessões de mensagens) |
| Estado da aplicação por grupo de mensagem |**Não** |**Sim** |
| Deteção de duplicados |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Navegação nos grupos de mensagem |**Não** |**Sim** |
| A obter sessões de mensagens por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de colocação em fila permitem uma mensagem a ser agendada para entrega posterior.
* Reencaminhamento automático de fila permite milhares de filas para reencaminhamento suas mensagens a uma fila única, a partir do qual o aplicativo de recebimento consome a mensagem. Pode usar esse mecanismo para atingir a segurança, controlar o fluxo e isolar armazenamento entre cada editor de mensagem.
* Filas de armazenamento fornecem suporte para atualizar o conteúdo da mensagem. Pode utilizar esta funcionalidade para a persistência de estado de informações e atualizações de progresso incremental na mensagem para que possam ser processados a partir do último ponto de verificação conhecido, em vez de começar do zero. Filas do Service Bus, pode ativar o mesmo cenário com o uso de sessões de mensagens. Sessões permitem-lhe guardar e obter o estado de processamento do aplicativo (usando [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Inutilizado lettering](service-bus-dead-letter-queues.md), que é apenas suportado por filas do Service Bus, pode ser útil para isolar as mensagens que não podem ser processados com êxito, o aplicativo de recebimento ou quando as mensagens não é possível alcançar o seu destino devido a um (time-to-live expirada Propriedade de valor de TTL). O valor de TTL, especifica quanto tempo uma mensagem permanece na fila. Com o Service Bus, a mensagem será movida para uma fila especial chamada $DeadLetterQueue, quando o valor de TTL expire.
* Para localizar "não processáveis" mensagens em filas de armazenamento, quando uma mensagem de remoção da fila a aplicação examina o [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) propriedade da mensagem. Se **DequeueCount** é maior do que um determinado limite, o aplicativo passa a mensagem numa fila definida pelo aplicativo "entregues".
* Filas de armazenamento permitem-lhe obter um log detalhado de todas as transações executadas com base em fila, como métricas agregadas, bem como. As duas opções são úteis para depurar e compreender como o aplicativo usa filas de armazenamento. Eles também são úteis para seu aplicativo ajuste de desempenho e reduzir os custos de utilização de filas.
* O conceito de "sessões de mensagens" suportado pelo Service Bus permite que as mensagens que pertencem a um determinado grupo lógico a ser associado um determinado receptor, que por sua vez, cria uma afinidade de sessão semelhantes entre mensagens e seus respectivos recetores. Pode ativar esta funcionalidade avançada no Service Bus, definindo a [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) propriedade numa mensagem. Recetores, em seguida, podem escutar um ID de sessão específico e receber mensagens que partilham o identificador de sessão especificado.
* A funcionalidade de deteção de duplicação suportada por filas do Service Bus automaticamente remove duplicadas mensagens enviadas para uma fila ou tópico, com base no valor do [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) propriedade.

## <a name="capacity-and-quotas"></a>Capacidade e de quotas
Esta seção compara as filas de armazenamento e filas do Service Bus, da perspectiva da [capacidade e de quotas](service-bus-quotas.md) que podem ser aplicadas.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a um [única capacidade das contas de armazenamento](../storage/common/storage-introduction.md#queue-storage)) |**1 GB a 80 GB**<br/><br/>(definido após a criação de uma fila e [permitindo a criação de partições](service-bus-partitioning.md) – consulte a secção "Additional Information") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao usar **Base64** codificação)<br/><br/>O Azure suporta mensagens grandes por meio da combinação filas e blobs – a altura em que pode colocar em fila até 200 GB para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluir o cabeçalho e o corpo, o tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende da [escalão de serviço](service-bus-premium-messaging.md). |
| TTL máximo de mensagens |**Infinito** (a partir da versão de api 2017-07-27) |**TimeSpan.Max** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por espaço de nomes de serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**Ilimitado**<br/><br/>(limite de ligações simultâneas 100 só se aplica a comunicação com base no protocolo TCP) |

### <a name="additional-information"></a>Informações adicionais
* Do Service Bus impõe limites de tamanho da fila. O tamanho máximo da fila é especificado durante a criação da fila e pode ter um valor entre 1 e 80 GB. Se for atingido o valor de tamanho da fila definido na criação da fila, mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para obter mais informações sobre as quotas no Service Bus, consulte [as Quotas do Service Bus](service-bus-quotas.md).
* Criação de partições não é suportada a [escalão Premium](service-bus-premium-messaging.md). No escalão Standard, pode criar filas do Service Bus em tamanhos de 1, 2, 3, 4 ou 5 GB (a predefinição é 1 GB). No padrão camada com a criação de partições ativado (que é a predefinição), Service Bus cria 16 partições por cada GB que especificar. Assim, se criar uma fila que é de 5 GB de tamanho, com 16 partições o tamanho máximo da fila se torna (5 * 16) = 80 GB. Pode ver o tamanho máximo de sua particionada fila ou tópico examinando sua entrada [portal do Azure][Azure portal].
* Com as filas de armazenamento, se o conteúdo da mensagem não é segura para XML, em seguida, tem de ser **Base64** codificado. Se **Base64**-codificar a mensagem, a carga de utilizador pode ter até 48 KB, em vez de 64 KB.
* Filas do Service Bus, cada mensagem armazenada numa fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo suportado pelo escalão de serviço.
* Quando os clientes comunicam com filas do Service Bus através do protocolo TCP, o número máximo de conexões simultâneas com uma única fila do Service Bus está limitado a 100. Este número é compartilhado entre remetentes e recetores. Se esta quota é atingida, serão rejeitados pedidos subsequentes para ligações adicionais e uma exceção será recebida pelo código de chamada. Este limite não é imposto em clientes que se conectam para as filas com a API baseada em REST.
* Se necessitar de mais de 10.000 filas num único espaço de nomes do Service Bus, pode contactar a equipa de suporte do Azure e pedir um aumento. Para dimensionar para além dos 10 000 filas com o Service Bus, também pode criar a espaços de nomes adicionais com o [portal do Azure][Azure portal].

## <a name="management-and-operations"></a>Gerenciamento e operações
Esta seção compara as funcionalidades de gestão fornecidas pelo filas de armazenamento e filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Protocolo de gestão |**REST através de HTTP/HTTPS** |**REST através de HTTPS** |
| Protocolo de tempo de execução |**REST através de HTTP/HTTPS** |**REST através de HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP com TLS)** |
| API .NET |**Sim**<br/><br/>(Cliente de armazenamento de .NET API) |**Sim**<br/><br/>(.NET do Service Bus API) |
| C++ nativo |**Sim** |**Sim** |
| API de Java |**Sim** |**Sim** |
| API DE PHP |**Sim** |**Sim** |
| API node. js |**Sim** |**Sim** |
| Suporte de metadados arbitrário |**Sim** |**Não** |
| Regras de nomenclatura de fila |**Até 63 carateres de comprimento**<br/><br/>(Letras de um nome da fila tem de estar em minúsculas.) |**Até 260 carateres de comprimento**<br/><br/>(Nomes e caminhos de fila diferenciam maiúsculas de minúsculas.) |
| Obter função de comprimento de fila |**Sim**<br/><br/>(Valor aproximado se as mensagens expiram além do valor TTL sem a ser eliminado.) |**Sim**<br/><br/>(Valor exato, de ponto no tempo). |
| Função de pré-visualização |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Filas de armazenamento fornecem suporte para atributos arbitrários que podem ser aplicados para a descrição da fila, na forma de pares nome/valor.
* Ambas as tecnologias de fila oferecem a capacidade de olhar uma mensagem sem ter de bloqueá-lo, o que pode ser útil ao implementar uma ferramenta do explorer/browser de fila.
* O barramento de serviço .NET mediadas mensagens APIs aproveitar full duplex conexões TCP para um melhor desempenho quando comparada aos REST através de HTTP e que suportam o protocolo de padrão de AMQP 1.0.
* Nomes de filas de armazenamento pode ter entre 3 a 63 carateres, podem conter letras minúsculas, números e hífenes. Para obter mais informações, consulte [nomenclatura de filas e metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nomes de filas do Service Bus podem ser até 260 carateres e ter regras de nomenclatura menos restritivas. Nomes de filas do Service Bus podem conter letras, números, períodos, hífenes e carateres de sublinhado.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta secção descreve as funcionalidades de autenticação e autorização suportadas pelo filas de armazenamento e filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Authentication |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado através de tokens SAS. |SAS |
| Federação do fornecedor de identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Cada solicitação para qualquer uma das tecnologias de colocação em fila tem de ser autenticada. Filas públicas com o acesso anônimo não são suportadas. Usando [SAS](service-bus-sas.md), pode tratar desse cenário ao publicar uma SAS só de escrita, SAS só de leitura ou até mesmo uma SAS de acesso total.
* O esquema de autenticação fornecidos pelo armazenamento de filas envolve a utilização de uma chave simétrica, que é uma base em hash Message Authentication Code (HMAC), calculada com o algoritmo SHA-256 e codificado como um **Base64** cadeia de caracteres. Para obter mais informações sobre o respetivo protocolo, consulte [autenticação para os serviços de armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Filas do Service Bus suportam um modelo semelhante usando chaves simétricas. Para obter mais informações, consulte [autenticação de assinatura de acesso partilhado com o Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Ganhando uma compreensão mais aprofundada das duas tecnologias, será capaz de tomar uma decisão mais informada sobre a tecnologia de fila desejada e quando. A decisão sobre o quando deve utilizar as filas de armazenamento ou filas do Service Bus claramente depende de vários fatores. Esses fatores podem depender fortemente as necessidades individuais da sua aplicação e respetiva arquitetura. Se a sua aplicação já utiliza os principais recursos do Microsoft Azure, pode ser preferível escolher as filas de armazenamento, especialmente se necessita de comunicação básica e de mensagens entre serviços ou filas de necessidade, que podem ser superiores a 80 GB de tamanho.

Porque as filas do Service Bus oferecem uma variedade de recursos avançados, como sessões, transações, duplicar a deteção, automática capacidades de mensagens não entregues, durável e de publicação/subscrição, podem ser uma opção preferencial se estiver criando um híbrido aplicação ou se seu aplicativo; caso contrário, requer esses recursos.

## <a name="next-steps"></a>Passos Seguintes
Os seguintes artigos fornecem mais orientações e informações sobre como utilizar as filas de armazenamento ou filas do Service Bus.

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Melhores práticas para melhorar o desempenho usando o barramento de serviço de mensagens mediadas](service-bus-performance-improvements.md)
* [Apresentando as filas e tópicos no Azure Service Bus (mensagem de blogue)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [Guia do programador para o Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Utilizar o serviço de colocação em fila no Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

