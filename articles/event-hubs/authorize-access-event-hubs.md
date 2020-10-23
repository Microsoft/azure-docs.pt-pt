---
title: Autorizar o acesso aos Hubs de Eventos do Azure
description: Este artigo fornece informações sobre diferentes opções para autorizar o acesso aos recursos do Azure Event Hubs.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332387"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizar o acesso aos Hubs de Eventos do Azure
Sempre que publica ou consome eventos/dados de um centro de eventos, o seu cliente está a tentar aceder aos recursos do Event Hubs. Todos os pedidos a um recurso seguro devem ser autorizados para que o serviço possa garantir que o cliente tem as permissões necessárias para publicar/consumir os dados. 

O Azure Event Hubs oferece as seguintes opções para autorizar o acesso a recursos seguros:

- Azure Active Directory
- Assinatura de acesso partilhado

> [!NOTE]
> Este artigo aplica-se tanto aos cenários de Event Hubs [como apache kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Ative Directory (Azure AD) para os recursos do Event Hubs fornece o controlo de acesso baseado em funções (Azure RBAC) para um controlo fino sobre o acesso de um cliente aos recursos. Você pode usar Azure RBAC para conceder permissões ao principal de segurança, que pode ser um utilizador, um grupo ou um diretor de serviço de aplicação. O principal de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O token pode ser usado para autorizar um pedido de acesso a um recurso Desemator de Eventos.

Para obter mais informações sobre a autenticação com Azure AD, consulte os seguintes artigos:

- [Autenticar pedidos para a Azure Event Hubs usando o Azure Ative Directory](authenticate-application.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado 
As assinaturas de acesso partilhado (SAS) para os recursos do Event Hubs proporcionam acesso limitado e delegado aos recursos do Event Hubs. A adição de restrições no intervalo de tempo para o qual a assinatura é válida ou sobre permissões que concede proporciona flexibilidade na gestão dos recursos. Para obter mais informações, consulte [Authenticate utilizando assinaturas de acesso partilhada (SAS)](authenticate-shared-access-signature.md). 

Autorizar utilizadores ou aplicações utilizando um token OAuth 2.0 devolvido pela Azure AD proporciona uma segurança superior e facilidade de utilização sobre assinaturas de acesso partilhado (SAS). Com o Azure AD, não há necessidade de armazenar os tokens de acesso com o seu código e arriscar potenciais vulnerabilidades de segurança. Enquanto pode continuar a usar assinaturas de acesso partilhado (SAS) para conceder acesso fino aos recursos do Event Hubs, a Azure AD oferece capacidades semelhantes sem a necessidade de gerir tokens SAS ou se preocupar em revogar um SAS comprometido. 

Por padrão, todos os recursos do Event Hubs estão seguros e estão disponíveis apenas para o proprietário da conta. Embora possa utilizar qualquer uma das estratégias de autorização descritas acima para garantir aos clientes o acesso aos recursos do Event Hub. A Microsoft recomenda a utilização do Azure AD sempre que possível para máxima segurança e facilidade de utilização.

Para obter mais informações sobre a autorização utilizando o SAS, consulte [autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado.](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Passos seguintes
- Reveja [as amostras do Azure RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publicadas no nosso repositório GitHub. 
- Consulte os seguintes artigos:
    - [Autenticar pedidos aos Azure Event Hubs a partir de uma aplicação utilizando o Azure Ative Directory](authenticate-application.md)
    - [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos](authenticate-managed-identity.md)
    - [Autenticar pedidos para Azure Event Hubs usando Assinaturas de Acesso Partilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)

