---
title: Azure Functions processamento fiável de eventos
description: Evite mensagens de centro de eventos em falta em Funções Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93379592"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions processamento fiável de eventos

O processamento de eventos é um dos cenários mais comuns associados à arquitetura sem servidor. Este artigo descreve como criar um processador de mensagens confiável com funções Azure para evitar perder mensagens.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Desafios dos streams de eventos em sistemas distribuídos

Considere um sistema que envia eventos a uma taxa constante de 100 eventos por segundo. A este ritmo, em poucos minutos, várias instâncias de funções paralelas podem consumir os 100 eventos que chegam a cada segundo.

No entanto, qualquer uma das seguintes condições menos ideais são possíveis:

- E se a editora do evento enviar um evento corrupto?
- E se o seu caso funções encontrar exceções sem manipulação?
- E se um sistema a jusante ficar offline?

Como lida com estas situações preservando a produção da sua aplicação?

Com as filas, as mensagens fiáveis vêm naturalmente. Quando emparelhado com um gatilho de Funções, a função cria um bloqueio na mensagem de fila. Se o processamento falhar, o bloqueio é libertado para permitir que outra instância relemque o processamento. O processamento continua até que a mensagem seja avaliada com sucesso, ou adicionada a uma fila de veneno.

Mesmo que uma única mensagem de fila possa permanecer num ciclo de relemis, outras execuções paralelas continuam a manter-se a desresírem mensagens restantes. O resultado é que a produção global permanece em grande parte inalterada por uma má mensagem. No entanto, as filas de armazenamento não garantem encomendas e não estão otimizadas para as elevadas exigências de produção exigidas pelos Centros de Eventos.

Em contraste, o Azure Event Hubs não inclui um conceito de bloqueio. Para permitir funcionalidades como alta produção, múltiplos grupos de consumidores e capacidade de repetição, os eventos do Event Hubs comportam-se mais como um leitor de vídeo. Os eventos são lidos a partir de um único ponto no fluxo por partição. A partir do ponteiro pode ler para a frente ou para trás a partir desse local, mas você tem que escolher mover o ponteiro para os eventos para processar.

Quando ocorrem erros num fluxo, se decidir manter o ponteiro no mesmo local, o processamento do evento é bloqueado até que o ponteiro seja avançado. Por outras palavras, se o ponteiro for parado para lidar com problemas de processamento de um único evento, os eventos não processados começam a acumular-se.

As Funções Azure evitam impasses avançando o ponteiro do fluxo, independentemente do sucesso ou do fracasso. Uma vez que o ponteiro continua a avançar, as suas funções precisam de lidar adequadamente com falhas.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Como as funções do Azure consomem eventos de Centros de Eventos

A Azure Functions consome eventos Event Hub enquanto pedala pelos seguintes passos:

1. Um ponteiro é criado e persistiu no Azure Storage para cada partição do centro do evento.
2. Quando novas mensagens são recebidas (num lote por predefinição), o anfitrião tenta ativar a função com o lote de mensagens.
3. Se a função completar a execução (com ou sem exceção), o ponteiro avança e um ponto de verificação é guardado na conta de armazenamento.
4. Se as condições impedirem que a execução da função se complete, o hospedeiro não consegue progredir no ponteiro. Se o ponteiro não for avançado, os controlos posteriores acabam por processar as mesmas mensagens.
5. Passos repetidos 2-4

Este comportamento revela alguns pontos importantes:

