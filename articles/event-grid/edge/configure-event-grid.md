---
title: Configuração - Azure Event Grid IoT Edge  Microsoft Docs
description: Configuração na Grelha de Eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846464"
---
# <a name="event-grid-configuration"></a>Configuração da grelha de eventos

A Rede de Eventos fornece muitas configurações que podem ser modificadas por ambiente. A secção seguinte é uma referência a todas as opções disponíveis e aos seus incumprimentos.

## <a name="tls-configuration"></a>Configuração TLS

Para conhecer a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos da sua utilização podem ser encontrados [neste artigo.](configure-api-protocol.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Política TLS do módulo de grelha de eventos. O valor predefinido é apenas HTTPS.
|`inbound__serverAuth__serverCert__source`| Fonte do certificado de servidor utilizado pelo Módulo de Grelha de Eventos para a sua configuração TLS. O valor padrão é IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticação de cliente de entrada

Para conhecer a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-client-auth.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Para ativar/desligar a autenticação do cliente baseado em certificados. O valor padrão é verdadeiro.
|`inbound__clientAuth__clientCert__source`| Fonte para validação de certificados de cliente. O valor padrão é IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Política para permitir um certificado de cliente auto-assinado. O valor padrão é verdadeiro.
|`inbound__clientAuth__sasKeys__enabled`| Para ligar/desligar a autenticação do cliente baseado na chave SAS. O valor padrão está desligado.
|`inbound__clientAuth__sasKeys__key1`| Um dos valores para validar os pedidos de entrada.
|`inbound__clientAuth__sasKeys__key2`| Segundo valor opcional para validar pedidos de entrada.

## <a name="outgoing-client-authentication"></a>Autenticação de cliente cessante
Para conhecer a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-identity-auth.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Para ligar/desligar anexando um certificado de identidade para pedidos de saída. O valor padrão é verdadeiro.
|`outbound__clientAuth__clientCert__source`| Fonte para recuperar o certificado de saída do módulo Event Grid. O valor padrão é IoT Edge.

## <a name="webhook-event-handlers"></a>Manipuladores de eventos webhook

Para conhecer a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-webhook-subscriber-auth.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Política para controlar se apenas os assinantes HTTPS serão permitidos. O valor predefinido é verdadeiro (apenas HTTPS).
|`outbound__webhook__skipServerCertValidation`| Sinalizar para controlar se valida o certificado do assinante. O valor padrão é verdadeiro.
|`outbound__webhook__allowUnknownCA`| Política para controlar se um certificado auto-assinado pode ser apresentado por um assinante. O valor padrão é verdadeiro. 

## <a name="delivery-and-retry"></a>Entrega e tentativa

Para saber mais sobre esta funcionalidade em geral, consulte [Entrega e Retry](delivery-retry.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. O valor predefinido é 30.
| `broker__defaultEventTimeToLiveInSeconds` | Tempo de vida (TTL) em segundos após o qual um evento será abandonado se não for entregue. Valor padrão é **de 7200** segundos

## <a name="output-batching"></a>Criação de batches de saída

Para saber mais sobre esta funcionalidade em geral, consulte o [lote de entrega e saída](delivery-output-batching.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valor máximo permitido para o botão `ApproxBatchSizeInBytes`. O valor predefinido é `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido para o botão `MaxEventsPerBatch`. O valor predefinido é `50`.
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo do pedido de entrega quando apenas `MaxEventsPerBatch` é especificado. O valor predefinido é `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos para adicionar a um lote quando apenas `MaxBatchSizeInBytes` é especificado. O valor predefinido é `10`.

## <a name="metrics"></a>Métricas

Para aprender sobre a utilização de métricas com a Grelha de Eventos no IoT Edge, consulte [tópicos e subscrições](monitor-topics-subscriptions.md) do monitor

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo de repórter para métricas. O padrão é `none` e desativa as métricas. A definição para `prometheus` permite métricas no formato de exposição Prometheus.