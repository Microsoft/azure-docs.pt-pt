---
title: Funcionalidades de FHIR suportadas em Azure - Azure API para FHIR
description: Este artigo explica quais as características da especificação FHIR que são implementadas na AZure API para fHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: afb4026a7865f2cc8f831d8d1d7b1d332014d310
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007575"
---
# <a name="features"></a>Funcionalidades

A Azure API para FHIR fornece uma implementação totalmente gerida do Microsoft FHIR Server para Azure. O servidor é uma implementação da norma [FHIR.](https://hl7.org/fhir) Este documento lista as principais características do Servidor FHIR.

## <a name="fhir-version"></a>Versão FHIR

Versão mais recente suportada: `4.0.1`

As versões anteriores também suportadas incluem: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| leitura                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| atualização com bloqueio otimista | Yes       | Yes       | Yes       |                                                     |
| atualização (condicional)           | Yes       | Yes       | Yes       |                                                     |
| patch                          | No        | No        | No        |                                                     |
| delete                         | Yes       | Yes       | Yes       |                                                     |
| eliminar (condicional)           | No        | No        | No        |                                                     |
| criar                         | Yes       | Yes       | Yes       | Apoiar tanto o POST/PUT                               |
| criar (condicional)           | Yes       | Yes       | Yes       |                                                     |
| pesquisar                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa acorrentada                 | No        | Yes       | No        |                                           |
| pesquisa acorrentada inversa         | No        | No        | No        |                                            |
| capacidades                   | Yes       | Yes       | Yes       |                                                     |
| lote                          | Yes       | Yes       | Yes       |                                                     |
| transação                    | No        | Yes       | No        |                                                     |
| histórico                        | Yes       | Yes       | Yes       |                                                     |
| paging                         | Parcial   | Parcial   | Parcial   | `self` e `next` são apoiados                     |
| intermediários                 | No        | No        | No        |                                                     |

## <a name="search"></a>Pesquisar

Todos os tipos de parâmetros de pesquisa são suportados. 

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| Número                | Yes       | Yes       | Yes       |         |
| Data/Data         | Yes       | Yes       | Sim       |         |
| String                | Yes       | Yes       | Yes       |         |
| Token                 | Yes       | Yes       | Yes       |         |
| Referência             | Yes       | Yes       | Yes       |         |
| Composto             | Yes       | Yes       | Yes       |         |
| Quantidade              | Yes       | Yes       | Yes       |         |
| URI                   | Yes       | Yes       | Yes       |         |
| Especial               | No        | No        | No        |         |


| Modificadores             | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Yes       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:in` (símbolo)          | No        | No        | No        |         |
|`:below` (símbolo)       | No        | No        | No        |         |
|`:above` (símbolo)       | No        | No        | No        |         |
|`:not-in` (símbolo)      | No        | No        | No        |         |
|`:[type]` (referência)  | No        | No        | No        |         |
|`:below` (uri)         | Yes       | Yes       | Yes       |         |
|`:not`                 | No        | No        | No        |         |
|`:above` (uri)         | No        | No        | No        | Emissão [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Parâmetro de pesquisa comum | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_profile`              | Yes       | Yes       | Yes       |         |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_has`                  | No        | No        | No        |         |
| `_type`                 | Yes       | Yes       | Yes       |         |
| `_query`                | No        | No        | No        |         |

| Operações de pesquisa       | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | No        | No        | No        |         |
| `_sort`                 | Parcial        | Parcial   | Parcial        |   `_sort=_lastUpdated` é apoiado       |
| `_score`                | No        | No        | No        |         |
| `_count`                | Yes       | Yes       | Yes       |         |
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é apoiado |
| `_include`              | No        | Yes       | No        |         |
| `_revinclude`           | No        | Yes       | No        | Os itens incluídos estão limitados a 100. |
| `_contained`            | No        | No        | No        |         |
| `_elements`             | Yes        | Yes        | Yes        |         |

## <a name="extended-operations"></a>Operações Alargadas

Todas as operações que são apoiadas que alargam a API RESTful.

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| $export (todo o sistema)                | Yes       | Yes       | Yes       |         |
| Paciente/$export         | Yes       | Yes       | Yes       |         |
| Grupo/$export               | Yes       | Yes       | Yes       |         |

## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência pluggável (ver [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código de código aberto do FHIR Server inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é uma base de dados multi-modelo distribuída globalmente (SQL API, MongoDB API, etc.). Suporta diferentes [níveis de consistência.](../cosmos-db/consistency-levels.md) O modelo de implementação predefinido configura o Servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) numa base de pedido por pedido utilizando o cabeçalho de `x-ms-consistency-level` pedido.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Servidor FHIR utiliza [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para controlo de acessos. Especificamente, o Controlo de Acesso Baseado em Funções (RBAC) é aplicado, se o parâmetro de `FhirServer:Security:Enabled` configuração estiver definido para `true` , e todos os pedidos (exceto ) para o Servidor `/metadata` FHIR devem ter o `Authorization` cabeçalho de pedido definido para `Bearer <TOKEN>` . O símbolo deve conter uma ou mais funções, tal como definidas na `roles` reivindicação. Um pedido será permitido se o token contiver uma função que permita a ação especificada sobre o recurso especificado.

Atualmente, as ações permitidas para um determinado papel são aplicadas *globalmente* na API.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você leu sobre as funcionalidades de FHIR suportadas na Azure API para FHIR. Em seguida, implemente a AZure API para fHIR.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)
