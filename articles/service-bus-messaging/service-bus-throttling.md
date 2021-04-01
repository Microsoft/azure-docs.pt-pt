---
title: Visão geral do autocarro da Azure Service a estrangular | Microsoft Docs
description: Visão geral do estrangulamento do Service Bus - Standard e Premium.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340958"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Operações de estrangulamento no Azure Service Bus

As soluções nativas em nuvem dão uma noção de recursos ilimitados que podem escalar com a sua carga de trabalho. Embora esta noção seja mais verdadeira na nuvem do que com os sistemas no local, ainda existem limitações que existem na nuvem.

Estas limitações podem causar estrangulamento dos pedidos de pedidos de clientes nos níveis Standard e Premium, conforme discutido neste artigo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Throttling no nível standard de ônibus de serviço Azure

O nível Azure Service Bus Standard funciona como uma configuração multi-inquilino com um modelo de preços pay-as-you-go. Aqui vários espaços de nome no mesmo cluster partilham os recursos atribuídos. O nível padrão é a escolha recomendada para ambientes de desenvolvimento, teste e QA, juntamente com sistemas de produção de baixa produção.

No passado, a Azure Service Bus tinha limites de estrangulamento grosseiros estritamente dependentes da utilização de recursos. No entanto, há uma oportunidade de refinar a lógica de estrangulamento e fornecer um comportamento previsível de estrangulamento a todos os espaços de nome que estão a partilhar estes recursos.

Numa tentativa de garantir uma utilização justa e distribuição de recursos em todos os espaços de nome da Azure Service Standard que utilizam os mesmos recursos, a lógica de estrangulamento foi modificada para ser baseada em crédito.

> [!NOTE]
> É importante notar que o estrangulamento não é ***novo*** para a Azure Service Bus, ou qualquer serviço nativo na nuvem.
>
> O estrangulamento baseado no crédito é simplesmente refinar a forma como vários espaços de nome partilham recursos num ambiente de nível standard multi-inquilino e, assim, permitindo uma utilização justa por todos os espaços de nome que partilham os recursos.

### <a name="what-is-credit-based-throttling"></a>O que é estrangulamento baseado no crédito?

O estrangulamento baseado no crédito limita o número de operações que podem ser realizadas num determinado espaço de nome num determinado período de tempo. 

Abaixo está o fluxo de trabalho para estrangulamento baseado no crédito - 

  * No início de cada período de tempo, fornecemos um certo número de créditos para cada espaço de nome.
  * Quaisquer operações realizadas pelo remetente ou pedidos de cliente recetores serão contabilizadas contra estes créditos (e subtraídas dos créditos disponíveis).
  * Se os créditos estiverem esgotados, as operações subsequentes serão estranguladas até ao início do próximo período de tempo.
  * Os créditos são repostos no início do próximo período de tempo.

### <a name="what-are-the-credit-limits"></a>Quais são os limites de crédito?

Os limites de crédito estão atualmente definidos para créditos '1000' a cada segundo (por espaço de nome).

Nem todas as operações são criadas iguais. Aqui estão os custos de crédito de cada uma das operações - 

| Operação | Custo de crédito|
|-----------|-----------|
| Operações de dados (Enviar, Enviar, Receber, Receber, Receber Async, Espreitar) |1 crédito por mensagem |
| Operações de gestão (Criar, Ler, Atualizar, Eliminar em Filas, Tópicos, Assinaturas, Filtros) | 10 créditos |

> [!NOTE]
> Tenha em atenção que, ao enviar para um Tópico, cada mensagem é avaliada com filtros antes de ser disponibilizada na Subscrição.
> Cada avaliação do filtro também conta contra o limite de crédito (ou seja, 1 crédito por avaliação de filtro).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Como vou saber que estou a ser estrangulado?

Quando os pedidos de pedido do cliente estiverem a ser acelerados, a resposta abaixo do servidor será recebida pela sua aplicação e registada.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Com recursos partilhados, é importante impor algum tipo de uso justo em vários espaços de nomes de Service Bus que partilham esses recursos. O estrangulamento garante que qualquer pico numa única carga de trabalho não faz com que outras cargas de trabalho sobre os mesmos recursos sejam estranguladas.

Como mencionado mais tarde no artigo, não há risco de ser estrangulado porque os SDKs clientes (e outras ofertas Azure PaaS) têm a política de recandidatura padrão incorporada neles. Quaisquer pedidos acelerados serão novamente julgados com recuo exponencial e eventualmente passarão quando os créditos forem reabastecidos.

Compreensivelmente, algumas aplicações podem ser sensíveis a serem estranguladas. Nesse caso, recomenda-se [migrar o seu atual espaço de nome Service Bus Standard para Premium](service-bus-migrate-standard-premium.md). 

Na migração, você pode alocar recursos dedicados ao seu espaço de nomes service bus e aumentar adequadamente os recursos se houver um pico na sua carga de trabalho e reduzir a probabilidade de ser estrangulado. Além disso, quando a sua carga de trabalho reduz para níveis normais, pode reduzir os recursos atribuídos ao seu espaço de nome.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Throttling no nível premium de ônibus de serviço Azure

O nível [Azure Service Bus Premium](service-bus-premium-messaging.md) aloca recursos dedicados, em termos de unidades de mensagens, a cada configuração de espaço de nome pelo cliente. Estes recursos dedicados proporcionam produção e latência previsíveis e são recomendados para sistemas de produção de alta produção ou sensíveis.

Além disso, o nível Premium também permite que os clientes aumentem a sua capacidade de produção quando experimentam picos na carga de trabalho.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Como funciona o estrangulamento no Service Bus Premium?

Com a atribuição exclusiva de recursos para o Service Bus Premium, o estrangulamento é puramente impulsionado pelas limitações dos recursos atribuídos ao espaço de nome.

Se o número de pedidos for superior ao que os recursos atuais podem servir, então os pedidos serão estrangulados.

### <a name="how-will-i-know-that-im-being-throttled"></a>Como vou saber que estou a ser estrangulado?

Existem várias formas de identificar o estrangulamento no Azure Service Bus Premium - 
  * **Os pedidos de aceleração** aparecem nas [métricas do Azure Monitor Request](service-bus-metrics-azure-monitor.md#request-metrics) para identificar quantos pedidos foram estrangulados.
  * O **uso elevado do CPU** indica que a alocação atual de recursos é elevada e os pedidos podem ser acelerados se a carga de trabalho atual não reduzir.
  * **O uso de alta memória** indica que a alocação atual de recursos é elevada e os pedidos podem ser estrangulados se a carga de trabalho atual não reduzir.

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Uma vez que o espaço de nome Service Bus Premium já tem recursos dedicados, pode reduzir a possibilidade de ser estrangulado aumentando o número de Unidades de Mensagens alocadas ao seu espaço de nome no caso (ou antecipação) de um pico na carga de trabalho.

A escala para cima/para baixo pode ser conseguida criando [runbooks](../automation/automation-create-alert-triggered-runbook.md) que podem ser desencadeados por alterações nas métricas acima.

## <a name="faqs"></a>FAQs

### <a name="how-does-throttling-affect-my-application"></a>Como é que o estrangulamento afeta a minha candidatura?

Quando um pedido é acelerado, implica que o serviço está ocupado porque está a enfrentar mais pedidos do que os recursos permitem. Se a mesma operação for novamente experimentada após alguns momentos, uma vez que o serviço tenha funcionado através da sua carga de trabalho atual, então o pedido pode ser honrado.

Uma vez que o estrangulamento é o comportamento esperado de qualquer serviço nativo em nuvem, construímos a lógica de retíria no próprio Azure Service Bus SDK. O padrão é definido para tentar automaticamente com um back-off exponencial para garantir que não temos o mesmo pedido sendo estrangulado cada vez.

A lógica de relemisso padrão aplicar-se-á a cada operação.

### <a name="does-throttling-result-in-data-loss"></a>O estrangulamento resulta em perda de dados?

A Azure Service Bus está otimizada para persistência, garantimos que todos os dados enviados para a Service Bus estão comprometidos com o armazenamento antes que o serviço reconheça o sucesso do pedido.

Uma vez que o pedido é com sucesso 'ACK' (reconhecido) pela Service Bus, implica que a Service Bus processou com sucesso o pedido. Se a Service Bus devolver um 'NACK' (falha), então implica que a Service Bus não foi capaz de processar o pedido e o pedido do cliente deve voltar a tentar o pedido.

No entanto, quando um pedido é acelerado, o serviço está a insinuar que não pode aceitar e processar o pedido agora devido a limitações de recursos. Isto **não** implica qualquer tipo de perda de dados porque a Service Bus simplesmente não analisou o pedido. Neste caso, contando com a política de relagem padrão do Service Bus SDK garante que o pedido é eventualmente processado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
* [Início Rápido: Enviar e receber mensagens com o portal do Azure e o .NET](service-bus-quickstart-portal.md)
* [Tutorial: atualizar inventário utilizando o portal do Azure e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)

