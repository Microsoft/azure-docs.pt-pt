---
title: Azure Service Bus - expiração da mensagem
description: Este artigo explica sobre a expiração e tempo para viver das mensagens do Azure Service Bus. Após tal prazo, a mensagem já não é entregue.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 5d60d84bdc0d437d97c369296a414d55beda4167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952325"
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL)
A carga útil numa mensagem, ou um comando ou inquérito que uma mensagem transmite a um recetor, está quase sempre sujeita a algum tipo de prazo de validade de nível de aplicação. Após esse prazo, o conteúdo deixou de ser entregue ou a operação solicitada deixou de ser executada.

Para ambientes de desenvolvimento e teste em que as filas e tópicos são frequentemente utilizados no contexto de execuções parciais de aplicações ou peças de aplicação, também é desejável que as mensagens de teste encalhadas sejam automaticamente recolhidas para que o próximo teste possa começar a ser limpo.

A expiração de qualquer mensagem individual pode ser controlada definindo a propriedade do sistema **time-to-live,** que especifica uma duração relativa. A expiração torna-se um instante absoluto quando a mensagem é encosa na entidade. Nessa altura, a propriedade **expira-at-utc** assume o valor **enqueso tempo-utc**  +  **tempo-a-viver**. A definição de tempo-a-viver (TTL) numa mensagem intermediada não é aplicada quando não há clientes a ouvir ativamente.

Passado o instante **expira-at-utc,** as mensagens tornam-se inelegíveis para a recuperação. A expiração não afeta as mensagens que estão atualmente bloqueadas para entrega. As mensagens ainda são tratadas normalmente. Se o bloqueio expirar ou a mensagem for abandonada, a expiração produz efeitos imediatos.

Enquanto a mensagem está bloqueada, a aplicação pode estar na posse de uma mensagem que expirou. Se a aplicação está disposta a avançar com o processamento ou opta por abandonar a mensagem cabe ao implementador.

TTL extremamente baixo na ordem dos milissegundos ou segundos pode fazer com que as mensagens expirem antes de as aplicações do recetor a receberem. Considere a TTL mais alta que funciona para a sua aplicação.

## <a name="entity-level-expiration"></a>Caducidade ao nível da entidade
Todas as mensagens enviadas para uma fila ou tópico estão sujeitas a uma expiração padrão que é definida ao nível da entidade. Também pode ser definido no portal durante a criação e ajustado mais tarde. A expiração padrão é usada para todas as mensagens enviadas para a entidade onde o tempo de vida não é explicitamente definido. A expiração predefinida também funciona como um teto para o valor de tempo para viver. As mensagens que têm uma expiração de tempo a viver mais longa do que o valor predefinido são ajustadas silenciosamente ao valor de tempo a vida da mensagem padrão antes de serem encosadas.

> [!NOTE]
> O valor de tempo a vida padrão para uma mensagem intermediada é o maior valor possível para um número inteiro de 64 bits, se não for especificado de outra forma.
>
> Para as entidades de mensagens (filas e tópicos), o tempo de validade padrão é também o maior valor possível para um inteiro de 64 bits para padrão de Service Bus e níveis premium. Para o nível **básico,** o prazo de validade (também máximo) é **de 14 dias**.

As mensagens expiradas podem opcionalmente ser transferidas para uma [fila de letras mortas](service-bus-dead-letter-queues.md). Pode configurar esta definição programáticamente ou utilizar o portal Azure. Se a opção for deixada desativada, as mensagens caducadas são retiradas. As mensagens caducadas transferidas para a fila de cartas mortas podem ser distinguidas de outras mensagens com letras mortas, avaliando a propriedade [de razão de letra morta](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que o corretor armazena na secção de propriedades do utilizador. 

No caso acima referido, em que a mensagem está protegida de expiração enquanto está trancada e se a bandeira estiver colocada na entidade, a mensagem é transferida para a fila da letra morta à medida que o cadeado é abandonado ou expira. No entanto, não se move se a mensagem for resolvida com sucesso, o que pressupõe então que a aplicação tenha lidado com sucesso, apesar da expiração nominal.

A combinação de letras mortas (e transacionais) automáticas (e transacionais) no termo são uma ferramenta valiosa para estabelecer a confiança no facto de um trabalho dado a um manipulador ou a um grupo de manipuladores ao abrigo de um prazo ser recuperado para processamento à medida que o prazo é atingido.

Por exemplo, considere um site que precisa de executar empregos de forma fiável num backend limitado à escala, e que ocasionalmente experimenta picos de tráfego ou quer ser isolado contra episódios de disponibilidade desse backend. No caso regular, o manipulador do lado do servidor para os dados do utilizador submetidos empurra a informação para uma fila e, posteriormente, recebe uma resposta confirmando o manuseamento bem sucedido da transação numa fila de resposta. Se houver um pico de tráfego e o manipulador de backend não puder processar os seus itens de atraso a tempo, os empregos caducados são devolvidos na fila da carta morta. O utilizador interativo pode ser notificado de que a operação solicitada demorará um pouco mais do que o habitual, podendo então o pedido ser colocado numa fila diferente para uma trajetória de processamento onde o resultado do processamento eventual é enviado ao utilizador por e-mail. 


## <a name="temporary-entities"></a>Entidades temporárias

As filas de autocarros de serviço, tópicos e subscrições podem ser criadas como entidades temporárias, que são automaticamente removidas quando não foram usadas por um determinado período de tempo.
 
A limpeza automática é útil em cenários de desenvolvimento e teste em que as entidades são criadas de forma dinâmica e não são limpas após o uso, devido a alguma interrupção do teste ou depuramento. Também é útil quando uma aplicação cria entidades dinâmicas, como uma fila de respostas, para receber respostas de volta a um processo de servidor web, ou em outro objeto de vida relativamente curta onde é difícil limpar essas entidades de forma fiável quando a instância do objeto desaparece.

A função é ativada utilizando a exclusão automática da propriedade **ociosa** no espaço de nomes. Esta propriedade é definida para a duração para a qual uma entidade deve estar inativa (não ser usada) antes de ser automaticamente eliminada. O valor mínimo para esta propriedade é de 5 minutos.
 
## <a name="idleness"></a>Ociosidade

Eis o que considerou o ociosidade das entidades (filas, tópicos e assinaturas):

- Filas
    - Sem envios  
    - Sem receber  
    - Sem atualizações para a fila  
    - Sem mensagens agendadas  
    - Sem navegação/espreitar 
- Tópicos  
    - Sem envios  
    - Sem atualizações para o tópico  
    - Sem mensagens agendadas 
- Subscrições
    - Sem receber  
    - Sem atualizações para a subscrição  
    - Nenhuma nova regra adicionada à subscrição  
    - Sem navegação/espreitar  
 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
