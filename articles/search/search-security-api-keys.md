---
title: Criar, gerir e proteger administração e perguntas api-keys
titleSuffix: Azure Cognitive Search
description: Uma chave api controla o acesso ao ponto final do serviço. As chaves de administração concedem acesso a escrita. As chaves de consulta podem ser criadas para acesso apenas para leitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72794377"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Crie e gereça as api-keys para um serviço de pesquisa cognitiva Azure

Todos os pedidos para um serviço de pesquisa precisam de uma chave api apenas de leitura que foi gerada especificamente para o seu serviço. A chave api é o único mecanismo para autenticar o acesso ao seu ponto final do serviço de pesquisa e deve ser incluída em todos os pedidos. Nas [soluções REST,](search-get-started-postman.md)a chave api é normalmente especificada num cabeçalho de pedido. Em [soluções .NET,](search-howto-dotnet-sdk.md#core-scenarios)uma chave é frequentemente especificada como uma definição de configuração e depois passada como [Credenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chave de administração) ou [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chave de consulta) no [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

As chaves são criadas com o seu serviço de pesquisa durante o fornecimento de serviços. Pode ver e obter valores-chave no [portal Azure.](https://portal.azure.com)

![Página do portal, Definições, Secção de Chaves](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>O que é uma api-key

Uma chave api é uma cadeia composta por números e letras gerados aleatoriamente. Através de [permissões baseadas em funções,](search-security-rbac.md)pode eliminar ou ler as teclas, mas não pode substituir uma chave por uma palavra-passe definida pelo utilizador ou utilizar o Ative Directory como a metodologia de autenticação primária para aceder a operações de pesquisa. 

São utilizados dois tipos de teclas para aceder ao seu serviço de pesquisa: administrador (read-write) e consulta (apenas para leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|Administrador|Concede todos os direitos a todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar índices, indexadores e fontes de dados.<br /><br /> Duas chaves de administração, referidas como chaves *primárias* e *secundárias* no portal, são geradas quando o serviço é criado e podem ser regenerados individualmente a pedido. Ter duas teclas permite-lhe rolar uma tecla enquanto utiliza a segunda tecla para o acesso continuado ao serviço.<br /><br /> As chaves de administrador são especificadas apenas em cabeçalhos de pedido HTTP. Não é possível colocar uma chave de api-key numa URL.|Máximo de 2 por serviço|  
|Consulta|Concede acesso apenas a índices e documentos, e são normalmente distribuídos para aplicações de clientes que emitem pedidos de pesquisa.<br /><br /> As chaves de consulta são criadas a pedido. Pode criá-los manualmente no portal ou programáticamente através da [API DE GESTÃO REST](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas num cabeçalho de pedido http para pesquisa, sugestão ou operação de pesquisa. Em alternativa, pode passar uma chave de consulta como parâmetro numa URL. Dependendo da forma como a sua aplicação de cliente formula o pedido, pode ser mais fácil passar a chave como parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 por serviço|  

 Visualmente, não há distinção entre uma chave de administração ou chave de consulta. Ambas as teclas são cordas compostas por 32 caracteres alfa-numéricos gerados aleatoriamente. Se perder a noção do tipo de chave especificado na sua aplicação, pode [verificar os valores-chave no portal](https://portal.azure.com) ou utilizar a [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) para devolver o valor e o tipo de chave.  

> [!NOTE]  
>  É considerada uma má prática de segurança `api-key` para passar dados sensíveis, como um no pedido URI. Por esta razão, a Azure Cognitive Search `api-key` apenas aceita uma chave de consulta como uma linha de consulta, e deve evitar fazê-lo a menos que o conteúdo do seu índice esteja disponível publicamente. Como regra geral, recomendamos `api-key` que passe o seu como um cabeçalho de pedido.  

## <a name="find-existing-keys"></a>Encontre as chaves existentes

Pode obter chaves de acesso no portal ou através da [API DE GESTÃO REST](https://docs.microsoft.com/rest/api/searchmanagement/). Para mais informações, consulte [Gerir administração e consultar api-keys](search-security-api-keys.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Enumera rine os [serviços](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa para a sua subscrição.
3. Selecione o serviço e na página 'Overview', clique em **Definições** >**para** visualizar as teclas de administração e consulta.

   ![Página do portal, Definições, Secção de Chaves](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são usadas para o acesso apenas a documentos dentro de um índice para operações direcionadas a uma recolha de documentos. Pesquisa, filtro e sugestões são todas as operações que levam uma chave de consulta. Qualquer operação apenas de leitura que retorne os dados do sistema ou definições de objetos, como uma definição de índice ou um estado indexante, requer uma chave de administração.

Restringir o acesso e as operações em aplicações de clientes é essencial para salvaguardar os ativos de pesquisa no seu serviço. Utilize sempre uma chave de consulta em vez de uma chave de administração para qualquer consulta originária de uma aplicação de cliente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Enumera rine os [serviços](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa para a sua subscrição.
3. Selecione o serviço e na página 'Overview', clique em >**Definições**. **Settings**
4. Clique em **Gerir chaves de consulta**.
5. Utilize a chave de consulta já gerada para o seu serviço, ou crie até 50 novas teclas de consulta. A chave de consulta padrão não está nomeada, mas as chaves de consulta adicionais podem ser nomeadas para a capacidade de gestão.

   ![Criar ou usar uma chave de consulta](media/search-security-overview/create-query-key.png) 

> [!Note]
> Um exemplo de código que mostra o uso da chave de consulta pode ser encontrado em Consulta um índice de [Pesquisa Cognitiva Azure em C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Chaves de administração regeneradas

São criadas duas chaves de administração para cada serviço para que possa rodar uma chave primária, utilizando a chave secundária para a continuidade do negócio.

1. Na página > **Definições,** copie a tecla secundária.**Keys**
2. Para todas as aplicações, atualize as definições de tecla api para utilizar a tecla secundária.
3. Regenerar a chave primária.
4. Atualize todas as aplicações para utilizar a nova chave primária.

Se regenerar inadvertidamente ambas as teclas ao mesmo tempo, todos os pedidos do cliente que utilizam essas chaves falharão com http 403 Proibido. No entanto, o conteúdo não é apagado e não está bloqueado permanentemente. 

Ainda pode aceder ao serviço através do portal ou da camada de gestão[(REST API,](https://docs.microsoft.com/rest/api/searchmanagement/) [PowerShell,](https://docs.microsoft.com/azure/search/search-manage-powershell)ou Azure Resource Manager). As funções de gestão são operativas através de um ID de subscrição, não uma chave de serviço, e assim ainda estão disponíveis mesmo que as suas chaves api não estejam. 

Depois de criar novas teclas através do portal ou camada de gestão, o acesso é restaurado ao seu conteúdo (índices, indexadores, fontes de dados, mapas de sinónimo) assim que tiver as novas teclas e fornecer essas chaves nos pedidos.

## <a name="secure-api-keys"></a>Chaves de api segura
A segurança chave é assegurada restringindo o acesso através das interfaces portal ou Resource Manager (PowerShell ou interface de linha de comando). Como notado, os administradores de subscrição podem ver e regenerar todas as teclas api. Por precaução, reveja as atribuições de funções para entender quem tem acesso às chaves de administração.

+ No painel de instrumentos de serviço, clique no controlo de **acesso (IAM)** e, em seguida, no separador de **atribuições de role** para visualizar as atribuições de funções para o seu serviço.

Os membros das seguintes funções podem ver e regenerar chaves: Proprietário, Colaborador, [Colaborador do Serviço](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) de Busca

> [!Note]
> Para acesso baseado na identidade sobre resultados de pesquisa, pode criar filtros de segurança para aparar resultados por identidade, removendo documentos para os quais o solicitador não deve ter acesso. Para mais informações, consulte [filtros](search-security-trimming-for-azure-search.md) de segurança e [Proteja-o com Diretório Ativo](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Consulte também

+ [Controlo de acesso baseado em funções na Pesquisa Cognitiva azure](search-security-rbac.md)
+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Artigo de desempenho e otimização](search-performance-optimization.md)