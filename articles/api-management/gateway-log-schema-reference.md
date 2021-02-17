---
title: Referência - Registo de recursos da API Management Azure
description: Referência de Schema para o registo de recursos da Azure API Management GatewayLogs
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: df0018e323bc0c5725c9752b25b77612f035c196
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582501"
---
# <a name="reference-api-management-resource-log-schema"></a>Referência: Esquema de registo de recursos da API Management

Este artigo fornece uma referência de esquema para o registo de recursos da Azure API Management GatewayLogs. As entradas de registo também incluem campos no [esquema comum de nível superior.](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)

Para permitir a recolha do registo de recursos na Gestão da API, consulte [as APIs publicadas no Monitor](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>Esquema gatewaylogs

As seguintes propriedades são registadas para cada pedido de API.

| Propriedade  | Tipo | Description |
| ------------- | ------------- | ------------- |
| método | string | Método HTTP do pedido a receber |
| url | string | URL do pedido a receber |
| responseCode | número inteiro | Código de estado da resposta  HTTP enviada para um cliente |
| responseSize | número inteiro | Número de bytes enviados para um cliente durante o processamento do pedido | 
| cache | string | Estado do envolvimento da cache da API Management no processamento de pedidos (atropelamento, falta, nenhum) | 
| apiId | string | Identificador da entidade de API do pedido atual | 
| operationId | string | Identificador da entidade de operação do pedido atual | 
| clientProtocol | string | Versão do protocolo HTTP do pedido a receber |
| clientTime | número inteiro | Número de milissegundos gastos em I/O do cliente global (ligar, enviar e receber bytes) | 
| apiRevision | string | Revisão da API para pedido em curso | 
| clientTlsVersion| string | Versão TLS usada pelo pedido de envio do cliente |
| lastError | objeto | Para um pedido infrutífero, detalhes sobre o último erro de processamento do pedido | 
| backendMethod | string | Método HTTP do pedido enviado para um back-end |
| backendUrl | string | URLdo pedido enviado para um back-end |
| backendResponseCode | número inteiro | Código da resposta HTTP recebido a partir de um back-end |
| apoiadoProtocol | string | Versão do protocolo HTTP do pedido enviado para um back-end |
| backendTime | número inteiro | Número de milissegundos gastos em IO de backend global (ligar, enviar e receber bytes) | 


## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização de APIs na Gestão de API, consulte [as APIs publicadas no Monitor](api-management-howto-use-azure-monitor.md)
* Saiba mais sobre [esquemas comuns e específicos de serviço para registos de recursos Azure](../azure-monitor/essentials/resource-logs-schema.md)

