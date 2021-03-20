---
title: Configuração - Azure Event Grid IoT Edge | Microsoft Docs
description: Configuração em Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171674"
---
# <a name="event-grid-configuration"></a>Configuração da grelha de eventos

A Grade de Eventos fornece muitas configurações que podem ser modificadas por ambiente. A seguinte secção é uma referência a todas as opções disponíveis e seus predefinidos.

## <a name="tls-configuration"></a>Configuração TLS

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos da sua utilização podem ser encontrados [neste artigo.](configure-api-protocol.md)

| Nome da Propriedade | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Política TLS do módulo de grelha de eventos. O valor predefinido é apenas HTTPS.
|`inbound__serverAuth__serverCert__source`| Certificado de servidor utilizado pelo Módulo de Grelha de Evento para a sua configuração TLS. O valor predefinido é IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticação de clientes

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-client-auth.md)

| Nome da Propriedade | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Para ligar/desligar a autenticação do cliente baseada em certificados. O valor predefinido é verdadeiro.
|`inbound__clientAuth__clientCert__source`| Fonte para validar certificados de cliente. O valor predefinido é IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Política para permitir um certificado de cliente auto-assinado. O valor predefinido é verdadeiro.
|`inbound__clientAuth__sasKeys__enabled`| Para ligar/desligar a autenticação do cliente baseado na chave SAS. O valor predefinido está desligado.
|`inbound__clientAuth__sasKeys__key1`| Um dos valores para validar pedidos de entrada.
|`inbound__clientAuth__sasKeys__key2`| Valor opcional de segundo valor para validar pedidos de entrada.

## <a name="outgoing-client-authentication"></a>Autenticação de clientes cessantes
Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-identity-auth.md)

| Nome da Propriedade | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Ligar/desligar anexando um certificado de identidade para pedidos de saída. O valor predefinido é verdadeiro.
|`outbound__clientAuth__clientCert__source`| Fonte para recuperar o certificado de saída do módulo Desemisso de Event Grid. O valor predefinido é IoT Edge.

## <a name="webhook-event-handlers"></a>Manipuladores de eventos Webhook

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação.](security-authentication.md) Exemplos podem ser encontrados [neste artigo.](configure-webhook-subscriber-auth.md)

| Nome da Propriedade | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Política para controlar se apenas os assinantes HTTPS serão permitidos. O valor predefinido é verdadeiro (apenas HTTPS).
|`outbound__webhook__skipServerCertValidation`| Bandeira para controlar se valida o certificado do assinante. O valor predefinido é verdadeiro.
|`outbound__webhook__allowUnknownCA`| Política para controlar se um certificado auto-assinado pode ser apresentado por um assinante. O valor predefinido é verdadeiro. 

## <a name="delivery-and-retry"></a>Entrega e tentativa

Para conhecer esta funcionalidade em geral, consulte [Entrega e Retry](delivery-retry.md).

| Nome da Propriedade | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. O valor predefinido é 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-live (TTL) em segundos após o qual um evento será largado se não for entregue. O valor predefinido é  **de 7200** segundos

## <a name="output-batching"></a>Criação de batches de saída

Para saber mais sobre esta funcionalidade em geral, consulte [o lote de entrega e saída](delivery-output-batching.md).

| Nome da Propriedade | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valor máximo permitido para o `ApproxBatchSizeInBytes` botão. O valor predefinido é `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido para o `MaxEventsPerBatch` botão. O valor predefinido é `50`.
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo do pedido de entrega quando apenas `MaxEventsPerBatch` é especificado. O valor predefinido é `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos a adicionar a um lote quando apenas `MaxBatchSizeInBytes` é especificado. O valor predefinido é `10`.

## <a name="metrics"></a>Métricas

Para aprender a usar métricas com a Grade de Eventos no IoT Edge, consulte [tópicos e subscrições do monitor](monitor-topics-subscriptions.md)

| Nome da Propriedade | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo repórter para ponto final de métricas. O padrão é `none` e desativa as métricas. Definição para `prometheus` permitir métricas no formato de exposição Prometheus.