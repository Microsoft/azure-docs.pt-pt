---
title: Criar, gerenciar e proteger o administrador e as chaves de API de consulta-Azure Search
description: as chaves de API controlam o acesso ao ponto de extremidade de serviço. As chaves de administração concedem acesso de gravação. As chaves de consulta podem ser criadas para acesso somente leitura.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: d3880ed367ebe33e04f37b139927b75e3d01b178
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855786"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Criar e gerenciar chaves de API para um serviço de Azure Search

Todas as solicitações para um serviço de pesquisa precisam de uma chave de API somente leitura que foi gerada especificamente para seu serviço. A chave de API é o único mecanismo para autenticar o acesso ao ponto de extremidade do serviço de pesquisa e deve ser incluído em cada solicitação. Em [soluções REST](search-get-started-postman.md), a chave de API é normalmente especificada em um cabeçalho de solicitação. Em [soluções .net](search-howto-dotnet-sdk.md#core-scenarios), uma chave é geralmente especificada como uma definição de configuração e, em seguida, passada como [credenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chave de administração) ou [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chave de consulta) em [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

As chaves são criadas com o serviço de pesquisa durante o provisionamento de serviço. Você pode exibir e obter valores de chave no [portal do Azure](https://portal.azure.com).

![Página do portal, configurações, seção chaves](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>O que é uma chave de API

Uma chave de API é uma cadeia de caracteres composta de números e letras gerados aleatoriamente. Por meio de [permissões baseadas em função](search-security-rbac.md), você pode excluir ou ler as chaves, mas não pode substituir uma chave por uma senha definida pelo usuário ou usar Active Directory como a metodologia de autenticação primária para acessar as operações de pesquisa. 

Dois tipos de chaves são usados para acessar o serviço de pesquisa: administrador (leitura-gravação) e consulta (somente leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|administrador|Concede direitos totais a todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados.<br /><br /> Duas chaves de administração, chamadas de chaves primárias e *secundárias* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você passe por uma chave enquanto usa a segunda chave para obter acesso contínuo ao serviço.<br /><br /> As chaves de administração são especificadas apenas nos cabeçalhos de solicitação HTTP. Não é possível inserir uma chave de API de administração em uma URL.|Máximo de 2 por serviço|  
|Consulta|Concede acesso somente leitura a índices e documentos, e normalmente são distribuídos para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda. Você pode criá-los manualmente no portal ou programaticamente por meio da [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestão ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como o aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre uma chave de administração ou uma chave de consulta. Ambas as chaves são cadeias de caracteres compostas de 32 de caractere alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave é especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com) ou usar a [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) para retornar o valor e o tipo de chave.  

> [!NOTE]  
>  É considerada uma prática de segurança inadequada para passar dados confidenciais, como `api-key` um no URI de solicitação. Por esse motivo, Azure Search aceita apenas uma chave de consulta como `api-key` uma na cadeia de caracteres de consulta, e você deve evitar fazer isso, a menos que o conteúdo do índice esteja disponível publicamente. Como regra geral, recomendamos passar seu `api-key` como um cabeçalho de solicitação.  

## <a name="find-existing-keys"></a>Localizar chaves existentes

Você pode obter chaves de acesso no portal ou por meio da [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/). Para obter mais informações, consulte [gerenciar o administrador e as chaves de API de consulta](search-security-api-keys.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página Visão geral, clique em **configurações** >**chaves** para exibir as chaves de administrador e consulta.

   ![Página do portal, configurações, seção chaves](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são usadas para acesso somente leitura a documentos dentro de um índice para operações direcionadas a uma coleção de documentos. Consultas de pesquisa, filtro e sugestão são operações que usam uma chave de consulta. Qualquer operação somente leitura que retorna dados do sistema ou definições de objeto, como uma definição de índice ou um status de indexador, requer uma chave de administração.

Restringir o acesso e as operações em aplicativos cliente é essencial para proteger os ativos de pesquisa em seu serviço. Sempre use uma chave de consulta em vez de uma chave de administração para qualquer consulta proveniente de um aplicativo cliente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página Visão geral, clique em **configurações** >**chaves**.
4. Clique em **gerenciar chaves de consulta**.
5. Use a chave de consulta já gerada para seu serviço ou crie até 50 novas chaves de consulta. A chave de consulta padrão não é nomeada, mas chaves de consulta adicionais podem ser nomeadas para capacidade de gerenciamento.

   ![Criar ou usar uma chave de consulta](media/search-security-overview/create-query-key.png) 

> [!Note]
> Um exemplo de código que mostra o uso da chave de consulta pode ser encontrado em [consultar um índice de Azure Search em C# ](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Duas chaves de administração são criadas para cada serviço para que você possa girar uma chave primária usando a chave secundária para continuidade dos negócios.

1. Na página**chaves** de **configurações** >, copie a chave secundária.
2. Para todos os aplicativos, atualize as configurações de chave de API para usar a chave secundária.
3. Regenerar a chave primária.
4. Atualize todos os aplicativos para usar a nova chave primária.

Se você regenerar inadvertidamente as duas chaves ao mesmo tempo, todas as solicitações de cliente que usam essas chaves falharão com HTTP 403 Proibido. No entanto, o conteúdo não é excluído e você não está bloqueado permanentemente. 

Você ainda pode acessar o serviço por meio do portal ou da camada de gerenciamento ([API REST](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)ou Azure Resource Manager). As funções de gerenciamento estão funcionando por meio de uma ID de assinatura que não é uma chave de API de serviço e, assim, ainda ficam disponíveis mesmo que suas chaves de API não sejam. 

Depois de criar novas chaves por meio do portal ou da camada de gerenciamento, o acesso é restaurado para seu conteúdo (índices, indexadores, fontes de dados, mapas de sinônimos) quando você tem as novas chaves e fornece essas chaves em solicitações.

## <a name="secure-api-keys"></a>Proteger chaves de API
A segurança da chave é garantida restringindo o acesso por meio do portal ou das interfaces do Resource Manager (PowerShell ou interface de linha de comando). Como observado, os administradores de assinatura podem exibir e regenerar todas as chaves de API. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves de administração.

+ No painel de serviço, clique em **controle de acesso (iam)** e na guia atribuições de **função** para exibir as atribuições de função para seu serviço.

Os membros das seguintes funções podem exibir e regenerar chaves: Proprietário, colaborador, [colaboradores de serviço de pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Para obter acesso baseado em identidade nos resultados da pesquisa, você pode criar filtros de segurança para cortar os resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md) e [proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Consulte também

+ [Controle de acesso baseado em função no Azure Search](search-security-rbac.md)
+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Artigo de desempenho e otimização](search-performance-optimization.md)