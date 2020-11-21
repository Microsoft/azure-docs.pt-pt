---
title: Funcionalidades de FHIR suportadas em Azure - Azure API para FHIR
description: Este artigo explica quais as características da especificação FHIR que são implementadas na AZure API para fHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 71097f13fffbbe5cb57a69c98fb0ab272e16af5c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026306"
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
| histórico                        | Yes       | Yes       | Yes       |                                                     |
| criar                         | Yes       | Yes       | Yes       | Apoiar tanto o POST/PUT                               |
| criar (condicional)           | Yes       | Yes       | Yes       | Emissão [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| pesquisar                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa acorrentada                 | No        | Yes       | No        |                                           |
| pesquisa acorrentada inversa         | No        | No        | No        |                                            |
| capacidades                   | Yes       | Yes       | Yes       |                                                     |
| lote                          | Yes       | Yes       | Yes       |                                                     |
| transação                    | No        | Yes       | No        |                                                     |
| paging                         | Parcial   | Parcial   | Parcial   | `self` e `next` são apoiados                     |
| intermediários                 | No        | No        | No        |                                                     |

## <a name="search"></a>Pesquisa

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
| `_filter`               | No        | No        | No        |         |

| Parâmetros de resultado de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Parcial        | Parcial   | Parcial        |   `_sort=_lastUpdated` é apoiado       |
| `_count`                | Yes       | Yes       | Yes       | `_count` está limitado a 100 caracteres. Se for fixado para mais de 100, apenas 100 serão devolvidos e um aviso será devolvido no pacote. |
| `_include`              | Yes       | Yes       | Yes       |Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB não inclui :suporte iterate.|
| `_revinclude`           | Yes       | Yes       | Yes       | Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB não inclui :suporte iterate.|
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é apoiado |
| `_total`                | Parcial   | Parcial   | Parcial   | _total=não e _total=preciso      |
| `_elements`             | Yes       | Yes       | Yes       |         |
| `_contained`            | No        | No        | No        |         |
| `containedType`         | No        | No        | No        |         |
| `_score`                | No        | No        | No        |         |

## <a name="extended-operations"></a>Operações Alargadas

Todas as operações que são apoiadas que alargam a API RESTful.

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|------------------------|-----------|-----------|-----------|---------|
| $export (todo o sistema) | Yes       | Yes       | Yes       |         |
| Paciente/$export        | Yes       | Yes       | Yes       |         |
| Grupo/$export          | Yes       | Yes       | Yes       |         |

## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência pluggável (ver [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código de código aberto do FHIR Server inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é uma base de dados multi-modelo distribuída globalmente (SQL API, MongoDB API, etc.). Suporta diferentes [níveis de consistência.](../cosmos-db/consistency-levels.md) O modelo de implementação predefinido configura o Servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) numa base de pedido por pedido utilizando o cabeçalho de `x-ms-consistency-level` pedido.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Servidor FHIR utiliza [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para controlo de acessos. Especificamente, Role-Based Controlo de Acesso (RBAC) é aplicado, se o `FhirServer:Security:Enabled` parâmetro de configuração estiver definido para `true` , e todos os pedidos (exceto `/metadata` ) para o Servidor FHIR devem ter `Authorization` o cabeçalho de pedido definido para `Bearer <TOKEN>` . O símbolo deve conter uma ou mais funções, tal como definidas na `roles` reivindicação. Um pedido será permitido se o token contiver uma função que permita a ação especificada sobre o recurso especificado.

Atualmente, as ações permitidas para um determinado papel são aplicadas *globalmente* na API.

## <a name="service-limits"></a>Limites do serviço

* [**Unidades de Pedido (RUs)**](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) - Pode configurar até 10.000 RUs no portal da Azure API para FHIR. Você precisará de um mínimo de 400 RUs ou 10 RUs/GB, o que for maior. Se você precisar de mais de 10.000 RUs, você pode colocar um bilhete de apoio para que este aumente. O máximo disponível é de 1.000.000.

* **Conexões e instâncias simultâneas** - Por dafault, tem cinco ligações simultâneas em duas instâncias no cluster (para um total de 10 pedidos simultâneos). **Instances** Se você acredita que precisa de mais pedidos simultâneos, abra um bilhete de apoio com detalhes sobre suas necessidades.

* **Tamanho do pacote** - Cada pacote é limitado a 500 itens.

* **Tamanho dos dados** - Os dados/documentos devem ser ligeiramente inferiores a 2 MB.

## <a name="performance-expectations"></a>Expectativas de desempenho

O desempenho do sistema depende do número de RUs, ligações simultâneas e do tipo de operações que está a realizar (Put, Post, etc.). Abaixo estão algumas gamas gerais do que se pode esperar com base em RUs configurados. Em geral, o desempenho escala linearmente com um aumento de RUs:

| # de RUs | Recursos/seg |
|----------|---------------|
| 400      | 5-10          |
| 1,000    | 100-150       |
| 10,000   | 225-400       |
| 100.000  | 2,500-4,000   |

## <a name="next-steps"></a>Próximos passos

Neste artigo, você leu sobre as funcionalidades de FHIR suportadas na Azure API para FHIR. Em seguida, implemente a AZure API para fHIR.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)
