---
title: Gerenciamento de versão de API para SDK do .NET e APIs REST-Azure Search
description: Política de versão para Azure Search APIs REST e a biblioteca de cliente no SDK do .NET.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: f400c6fcb2b35e2adcf605c96bb802041cd9e0a9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182353"
---
# <a name="api-versions-in-azure-search"></a>Versões de API no Azure Search
Azure Search distribui atualizações de recursos regularmente. Às vezes, mas nem sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite que você controle quando e como você integra atualizações de serviço de pesquisa em seu código.

Como regra, a equipe de Azure Search publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se algum aspecto da API tiver sido alterado de forma a interromper a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções em recursos existentes ou devido a novos recursos que alteram a área de superfície da API existente.

A mesma regra se aplica a atualizações do SDK. O SDK do Azure Search segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de Build (por exemplo, 1.1.0). Uma nova versão principal do SDK é liberada apenas para alterações que interrompem a compatibilidade com versões anteriores. As atualizações de recursos não separáveis incrementarão a versão secundária e as correções de bugs aumentarão apenas a versão da compilação.

> [!NOTE]
> Sua instância do serviço de Azure Search dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

## <a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação para Azure Search.


| Interfaces | Versão principal mais recente | State |
| --- | --- | --- |
| [SDK do .NET](https://aka.ms/search-sdk) |9.0 |Disponibilidade geral, lançada em maio de 2019 |
| [Versão prévia do SDK do .NET](https://aka.ms/search-sdk-preview) |8,0-visualização |Versão prévia, lançada em abril de 2019 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Disponível em Geral |
| [API REST do serviço 2019-05-06-Preview](search-api-preview.md) |2019-05-06-visualização |Pré-visualização |
| [SDK de Gestão .NET](https://aka.ms/search-mgmt-sdk) |3.0 |Disponível em Geral |
| [API REST de Gestão](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível em Geral |

Para as APIs REST, é necessário `api-version` incluir a em cada chamada. O `api-version` uso do torna mais fácil direcionar uma versão específica, como uma API de visualização. O exemplo a seguir ilustra como `api-version` o parâmetro é especificado:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Embora cada solicitação tenha um `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso é especialmente verdadeiro quando novas versões de API introduzem atributos ou operações que não são reconhecidas por versões anteriores. A combinação de versões de API pode ter consequências indesejadas e deve ser evitada.
>
> A API REST do serviço e a API REST de gerenciamento têm controle de versão de forma independente. Qualquer similaridade nos números de versão é coincidente.

As APIs de disponibilidade geral (ou GA) podem ser usadas na produção e estão sujeitas aos contratos de nível de serviço do Azure. As versões de visualização têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável evitar o uso de APIs de visualização em aplicativos de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões de visualização e geralmente disponíveis
Azure Search sempre libera previamente recursos experimentais por meio da API REST primeiro, depois por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de visualização estão disponíveis para teste e experimentação, com o objetivo de reunir comentários sobre design e implementação de recursos. Por esse motivo, os recursos de visualização podem mudar com o passar do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso está em contraste com os recursos de uma versão GA, que são estáveis e improvável de mudar com a exceção de pequenas correções e melhorias de compatibilidade com versões anteriores. Além disso, os recursos de visualização nem sempre o tornam em uma versão GA.

Por esses motivos, é recomendável escrever o código de produção que assume uma dependência das versões de visualização. Se você estiver usando uma versão de visualização mais antiga, é recomendável migrar para a versão de GA (disponibilidade geral).

Para o SDK do .NET: As diretrizes para a migração de código podem ser encontradas em [atualizar o SDK do .net](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que Azure Search agora está sob o SLA (contrato de nível de serviço). O SLA pode ser encontrado em [Azure Search contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
