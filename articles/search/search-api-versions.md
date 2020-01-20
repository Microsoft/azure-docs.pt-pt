---
title: Gerenciamento de versão de API para .NET e REST
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c2f774401f32ff02ced690173c418dafc4c789b5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278797"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas nem sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite que você controle quando e como você integra atualizações de serviço de pesquisa em seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se algum aspecto da API tiver sido alterado de forma a interromper a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções em recursos existentes ou devido a novos recursos que alteram a área de superfície da API existente.

A mesma regra se aplica a atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK é liberada apenas para alterações que interrompem a compatibilidade com versões anteriores. As atualizações de recursos não separáveis incrementarão a versão secundária e as correções de bugs aumentarão apenas a versão da compilação.

> [!NOTE]
> Sua instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

## <a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação para o Azure Pesquisa Cognitiva.


| Interfaces | Versão principal mais recente | Estado |
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

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Atualize para a versão mais recente da API REST até 15 de outubro de 2020
As seguintes versões da API REST do Azure Pesquisa Cognitiva serão desativadas e não terão mais suporte a partir de 15 de outubro de 2020: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**e **2015-02-28**. Além disso, as versões do SDK do .net do Azure Pesquisa Cognitiva **com mais de 3.0.0-RC** também serão desativadas, pois destinam-se a uma dessas versões da API REST. Após essa data, os aplicativos que usam qualquer uma das versões preteridas da API REST ou do SDK deixarão de funcionar e deverão ser atualizados. Assim como acontece com qualquer alteração desse tipo, estamos dando um aviso de 12 meses, portanto, você tem tempo adequado para se ajustar.  Para continuar usando o Azure Pesquisa Cognitiva, migre o código existente que tem como alvo a [API](search-api-migration.md) REST para a [api REST versão 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) ou mais recente ou o SDK do .net para a [versão 3,0](search-dotnet-sdk-migration.md) ou mais recente em 15 de outubro de 2020.  Se você tiver alguma dúvida sobre a atualização para a versão mais recente, envie um email para azuresearch_contact@microsoft.com de 15 de maio de 2020 para garantir que você tenha tempo suficiente para atualizar seu código.

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões de visualização e geralmente disponíveis
O Azure Pesquisa Cognitiva sempre libera recursos experimentais por meio da API REST primeiro e, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de visualização estão disponíveis para teste e experimentação, com o objetivo de reunir comentários sobre design e implementação de recursos. Por esse motivo, os recursos de visualização podem mudar com o passar do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso está em contraste com os recursos de uma versão GA, que são estáveis e improvável de mudar com a exceção de pequenas correções e melhorias de compatibilidade com versões anteriores. Além disso, os recursos de visualização nem sempre o tornam em uma versão GA.

Por esses motivos, é recomendável escrever o código de produção que assume uma dependência das versões de visualização. Se você estiver usando uma versão de visualização mais antiga, é recomendável migrar para a versão de GA (disponibilidade geral).

Para o SDK do .NET: a orientação para a migração de código pode ser encontrada em [atualizar o SDK do .net](search-dotnet-sdk-migration-version-9.md).

A disponibilidade geral significa que o Azure Pesquisa Cognitiva está agora sob o SLA (contrato de nível de serviço). O SLA pode ser encontrado em [contratos de nível de serviço do Azure pesquisa cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
