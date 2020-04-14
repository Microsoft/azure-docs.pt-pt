---
title: Considerações da API [ Mercado Azure
description: Problemas de versão, manipulação de erros e autorização ao utilizar as APIs do mercado.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256336"
---
# <a name="api-considerations"></a>Considerações DaPi

<a name="api-versioning"></a>Versão API
--------------

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Pode haver várias versões da API que estão disponíveis ao mesmo tempo. Os clientes devem indicar qual a versão que `api-version` pretendem invocar, fornecendo o parâmetro como parte da cadeia de consulta.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A resposta a um pedido com uma versão API desconhecida ou inválida é um código HTTP 400. Este erro devolve a recolha de versões API conhecidas no corpo de resposta.

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

A API responde a erros com os códigos de estado http correspondentes e, opcionalmente, informações adicionais na resposta serializada como JSON.
Quando receber um erro, especialmente um erro de 400 classes, não tente novamente o pedido antes de fixar a causa subjacente. Por exemplo, na resposta da amostra acima, fixe o parâmetro da versão API antes de reenviar o pedido.

<a name="authorization-header"></a>Cabeçalho de autorização
--------------------

Para todas as APIs nesta referência, deve passar o cabeçalho de autorização juntamente com o token do portador obtido a partir do Azure Ative Directory (Azure AD). Este cabeçalho é necessário para receber uma resposta válida; se não estiver `401 Unauthorized` presente, um erro é devolvido. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
