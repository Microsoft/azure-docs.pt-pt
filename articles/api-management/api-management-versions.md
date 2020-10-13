---
title: Versões em Gestão API Azure / Microsoft Docs
description: Conheça o conceito de versões na Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 578bb511175d88a1507af9520265a1acd068b27c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098001"
---
# <a name="versions-in-azure-api-management"></a>Versões em Gestão API Azure

As versões permitem apresentar grupos de APIs relacionados aos seus desenvolvedores. Pode utilizar versões para lidar com alterações de rutura na API com segurança. Os clientes podem optar por utilizar a sua nova versão API quando estiverem prontos, enquanto os clientes existentes continuam a utilizar uma versão mais antiga. As versões são diferenciadas através de um identificador de versão (que é qualquer valor de cadeia que escolha), e um esquema de versão permite aos clientes identificar qual a versão de um API que querem usar.

Para a maioria dos fins, cada versão API pode ser considerada a sua própria API independente. Duas versões diferentes da API podem ter diferentes conjuntos de operações e políticas diferentes.

Com versões pode:

- Publique várias versões da sua API ao mesmo tempo.
- Utilize um caminho, uma corda de consulta ou um cabeçalho para diferenciar as versões.
- Use qualquer valor de cadeia que deseje identificar a sua versão, que pode ser um número, uma data ou um nome.
- Mostre as suas versões API agrupadas no portal do desenvolvedor.
- Pegue numa API existente (não versão) e crie uma nova versão do mesmo sem quebrar os clientes existentes.

[Começa com versões seguindo a nossa passagem.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Esquemas de versão

Diferentes desenvolvedores de API têm diferentes requisitos para a versão. A Azure API Management não prescreve uma única abordagem à versão, mas fornece várias opções.

### <a name="path-based-versioning"></a>Versão baseada em caminhos

Quando o esquema de versão de caminhos é utilizado, o identificador de versão precisa de ser incluído no caminho URL para quaisquer pedidos de API.

Por exemplo, `https://apis.contoso.com/products/v1` e `https://apis.contoso.com/products/v2` poderia referir-se à mesma `products` API, mas a versões `v1` `v2` e, respectivamente.

O formato de um URL de pedido de API ao utilizar a versão baseada em cabeçalho é: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Veragem baseada em cabeçalho

Quando o esquema de versão do cabeçalho é utilizado, o identificador de versão tem de ser incluído num cabeçalho de pedido HTTP para quaisquer pedidos de API. Pode especificar o nome do cabeçalho de pedido HTTP.

Por exemplo, pode criar um cabeçalho personalizado chamado `Api-Version` , e os clientes podem especificar `v1` ou no valor deste `v2` cabeçalho.

### <a name="query-string-based-versioning"></a>Veragem baseada em cordas de consulta

Quando o esquema de versão de cadeia de consulta é utilizado, o identificador de versão precisa de ser incluído num parâmetro de cadeia de consulta para quaisquer pedidos de API. Pode especificar o nome do parâmetro da cadeia de consulta.

O formato de um URL de pedido de API ao utilizar a versão baseada em cadeias de consulta é: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Por exemplo, `https://apis.contoso.com/products?api-version=v1` e `https://apis.contoso.com/products/api-version=v2` poderia referir-se à mesma `products` API, mas a versões `v1` `v2` e, respectivamente.

## <a name="original-versions"></a>Versões originais

Se adicionar uma versão a uma API não versão, uma `Original` versão será criada automaticamente e responderá no URL predefinido, sem um identificador de versão especificado. A `Original` versão garante que os chamadores existentes não são quebrados pelo processo de adição de uma versão. Se criar uma nova API com versões ativadas no início, não é criada uma `Original` versão.

## <a name="how-versions-are-represented"></a>Como as versões são representadas

A Azure API Management mantém um recurso chamado conjunto de *versão*, que representa um conjunto de versões para uma única API lógica. Quando utiliza o portal Azure para gerir versões não vê o conjunto de versões, mas se interagir com o seu serviço de Gestão API utilizando modelos PowerShell, Resource Manager ou a API do Gestor de Recursos Azure, pode visualizar e gerir diretamente os conjuntos de versões. Um conjunto de versão contém o nome de exibição da API versão, bem como o [esquema de versão utilizado](#versioning-schemes) para direcionar pedidos para versões especificadas.

Cada versão de uma API é mantida como o seu próprio recurso API, que é então associado a um conjunto de versão. Um conjunto de versão pode conter APIs com operações ou políticas muito diferentes, o que reflete o facto de que você pode fazer alterações significativas entre versões da sua API.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrar uma API não ver versão para uma API versão

Quando utiliza o portal Azure para ativar a versão numa API existente, são feitas as seguintes alterações aos recursos de Gestão da API:

 * É criado um novo conjunto de versão.
 * A versão existente é mantida e [configurada como a `Original` versão API](#original-versions). A API está ligada ao conjunto de versão, mas não requer que seja especificado um identificador de versão.
 * A nova versão é criada como uma nova API, e está ligada ao conjunto de versão. Esta nova API deve ser acedida através do sistema de versão e do identificador.

## <a name="versions-and-revisions"></a>Versões e revisões

Versões e revisões são características distintas. Cada versão pode ter múltiplas revisões, tal como uma API não ver versão. Pode utilizar revisões sem usar versões ou ao contrário. Normalmente, as versões são usadas para separar versões API com alterações de rutura, enquanto as revisões podem ser usadas para alterações menores e não quebrando uma API.

Caso descubra que a sua revisão tem alterações de rutura, ou se pretender transformar formalmente a sua revisão numa versão beta/teste, pode criar uma versão a partir de uma revisão. Utilizando o portal Azure, clique na versão 'Criar versão a partir de Revisão' no menu de contexto de revisão no separador Revisões.

## <a name="developer-portal"></a>Portal do programador

O [portal do desenvolvedor](./api-management-howto-developer-portal.md) lista cada versão de uma API separadamente.

![Portal de desenvolvimento de gestão de API que apresenta uma lista de APIs em versão](media/api-management-versions/portal-list.png)

Os detalhes de uma API também mostram uma lista de todas as versões dessa API. Uma `Original` versão é exibida sem um identificador de versão.

![Portal de desenvolvimento de gestão da API que apresenta detalhes da API e uma lista de versões para essa API](media/api-management-versions/portal-details.png)

> [!TIP]
> As versões API precisam de ser adicionadas a um produto antes de serem visíveis no portal do desenvolvedor.
