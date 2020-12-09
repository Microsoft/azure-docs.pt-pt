---
title: Azure App Configuration REST API - Autorização HMAC
description: Utilize o HMAC para autorização contra a Configuração de Aplicações Azure utilizando a API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932664"
---
# <a name="hmac-authorization---rest-api-reference"></a>Autorização HMAC - Referência REST API

Quando a autenticação HMAC é utilizada, as operações enquadram-se numa de duas categorias, ler ou escrever. As chaves de acesso de leitura-escrita concedem permissão para ligar para todas as operações. As chaves de acesso apenas de leitura concedem permissão para ligar apenas para operações de leitura. Se uma chave de acesso é apenas de leitura ou leitura-escrita é determinada pela sua `readOnly` propriedade. Qualquer tentativa de fazer um pedido de escrita com uma chave de acesso apenas de leitura resultará na não autorização do pedido.

## <a name="obtaining-access-keys"></a>Obtenção de chaves de acesso

A especificação que descreve as teclas de acesso e a API utilizada para as obter está detalhada na especificação do fornecedor de recursos de configuração de aplicações Azure [aqui.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json) As chaves de acesso são obtidas através da operação "ConfigurationStores_ListKeys".

## <a name="errors"></a>Erros

```http
HTTP/1.1 403 Forbidden
```

**Razão:** A chave de acesso utilizada para autenticar o pedido não fornece as permissões necessárias para a realização da operação solicitada.
**Solução:** Obtenha uma chave de acesso que forneça permissão para realizar a operação solicitada e use-a para autenticar o pedido.
