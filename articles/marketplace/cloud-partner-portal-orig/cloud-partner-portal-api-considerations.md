---
title: Considerações sobre a API | O Azure Marketplace
description: Problemas de controle de versão, tratamento de erros e autorização ao utilizar o APIs do marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935566"
---
# <a name="api-considerations"></a>Considerações sobre a API


<a name="api-versioning"></a>Controlo de versões de API
--------------

Podem existir várias versões da API que estão disponíveis ao mesmo tempo. Os clientes têm de indicar qual é a versão que pretendem invocar o uso, fornecendo o `api-version` parâmetro como parte da cadeia de consulta.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A resposta a um pedido com uma versão de API desconhecido ou inválido é um código HTTP 400. Este erro retorna a coleção de versões de API conhecidas no corpo da resposta.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Erros
------

A API responde erros com os códigos de estado HTTP correspondentes e, opcionalmente, informações adicionais na resposta serializada como JSON.
Quando receber um erro, especialmente um erro de classe de 400, não repita o pedido antes de corrigir a causa subjacente. Por exemplo, na resposta de exemplo acima, corrija o parâmetro de versão de API antes de reenviar o pedido.

<a name="authorization-header"></a>Cabeçalho de autorização
--------------------

Para todas as APIs nesta referência, tem de passar o cabeçalho de autorização, juntamente com o token de portador obtido a partir do Azure Active Directory (Azure AD). Este cabeçalho é necessário para receber uma resposta válida; Se não existir, um `401 Unauthorized` erro é retornado. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
