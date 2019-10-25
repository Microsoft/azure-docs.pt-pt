---
title: Gerenciamento de versão de API para SDK do .NET e APIs REST
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a976655d0f634c7026f008d64516a629947e90c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793069"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas nem sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite que você controle quando e como você integra atualizações de serviço de pesquisa em seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se algum aspecto da API tiver sido alterado de forma a interromper a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções em recursos existentes ou devido a novos recursos que alteram a área de superfície da API existente.

A mesma regra se aplica a atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK é liberada apenas para alterações que interrompem a compatibilidade com versões anteriores. As atualizações de recursos não separáveis incrementarão a versão secundária e as correções de bugs aumentarão apenas a versão da compilação.

> [!NOTE]
> Sua instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

## <a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação para o Azure Pesquisa Cognitiva.


| Interface | Versão principal mais recente | Estado |
| --- | --- | --- |
| [SDK do .NET](https://aka.ms/search-sdk) |9.0 |Disponibilidade geral, lançada em maio de 2019 |
| [Versão prévia do SDK do .NET](https://aka.ms/search-sdk-preview) |8,0-visualização |Versão prévia, lançada em abril de 2019 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Disponível em Geral |
| [API REST do serviço 2019-05-06-Preview](search-api-preview.md) |2019-05-06-visualização |Pré-visualização |
| [SDK de Gestão .NET](https://aka.ms/search-mgmt-sdk) |3.0 |Disponível em Geral |
| [API REST de Gestão](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível em Geral |

Para as APIs REST, é necessário incluir o `api-version` em cada chamada. O uso de `api-version` facilita a destinação a uma versão específica, como uma API de visualização. O exemplo a seguir ilustra como o parâmetro `api-version` é especificado:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso é especialmente verdadeiro quando novas versões de API introduzem atributos ou operações que não são reconhecidas por versões anteriores. A combinação de versões de API pode ter consequências indesejadas e deve ser evitada.
>
> A API REST do serviço e a API REST de gerenciamento têm controle de versão de forma independente. Qualquer similaridade nos números de versão é coincidente.

As APIs de disponibilidade geral (ou GA) podem ser usadas na produção e estão sujeitas aos contratos de nível de serviço do Azure. As versões de visualização têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável evitar o uso de APIs de visualização em aplicativos de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões de visualização e geralmente disponíveis
O Azure Pesquisa Cognitiva sempre libera recursos experimentais por meio da API REST primeiro e, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de visualização estão disponíveis para teste e experimentação, com o objetivo de reunir comentários sobre design e implementação de recursos. Por esse motivo, os recursos de visualização podem mudar com o passar do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso está em contraste com os recursos de uma versão GA, que são estáveis e improvável de mudar com a exceção de pequenas correções e melhorias de compatibilidade com versões anteriores. Além disso, os recursos de visualização nem sempre o tornam em uma versão GA.

Por esses motivos, é recomendável escrever o código de produção que assume uma dependência das versões de visualização. Se você estiver usando uma versão de visualização mais antiga, é recomendável migrar para a versão de GA (disponibilidade geral).

Para o SDK do .NET: a orientação para a migração de código pode ser encontrada em [atualizar o SDK do .net](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que o Azure Pesquisa Cognitiva está agora sob o SLA (contrato de nível de serviço). O SLA pode ser encontrado em [contratos de nível de serviço do Azure pesquisa cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
