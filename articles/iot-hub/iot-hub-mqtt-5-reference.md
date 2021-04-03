---
title: Referência Azure IoT Hub MQTT 5 API (pré-visualização)
description: Saiba mais sobre a referência API MQTT 5 do IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603563"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Referência API do avião de dados IoT Hub MQTT 5 API

Este documento define as operações disponíveis na versão 2.0 (versão api: `2020-10-01-preview` ) do plano de dados IoT Hub API.

## <a name="operations"></a>Operações

### <a name="get-twin"></a>Get Twin

Obter estado gémeo

#### <a name="request"></a>Pedir

**Nome do tópico:**`$iothub/twin/get`

**Propriedades**: nenhuma

**Carga útil**: vazio

#### <a name="success-response"></a>Resposta ao Sucesso

**Propriedades**: nenhuma

**Carga útil**: Twin

#### <a name="alternative-responses"></a>Respostas Alternativas

| Estado | Nome | Descrição |
| :----- | :--- | :---------- |
| 0100 |  Pedido Incorreto | A mensagem de operação está mal formada e não pode ser processada. |
| 0101 |  Não Autorizado | O cliente não está autorizado a realizar a operação. |
| 0102 |  Não é permitido | A operação não é permitida. |
| 0501 |  Acelerador | taxa de pedido é muito alta por SKU |
| 0502 |  Quota Excedida | quota diária por SKU atual é excedido |
| 0601 |  Erro do Servidor | erro interno do servidor |
| 0602 |  Tempo Limite | operação cronometrada antes de poder ser concluída |
| 0603 |  Servidor Ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch Twin Reportado

Patch Twin's reportado estado

#### <a name="request"></a>Pedir

**Nome do tópico:**`$iothub/twin/patch/reported`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| se versão | u64 | não |  |

**Carga útil**: TwinState

#### <a name="success-response"></a>Resposta ao Sucesso

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| versão | u64 | sim | Versão do estado relatado após patch foi aplicado |

**Carga útil**: vazio

#### <a name="alternative-responses"></a>Respostas Alternativas

| Estado | Nome | Descrição |
| :----- | :--- | :---------- |
| 0104 |  Pré-condição falhou | pré-condição não foi cumprido, resultando em pedido de cancelamento |
| 0100 |  Pedido Incorreto | A mensagem de operação está mal formada e não pode ser processada. |
| 0101 |  Não Autorizado | O cliente não está autorizado a realizar a operação. |
| 0102 |  Não é permitido | A operação não é permitida. |
| 0501 |  Acelerador | taxa de pedido é muito alta por SKU |
| 0502 |  Quota Excedida | quota diária por SKU atual é excedido |
| 0601 |  Erro do Servidor | erro interno do servidor |
| 0602 |  Tempo Limite | operação cronometrada antes de poder ser concluída |
| 0603 |  Servidor Ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Receber Comandos

Receber e manusear comandos

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/commands`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| sequência-não | u64 | sim | Número de sequência da mensagem |
| tempo enqueso | hora | sim | Tempo de altura de quando a mensagem entrou no sistema |
| contagem de entrega | u32 | sim | Número de vezes que a entrega da mensagem foi tentada |
| tempo de criação | hora | não | Timetamp de quando a mensagem foi criada (fornecida pelo remetente) |
| mensagem id | string | não | Identidade da mensagem (fornecida por remetente) |
| user-id | string | não | Identidade do utilizador (fornecida por remetente) |
| correlation-id | string | não | Identidade de correlação (fornecida pelo remetente) |
| Tipo de Conteúdo | string | não | determina o Tipo de Conteúdo da carga útil |
| codificação de conteúdos | string | não | determina a codificação do conteúdo da carga útil |

**Carga útil**: qualquer sequência de byte

#### <a name="success-acknowledgment"></a>Reconhecimento de Sucesso

Indica que o comando foi aceite para manuseamento pelo cliente

**Propriedades**: nenhuma

**Carga útil**: vazio

#### <a name="alternative-acknowledgments"></a>Agradecimentos Alternativos

| Código da Razão | Estado | Nome | Descrição |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandonar | Indica que o comando não será processado neste momento e deve ser reenvitado no futuro. |
| 131 | 0100 | Rejeitar | Indica que o comando é rejeitado pelo cliente e não deve ser tentado novamente. |

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Receber Métodos Diretos

Receber e lidar com chamadas de Método Direto

#### <a name="request"></a>Pedir

**Nome do tópico:**`$iothub/methods/{name}`

**Propriedades**: nenhuma

**Carga útil**: qualquer sequência de byte

#### <a name="success-response"></a>Resposta ao Sucesso

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| código de resposta | u32 | sim |  |

**Carga útil**: qualquer sequência de byte

#### <a name="alternative-responses"></a>Respostas Alternativas

| Estado | Nome | Descrição |
| :----- | :--- | :---------- |
| 06A0 |  Indisponível | Indica que o cliente não está acessível através desta ligação. |

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Receber alterações de estado duplas desejadas

Receba atualizações para o estado desejado de Twin

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/twin/patch/desired`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| versão | u64 | sim | Versão do estado desejado que corresponde a esta atualização |

