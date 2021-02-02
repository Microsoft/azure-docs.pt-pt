---
title: Funcionalidades de FHIR suportadas em Azure - Azure API para FHIR
description: Este artigo explica quais as características da especificação FHIR que são implementadas na AZure API para fHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: e75cf8d6660bf6f2630b83e0c2c812fa7cf59057
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430247"
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
| eliminação                         | Sim       | Sim       | Sim       |                                                     |
| eliminar (condicional)           | Não        | Não        | Não        |                                                     |
| histórico                        | Sim       | Sim       | Sim       |                                                     |
| criar                         | Sim       | Sim       | Sim       | Apoiar tanto o POST/PUT                               |
| criar (condicional)           | Sim       | Sim       | Sim       | Emissão [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| pesquisar                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa acorrentada                 | Não        | Sim       | Não        |                                                     |
| pesquisa acorrentada inversa         | Não        | Sim       | Não        |                                                     |
| capacidades                   | Sim       | Sim       | Sim       |                                                     |
| lote                          | Sim       | Sim       | Sim       |                                                     |
| transação                    | Não        | Sim       | Não        |                                                     |
| paging                         | Parcial   | Parcial   | Parcial   | `self` e `next` são apoiados                     |
| intermediários                 | Não        | Não        | Não        |                                                     |

## <a name="search"></a>Pesquisar

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
|`:[type]` (referência)  | Sim       | Sim       | Sim       |         |
|`:not`                 | Sim       | Sim       | Sim       |         |
|`:below` (uri)         | Sim       | Sim       | Sim       |         |
|`:above` (uri)         | Não        | Não        | Não        | Emissão [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (símbolo)          | Não        | Não        | Não        |         |
|`:below` (símbolo)       | Não        | Não        | Não        |         |
|`:above` (símbolo)       | Não        | Não        | Não        |         |
|`:not-in` (símbolo)      | Não        | Não        | Não        |         |

| Parâmetro de pesquisa comum | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sim       | Sim       | Sim       |         |
| `_lastUpdated`          | Sim       | Sim       | Sim       |         |
| `_tag`                  | Sim       | Sim       | Sim       |         |
| `_list`                 | Sim       | Sim       | Sim       |         |
| `_type`                 | Sim       | Sim       | Sim       | #1562 [de emissão](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Sim       | Sim       | Sim       |         |
| `_profile`              | Parcial   | Parcial   | Parcial   | Apenas apoiado na STU3, sem apoio em R4 |
| `_text`                 | Não        | Não        | Não        |         |
| `_content`              | Não        | Não        | Não        |         |
| `_has`                  | Não        | Não        | Não        |         |
| `_query`                | Não        | Não        | Não        |         |
| `_filter`               | Não        | Não        | Não        |         |

| Parâmetros de resultado de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Sim       | Sim       | Sim       | Emissão [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Sim       | Sim       | Sim       | `_count` está limitado a 100 caracteres. Se for fixado para mais de 100, apenas 100 serão devolvidos e um aviso será devolvido no pacote. |
| `_include`              | Sim       | Sim       | Sim       |Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB não inclui :suporte iterate.|
| `_revinclude`           | Sim       | Sim       | Sim       | Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB [não inclui :suporte iterate](https://github.com/microsoft/fhir-server/issues/1313). Emissão [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é apoiado |
| `_total`                | Parcial   | Parcial   | Parcial   | `_total=none` e `_total=accurate`      |
| `_sort`                 | Parcial   | Parcial   | Parcial   |   `_sort=_lastUpdated` é apoiado       |
| `_contained`            | Não        | Não        | Não        |         |
| `containedType`         | Não        | Não        | Não        |         |
| `_score`                | Não        | Não        | Não        |         |

## <a name="extended-operations"></a>Operações Alargadas

Todas as operações que são apoiadas que alargam a API RESTful.

| Tipo de parâmetro de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|------------------------|-----------|-----------|-----------|---------|
| $export (todo o sistema) | Sim       | Sim       | Sim       |         |
| Paciente/$export        | Sim       | Sim       | Sim       |         |
| Grupo/$export          | Sim       | Sim       | Sim       |         |
| $convert dados          | Sim       | Sim       | Sim       |         |


## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência pluggável (ver [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código de código aberto do FHIR Server inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é uma base de dados multi-modelo distribuída globalmente (SQL API, MongoDB API, etc.). Suporta diferentes [níveis de consistência.](../cosmos-db/consistency-levels.md) O modelo de implementação predefinido configura o Servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) numa base de pedido por pedido utilizando o cabeçalho de `x-ms-consistency-level` pedido.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Servidor FHIR utiliza [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para controlo de acessos. Especificamente, o controlo de acesso baseado em funções (RBAC) é aplicado, se o parâmetro de `FhirServer:Security:Enabled` configuração for definido para `true` , e todos os pedidos (exceto ) para o Servidor `/metadata` FHIR devem ter o `Authorization` cabeçalho de pedido definido para `Bearer <TOKEN>` . O símbolo deve conter uma ou mais funções, tal como definidas na `roles` reivindicação. Um pedido será permitido se o token contiver uma função que permita a ação especificada sobre o recurso especificado.

Atualmente, as ações permitidas para um determinado papel são aplicadas *globalmente* na API.

## <a name="service-limits"></a>Limites do serviço

* [**Unidades de Pedido (RUs)**](../cosmos-db/concepts-limits.md) - Pode configurar até 10.000 RUs no portal da Azure API para FHIR. Você precisará de um mínimo de 400 RUs ou 10 RUs/GB, o que for maior. Se você precisar de mais de 10.000 RUs, você pode colocar um bilhete de apoio para que este aumente. O máximo disponível é de 1.000.000.

* **Ligações simultâneas** e **instâncias** - Por padrão, tem cinco ligações simultâneas em duas instâncias no cluster (para um total de 10 pedidos simultâneos). Se você acredita que precisa de mais pedidos simultâneos, abra um bilhete de apoio com detalhes sobre suas necessidades.

* **Tamanho do pacote** - Cada pacote é limitado a 500 itens.

* **Tamanho dos dados** - Os dados/documentos devem ser ligeiramente inferiores a 2 MB.

## <a name="performance-expectations"></a>Expectativas de desempenho

O desempenho do sistema depende do número de RUs, ligações simultâneas e do tipo de operações que está a realizar (Put, Post, etc.). Abaixo estão algumas gamas gerais do que se pode esperar com base em RUs configurados. Em geral, o desempenho escala linearmente com um aumento de RUs:

| # de RUs | Recursos/seg |    Armazenamento Max (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1,000    | 100-150       |      100  |
| 10,000   | 225-400       |      1,000  |
| 100.000  | 2,500-4,000   |      10,000  |

Nota: Por exigência de Coss dB, existe um requisito de produção mínima de 10 RU/s por GB de armazenamento. Para mais informações, consulte [as quotas de serviço da Cosmos DB.](../cosmos-db/concepts-limits.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você leu sobre as funcionalidades de FHIR suportadas na Azure API para FHIR. Em seguida, implemente a AZure API para fHIR.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)
