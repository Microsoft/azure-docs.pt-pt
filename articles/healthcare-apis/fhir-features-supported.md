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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
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
| leitura                           | Sim       | Sim       | Sim       |                                                     |
| vread                          | Sim       | Sim       | Sim       |                                                     |
| update                         | Sim       | Sim       | Sim       |                                                     |
| atualização com bloqueio otimista | Sim       | Sim       | Sim       |                                                     |
| atualização (condicional)           | Sim       | Sim       | Sim       |                                                     |
| patch                          | Não        | Não        | Não        |                                                     |
| delete                         | Sim       | Sim       | Sim       |                                                     |
| eliminar (condicional)           | Não        | Não        | Não        |                                                     |
| criar                         | Sim       | Sim       | Sim       | Apoiar tanto o POST/PUT                               |
| criar (condicional)           | Sim       | Sim       | Sim       |                                                     |
| pesquisar                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa acorrentada                 | Não        | Sim       | Não        |                                           |
| pesquisa acorrentada inversa         | Não        | Não        | Não        |                                            |
| capacidades                   | Sim       | Sim       | Sim       |                                                     |
| lote                          | Sim       | Sim       | Sim       |                                                     |
| transação                    | Não        | Sim       | Não        |                                                     |
| histórico                        | Sim       | Sim       | Sim       |                                                     |
| paging                         | Parcial   | Parcial   | Parcial   | `self` e `next` são apoiados                     |
| intermediários                 | Não        | Não        | Não        |                                                     |

## <a name="search"></a>Pesquisa

Todos os tipos de parâmetros de pesquisa são suportados. 

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| Número                | Sim       | Sim       | Sim       |         |
| Data/Data         | Sim       | Sim       | Sim       |         |
| String                | Sim       | Sim       | Sim       |         |
| Token                 | Sim       | Sim       | Sim       |         |
| Referência             | Sim       | Sim       | Sim       |         |
| Composto             | Sim       | Sim       | Sim       |         |
| Quantidade              | Sim       | Sim       | Sim       |         |
| URI                   | Sim       | Sim       | Sim       |         |
| Especial               | Não        | Não        | Não        |         |


| Modificadores             | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sim       | Sim       | Sim       |         |
|`:exact`               | Sim       | Sim       | Sim       |         |
|`:contains`            | Sim       | Sim       | Sim       |         |
|`:text`                | Sim       | Sim       | Sim       |         |
|`:in` (símbolo)          | Não        | Não        | Não        |         |
|`:below` (símbolo)       | Não        | Não        | Não        |         |
|`:above` (símbolo)       | Não        | Não        | Não        |         |
|`:not-in` (símbolo)      | Não        | Não        | Não        |         |
|`:[type]` (referência)  | Não        | Não        | Não        |         |
|`:below` (uri)         | Sim       | Sim       | Sim       |         |
|`:not`                 | Não        | Não        | Não        |         |
|`:above` (uri)         | Não        | Não        | Não        | Emissão [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Parâmetro de pesquisa comum | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sim       | Sim       | Sim       |         |
| `_lastUpdated`          | Sim       | Sim       | Sim       |         |
| `_tag`                  | Sim       | Sim       | Sim       |         |
| `_profile`              | Sim       | Sim       | Sim       |         |
| `_security`             | Sim       | Sim       | Sim       |         |
| `_text`                 | Não        | Não        | Não        |         |
| `_content`              | Não        | Não        | Não        |         |
| `_list`                 | Sim       | Sim       | Sim       |         |
| `_has`                  | Não        | Não        | Não        |         |
| `_type`                 | Sim       | Sim       | Sim       |         |
| `_query`                | Não        | Não        | Não        |         |

| Operações de pesquisa       | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Não        | Não        | Não        |         |
| `_sort`                 | Parcial        | Parcial   | Parcial        |   `_sort=_lastUpdated` é apoiado       |
| `_score`                | Não        | Não        | Não        |         |
| `_count`                | Sim       | Sim       | Sim       |         |
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é apoiado |
| `_include`              | Não        | Sim       | Não        |         |
| `_revinclude`           | Não        | Sim       | Não        | Os itens incluídos estão limitados a 100. |
| `_contained`            | Não        | Não        | Não        |         |
| `_elements`             | Sim        | Sim        | Sim        |         |

## <a name="extended-operations"></a>Operações Alargadas

Todas as operações que são apoiadas que alargam a API RESTful.

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| $export (todo o sistema)                | Sim       | Sim       | Sim       |         |
| Paciente/$export         | Sim       | Sim       | Sim       |         |
| Grupo/$export               | Sim       | Sim       | Sim       |         |

## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência pluggável (ver [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código de código aberto do FHIR Server inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é uma base de dados multi-modelo distribuída globalmente (SQL API, MongoDB API, etc.). Suporta diferentes [níveis de consistência.](../cosmos-db/consistency-levels.md) O modelo de implementação predefinido configura o Servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) numa base de pedido por pedido utilizando o cabeçalho de `x-ms-consistency-level` pedido.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Servidor FHIR utiliza [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para controlo de acessos. Especificamente, Role-Based Controlo de Acesso (RBAC) é aplicado, se o `FhirServer:Security:Enabled` parâmetro de configuração estiver definido para `true` , e todos os pedidos (exceto `/metadata` ) para o Servidor FHIR devem ter `Authorization` o cabeçalho de pedido definido para `Bearer <TOKEN>` . O símbolo deve conter uma ou mais funções, tal como definidas na `roles` reivindicação. Um pedido será permitido se o token contiver uma função que permita a ação especificada sobre o recurso especificado.

Atualmente, as ações permitidas para um determinado papel são aplicadas *globalmente* na API.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você leu sobre as funcionalidades de FHIR suportadas na Azure API para FHIR. Em seguida, implemente a AZure API para fHIR.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)
