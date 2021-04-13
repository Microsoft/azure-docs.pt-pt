---
title: Autenticação de chave API
titleSuffix: Azure Cognitive Search
description: Uma chave API controla o acesso à entrada do ponto final de serviço. As chaves de administração concedem acesso por escrito. As chaves de consulta podem ser criadas para acesso apenas à leitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 6954ce289cb3cf219f8c4024a112411fd60d70e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310670"
---
# <a name="create-and-manage-api-keys-for-authentication-to-azure-cognitive-search"></a>Criar e gerir chaves API para autenticação para Azure Cognitive Search

Ao ligar-se a um serviço de pesquisa, todos os pedidos precisam incluir uma chave API apenas de leitura que foi gerada especificamente para o seu serviço. A chave API é o único mecanismo para autenticar pedidos de entrada no ponto final do seu serviço de pesquisa e é necessária em todos os pedidos. 

+ Nas [soluções REST](search-get-started-rest.md), o `api-key` é tipicamente especificado num cabeçalho de pedido

+ Em [soluções .NET](search-howto-dotnet-sdk.md), uma chave é frequentemente especificada como uma configuração e depois passada como [um AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Pode visualizar e gerir as teclas API no [portal Azure,](https://portal.azure.com)ou através do [PowerShell,](/powershell/module/az.search) [Azure CLI,](/cli/azure/search)ou [REST API](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Página do portal, configurações de recuperação, secção de chaves" border="false":::

## <a name="what-is-an-api-key"></a>O que é uma chave API?

Uma chave API é uma cadeia única composta por números e letras gerados aleatoriamente que é transmitida em cada pedido ao serviço de pesquisa. O serviço aceitará o pedido, se o pedido em si e a chave forem válidos. 

São utilizados dois tipos de chaves para aceder ao seu serviço de pesquisa: administração (ler-escrever) e consulta (apenas para leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|Administrador|Concede plenos direitos a todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar índices, indexadores e fontes de dados.<br /><br /> Duas teclas de administração, referidas como chaves *primárias* e *secundárias* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente a pedido. Ter duas teclas permite-lhe passar por cima de uma tecla enquanto utiliza a segunda chave para o acesso continuado ao serviço.<br /><br /> As teclas de administração só são especificadas nos cabeçalhos de pedido HTTP. Não é possível colocar uma chave API de administração num URL.|Máximo de 2 por serviço|  
|Consulta|Concede acesso apenas de leitura a índices e documentos, e são normalmente distribuídos para aplicações de clientes que emitem pedidos de pesquisa.<br /><br /> As chaves de consulta são criadas a pedido.<br /><br /> As teclas de consulta podem ser especificadas num cabeçalho de pedido HTTP para pesquisa, sugestão ou operação de pesquisa. Em alternativa, pode passar uma chave de consulta como parâmetro num URL. Dependendo da forma como a sua aplicação de cliente formula o pedido, pode ser mais fácil passar a chave como parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 por serviço|  

 Visualmente, não há distinção entre uma chave de administração ou chave de consulta. Ambas as teclas são cordas compostas por 32 caracteres alfanuméricos gerados aleatoriamente. Se perder a noção do tipo de chave especificada na sua aplicação, pode [verificar os valores-chave no portal](https://portal.azure.com).  

> [!NOTE]  
> É considerada uma má prática de segurança para passar dados sensíveis, como um `api-key` no pedido URI. Por esta razão, a Azure Cognitive Search apenas aceita uma chave de consulta como uma `api-key` na cadeia de consulta, e deve evitar fazê-lo a menos que o conteúdo do seu índice esteja disponível ao público. Regra geral, recomendamos passar o seu `api-key` cabeçalho como pedido.  

## <a name="find-existing-keys"></a>Encontre as chaves existentes

Pode obter teclas de acesso no portal ou através do [PowerShell,](/powershell/module/az.search) [Azure CLI](/cli/azure/search)ou [REST API](/rest/api/searchmanagement/).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Enuse os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) da sua subscrição.
1. Selecione o serviço e na página 'Vista Geral', clique em  > **Definições Teclas** para visualizar as teclas de administração e consulta.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Página do portal, ver definições, seção de teclas" border="false":::

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são utilizadas para o acesso apenas à leitura de documentos dentro de um índice para operações direcionadas a uma recolha de documentos. Pesquisa, filtro e consultas de sugestão são todas as operações que levam uma chave de consulta. Qualquer operação de leitura que retorne dados do sistema ou definições de objetos, como uma definição de índice ou estado de indexante, requer uma chave de administração.

Restringir o acesso e as operações em aplicações de clientes é essencial para salvaguardar os ativos de pesquisa no seu serviço. Utilize sempre uma chave de consulta em vez de uma chave de administração para qualquer consulta originária de uma aplicação do cliente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Enuse os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  da sua subscrição.
3. Selecione o serviço e na página 'Vista Geral', clique em  > **Definições Teclas**.
4. Clique **em Gerir as teclas de consulta**.
5. Utilize a tecla de consulta já gerada para o seu serviço ou crie até 50 novas teclas de consulta. A chave de consulta padrão não está nomeada, mas as chaves de consulta adicionais podem ser nomeadas para a gestão.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Criar ou utilizar uma chave de consulta" border="false":::

> [!Note]
> Um exemplo de código que mostra a utilização da chave de consulta pode ser encontrado no [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administração

São criadas duas teclas de administração para cada serviço para que possa rodar uma chave primária, utilizando a chave secundária para a continuidade do negócio.

1. Na página **'Chaves'**  > **'Definições',** copie a tecla secundária.
2. Para todas as aplicações, atualize as definições das chaves API para utilizar a chave secundária.
3. Regenerar a chave primária.
4. Atualize todas as aplicações para utilizar a nova chave primária.

Se regenerar inadvertidamente ambas as teclas ao mesmo tempo, todos os pedidos do cliente que utilizem essas teclas falharão com HTTP 403 Forbidden. No entanto, o conteúdo não é apagado e não está bloqueado permanentemente. 

Ainda pode aceder ao serviço através do portal ou programaticamente. As funções de gestão são operativas através de um ID de subscrição e não uma chave API de serviço, e assim ainda disponíveis mesmo que as suas teclas API não estejam. 

Depois de criar novas teclas através de portal ou camada de gestão, o acesso é restaurado ao seu conteúdo (índices, indexadores, fontes de dados, mapas de sinónimo) uma vez que tenha as novas teclas e forneça essas teclas nos pedidos.

## <a name="secure-api-keys"></a>Chaves API seguras

Através [de permissões baseadas em funções,](search-security-rbac.md)pode eliminar ou ler as teclas, mas não pode substituir uma chave por uma palavra-passe definida pelo utilizador ou utilizar o Ative Directory como a metodologia de autenticação primária para aceder às operações de pesquisa. 

A segurança da chave é assegurada restringindo o acesso através das interfaces portal ou Gestor de Recursos (Interface PowerShell ou linha de comando). Como notado, os administradores de subscrição podem ver e regenerar todas as chaves API. Como precaução, reveja as atribuições de funções para entender quem tem acesso às teclas de administração.

+ No painel de instrumentos de serviço, clique no **controlo de acesso (IAM)** e, em seguida, no separador **atribuições de funções** para visualizar as atribuições de funções para o seu serviço.

Os membros das seguintes funções podem visualizar e regenerar chaves: Proprietário, Colaborador, [Contribuintes do Serviço de Busca](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Ver também

+ [Segurança em Pesquisa Cognitiva Azure](search-security-overview.md)
+ [Controlo de acesso baseado em funções Azure em Pesquisa Cognitiva Azure](search-security-rbac.md)
+ [Gerir com o PowerShell](search-manage-powershell.md) 