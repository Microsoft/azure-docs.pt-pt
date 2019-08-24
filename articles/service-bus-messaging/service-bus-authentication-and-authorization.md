---
title: Autenticação e autorização do barramento de serviço do Azure | Microsoft Docs
description: Autentique aplicativos no barramento de serviço com autenticação SAS (assinatura de acesso compartilhado).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013234"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus

Os aplicativos têm acesso aos recursos do barramento de serviço do Azure usando a autenticação de token SAS (assinatura de acesso compartilhado). Com a SAS, os aplicativos apresentam um token para o barramento de serviço que foi assinado com uma chave simétrica conhecida tanto pelo emissor do token quanto pelo barramento de serviço (portanto, "compartilhado") e essa chave está diretamente associada a uma regra que concede direitos de acesso específicos, como a permissão para receber/ouvir ou enviar mensagens. As regras de SAS são configuradas no namespace ou diretamente em entidades, como uma fila ou um tópico, permitindo um controle de acesso refinado.

Os tokens SAS podem ser gerados diretamente por um cliente do barramento de serviço ou podem ser gerados por algum ponto de extremidade de emissão de token intermediário com o qual o cliente interage. Por exemplo, um sistema pode exigir que o cliente chame um ponto de extremidade de serviço Web protegido por autorização Active Directory para provar seus direitos de acesso ao sistema e à identidade, e o serviço Web retorna o token do barramento de serviço apropriado. Esse token SAS pode ser facilmente gerado usando o provedor de token do barramento de serviço incluído no SDK do Azure. 

> [!IMPORTANT]
> Se você estiver usando o controle de acesso Azure Active Directory (também conhecido como serviço de controle de acesso ou ACS) com o barramento de serviço, observe que o suporte para esse método agora é limitado e você deve migrar seu aplicativo para usar SAS. Para obter mais informações, consulte [esta postagem de blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e [Este artigo](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Active Directory (Azure AD) para recursos do barramento de serviço fornece controle de acesso baseado em função (RBAC) para controle refinado sobre o acesso de um cliente aos recursos. Você pode usar o RBAC (controle de acesso baseado em função) para conceder permissões à entidade de segurança, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação para acessar um recurso do barramento de serviço (fila, tópico, etc.).

Para obter mais informações sobre como autenticar com o Azure AD, consulte os seguintes artigos:

- [Autenticar com identidades gerenciadas](service-bus-managed-service-identity.md)
- [Autenticar de um aplicativo](authenticate-application.md)

> [!IMPORTANT]
> A autorização de usuários ou aplicativos usando o token 2,0 do OAuth retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar os tokens no código e arriscar as vulnerabilidades de segurança potenciais. É recomendável usar o Azure AD com seus aplicativos do barramento de serviço do Azure quando possível. 


## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
A [autenticação SAS](service-bus-sas.md) permite conceder a um usuário acesso aos recursos do barramento de serviço, com direitos específicos. A autenticação SAS no barramento de serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do barramento de serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS, que consiste no URI do recurso que está sendo acessado e uma expiração assinada com a chave configurada.

Você pode configurar chaves para SAS em um namespace do barramento de serviço. A chave se aplica a todas as entidades de mensagens dentro desse namespace. Você também pode configurar chaves em filas e tópicos do barramento de serviço. Também há suporte para SAS na [retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace, fila ou tópico. Essa regra consiste nos seguintes elementos:

* *KeyName*: identifica a regra.
* *PrimaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *Rights*: representa a coleção de direitos de **escuta**, **envio**ou **Gerenciamento** concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a chave correspondente. Você pode configurar até 12 regras de autorização em um namespace, fila ou tópico do barramento de serviço. Por padrão, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurado para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)específico. O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é reivindicado e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o barramento de serviço está incluído no SDK do .NET do Azure versões 2,0 e posteriores. A SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como um parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Passos seguintes

- Continue lendo [a autenticação do barramento de serviço com assinaturas de acesso compartilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.
- Como [migrar do ACS (controle de acesso Azure Active Directory) para a autorização de assinatura de acesso compartilhado](service-bus-migrate-acs-sas.md).
- [Alterações nos namespaces habilitados para ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Para obter informações correspondentes sobre autenticação e autorização de retransmissão do Azure, consulte [autenticação e autorização de retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md). 

