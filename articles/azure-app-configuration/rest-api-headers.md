---
title: Azure App Configuration REST API - Cabeçalhos
description: Páginas de referência para cabeçalhos utilizados com a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932579"
---
# <a name="headers"></a>Cabeçalhos

Este artigo fornece links para páginas de referência para cabeçalhos utilizados com a API de Configuração de Aplicação Azure.

## <a name="request-headers"></a>Pedido cabeçalhos

A tabela seguinte descreve os cabeçalhos de pedido comuns utilizados na Configuração da Aplicação Azure.

| Cabeçalho | Descrição | Exemplo |
|--|--|--|
| **Autorização** | Usado para [autenticar](./rest-api-authentication-index.md) um pedido ao serviço. Ver [secção 14.8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Aceitação** | Informa o servidor que tipo de mídia o cliente aceitará numa resposta HTTP. Ver [secção 14.1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Horário de aceitação** | Solicita ao servidor que devolva o seu conteúdo como representação do seu estado anterior. O valor deste cabeçalho é a data solicitada desse estado. Ver [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Tipo de conteúdo** | Contém o tipo de suporte do conteúdo dentro do organismo de pedido HTTP. Ver [secção 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Data** | A data em que o pedido HTTP foi emitido. Este cabeçalho é utilizado na [autenticação HMAC](./rest-api-authentication-hmac.md). Ver [secção 14.18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Anfitrião** | Especifica o arrendatário para o qual o pedido foi emitido. Este cabeçalho é utilizado na [autenticação HMAC](./rest-api-authentication-hmac.md). Ver [secção 14.23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **Se-Match** | Usado para condicionar um pedido HTTP. Este pedido só deverá ter sucesso se o ETag do recurso visado corresponder ao valor deste cabeçalho. O valor '*' corresponde a qualquer ETag. Ver [secção 14.24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Se-Nenhum-Match** | Usado para condicionar um pedido HTTP. Este pedido só deverá ter sucesso se o ETag do recurso visado não corresponder ao valor deste cabeçalho. O valor '*' corresponde a qualquer ETag. Ver [secção 14.26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | Usado para permitir a consistência em tempo real durante uma sequência de pedidos. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-cliente-pedido-id** | Uma identificação única fornecida pelo cliente usado para acompanhar a viagem de ida e volta de um pedido. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-sha256** | Uma digestão sha256 do corpo de pedido HTTP. Este cabeçalho é utilizado na [autenticação HMAC](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-data** | Este cabeçalho pode ser definido e utilizado no lugar do `Date` cabeçalho se o cabeçalho da data não puder ser acedido. Este cabeçalho é utilizado na [autenticação HMAC](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-retorno-cliente-id-id** | Usado em conjunto com o `x-ms-client-request-id` cabeçalho. Se o valor deste cabeçalho for 'verdadeiro', o servidor será instruído a devolver o valor do cabeçalho do `x-ms-client-request-id` pedido. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Cabeçalhos de Resposta

O servidor pode incluir os seguintes cabeçalhos HTTP nas suas respostas.

| Cabeçalho | Descrição | Exemplo |
|--|--|--|
| **Tipo de conteúdo** | Contém o tipo de suporte do conteúdo dentro do organismo de resposta HTTP. Ver [secção 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Um símbolo opaco que representa o estado de um dado recurso. Pode ser usado em operações condicionais. Ver [secção 14.19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Última Modificação** | Descreve quando o recurso solicitado foi modificado pela última vez. Formatado como uma [data HTTP](https://tools.ietf.org/html/rfc2616#section-3.3.1). Ver [secção 14.29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Ligação** | Fornece ligações a recursos que estão relacionados com a resposta. Este cabeçalho é utilizado para a paging utilizando o _link seguinte._ Ver [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Horário de memento** | Indica que o conteúdo contido numa resposta representa um estado anterior. O valor deste cabeçalho é a data desse estado. Ver [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-after-ms** | Proporciona um período sugerido (em milissegundos) para que o cliente aguarde antes de voltar a julgar um pedido falhado. | `retry-after-ms: 10` |
| **x-ms-request-id** | Um ID único gerado pelo servidor que é usado para rastrear o pedido dentro do serviço. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Autenticação** | Usado para desafiar os clientes para a autenticação e fornecer uma razão para que uma tentativa de autenticação falhou. Ver [secção 14.47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
