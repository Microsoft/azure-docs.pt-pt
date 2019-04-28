---
title: Gestão de versões de API para o SDK do .NET e APIs REST - Azure Search
description: Política de versão para as APIs de REST do Azure Search e a biblioteca de cliente no SDK do .NET.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c07a0c8f5440033455c69fe40806adf9b548c16f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126964"
---
# <a name="api-versions-in-azure-search"></a>Versões de API no Azure Search
O Azure Search implementa atualizações de funcionalidades regularmente. Às vezes, mas nem sempre, estas atualizações requerem uma nova versão da API para preservar a compatibilidade com versões anteriores. Publicar uma nova versão permite-lhe controlar quando e como integrar atualizações de serviço de pesquisa em seu código.

Como regra, a equipe de pesquisa do Azure publica novas versões apenas quando necessário, uma vez que pode envolver algum esforço para atualizar o código para usar uma nova versão de API. Uma nova versão é necessária apenas se algum aspeto da API foi alterado de forma que venha a compatibilidade com versões anteriores. Tais alterações podem ocorrer devido a correções para os recursos existentes, ou devido a novos recursos que alteram a área de superfície de API existente.

A mesma regra se aplica a atualizações do SDK. Segue-se o SDK do Azure Search a [controle de versão semântica](https://semver.org/) regras, o que significa que a sua versão possui três partes: hlavní, menor e número (por exemplo, 1.1.0) de compilação. Uma versão principal nova do SDK é lançada apenas para as alterações que interromper a compatibilidade com versões anteriores. Atualizações de funcionalidades mais recentes não irão incrementar a versão secundária e correções de erros será aumentada somente a versão de compilação.

> [!NOTE]
> A instância de serviço do Azure Search oferece suporte a várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando ele é já não é a mais recente, mas recomendamos que migre o código para usar a versão mais recente. Ao utilizar a API REST, tem de especificar a versão de API em cada solicitação por meio do parâmetro de versão de api. Quando utilizar o SDK de .NET, a versão do SDK que está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço é atualizado para suportar uma versão mais recente do API.

## <a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais
Abaixo é um instantâneo das versões atuais de todas as interfaces de programação para o Azure Search.

| Interfaces | Versão principal mais recente | Estado |
| --- | --- | --- |
| [SDK do .NET](https://aka.ms/search-sdk) |5.0 |Disponível em geral, lançado Abril de 2018 |
| [Pré-visualização do .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-preview |Pré-visualização, lançado em Maio de 2017 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Disponível em Geral |
| [Serviço REST API 2017-11-11-pré-visualização](search-api-2017-11-11-preview.md) |2017-11-11-Preview |Pré-visualização |
| [SDK de Gestão .NET](https://aka.ms/search-mgmt-sdk) |2.0 |Disponível em Geral |
| [API REST de Gestão](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível em Geral |

Para as APIs REST, incluindo o `api-version` em cada chamada é necessária. Usando `api-version` torna mais fácil para uma versão específica, por exemplo, uma API de pré-visualização de destino. O exemplo a seguir ilustra como o `api-version` parâmetro for especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Embora cada pedido tem um `api-version`, recomendamos que utilize a mesma versão para todos os pedidos de API. Isso é especialmente verdadeiro quando novas versões de API introduzem atributos ou operações que não são reconhecidas por versões anteriores. Misturar as versões de API pode ter conseqüências indesejadas e deve ser evitado.
>
> A API REST do serviço e a API de REST de gestão têm versões independentemente uns dos outros. Qualquer semelhança em números de versão é uma coincidência.

Em disponibilidade geral (ou GA) APIs podem ser utilizadas na produção e estão sujeitas a contratos de nível de serviço do Azure. Versões de pré-visualização tem funcionalidades experimentais que não estão sempre migradas para uma versão de disponibilidade geral. **É recomendável evitar usar as APIs de pré-visualização em aplicações de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre as versões de pré-visualização e disponível em geral
O Azure Search sempre previamente versões funcionalidades experimentais através da API REST em primeiro lugar, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Funcionalidades de pré-visualização estão disponíveis para teste e experimentação, com o objetivo de recolha de comentários no design de funcionalidade e a implementação. Por esse motivo, as funcionalidades de pré-visualização podem alterar ao longo do tempo, possivelmente de forma a interromper a compatibilidade com versões anteriores. Isso contrasta com recursos numa versão de disponibilidade geral, que são estáveis e improvável que sejam alteradas com a exceção pequeno compatível correções e aprimoramentos. Além disso, as funcionalidades de pré-visualização não sempre a tornar a uma versão de disponibilidade geral.

Por esses motivos, recomendamos a escrever um código de produção que utiliza uma dependência em versões de pré-visualização. Se estiver a utilizar uma versão de pré-visualização mais antiga, recomendamos que migre para a versão em disponibilidade geral (GA).

Para o SDK .NET: Orientações para a migração de código podem ser encontradas em [atualizar o SDK de .NET](search-dotnet-sdk-migration.md).

Disponibilidade geral, significa que o Azure Search agora está sob o contrato de nível de serviço (SLA). O SLA pode ser encontrado em [contratos de nível de serviço do Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