- *Exceções não tratadas podem fazer com que perca mensagens.* As execuções que resultam numa exceção continuarão a progredir no ponteiro.  A definição de uma [política de re-julgamento](./functions-bindings-error-pages.md#retry-policies-preview) atrasará a progressão do ponteiro até que toda a política de reagem seja avaliada.
- *As funções garantem, pelo menos, uma vez a entrega.* O seu código e sistemas dependentes podem ter de [explicar o facto de que a mesma mensagem pode ser recebida duas vezes](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Processamento de exceções

Regra geral, todas as funções devem incluir um [bloco de tentativa/captura](./functions-bindings-error-pages.md) ao mais alto nível de código. Especificamente, todas as funções que consomem eventos do Event Hubs devem ter um `catch` bloqueio. Assim, quando se levanta uma exceção, o bloco de capturas lida com o erro antes do ponteiro progredir.

### <a name="retry-mechanisms-and-policies"></a>Mecanismos e políticas de retíria

Algumas exceções são transitórias de natureza e não reaparecem quando uma operação é tentada novamente momentos depois. É por isso que o primeiro passo é sempre voltar a tentar a operação.  Pode alavancar as [políticas de relíparação](./functions-bindings-error-pages.md#retry-policies-preview) da aplicação de função ou a lógica de relempenhá-lo dentro da execução da função.

A introdução de comportamentos de manuseamento de falhas nas suas funções permite-lhe definir políticas básicas e avançadas de relagem. Por exemplo, pode implementar uma política que siga um fluxo de trabalho ilustrado pelas seguintes regras:

- Tente inserir uma mensagem três vezes (potencialmente com um atraso entre as retretes).
- Se o resultado eventual de todas as retróscais for um fracasso, então adicione uma mensagem a uma fila para que o processamento possa continuar no stream.
- As mensagens corruptas ou não processadas são tratadas mais tarde.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) é um exemplo de uma biblioteca de resiliência e tratamento de falhas transitórias para aplicações C#.

## <a name="non-exception-errors"></a>Erros de não exceção

Alguns problemas surgem mesmo quando um erro não está presente. Por exemplo, considere uma falha que ocorre no meio de uma execução. Neste caso, se uma função não completar a execução, o ponteiro offset nunca é progredido. Se o ponteiro não avançar, qualquer instância que corra depois de uma execução falhada continua a ler as mesmas mensagens. Esta situação constitui uma garantia "pelo menos uma vez".

A garantia de que cada mensagem é processada pelo menos uma vez implica que algumas mensagens podem ser processadas mais de uma vez. As suas aplicações de função precisam de estar cientes desta possibilidade e devem ser construídas em torno [dos princípios da idempoteência.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Parar e reiniciar a execução

Embora alguns erros possam ser aceitáveis, e se a sua aplicação sofrer falhas significativas? É melhor parar de desencadear eventos até que o sistema atinja um estado saudável. Ter a oportunidade de interromper o processamento é muitas vezes alcançado com um padrão de disjuntor. O padrão de disjuntor permite à sua aplicação "quebrar o circuito" do processo do evento e retomar posteriormente.

São necessárias duas peças para implementar um disjuntor num processo de evento:

- Estado compartilhado em todos os casos para rastrear e monitorizar a saúde do circuito
- Master processo que pode gerir o estado do circuito (aberto ou fechado)

Os detalhes da implementação podem variar, mas para partilhar o estado entre as instâncias você precisa de um mecanismo de armazenamento. Pode optar por armazenar o estado no Azure Storage, numa cache Redis ou em qualquer outra conta acessível através de uma coleção de funções.

[As Azure Logic Apps](../logic-apps/logic-apps-overview.md) ou [funções duráveis](./durable/durable-functions-overview.md) são um ajuste natural para gerir o estado de fluxo de trabalho e circuito. Outros serviços podem funcionar tão bem, mas aplicações lógicas são usadas para este exemplo. Utilizando aplicações lógicas, pode parar e reiniciar a execução de uma função, dando-lhe o controlo necessário para implementar o padrão de disjuntor.

### <a name="define-a-failure-threshold-across-instances"></a>Definir um limiar de falha em todos os casos

Para ter em conta vários casos de processamento de eventos em simultâneo, é necessário um estado externo partilhado que persista para monitorizar a saúde do circuito.

Uma regra que pode optar por implementar pode impor que:

- Se houver mais de 100 eventuais falhas em 30 segundos em todas as instâncias, então quebre o circuito e pare de ativar novas mensagens.

Os detalhes da implementação variarão dadas as suas necessidades, mas em geral pode criar um sistema que:

1. Falhas de registo de uma conta de armazenamento (Azure Storage, Redis, etc.)
1. Quando for registada nova falha, inspecione a contagem de rolos para ver se o limiar está atingido (por exemplo, mais de 100 nos últimos 30 segundos).
1. Se o limiar for atingido, emita um evento para Azure Event Grid dizendo ao sistema para quebrar o circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Estado de gestão do circuito com apps Azure Logic

A seguinte descrição realça uma forma de criar uma App Azure Logic para impedir que uma aplicação De Funções seja processada.

Azure Logic Apps vem com conectores incorporados para diferentes serviços, apresenta orquestrações imponentes, e é uma escolha natural para gerir o estado do circuito. Depois de detetar a necessidade de quebrar o circuito, pode construir uma aplicação lógica para implementar o seguinte fluxo de trabalho:

1. Desencadeie um fluxo de trabalho da Grade de Eventos e pare a Função Azure (com o conector de recursos Azure)
1. Envie um e-mail de notificação que inclua uma opção para reiniciar o fluxo de trabalho

O destinatário do e-mail pode investigar a saúde do circuito e, quando apropriado, reiniciar o circuito através de um link no e-mail de notificação. À medida que o fluxo de trabalho reinicia a função, as mensagens são processadas a partir do último ponto de verificação do Event Hub.

Utilizando esta abordagem, não se perdem mensagens, todas as mensagens são processadas por ordem e pode quebrar o circuito o tempo que for necessário.

## <a name="resources"></a>Recursos

- [Amostras fiáveis de processamento de eventos](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Disjuntor de entity durável Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

- [Tratamento de erros de funções Azure](./functions-bindings-error-pages.md)
- [Automatizar o redimensionamento de imagens carregadas com o Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Create a function that integrates with Azure Logic Apps](./functions-twitter-email.md) (Criar uma função que se integra no Azure Logic Apps)
