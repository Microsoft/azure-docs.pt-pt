---
title: Programar com v3 APIs - Azure | Documentos da Microsoft
description: Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9d1fa5786dcde70d42363dbb9af7221ca5383e64
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546403"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvimento com os serviços de multimédia de v3 APIs

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessar a API dos serviços de multimédia do Azure

Para acessar os recursos de serviços de multimédia do Azure, deve utilizar a autenticação do principal de serviço do Azure Active Directory (AD). API de serviços de multimédia do Azure requer que a aplicação que faz com que a API REST ou o utilizador solicita a ter acesso ao recurso de conta de Media Services do Azure (normalmente, um a **contribuinte** ou **proprietário** função). Para obter mais informações, consulte [controlo de acesso baseado em funções para contas de serviços de multimédia](rbac-overview.md).

Em vez de criar um principal de serviço, considere a utilização de identidades geridas para recursos do Azure para aceder à API de serviços de multimédia através do Gestor de recursos do Azure. Para saber mais sobre identidades geridas para recursos do Azure, veja [o que há de identidades geridas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Principal de serviço do Azure AD 

Se estiver a criar uma aplicação do Azure AD e o serviço principal, a aplicação tem de ser no seu próprio inquilino. Depois de criar a aplicação, atribuir a aplicação **contribuinte** ou **proprietário** acesso à função para a conta de Media Services. 

Se não tiver a certeza se tem permissões para criar uma aplicação do Azure AD, consulte [permissões obrigatórias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo de pedidos por ordem cronológica:

![Aplicações de camada intermediária](../previous/media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Uma aplicação de camada intermediária solicita um token de acesso do Azure AD que tenha os seguintes parâmetros:  

   * Endpoint de inquilino do Azure AD.
   * URI do recurso de serviços de multimédia.
   * URI do recurso para os serviços de multimédia REST.
   * Valores de aplicações do Azure AD: o ID de cliente e o segredo de cliente.
   
   Para obter todos os valores necessários, consulte [acesso à API de serviços de multimédia do Azure com a CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do Azure AD é enviado para a camada intermediária.
4. A camada média envia o pedido à API de REST de multimédia do Azure com o token do Azure AD.
5. A camada média volta obtém os dados dos serviços de multimédia.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomes](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operações de longa execução

As operações marcadas com `x-ms-long-running-operation` nos serviços de multimédia do Azure [swagger ficheiros](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são longos em execução de operações. 

Para obter detalhes sobre como controlar as operações assíncronas do Azure, consulte [operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Serviços de multimédia têm as seguintes operações de longa execução:

* Criar LiveEvent
* Atualizar LiveEvent
* Delete LiveEvent
* Iniciar LiveEvent
* Parar LiveEvent
* Reset LiveEvent
* Criar LiveOutput
* Delete LiveOutput
* Criar StreamingEndpoint
* Atualizar StreamingEndpoint
* Eliminar StreamingEndpoint
* Iniciar StreamingEndpoint
* Parar StreamingEndpoint
* Dimensionamento StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, ordenação, paginação de entidades de serviços de multimédia

Consulte [filtragem, ordenação, a paginação de entidades de serviços de multimédia do Azure](entities-overview.md)

## <a name="next-steps"></a>Passos Seguintes

[Comece a desenvolver com os serviços de multimédia v3 API usando ferramentas/SDKs](developers-guide.md)
