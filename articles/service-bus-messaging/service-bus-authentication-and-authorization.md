---
title: Autenticação e autorização do Azure Service Bus Microsoft Docs
description: Autenticar aplicativos para Service Bus com autenticação de assinatura de acesso partilhado (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d5d0ed03c869bd574e4cfaa52ac7b62e8cb7fb98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066867"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus
Existem duas formas de autenticar e autorizar o acesso aos recursos do Azure Service Bus: Azure Activity Directory (Azure AD) e Shared Access Signatures (SAS). Este artigo dá-lhe detalhes sobre a utilização destes dois tipos de mecanismos de segurança. 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração AD da AD para os recursos de Service Bus fornece controlo de acesso baseado em funções (RBAC) para um controlo de grãos finos sobre o acesso de um cliente aos recursos. Pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador, um grupo ou um diretor de serviço de aplicação. O principal de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O token pode ser usado para autorizar um pedido de acesso a um recurso de Service Bus (fila, tópico, etc.).

Para obter mais informações sobre a autenticação com Azure AD, consulte os seguintes artigos:

- [Autenticar com identidades geridas](service-bus-managed-service-identity.md)
- [Autenticar a partir de uma aplicação](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) suporta a autenticação OAuth com Azure AD.

> [!IMPORTANT]
> Autorizar utilizadores ou aplicações utilizando o símbolo OAuth 2.0 devolvido pela Azure AD proporciona uma segurança superior e facilidade de utilização sobre assinaturas de acesso partilhada (SAS). Com o Azure AD, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança. Recomendamos que utilize a Azure AD com as suas aplicações Azure Service Bus sempre que possível. 

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
[A autenticação SAS](service-bus-sas.md) permite-lhe conceder acesso ao utilizador aos recursos do Service Bus, com direitos específicos. A autenticação SAS em Service Bus envolve a configuração de uma chave criptográfica com direitos associados num recurso de Service Bus. Os clientes podem então ter acesso a esse recurso apresentando um token SAS, que consiste no recurso a que o URI está a ser acedido e um prazo de validade assinado com a chave configurada.

Pode configurar as chaves para SAS num espaço de nomes de Service Bus. A chave aplica-se a todas as entidades de mensagens dentro desse espaço de nome. Também pode configurar chaves nas filas e tópicos do Service Bus. O SAS também é suportado no [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Para utilizar SAS, pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) num espaço de nome, fila ou tópico. Esta regra consiste nos seguintes elementos:

* *Nome-chave*: identifica a regra.
* *PrimaryKey*: uma chave criptográfica usada para assinar/validar fichas SAS.
* *SecondaryKey*: uma chave criptográfica usada para assinar/validar fichas SAS.
* *Direitos*: representa a recolha de direitos **de escuta,** **envio**ou **gestão** concedidos.

As regras de autorização configuradas ao nível do espaço de nome podem conceder acesso a todas as entidades num espaço de nome para clientes com fichas assinadas usando a chave correspondente. Pode configurar até 12 tais regras de autorização num espaço de nomes, fila ou tópico do Service Bus. Por padrão, uma [Regra de Polidissauto](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada espaço de nome quando é for provisionada pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado usando uma [regra de politização partilhada.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de recursos que consiste no recurso URI a que o acesso é reclamado, e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte de autenticação SAS para Service Bus está incluído nas versões Azure .NET SDK 2.0 e posteriormente. O SAS inclui suporte para uma [Regra de Poliétula Desauthorization.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Todas as APIs que aceitam uma cadeia de ligação como parâmetro incluem suporte para cordas de conexão SAS.

> [!IMPORTANT]
> Se estiver a utilizar o Azure Ative Directory Access Control (também conhecido como Access Control Service ou ACS) com o Service Bus, note que o suporte para este método é agora limitado e que deverá [migrar a sua aplicação para utilizar SAS](service-bus-migrate-acs-sas.md) ou utilizar a autenticação OAuth 2.0 com Azure AD (recomendado). Para mais informações sobre a depreciação da ACS, consulte [este post de blog.](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a autenticação com Azure AD, consulte os seguintes artigos:

- [Autenticação com identidades geridas](service-bus-managed-service-identity.md)
- [Autenticação a partir de uma aplicação](authenticate-application.md)

Para obter mais informações sobre a autenticação com SAS, consulte os seguintes artigos:

- [Autenticação com SAS](service-bus-sas.md)