---
title: Autorizar o acesso aos Hubs de Eventos do Azure
description: Este artigo fornece informações sobre diferentes opções para autorizar o acesso aos recursos dos hubs de eventos do Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: b5a037eaf310aa28c76d831dc9fe56eefaddbe56
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123477"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizar o acesso aos Hubs de Eventos do Azure
Sempre que você publica ou consome eventos/dados de um hub de eventos, seu cliente está tentando acessar os recursos dos hubs de eventos. Cada solicitação para um recurso seguro deve ser autorizada para que o serviço possa garantir que o cliente tenha as permissões necessárias para publicar/consumir os dados. 

Os hubs de eventos do Azure oferecem as seguintes opções para autorizar o acesso a recursos seguros:

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Active Directory (Azure AD) para os recursos dos hubs de eventos fornece o RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente aos recursos. Você pode usar o RBAC (controle de acesso baseado em função) para conceder permissões à entidade de segurança, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação para acessar um recurso de hubs de eventos.

Para obter mais informações sobre como autenticar com o Azure AD, consulte os seguintes artigos:

- [Autenticar solicitações para hubs de eventos do Azure usando Azure Active Directory](authenticate-application.md)
- [Autorize o acesso aos recursos dos hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado 
As SAS (assinaturas de acesso compartilhado) para os recursos dos hubs de eventos fornecem acesso delegado limitado aos recursos dos hubs de eventos. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões que ela concede fornece flexibilidade no gerenciamento de recursos. Para obter mais informações, consulte [autenticar usando SAS (assinaturas de acesso compartilhado)](authenticate-shared-access-signature.md). 

A autorização de usuários ou aplicativos que usam um token OAuth 2,0 retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não há necessidade de armazenar os tokens de acesso com seu código e arriscar as vulnerabilidades de segurança potenciais. Embora você possa continuar a usar as SAS (assinaturas de acesso compartilhado) para conceder acesso refinado aos recursos dos hubs de eventos, o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar com a revogação de uma SAS comprometida. 

Por padrão, todos os recursos de hubs de eventos são protegidos e estão disponíveis somente para o proprietário da conta. Embora você possa usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes acesso aos recursos do hub de eventos. Somente os recursos de hubs de eventos criados com Azure Resource Manager modelo de implantação dão suporte à autorização do Azure AD. A Microsoft recomenda usar o Azure AD quando possível para segurança máxima e facilidade de uso.

Para obter mais informações sobre a autorização usando SAS, consulte [autorizando o acesso aos recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Passos seguintes
- Examine os [exemplos de RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publicados em nosso repositório github. 
- Consulte os seguintes artigos:
    - [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
    - [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
    - [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

