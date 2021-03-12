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
ms.openlocfilehash: a31fb48443cf760186faad705b8be21a62846a44
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020801"
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
| delete                         | Yes       | Yes       | Yes       |  Ver Nota Abaixo                                                   |
| eliminar (condicional)           | No        | No        | No        |                                                     |
| histórico                        | Yes       | Yes       | Yes       |                                                     |
| criar                         | Yes       | Yes       | Yes       | Apoiar tanto o POST/PUT                               |
| criar (condicional)           | Yes       | Yes       | Yes       | Emissão [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| pesquisar                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa acorrentada                 | No        | Yes       | No        |                                                     |
| pesquisa acorrentada inversa         | No        | Yes       | No        |                                                     |
| capacidades                   | Yes       | Yes       | Yes       |                                                     |
| lote                          | Yes       | Yes       | Yes       |                                                     |
| transação                    | No        | Yes       | No        |                                                     |
| paging                         | Parcial   | Parcial   | Parcial   | `self` e `next` são apoiados                     |
| intermediários                 | No        | No        | No        |                                                     |

> [!Note]
> A eliminação definida pela especificação FHIR requer que, após a eliminação, as leituras específicas subsequentes não-versão de um recurso retorne um código de estado 410 HTTP e o recurso já não seja encontrado através da pesquisa. A Azure API para FHIR também lhe permite eliminar totalmente (incluindo toda a história) o recurso. Para eliminar totalmente o recurso, pode passar as definições de parâmetro `hardDelete` para verdadeiro `DELETE {server}/{resource}/{id}?hardDelete=true` (). Se não passar este parâmetro ou definido `hardDelete` como falso, as versões históricas do recurso continuarão disponíveis.

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
|`:[type]` (referência)  | Yes       | Yes       | Yes       |         |
|`:not`                 | Yes       | Yes       | Yes       |         |
|`:below` (uri)         | Yes       | Yes       | Yes       |         |
|`:above` (uri)         | No        | No        | No        | Emissão [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (símbolo)          | No        | No        | No        |         |
|`:below` (símbolo)       | No        | No        | No        |         |
|`:above` (símbolo)       | No        | No        | No        |         |
|`:not-in` (símbolo)      | No        | No        | No        |         |

| Parâmetro de pesquisa comum | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_type`                 | Yes       | Yes       | Yes       | #1562 [de emissão](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_profile`              | Parcial   | Parcial   | Parcial   | Apoiado na STU3. Se criou a sua base de dados **depois de** 20 de fevereiro de 2021, também terá suporte em R4. Estamos a trabalhar para permitir _profile em bases de dados criadas antes de 20 de fevereiro de 2021. |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_has`                  | No        | No        | No        |         |
| `_query`                | No        | No        | No        |         |
| `_filter`               | No        | No        | No        |         |

| Parâmetros de resultado de pesquisa | Suportado - PaaS | Suportado - OSS (SQL) | Suportado - OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Yes       | Yes       | Yes       | Emissão [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Yes       | Yes       | Yes       | `_count` está limitado a 1000 caracteres. Se for fixado para mais de 1000, apenas 1000 serão devolvidos e um aviso será devolvido no pacote. |
| `_include`              | Yes       | Yes       | Yes       |Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB não inclui :suporte iterate.|
| `_revinclude`           | Yes       | Yes       | Yes       | Os itens incluídos estão limitados a 100. Incluir no PaaS e o OSS na Cosmos DB [não inclui :suporte iterate](https://github.com/microsoft/fhir-server/issues/1313). Emissão [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é apoiado |
| `_total`                | Parcial   | Parcial   | Parcial   | `_total=none` e `_total=accurate`      |
| `_sort`                 | Parcial   | Parcial   | Parcial   |   `_sort=_lastUpdated` é apoiado       |
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
| $convert dados          | Yes       | Yes       | Yes       |         |


## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência pluggável (ver [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código de código aberto do FHIR Server inclui uma implementação para [Azure Cosmos DB](../../cosmos-db/index-overview.md) e [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é uma base de dados multi-modelo distribuída globalmente (SQL API, MongoDB API, etc.). Suporta diferentes [níveis de consistência.](../../cosmos-db/consistency-levels.md) O modelo de implementação predefinido configura o Servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) numa base de pedido por pedido utilizando o cabeçalho de `x-ms-consistency-level` pedido.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O Servidor FHIR utiliza [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para controlo de acessos. Especificamente, o controlo de acesso baseado em funções (RBAC) é aplicado, se o parâmetro de `FhirServer:Security:Enabled` configuração for definido para `true` , e todos os pedidos (exceto ) para o Servidor `/metadata` FHIR devem ter o `Authorization` cabeçalho de pedido definido para `Bearer <TOKEN>` . O símbolo deve conter uma ou mais funções, tal como definidas na `roles` reivindicação. Um pedido será permitido se o token contiver uma função que permita a ação especificada sobre o recurso especificado.

Atualmente, as ações permitidas para um determinado papel são aplicadas *globalmente* na API.

## <a name="service-limits"></a>Limites do serviço

* [**Unidades de Pedido (RUs)**](../../cosmos-db/concepts-limits.md) - Pode configurar até 10.000 RUs no portal da Azure API para FHIR. Você precisará de um mínimo de 400 RUs ou 10 RUs/GB, o que for maior. Se você precisar de mais de 10.000 RUs, você pode colocar um bilhete de apoio para que este aumente. O máximo disponível é de 1.000.000.

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

Nota: Por exigência de Coss dB, existe um requisito de produção mínima de 10 RU/s por GB de armazenamento. Para mais informações, consulte [as quotas de serviço da Cosmos DB.](../../cosmos-db/concepts-limits.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você leu sobre as funcionalidades de FHIR suportadas na Azure API para FHIR. Em seguida, implemente a AZure API para fHIR.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)
