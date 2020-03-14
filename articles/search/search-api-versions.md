---
title: Gestão de versão API para .NET e REST
titleSuffix: Azure Cognitive Search
description: Política de versão para ApIs de Pesquisa Cognitiva Azure e a biblioteca de clientes no .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137293"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões API em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure lança atualizações regulares. Por vezes, mas nem sempre, estas atualizações requerem uma nova versão da API para preservar a compatibilidade retrógrada. Publicar uma nova versão permite controlar quando e como integra as atualizações do serviço de pesquisa no seu código.

Em regra, a equipa de Pesquisa Cognitiva Azure publica novas versões apenas quando necessário, uma vez que pode envolver algum esforço para atualizar o seu código para usar uma nova versão API. Uma nova versão só é necessária se algum aspeto da API tiver mudado de uma forma que quebra a compatibilidade retrógrada. Tais alterações podem acontecer devido a correções às funcionalidades existentes, ou devido a novas funcionalidades que alteram a área de superfície aPi existente.

A mesma regra aplica-se às atualizações do SDK. O Azure Cognitive Search SDK segue as regras de [versão semântica,](https://semver.org/) o que significa que a sua versão tem três partes: maior, menor e número de construção (por exemplo, 1.1.0). Uma nova versão principal do SDK é lançada apenas para alterações que quebram a compatibilidade para trás. As atualizações de funcionalidades que não se rompem irão aumentar a versão menor, e as correções de bugs apenas aumentarão a versão de construção.

> [!NOTE]
> A sua instância de serviço de Pesquisa Cognitiva Azure suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigra o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em todos os pedidos através do parâmetro da versão api. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

## <a name="snapshot-of-current-versions"></a>Instantâneo das versões atuais
Abaixo está uma imagem das versões atuais de todas as interfaces de programação para a Pesquisa Cognitiva Azure.


| Interfaces | Versão principal mais recente | Estado |
| --- | --- | --- |
| [SDK do .NET](https://aka.ms/search-sdk) |9.0 |Geralmente Disponível, lançado maio de 2019 |
| [Pré-visualização de .NET SDK](https://aka.ms/search-sdk-preview) |8.0-pré-visualização |Pré-visualização, lançado abril de 2019 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Geralmente disponível |
| [Serviço REST API 2019-05-06-Pré-visualização](search-api-preview.md) |Antevisão 2019-05-06 |Pré-visualização |
| [SDK de Gestão .NET](https://aka.ms/search-mgmt-sdk) |3.0 |Geralmente disponível |
| [API REST de Gestão](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Geralmente disponível |

Para as APIs restantes, incluindo o `api-version` em cada chamada é necessário. A utilização `api-version` facilita o alvo de uma versão específica, como uma API de pré-visualização. O exemplo que se segue ilustra como é especificado o parâmetro `api-version`:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Embora cada pedido tenha um `api-version`, recomendamos que utilize a mesma versão para todos os pedidos da API. Isto é especialmente verdade quando novas versões API introduzem atributos ou operações que não são reconhecidas por versões anteriores. Misturar versões API pode ter consequências não intencionais e deve ser evitado.
>
> A API de REPOUSO de Serviço e API de Gestão REST são versonizadas independentemente umas das outras. Qualquer semelhança nos números da versão é coincidência.

As APIs geralmente disponíveis (ou GA) podem ser utilizadas na produção e estão sujeitas a acordos de nível de serviço Azure. As versões de pré-visualização têm funcionalidades experimentais que nem sempre são migradas para uma versão GA. **É fortemente aconselhável evitar a utilização de APIs de pré-visualização em aplicações de produção.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Atualização para a versão mais recente do REST API até 15 de outubro de 2020
As seguintes versões da API de Pesquisa Cognitiva Azure serão retiradas e deixarão de ser suportadas a partir de 15 de outubro de 2020: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**, e **2015-02-28**. Além disso, as versões da Pesquisa Cognitiva Azure .NET SDK com mais de **3.0.0-0-rc** também serão retiradas, uma vez que visam uma destas versões REST API. Após esta data, as aplicações que utilizem qualquer uma das versões de API ou SDK desimpedidas deixarão de funcionar e devem ser atualizadas. Como em qualquer alteração deste tipo, estamos a dar 12 meses de antecedência, por isso tem tempo suficiente para se ajustar.  Para continuar a utilizar a Pesquisa Cognitiva Azure, por favor, emigrar o código existente que visa a [versão REST API](search-api-migration.md) para rest [API versão 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) ou mais recente ou o .NET SDK para a [versão 3.0](search-dotnet-sdk-migration.md) ou mais recente até 15 de outubro de 2020.  Se tiver alguma dúvida sobre a atualização para a versão mais recente, por favor envie correio para azuresearch_contact@microsoft.com até 15 de maio de 2020 para garantir que tem tempo suficiente para atualizar o seu código.

## <a name="about-preview-and-generally-available-versions"></a>Sobre pré-visualização e versões geralmente disponíveis
A Azure Cognitive Search lança sempre as funcionalidades experimentais através da API REST primeiro, depois através de versões pré-lançamento do .NET SDK.

As funcionalidades de pré-visualização estão disponíveis para testes e experimentação, com o objetivo de recolher feedback sobre design de funcionalidades e implementação. Por esta razão, as funcionalidades de pré-visualização podem mudar ao longo do tempo, possivelmente de formas que rompem a compatibilidade. Isto contrasta com as características de uma versão GA, que são estáveis e pouco prováveis de mudar, com exceção de pequenas correções e melhorias compatíveis para trás. Além disso, as funcionalidades de pré-visualização nem sempre o transformam num lançamento de GA.

Por estas razões, recomendamos não escrever código de produção que assuma uma dependência das versões de pré-visualização. Se estiver a utilizar uma versão de pré-visualização mais antiga, recomendamos que se migrasse para a versão geralmente disponível (GA).

Para o .NET SDK: A orientação para a migração de códigos pode ser encontrada na [atualização do .NET SDK](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que a Pesquisa Cognitiva Azure está agora ao abrigo do acordo de nível de serviço (SLA). O SLA pode ser encontrado em Acordos de Nível de Serviço de [Pesquisa Cognitiva Azure.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
