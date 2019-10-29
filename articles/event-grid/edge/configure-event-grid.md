---
title: Configuração-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configuração na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992434"
---
# <a name="event-grid-configuration"></a>Configuração da grade de eventos

A grade de eventos fornece muitas configurações que podem ser modificadas por ambiente. A seção a seguir é uma referência a todas as opções disponíveis e seus padrões.

## <a name="tls-configuration"></a>Configuração de TLS

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos de uso podem ser encontrados neste [artigo](configure-api-protocol.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Política TLS do módulo de grade de eventos. O valor padrão é somente HTTPS.
|`inbound:serverAuth:serverCert:source`| Origem do certificado de servidor usado pelo módulo de grade de eventos para sua configuração de TLS. O valor padrão é IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticação de cliente de entrada

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-client-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Para ativar/desativar a autenticação de cliente baseada em certificado. O valor padrão é true.
|`inbound:clientAuth:clientCert:source`| Fonte para validar certificados de cliente. O valor padrão é IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Política para permitir um certificado de cliente autoassinado. O valor padrão é true.
|`inbound:clientAuth:sasKeys:enabled`| Para ativar/desativar a autenticação de cliente baseada na chave SAS. O valor padrão é off.
|`inbound:clientAuth:sasKeys:key1`| Um dos valores para validar as solicitações de entrada.
|`inbound:clientAuth:sasKeys:key2`| Segundo valor opcional para validar solicitações de entrada.

## <a name="outgoing-client-authentication"></a>Autenticação de cliente de saída
Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-identity-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Para ativar/desativar a anexação de um certificado de identidade para solicitações de saída. O valor padrão é true.
|`outbound:clientAuth:clientCert:source`| Fonte para recuperar o certificado de saída do módulo de grade de eventos. O valor padrão é IoT Edge.

## <a name="webhook-event-handlers"></a>Manipuladores de eventos de webhook

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-webhook-subscriber-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Política para controlar se somente assinantes HTTPS serão permitidos. O valor padrão é true (somente HTTPS).
|`outbound:webhook:skipServerCertValidation`| Sinalizador para controlar se o certificado do Assinante deve ser validado. O valor padrão é true.
|`outbound:webhook:allowUnknownCA`| Política para controlar se um certificado autoassinado pode ser apresentado por um assinante. O valor padrão é true. 

## <a name="delivery-and-retry"></a>Entrega e tentativa

Para saber mais sobre esse recurso em geral, confira [entrega e repetição](delivery-retry.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. O valor padrão é 30.
| `broker:defaultEventTimeToLiveInSeconds` | Tempo de vida (TTL) em segundos após o qual um evento será Descartado se não for entregue. O valor padrão é **7200** segundos

## <a name="output-batching"></a>Envio em lote de saída

Para saber mais sobre esse recurso em geral, confira [envio e envio em lote de saída](delivery-output-batching.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Valor máximo permitido para o botão de `ApproxBatchSizeInBytes`. O valor padrão é `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Valor máximo permitido para o botão de `MaxEventsPerBatch`. O valor padrão é `50`.
| `broker:defaultMaxBatchSizeInBytes` | Tamanho máximo da solicitação de entrega quando apenas `MaxEventsPerBatch` é especificado. O valor padrão é `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Número máximo de eventos a serem adicionados a um lote quando somente `MaxBatchSizeInBytes` for especificado. O valor padrão é `10`.
