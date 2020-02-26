---
title: Visão geral do autocarro de serviço azure a estrangular Microsoft Docs
description: Visão geral da aceleração do ônibus de serviço - níveis Standard e Premium.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598294"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Operações de estrangulamento no ônibus de serviço azure

As soluções nativas da nuvem dão uma noção de recursos ilimitados que podem escalar com a sua carga de trabalho. Embora esta noção seja mais verdadeira na nuvem do que com os sistemas no local, ainda existem limitações que existem na nuvem.

Estas limitações podem causar o estrangulamento dos pedidos de pedidos de clientes tanto nos níveis Standard como Premium, conforme discutido neste artigo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Estrangulamento no nível Padrão de ônibus de serviço Azure

O nível Azure Service Bus Standard funciona como uma configuração multi-inquilino com um modelo de preços pay-as-you-go. Aqui vários espaços de nome no mesmo cluster partilham os recursos atribuídos. O nível padrão é a escolha recomendada para ambientes de desenvolvedores, testes e QA, juntamente com sistemas de produção de baixo suver.

No passado, o Azure Service Bus tinha limites de estrangulamento grosseiros estritamente dependentes da utilização de recursos. No entanto, existe a oportunidade de refinar a lógica de estrangulamento e proporcionar comportamentos previsíveis de estrangulamento a todos os espaços de nome que partilham estes recursos.

Numa tentativa de garantir uma utilização e distribuição equitativas de recursos em todos os espaços de nome sinuosos da Azure Service Bus Standard que utilizam os mesmos recursos, a lógica de estrangulamento foi modificada para ser baseada no crédito.

> [!NOTE]
> É importante notar que o estrangulamento ***não*** é novo para o Azure Service Bus, ou qualquer serviço nativo em nuvem.
>
> A aceleração baseada no crédito está simplesmente a refinar a forma como vários espaços de nome partilham recursos num ambiente de nível standard multi-inquilinoe permitindo assim uma utilização justa por todos os espaços de nome que partilham os recursos.

### <a name="what-is-credit-based-throttling"></a>O que é a aceleração baseada no crédito?

A aceleração baseada no crédito limita o número de operações que podem ser realizadas num determinado espaço de nome num determinado período de tempo. 

Abaixo está o fluxo de trabalho para a aceleração baseada no crédito - 

  * No início de cada período de tempo, fornecemos um certo número de créditos a cada espaço de nome.
  * Quaisquer operações efetuadas pelo remetente ou aplicações de clientes recetores serão contabilizadas com estes créditos (e subtraídas dos créditos disponíveis).
  * Se os créditos estiverem esgotados, as operações subsequentes serão aceleradas até ao início do próximo período de tempo.
  * Os créditos são repostos no início do próximo período.

### <a name="what-are-the-credit-limits"></a>Quais são os limites de crédito?

Os limites de crédito estão atualmente definidos para créditos '1000' a cada segundo (por espaço de nome).

Nem todas as operações são criadas iguais. Aqui estão os custos de crédito de cada uma das operações - 

| Operação | Custo de crédito|
|-----------|-----------|
| Operações de dados (Enviar, EnviarAsync, Receber, ReceberAsync, Espreitar) |1 crédito por mensagem |
| Operações de gestão (Criar, Ler, Atualizar, Eliminar em filas, tópicos, subscrições, filtros) | 10 créditos |

> [!NOTE]
> Por favor, note que ao enviar para um Tópico, cada mensagem é avaliada contra filtros(s) antes de ser disponibilizado na Subscrição.
> Cada avaliação do filtro também conta com o limite de crédito (ou seja, 1 crédito por avaliação do filtro).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Como vou saber que estou a ser estrangulado?

Quando os pedidos de pedido do cliente estiverem a ser estrangulados, a resposta do servidor abaixo será recebida pela sua aplicação e registada.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Com recursos partilhados, é importante impor algum tipo de uso justo em vários espaços de nome sinuoso de ônibus de serviço que partilham esses recursos. O estrangulamento garante que qualquer pico numa única carga de trabalho não provoque que outras cargas de trabalho nos mesmos recursos sejam estranguladas.

Como mencionado mais tarde no artigo, não há risco de ser estrangulado porque os SDKs clientes (e outras ofertas Azure PaaS) têm a política de retry padrão incorporada neles. Quaisquer pedidos de aceleração serão novamente julgados com recuos exponenciais e acabarão por passar quando os créditos forem reabastecidos.

