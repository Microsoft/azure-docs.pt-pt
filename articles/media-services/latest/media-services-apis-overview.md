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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492146"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvimento com os serviços de multimédia de v3 APIs

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomes](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>princípios de design de API v3 e RBAC

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolverem segredos ou as credenciais no **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa para chamar um método de ação separada para obter segredos ou as credenciais. O **leitor** função não é possível chamar operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança mais granulares do RBAC numa função personalizada, se assim o desejar.

Para obter mais informações, consulte:

- [Definições de funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Utilizar o RBAC para gerir o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Controlo de acesso baseado em funções para contas de serviços de multimédia](rbac-overview.md)
- [Obter política de chave de conteúdo - .NET](get-content-key-policy-dotnet-howto.md).

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
