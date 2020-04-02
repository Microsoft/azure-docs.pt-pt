---
title: Autorizar o acesso aos Hubs de Eventos do Azure
description: Este artigo fornece informações sobre diferentes opções para autorizar o acesso aos recursos do Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521294"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizar o acesso aos Hubs de Eventos do Azure
Sempre que publica ou consome eventos/dados de um centro de eventos, o seu cliente está a tentar aceder aos recursos do Event Hubs. Todos os pedidos a um recurso seguro devem ser autorizados para que o serviço possa garantir que o cliente tem as permissões necessárias para publicar/consumir os dados. 

O Azure Event Hubs oferece as seguintes opções para autorizar o acesso a recursos seguros:

- Azure Active Directory
- Assinatura de acesso partilhado

> [!NOTE]
> Este artigo aplica-se tanto aos cenários do Event Hubs como ao [Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Ative Directory (Azure AD) para os recursos do Event Hubs fornece um controlo de acesso baseado em papéis (RBAC) para um controlo fino sobre o acesso de um cliente aos recursos. Pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões ao diretor de segurança, que pode ser um utilizador, um grupo ou um diretor de serviço de aplicação. O diretor de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O símbolo pode ser usado para autorizar um pedido de acesso a um recurso do Event Hubs.

Para mais informações sobre autenticação com AD Azure, consulte os seguintes artigos:

- [Autenticação solicita aos Hubs de Eventos Azure usando diretório ativo Azure](authenticate-application.md)
- [Autorize o acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure.](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado 
As assinaturas de acesso partilhado (SAS) para os recursos do Event Hubs proporcionam acesso limitado aos recursos do Event Hubs. A adição de restrições no intervalo de tempo para o qual a assinatura é válida ou em permissões que concede proporciona flexibilidade na gestão dos recursos. Para mais informações, consulte [Authenticate utilizando assinaturas de acesso partilhado (SAS)](authenticate-shared-access-signature.md). 

Autorizar utilizadores ou aplicações utilizando um token OAuth 2.0 devolvido pela Azure AD proporciona uma segurança e facilidade de utilização superiores sobre assinaturas de acesso partilhado (SAS). Com o Azure AD, não há necessidade de armazenar os tokens de acesso com o seu código e potenciais vulnerabilidades de segurança. Enquanto pode continuar a usar assinaturas de acesso partilhado (SAS) para conceder acesso fino aos recursos do Event Hubs, a Azure AD oferece capacidades semelhantes sem a necessidade de gerir tokens SAS ou preocupar-se em revogar um SAS comprometido. 

Por padrão, todos os recursos do Event Hubs estão protegidos e estão disponíveis apenas para o proprietário da conta. Embora possa utilizar qualquer uma das estratégias de autorização acima descritas para dar aos clientes acesso aos recursos do Event Hub. A Microsoft recomenda a utilização de AD Azure sempre que possível para máxima segurança e facilidade de utilização.

Para obter mais informações sobre a autorização utilizando o SAS, consulte a autorização de acesso aos recursos do [Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Passos seguintes
- Reveja [as amostras de RBAC publicadas](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) no nosso repositório GitHub. 
- Consulte os seguintes artigos:
    - [Autenticação solicita aos Hubs de Eventos Azure de uma aplicação que usa o Diretório Ativo Azure](authenticate-application.md)
    - [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento](authenticate-managed-identity.md)
    - [Autenticar pedidos aos Hubs de Eventos Azure usando assinaturas de acesso partilhado](authenticate-shared-access-signature.md)
    - [Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)

