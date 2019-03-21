---
title: Criar, gerenciar e proteger a administração e a consulta as chaves de api - Azure Search
description: as chaves de API controlam o acesso ao ponto final do serviço. As chaves administrativas concedem acesso de escrita. Chaves de consulta podem ser criadas para acesso só de leitura.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: heidist
ms.openlocfilehash: a59451c659effb55a2e16236b359b7601eb31cd4
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286606"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Criar e gerir as chaves de api para um serviço Azure Search

Todos os pedidos para um serviço de pesquisa tem uma só de leitura a chave de api que foi gerada especificamente para o seu serviço. A chave de api é o único mecanismo para autenticar o acesso ao seu ponto final de serviço de pesquisa e têm de ser incluída em cada solicitação. Na [soluções REST](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), a chave de api, normalmente, é especificada no cabeçalho do pedido. Na [soluções .NET](search-howto-dotnet-sdk.md#core-scenarios), uma chave é, muitas vezes, especificada como uma definição de configuração e, em seguida, passada como [credenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chave de administrador) ou [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chave de consulta) no [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

As chaves são criadas com o serviço de pesquisa durante o aprovisionamento de serviço. Pode ver e obter valores de chave no [portal do Azure](https://portal.azure.com).

![Secção de chaves de página do portal, as definições,](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>O que é uma chave de api

Uma chave de api é uma cadeia de caracteres composta por letras e números gerados aleatoriamente. Por meio [permissões baseadas em função](search-security-rbac.md), pode eliminar ou as chaves de leitura, mas não é possível substituir uma chave com uma palavra-passe definida pelo utilizador ou utilizar o Active Directory como a metodologia de autenticação primária para aceder às operações de pesquisa. 

Dois tipos de chaves são utilizados para aceder ao seu serviço de pesquisa: administrador (leitura / escrita) e a consulta (só de leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|Administração|Concede todos os direitos para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados.<br /><br /> Duas chaves de administração, conhecido como *primário* e *secundário* chaves no portal, são gerados quando o serviço é criado e pode ser regenerado individualmente a pedido. Ter duas chaves permite-lhe fazer o rollover de uma chave ao utilizar a segunda chave de acesso contínuo ao serviço.<br /><br /> Chaves de administração só são especificadas nos cabeçalhos de pedido HTTP. Não é possível colocar uma chave de api de administração num URL.|Máximo de 2 por serviço|  
|Consulta|Concede acesso só de leitura aos índices e documentos e normalmente são distribuídas por aplicações cliente que emitem pedidos de pesquisa.<br /><br /> Chaves de consulta são criadas a pedido. Pode criá-las manualmente no portal ou através de programação através de [API REST da gestão](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Chaves de consulta podem ser especificadas no cabeçalho de pedido HTTP para pesquisa, sugestões ou operação de pesquisa. Em alternativa, pode transmitir uma chave de consulta como um parâmetro numa URL. Dependendo de como a aplicação cliente formulates o pedido, poderá ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre uma chave de administrador ou a chave de consulta. Ambas as chaves são cadeias de caracteres compostas 32 aleatoriamente geradas carateres alfanuméricos. Se perder o controle de que tipo de chave é especificado na sua aplicação, pode [Verifique os valores de chave no portal](https://portal.azure.com) ou utilizar o [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) para devolver o valor e o tipo de chave.  

> [!NOTE]  
>  Ele é considerado uma prática de segurança fraca para transmitir dados confidenciais, tais como um `api-key` no URI do pedido. Por esse motivo, o Azure Search aceita apenas uma chave de consulta como uma `api-key` na consulta de cadeia de caracteres e deve evitar fazer isso, a menos que o conteúdo do seu índice deve ser publicamente disponível. Como regra geral, recomendamos que passar seu `api-key` como um cabeçalho de pedido.  

## <a name="find-existing-keys"></a>Localizar as chaves existentes

Pode obter as chaves de acesso no portal ou através da [API REST da gestão](https://docs.microsoft.com/rest/api/searchmanagement/). Para obter mais informações, consulte [gerir chaves de api administrador e a consulta](search-security-api-keys.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Lista os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para a sua subscrição.
3. Selecione o serviço e na página Descrição geral, clique em **configurações** >**chaves** para exibir as chaves de administração e a consulta.

   ![Secção de chaves de página do portal, as definições,](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Criar chaves de consulta

Chaves de consulta são utilizadas para acesso só de leitura para documentos dentro de um índice. Restringir o acesso e de operações nas aplicações de cliente é essencial para proteger os ativos de pesquisa no seu serviço. Utilize sempre uma chave de consulta, em vez de uma chave de administração para qualquer consulta provenientes de uma aplicação de cliente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Lista os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para a sua subscrição.
3. Selecione o serviço e na página Descrição geral, clique em **configurações** >**chaves**.
4. Clique em **gerir chaves de consulta**.
5. Utilize a consulta já gerada para o seu serviço ou criar até 50 novas chaves de consulta. A chave de consulta padrão não tem o nome, mas as chaves de consulta adicionais podem ser nomeadas para capacidade de gestão.

   ![Criar ou utilizar uma chave de consulta](media/search-security-overview/create-query-key.png) 


> [!Note]
> Um exemplo de código que mostra a utilização de chave de consulta pode ser encontrado na [consultar um índice da Azure Search no C# ](search-query-dotnet.md).

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administração

Duas chaves administrativas são criadas para cada serviço, para que pode girar uma chave primária, com a chave secundária para acesso contínuo.

Se voltar a gerar chaves primárias e secundárias ao mesmo tempo, todas as aplicações com qualquer chave para aceder às operações de serviço já não terá acesso ao serviço.

1. Na **configurações** >**chaves** página, copie a chave secundária.
2. Para todos os aplicativos, Atualize as definições de chave de api para utilizar a chave secundária.
3. Regenere a chave primária.
4. Atualize todas as aplicações para utilizar a nova chave primária.

## <a name="secure-api-keys"></a>Proteger chaves de api
Segurança de chave é garantida por meio de restringir o acesso com o portal ou interfaces do Resource Manager (interface de linha de comandos ou PowerShell). Como observado, os administradores da subscrição podem ver e voltar a gerar todas as chaves de api. Como precaução, reveja as atribuições de funções para compreender quem tem acesso a chaves de administração.

+ No dashboard do serviço, clique em **controlo de acesso (IAM)** e, em seguida, o **atribuições de funções** separador para ver as atribuições de funções para o seu serviço.

Os membros das seguintes funções podem ver e voltar a gerar chaves: Proprietário, contribuinte, [contribuidores do serviço de pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Para acesso com base na identidade os resultados de pesquisa, pode criar filtros de segurança para limitar os resultados pela identidade, a remoção de documentos para o qual o requerente não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md) e [seguro com o Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Consulte também

+ [Controlo de acesso baseado em funções no Azure Search](search-security-rbac.md)
+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Artigo de desempenho e Otimização](search-performance-optimization.md)