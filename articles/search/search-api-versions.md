---
title: Versões da API
titleSuffix: Azure Cognitive Search
description: Política de versão para Azure Cognitive Search REST APIs e a biblioteca de clientes no .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 2cdac18ed20c90861fe23f0a9ccbd02680d380af
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496694"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões API em Pesquisa Cognitiva Azure

A Azure Cognitive Search lança regularmente atualizações de funcionalidades. Por vezes, mas nem sempre, estas atualizações requerem uma nova versão da API para preservar a retrocompatibilidade. Publicar uma nova versão permite-lhe controlar quando e como integra atualizações do serviço de pesquisa no seu código.

Em regra, a equipa de Pesquisa Cognitiva do Azure publica novas versões apenas quando necessário, uma vez que pode envolver algum esforço para atualizar o seu código para usar uma nova versão API. Uma nova versão só é necessária se algum aspeto da API tiver mudado de uma forma que rompa a retrocompatibilidade. Tais alterações podem ocorrer devido a correções às funcionalidades existentes, ou devido a novas funcionalidades que alteram a área de superfície da API existente.

A mesma regra aplica-se às atualizações do SDK. O Azure Cognitive Search SDK segue as regras [de versão semântica,](https://semver.org/) o que significa que a sua versão tem três partes: maior, menor e número de construção (por exemplo, 1.1.0). Uma nova versão principal do SDK é lançada apenas para alterações que quebram a compatibilidade retrógrada. As atualizações de funcionalidades sem quebra irão incrementar a versão menor, e as correções de bugs apenas aumentarão a versão de construção.

> [!Important]
> Os Azure SDKs para .NET, Java, Python e JavaScript estão a lançar novas bibliotecas de clientes para a Azure Cognitive Search. Atualmente, nenhuma das bibliotecas Azure SDK suporta as mais recentes APIs search REST (2020-06-30) ou Management REST APIs (2020-03-13), mas isso mudará ao longo do tempo. Pode consultar periodicamente esta página ou o [What's New](whats-new.md) para anúncios sobre melhorias funcionais. 

## <a name="rest-apis"></a>APIs REST

Uma instância do serviço de Pesquisa Cognitiva Azure suporta várias versões de API REST, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que [emigre o seu código](search-api-migration.md) para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

A tabela seguinte fornece o histórico da versão atual e previamente lançada da API do Serviço de Busca. A documentação é publicada apenas para as versões mais recentes estáveis e de pré-visualização.

### <a name="search-service-apis"></a>APIs do Serviço de Pesquisa

Criar e gerir conteúdos num serviço de pesquisa.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Estado | Questão de compatibilidade retrógrada |
|-------------------------|--------|------------------------------|
| [Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Estável | Mais recente e estável lançamento das APIs Search REST, com avanços na pontuação de relevância e disponibilidade geral para a loja de conhecimento.|
| [Pesquisa 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Pré-visualizar | Versão de pré-visualização associada à versão estável. Inclui várias [funcionalidades de pré-visualização](search-api-preview.md). |
| Pesquisa 2019-05-06 | Estável | Adiciona [tipos complexos.](search-howto-complex-data-types.md) |
| Pesquisa 2019-05-06-Preview | Pré-visualizar | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2017-11-11 | Estável  | Adiciona skillsets e [enriquecimento de IA.](cognitive-search-concept-intro.md) |
| Pesquisa 2017-11-11-Preview | Pré-visualizar | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2016-09-01 |Estável | Adiciona [indexantes](search-indexer-overview.md). |
| Pesquisa 2016-09-01-Pré-visualização | Pré-visualizar | Versão de pré-visualização associada à versão estável.|
| Pesquisa 2015-02-28 | Estável  | Primeira libertação geralmente disponível.  |
| Pesquisa 2015-02-28-Preview | Pré-visualizar | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2014-10-20-Preview | Pré-visualizar | Segunda pré-estreia pública. |
| Pesquisa 2014-07-31-Preview | Pré-visualizar | Primeira pré-visualização pública. |

### <a name="management-rest-apis"></a>APIs REST de Gestão

Crie e configuure um serviço de pesquisa e gere as chaves API.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Estado | Questão de compatibilidade retrógrada |
|-------------------------|--------|------------------------------|
| [Gestão 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Estável | Mais recente libertação estável das APIs de Gestão REST, com avanços na proteção do ponto final. Adiciona [ponto final privado](service-create-private-endpoint.md) através de link privado e [regras ip de rede](service-configure-firewall.md) para novos serviços. |
| [Gestão 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Pré-visualizar  | Apesar do seu número de versão, esta ainda é a versão atual de pré-visualização das APIs de Rest de Gestão. Não há funcionalidades de pré-visualização neste momento. Todas as funcionalidades de pré-visualização passaram recentemente para a disponibilidade geral. |
| Gestão 2015-08-19  | Estável | A primeira versão geralmente disponível das APIs management REST. Fornece prestação de serviços, ampliação e gestão de chave api. |
| Gestão 2015-08-19-Pré-visualização | Pré-visualizar | A primeira versão de pré-visualização das APIs management REST. |

## <a name="azure-sdk-for-net"></a>SDK do Azure para .NET

O histórico da versão do pacote está disponível no NuGet.org. Esta tabela fornece links para cada página de pacote.


| Versão do SDK | Estado | Descrição |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11.0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Estável | Nova biblioteca de clientes da Azure .NET SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2019-05-06. |
| [Microsoft.Azure.Search 10.0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Estável | Lançado em maio de 2019. Destina-se à versão api-versão search REST=2019-05-06.|
| [Microsoft.Azure.Search 8.0-preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Pré-visualizar | lançado abril de 2019. Destina-se à versão api-versão search REST=2019-05-06-Preview.|
| [Microsoft.Azure.Management.Search 3.0.0](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Estável | Destina-se à versão api-versão management REST=2015-08-19.  |

## <a name="azure-sdk-for-java"></a>SDK do Azure para Java

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Estável | Nova biblioteca de clientes da Azure .NET SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2019-05-06. |
| [Java Management Client 1.35.0](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>SDK do Azure para JavaScript

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [JavaScript azure-search 11.0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Estável | Nova biblioteca de clientes da Azure .NET SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2016-09-01. |
| [JavaScript azure-arm-search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |

## <a name="azure-sdk-for-python"></a>SDK do Azure para Python

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Estável | Nova biblioteca de clientes da Azure .NET SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2019-05-06. |
| [Python azure-mgmt-search 1.0](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |