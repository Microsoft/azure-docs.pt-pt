---
title: Considerações sobre API | Azure Marketplace
description: Controle de versão, tratamento de erros e problemas de autorização ao usar as APIs do Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819731"
---
# <a name="api-considerations"></a>Considerações sobre API


<a name="api-versioning"></a>Controlo de versões de API
--------------

Pode haver várias versões da API que estão disponíveis ao mesmo tempo. Os clientes devem indicar qual versão desejam invocar usar fornecendo o parâmetro `api-version` como parte da cadeia de caracteres de consulta.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A resposta a uma solicitação com uma versão de API desconhecida ou inválida é um código HTTP 400. Esse erro retorna a coleção de versões de API conhecidas no corpo da resposta.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Erros
------

A API responde a erros com os códigos de status HTTP correspondentes e, opcionalmente, informações adicionais na resposta serializadas como JSON.
Quando você receber um erro, especialmente um erro de classe 400, não repita a solicitação antes de corrigir a causa subjacente. Por exemplo, na resposta de exemplo acima, corrija o parâmetro de versão da API antes de reenviar a solicitação.

<a name="authorization-header"></a>Cabeçalho de autorização
--------------------

Para todas as APIs nesta referência, você deve passar o cabeçalho de autorização junto com o token de portador obtido do Azure Active Directory (AD do Azure). Esse cabeçalho é necessário para receber uma resposta válida; Se não estiver presente, um erro de `401 Unauthorized` será retornado. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