**Carga útil**: TwinState

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Enviar Telemetria

Enviar mensagem para o canal de telemetria - EventHubs por defeito ou outro ponto final através da configuração de encaminhamento.

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/telemetry`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| Tipo de Conteúdo | string | não | traduz-se em `content-type` propriedade do sistema em mensagem publicada |
| codificação de conteúdos | string | não | traduz-se em `content-encoding` propriedade do sistema em mensagem publicada |
| mensagem id | string | não | traduz-se em `message-id` propriedade do sistema em mensagem publicada |
| user-id | string | não | traduz-se em `user-id` propriedade do sistema em mensagem publicada |
| correlation-id | string | não | traduz-se em `correlation-id` propriedade do sistema em mensagem publicada |
| tempo de criação | hora | não | traduz-se em `iothub-creation-time-utc` propriedade na mensagem publicada |

**Carga útil**: qualquer sequência de byte

#### <a name="success-acknowledgment"></a>Reconhecimento de Sucesso

Mensagem foi postada com sucesso no canal de telemetria

**Propriedades**: nenhuma

**Carga útil**: vazio

#### <a name="alternative-acknowledgments"></a>Agradecimentos Alternativos

| Código da Razão | Estado | Nome | Descrição |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Pedido Incorreto | A mensagem de operação está mal formada e não pode ser processada. |
| 135 | 0101 | Não Autorizado | O cliente não está autorizado a realizar a operação. |
| 131 | 0102 | Não é permitido | A operação não é permitida. |
| 131 | 0601 | Erro do Servidor | erro interno do servidor |
| 151 | 0501 | Acelerador | taxa de pedido é muito alta por SKU |
| 151 | 0502 | Quota Excedida | quota diária por SKU atual é excedido |
| 131 | 0602 | Tempo Limite | operação cronometrada antes de poder ser concluída |
| 131 | 0603 | Servidor Ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Amostra de pseudo-código

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Respostas

### <a name="bad-request"></a>Pedido Incorreto

A mensagem de operação está mal formada e não pode ser processada.

**Código da Razão:** `131`

**Estado:**`0100`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="conflict"></a>Conflito

A operação está em conflito com outra operação em curso.

**Código da Razão:** `131`

**Estado:**`0103`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="not-allowed"></a>Não é permitido

A operação não é permitida.

**Código da Razão:** `131`

**Estado:**`0102`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="not-authorized"></a>Não Autorizado

O cliente não está autorizado a realizar a operação.

**Código da Razão:** `135`

**Estado:**`0101`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |

**Carga útil**: vazio

### <a name="not-found"></a>Não Encontrado

recurso solicitado não existe

**Código da Razão:** `131`

**Estado:**`0504`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="not-modified"></a>Não Modificado

O recurso não foi modificado com base na condição prévia fornecida.

**Código da Razão:** `0`

**Estado:**`0001`

**Propriedades**: nenhuma

**Carga útil**: vazio

### <a name="precondition-failed"></a>Pré-condição falhou

A condição prévia não foi cumprida, resultando no cancelamento do pedido

**Código da Razão:** `131`

**Estado:**`0104`

**Propriedades**: nenhuma

**Carga útil**: vazio

### <a name="quota-exceeded"></a>Quota Excedida

quota diária por SKU atual é excedido

**Código da Razão:** `151`

**Estado:**`0502`

**Propriedades**: nenhuma

**Carga útil**: vazio

### <a name="resource-exhausted"></a>Recursos Esgotados

recurso não tem capacidade para completar a operação

**Código da Razão:** `131`

**Estado:**`0503`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="server-busy"></a>Servidor Ocupado

servidor ocupado

**Código da Razão:** `131`

**Estado:**`0603`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |

**Carga útil**: vazio

### <a name="server-error"></a>Erro do Servidor

erro interno do servidor

**Código da Razão:** `131`

**Estado:**`0601`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |

**Carga útil**: vazio

### <a name="target-failed"></a>Alvo falhado

O alvo respondeu, mas a resposta foi inválida ou mal formada.

**Código da Razão:** `131`

**Estado:**`06A2`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="target-timeout"></a>Tempo limite de destino

cronometrado à espera do alvo para completar o pedido

**Código da Razão:** `131`

**Estado:**`06A1`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |
| reason | string | não | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga útil**: vazio

### <a name="target-unavailable"></a>Alvo Indisponível

O alvo é inalcançável para completar o pedido

**Código da Razão:** `131`

**Estado:**`06A0`

**Propriedades**: nenhuma

**Carga útil**: vazio

### <a name="throttled"></a>Acelerador

taxa de pedido é muito alta por SKU

**Código da Razão:** `151`

**Estado:**`0501`

**Propriedades**: nenhuma

**Carga útil**: vazio

### <a name="timeout"></a>Tempo Limite

operação cronometrada antes de poder ser concluída

**Código da Razão:** `131`

**Estado:**`0602`

**Propriedades:**

| Nome | Tipo | Necessário | Descrição |
| :--- | :--- | :------- | :---------- |
| trace-id | string | não | identificação de traço para correlação com diagnósticos adicionais para o erro |

**Carga útil**: vazio

