---
title: Upgrade para o Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Upgrade para o Azure Search .NET Management SDK a partir de versões anteriores. Conheça as novas funcionalidades e as alterações de código necessárias para a migração.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936712"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Versões de upgrade do Azure Search .NET Management SDK

Este artigo explica como migrar para versões sucessivas do Azure Search .NET Management SDK, usado para a prestação ou deprovisionamento de serviços de pesquisa, ajustar a capacidade e gerir as chaves API.

Os SDKs de gestão visam uma versão específica da API management REST. Para obter mais informações sobre conceitos e operações, consulte [Gestão de Pesquisa (REST)](/rest/api/searchmanagement/).

## <a name="versions"></a>Versões

| Versão do SDK | Versão API de REST correspondente | Adição de recursos ou mudança de comportamento |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-versão=2020-30-20 | Adiciona segurança de ponto final (firewalls IP e integração com [Azure Private Link)](../private-link/private-endpoint-overview.md) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-versão=2019-10-01 | Melhorias na usabilidade. Alteração de rutura nas [teclas de consulta de lista](/rest/api/searchmanagement/querykeys/listbysearchservice) (GET é descontinuado). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-versão=2015-08-19  | Primeira versão |

## <a name="how-to-upgrade"></a>Como atualizar

1. Atualize a sua referência NuGet para `Microsoft.Azure.Management.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." em Estúdio Visual.

1. Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código é estruturado, pode reconstruir com sucesso, caso em que está feito.

1. Se a sua construção falhar, pode ser porque implementou algumas das interfaces SDK (por exemplo, para efeitos de testes unitários), que mudaram. Para resolver isto, terá de implementar métodos mais recentes, tais `BeginCreateOrUpdateWithHttpMessagesAsync` como.

1. Depois de corrigir quaisquer erros de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades. 

## <a name="upgrade-to-30"></a>Upgrade para 3.0

A versão 3.0 adiciona proteção privada ao ponto final, restringindo o acesso às gamas IP e integrando opcionalmente com o Azure Private Link para serviços de pesquisa que não devem ser visíveis na internet pública.

### <a name="new-apis"></a>Novas APIs

| API | Categoria| Detalhes |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | Firewall de IP | Restringir o acesso a um ponto final de serviço a uma lista de endereços IP permitidos. Consulte [a firewall IP configurar](service-configure-firewall.md) para obter conceitos e instruções do portal. |
| [Recurso de ligação privada compartilhado](/rest/api/searchmanagement/sharedprivatelinkresources) | Ligação Privada | Crie um recurso de ligação privada partilhado para ser utilizado por um serviço de pesquisa.  |
| [Conexões de ponto final privadas](/rest/api/searchmanagement/privateendpointconnections) | Ligação Privada | Estabeleça e gere as ligações a um serviço de pesquisa através de um ponto final privado. Consulte [Criar um ponto final privado](service-create-private-endpoint.md) para obter conceitos e instruções do portal.|
| [Recursos de Ligação Privada](/rest/api/searchmanagement/privatelinkresources/) | Ligação Privada | Para um serviço de pesquisa que tenha uma ligação de ponto final privado, obtenha uma lista de todos os serviços utilizados na mesma rede virtual. Se a sua solução de pesquisa incluir indexadores que retiram de fontes de dados Azure (Azure Storage, Cosmos DB, Azure SQL), ou utiliza serviços cognitivos ou Cofre-Chave, então todos esses recursos devem ter pontos finais na rede virtual, e esta API deve devolver uma lista. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Ligação Privada | Esta é uma propriedade em pedidos de Serviço de Criar ou Atualizar. Quando desativado, o link privado é a única modalidade de acesso. |

### <a name="breaking-changes"></a>Alterações interruptivas

Já não é possível utilizar o GET num pedido [de Chaves de Consulta de Lista.](/rest/api/searchmanagement/querykeys/listbysearchservice) Em lançamentos anteriores pode utilizar GET ou POST, neste lançamento e em todas as versões a avançar, apenas o POST é suportado. 

## <a name="upgrade-to-20"></a>Upgrade para 2.0

A versão 2 do Azure Search .NET Management SDK é uma pequena atualização, pelo que alterar o seu código deve exigir apenas o mínimo de esforço. As alterações ao SDK são alterações estritamente do lado do cliente para melhorar a usabilidade do próprio SDK. Estas alterações incluem o seguinte:

* `Services.CreateOrUpdate` e as suas versões assíncronas agora automaticamente pesquisam o provisionamento `SearchService` e não regressam até que o fornecimento de serviço esteja completo. Isto evita que tenha de escrever um código de sondagens.

* Se ainda quiser fazer sondagens manualmente, pode utilizar o novo `Services.BeginCreateOrUpdate` método ou uma das suas versões assíncronos.

* Novos `Services.Update` métodos e as suas versões assíncronos foram adicionados ao SDK. Estes métodos utilizam HTTP PATCH para suportar a atualização incremental de um serviço. Por exemplo, agora pode escalar um serviço passando um `SearchService` exemplo para estes métodos que contém apenas as `partitionCount` propriedades e propriedades desejadas. `replicaCount` A velha forma de `Services.Get` chamar, modificar o `SearchService` devolvido, e passá-lo `Services.CreateOrUpdate` para ainda é suportado, mas já não é necessário. 

## <a name="next-steps"></a>Passos seguintes

Se encontrar problemas, o melhor fórum para publicar perguntas é [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de rotular o seu título de emissão com "[search]".