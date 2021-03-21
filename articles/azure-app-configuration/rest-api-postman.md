---
title: Azure Ative Directory REST API - Teste utilizando carteiro
description: Use o Carteiro para testar a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932494"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Teste a Azure App Configuration REST API usando o Carteiro

Para testar a API REST utilizando [o Carteiro,](https://www.getpostman.com/)terá de incluir os cabeçalhos HTTP necessários para [a autenticação](./rest-api-authentication-hmac.md) nos seus pedidos. Eis como configurar o Carteiro para testar a API REST, gerando automaticamente os cabeçalhos de autenticação:

1. Criar um novo [pedido](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Adicione a `signRequest` função da amostra de [autenticação JavaScript](./rest-api-authentication-hmac.md#javascript) ao [script de pré-pedido](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) para o pedido
1. Adicione o seguinte código ao fim do script de pré-pedido. Atualizar a chave de acesso como indicado pelo comentário TODO

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Enviar o pedido
