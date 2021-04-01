---
title: Versões da API
titleSuffix: Azure Cognitive Search
description: Política de versão para Azure Cognitive Search REST APIs e a biblioteca de clientes no .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: d2308d065755df834a224ef3b1df202d7b7dc5ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518951"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões API em Pesquisa Cognitiva Azure

A Azure Cognitive Search lança regularmente atualizações de funcionalidades. Por vezes, mas nem sempre, estas atualizações requerem uma nova versão da API para preservar a retrocompatibilidade. Publicar uma nova versão permite-lhe controlar quando e como integra atualizações do serviço de pesquisa no seu código.

Em regra, a equipa de Pesquisa Cognitiva do Azure publica novas versões apenas quando necessário, uma vez que pode envolver algum esforço para atualizar o seu código para usar uma nova versão API. Uma nova versão só é necessária se algum aspeto da API tiver mudado de uma forma que rompa a retrocompatibilidade. Tais alterações podem ocorrer devido a correções às funcionalidades existentes, ou devido a novas funcionalidades que alteram a área de superfície da API existente.

A mesma regra aplica-se às atualizações do SDK. O Azure Cognitive Search SDK segue as regras [de versão semântica,](https://semver.org/) o que significa que a sua versão tem três partes: maior, menor e número de construção (por exemplo, 1.1.0). Uma nova versão principal do SDK é lançada apenas para alterações que quebram a compatibilidade retrógrada. As atualizações de funcionalidades sem quebra irão incrementar a versão menor, e as correções de bugs apenas aumentarão a versão de construção.

> [!Important]
> Os Azure SDKs para .NET, Java, Python e JavaScript estão a lançar novas bibliotecas de clientes para a Azure Cognitive Search. Atualmente, nenhuma das bibliotecas Azure SDK suporta totalmente as mais recentes APIs de Search REST (2020-06-30) ou Management REST APIs (2020-03-13), mas isso mudará ao longo do tempo. Pode consultar periodicamente esta página ou o [What's New](whats-new.md) para anúncios sobre melhorias funcionais.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versões não suportadas

Atualizar as soluções de pesquisa existentes para a versão mais recente da API REST até 15 de outubro de 2020. Nessa altura, as seguintes versões da Azure Cognitive Search REST API serão aposentadas e deixarão de ser suportadas:

+ **2015-02-28**
+ **2015-02-28-Pré-visualização**
+ **2014-07-31-Pré-visualização**
+ **2014-10-20-Pré-visualização**

Além disso, as versões do Azure Cognitive Search .NET SDK com mais de [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) também serão retiradas, uma vez que visam uma destas versões REST API. 

Após esta data, as aplicações que utilizem qualquer uma das versões precímos da API ou SDK deixarão de funcionar e devem ser atualizadas. Como em qualquer alteração deste tipo, estamos a dar um pré-aviso de 12 meses, para que tenha tempo suficiente para se ajustar.

Para continuar a utilizar a Azure Cognitive Search, por favor emigure o código existente que direcione a [API](search-api-migration.md) REST para a [versão REST API 2020-06-30](/rest/api/searchservice/) ou para um SDK mais recente até 15 de outubro de 2020.  Se tiver alguma dúvida sobre a atualização para a versão mais recente, por favor envie o correio azuresearch_contact@microsoft.com para o dia 15 de maio de 2020 para garantir que tem tempo suficiente para atualizar o seu código.

## <a name="rest-apis"></a>APIs REST

Uma instância do serviço de Pesquisa Cognitiva Azure suporta várias versões de API REST, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que [emigre o seu código](search-api-migration.md) para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

A tabela seguinte fornece o histórico da versão atual e previamente lançada da API do Serviço de Busca. A documentação é publicada apenas para as versões mais recentes estáveis e de pré-visualização.

### <a name="search-service-apis"></a>APIs do Serviço de Pesquisa

Criar e gerir conteúdos num serviço de pesquisa.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Estado | Descrição |
|-------------------------|--------|------------------------------|
| [Pesquisa 2020-06-30](/rest/api/searchservice/index)| Estável | Mais recente e estável lançamento das APIs Search REST, com avanços na pontuação de relevância e disponibilidade geral para a loja de conhecimento.|
| [Pesquisa 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Pré-visualizar | Versão de pré-visualização associada à versão estável. Inclui várias [funcionalidades de pré-visualização](search-api-preview.md). |
| Pesquisa 2019-05-06 | Estável  | Adiciona [tipos complexos.](search-howto-complex-data-types.md) |
| Pesquisa 2019-05-06-Preview | Pré-visualizar | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2017-11-11 | Estável | Adiciona skillsets e [enriquecimento de IA.](cognitive-search-concept-intro.md) |
| Pesquisa 2017-11-11-Preview | Pré-visualizar | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2016-09-01 |Estável | Adiciona [indexantes](search-indexer-overview.md). |
| Pesquisa 2016-09-01-Pré-visualização | Pré-visualizar | Versão de pré-visualização associada à versão estável.|
| Pesquisa 2015-02-28 | Sem apoio após 10-10-2020   | Primeira libertação geralmente disponível.  |
| Pesquisa 2015-02-28-Preview | Sem apoio após 10-10-2020  | Versão de pré-visualização associada à versão estável. |
| Pesquisa 2014-10-20-Preview | Sem apoio após 10-10-2020 | Segunda pré-estreia pública. |
| Pesquisa 2014-07-31-Preview | Sem apoio após 10-10-2020  | Primeira pré-visualização pública. |

### <a name="management-rest-apis"></a>APIs REST de Gestão

Crie e configuure um serviço de pesquisa e gere as chaves API.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Estado | Descrição |
|-------------------------|--------|------------------------------|
| [Gestão 2020-08-01](/rest/api/searchmanagement/) | Estável | Mais recente e estável lançamento das APIs de Gestão REST. Adiciona suporte de recursos de ligação privada partilhada geralmente disponível para todos os recursos acedidos de saída, exceto os indicados na versão de pré-visualização |
| [Gestão 2020-08-01-Pré-visualização](/rest/api/searchmanagement/index-preview) | Pré-visualizar  | Atualmente em pré-visualização: suporte de recursos de ligação privada compartilhado para Funções Azure e Base de Dados Azure para o MySQL. |
| Gestão 2020-03-13  | Estável | Adiciona [ponto final privado](service-create-private-endpoint.md) através de link privado e [regras ip de rede](service-configure-firewall.md) para novos serviços. Para mais informações, consulte esta [especificação de swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Gestão 2019-10-01-Preview | Pré-visualizar  | Não foram introduzidos recursos de pré-visualização nesta lista. Esta pré-visualização é funcionalmente equivalente a 2020-03-13. Para mais informações, consulte esta [especificação de swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Gestão 2015-08-19  | Estável | A primeira versão geralmente disponível das APIs management REST. Fornece prestação de serviços, ampliação e gestão de chave api. Para mais informações, consulte esta [especificação de swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Gestão 2015-08-19-Pré-visualização  | Pré-visualizar | A primeira versão de pré-visualização das APIs management REST. Para mais informações, consulte esta [especificação de swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>SDK do Azure para .NET

A tabela seguinte fornece links para versões SDK mais recentes. 

| Versão do SDK | Estado | Descrição |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Estável | Nova biblioteca de clientes da Azure .NET SDK, lançada em julho de 2020. Destina-se à api-versão search REST=2020-06-30 REST API, mas ainda não fornece suporte nativo para geo-filtros. Recomendamos o pacote [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) para operações geográficas. Estão disponíveis exemplos para [System.Text.Js](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) e [Newtonsoft.Jsem](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md). |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Estável | Lançado em maio de 2019. Destina-se à versão api-versão search REST=2019-05-06.|
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Estável | Destina-se à versão api-versão management REST=2020-08-01.  |
| Microsoft.Azure.Management.Search 3.0.0 | Estável | Destina-se à versão api-versão management REST=2015-08-19.  |

## <a name="azure-sdk-for-java"></a>SDK do Azure para Java

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Estável | Nova biblioteca de clientes da Azure Java SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>SDK do Azure para JavaScript

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Estável | Nova biblioteca de clientes do Azure JavaScript & TypesScript SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2016-09-01. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |

## <a name="azure-sdk-for-python"></a>SDK do Azure para Python

| Versão do SDK | Estado | Descrição  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Estável | Nova biblioteca de clientes da Azure Python SDK, lançada em julho de 2020. Destina-se à versão api-versão search REST=2019-05-06. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Estável | Destina-se à versão api-versão management REST=2015-08-19. |