Compreensivelmente, algumas aplicações podem ser sensíveis a ser estranguladas. Nesse caso, recomenda-se [migrar o seu atual espaço](service-bus-migrate-standard-premium.md)de nome service bus Standard para Premium . 

Na migração, você pode alocar recursos dedicados ao seu espaço de nome service Bus e aumentar adequadamente os recursos se houver um pico na sua carga de trabalho e reduzir a probabilidade de ser estrangulado. Além disso, quando a sua carga de trabalho reduz para níveis normais, pode reduzir os recursos atribuídos ao seu espaço de nome.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Estrangulamento no nível Azure Service Bus Premium

O nível [Azure Service Bus Premium](service-bus-premium-messaging.md) atribui recursos dedicados, em termos de unidades de mensagens, a cada espaço de nome configurado pelo cliente. Estes recursos dedicados fornecem entrada e latência previsíveis e são recomendados para sistemas de alta produção ou de qualidade de produção sensíveis.

Além disso, o nível Premium também permite que os clientes aumentem a sua capacidade de compra quando experimentam picos na carga de trabalho.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Como funciona a aceleração no Service Bus Premium?

Com a atribuição exclusiva de recursos para o Service Bus Premium, o estrangulamento é puramente impulsionado pelas limitações dos recursos atribuídos ao espaço de nome.

Se o número de pedidos for mais do que os recursos atuais podem ser prestados, então os pedidos serão acelerados.

### <a name="how-will-i-know-that-im-being-throttled"></a>Como vou saber que estou a ser estrangulado?

Existem várias formas de identificar a aceleração no Azure Service Bus Premium - 
  * **Os pedidos de aceleração** aparecem nas métricas do Pedido de [Monitor azure](service-bus-metrics-azure-monitor.md#request-metrics) para identificar quantos pedidos foram estrangulados.
  * O uso elevado do **CPU** indica que a alocação de recursos atuais é elevada e os pedidos podem ser acelerados se a carga de trabalho atual não reduzir.
  * O uso de **memória** elevada indica que a alocação de recursos atuais é elevada e os pedidos podem ser estrangulados se a carga de trabalho atual não reduzir.

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Uma vez que o espaço de nome Saque Premium de serviço já tem recursos dedicados, pode reduzir a possibilidade de ser estrangulado aumentando o número de Unidades de Mensagens atribuídas ao seu espaço de nome no evento (ou antecipação) de um pico na carga de trabalho.

A escala para cima/para baixo pode ser alcançada através da criação de livros de [execução](../automation/automation-create-alert-triggered-runbook.md) que podem ser desencadeados por alterações nas métricas acima.

## <a name="faqs"></a>Perguntas mais frequentes

### <a name="how-does-throttling-affect-my-application"></a>Como é que o estrangulamento afeta a minha aplicação?

Quando um pedido é estrangulado, implica que o serviço está ocupado porque enfrenta mais pedidos do que os recursos permitem. Se a mesma operação for novamente tentada após alguns momentos, uma vez que o serviço tenha trabalhado através da sua carga de trabalho atual, então o pedido pode ser honrado.

Uma vez que o estrangulamento é o comportamento esperado de qualquer serviço nativo em nuvem, construímos a lógica de retry no próprio Azure Service Bus SDK. O padrão está definido para retry automático com um back-off exponencial para garantir que não temos o mesmo pedido sendo estrangulado cada vez.

A lógica de retry padrão aplicar-se-á a todas as operações.

### <a name="does-throttling-result-in-data-loss"></a>A aceleração resulta na perda de dados?

O Azure Service Bus está otimizado para a persistência, garantimos que todos os dados enviados para o Service Bus estão comprometidos com o armazenamento antes que o serviço reconheça o sucesso do pedido.

Uma vez que o pedido é com sucesso 'ACK' (reconhecido) pela Service Bus, implica que a Service Bus processou com sucesso o pedido. Se a Service Bus devolver um 'NACK' (falha), então implica que o Service Bus não tenha sido capaz de processar o pedido e a aplicação do cliente deve voltar a julgar o pedido.

No entanto, quando um pedido é estrangulado, o serviço insinua que não pode aceitar e processar o pedido agora devido a limitações de recursos. Isto **não** implica qualquer tipo de perda de dados porque o Service Bus simplesmente não analisou o pedido. Neste caso, confiar na política de retry padrão do Service Bus SDK garante que o pedido é eventualmente processado.

## <a name="next-steps"></a>Passos seguintes

Para mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Quickstart: Enviar e receber mensagens utilizando o portal Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar o inventário utilizando o portal Azure e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)

