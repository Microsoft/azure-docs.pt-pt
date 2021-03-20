---
title: Considerações da API - Azure Marketplace
description: Questões de versagem, tratamento de erros e autorização ao utilizar as APIs do mercado.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88035285"
---
# <a name="api-considerations"></a>Considerações sobre a API

<a name="api-versioning"></a>Versão API
--------------

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Pode haver várias versões da API que estão disponíveis ao mesmo tempo. Os clientes devem indicar qual a versão que pretendem invocar, fornecendo o `api-version` parâmetro como parte da cadeia de consulta.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A resposta a um pedido com uma versão API desconhecida ou inválida é um código HTTP 400. Este erro devolve a recolha de versões conhecidas da API no organismo de resposta.

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

A API responde a erros com os respetivos códigos de estado HTTP e, opcionalmente, informações adicionais na resposta serializada como JSON.
Quando receber um erro, especialmente um erro de 400 classes, não relemque o pedido antes de corrigir a causa subjacente. Por exemplo, na resposta da amostra acima, fixe o parâmetro da versão API antes de reensi para o reensar o pedido.

<a name="authorization-header"></a>Cabeçalho de autorização
--------------------

Para todas as APIs nesta referência, deve passar o cabeçalho de autorização juntamente com o token do portador obtido a partir do Azure Ative Directory (Azure AD). Este cabeçalho é necessário para receber uma resposta válida; se não estiver presente, um `401 Unauthorized` erro é devolvido. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
