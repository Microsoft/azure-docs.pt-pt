---
title: Autenticação e autorização do Azure Service Bus Microsoft Docs
description: Autenticar aplicações para service bus com autenticação de assinatura de acesso partilhado (SAS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013234"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus

As aplicações têm acesso aos recursos do Azure Service Bus utilizando a autenticação simbólica de assinatura de acesso partilhado (SAS). Com a SAS, as aplicações apresentam um símbolo ao Service Bus que foi assinado com uma chave simétrica conhecida tanto pelo emitente simbólico como pelo Autocarro de Serviço (portanto "partilhado") e essa chave está diretamente associada a uma regra que concede direitos de acesso específicos, como a permissão para receber/ouvir ou enviar mensagens. As regras sas são configuradas no espaço de nome, ou diretamente em entidades como uma fila ou tópico, permitindo um controlo de acesso fino.

As fichas SAS podem ser geradas diretamente por um cliente de Ônibus de serviço, ou podem ser geradas por algum ponto final de emissão de fichas intermédiacom o qual o cliente interage. Por exemplo, um sistema pode exigir que o cliente ligue para um ponto final de serviço web protegido de autorização de Diretório Ativo para provar a sua identidade e direitos de acesso ao sistema, e o serviço web devolve então o token Service Bus apropriado. Este token SAS pode ser facilmente gerado utilizando o fornecedor de fichas service Bus incluído no Azure SDK. 

> [!IMPORTANT]
> Se estiver a utilizar o Azure Ative Directory Access Control (também conhecido como Access Control Service ou ACS) com o Service Bus, note que o suporte para este método é agora limitado e deverá migrar a sua aplicação para utilizar o SAS. Para mais informações, consulte [esta publicação de blogue](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e este [artigo.](service-bus-migrate-acs-sas.md)

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Ative Directory (Azure AD) para os recursos dos autocarros de serviço sintetizar o controlo de acesso baseado em funções (RBAC) para um controlo fino sobre o acesso de um cliente aos recursos. Pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões ao diretor de segurança, que pode ser um utilizador, um grupo ou um diretor de serviço de aplicação. O diretor de segurança é autenticado pela Azure AD para devolver um token OAuth 2.0. O símbolo pode ser usado para autorizar um pedido de acesso a um recurso de ônibus de serviço (fila, tópico, etc.).

Para mais informações sobre autenticação com AD Azure, consulte os seguintes artigos:

- [Autenticar com identidades geridas](service-bus-managed-service-identity.md)
- [Autenticar a partir de uma aplicação](authenticate-application.md)

> [!IMPORTANT]
> Autorizar utilizadores ou aplicações utilizando o token OAuth 2.0 devolvido pela Azure AD proporciona uma segurança e facilidade de utilização superiores sobre assinaturas de acesso partilhado (SAS). Com a AD Azure, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança. Recomendamos que utilize a utilização do Azure AD com as suas aplicações Azure Service Bus sempre que possível. 


## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
[A autenticação SAS](service-bus-sas.md) permite-lhe conceder ao utilizador acesso aos recursos do Service Bus, com direitos específicos. A autenticação SAS no Bus de Serviço envolve a configuração de uma chave criptográfica com direitos associados num recurso de ônibus de serviço. Os clientes podem então ter acesso a esse recurso apresentando um token SAS, que consiste no recurso URI a ser acedido e uma caducidade assinada com a chave configurada.

Pode configurar as chaves para SAS num espaço de nome de autocarro de serviço. A chave aplica-se a todas as entidades de mensagens dentro desse espaço de nome. Também pode configurar chaves em filas e tópicos de ônibus de serviço. A SAS também é apoiada no [Relé Azure.](../service-bus-relay/relay-authentication-and-authorization.md)

Para utilizar o SAS, pode configurar um objeto [sharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) num espaço de nome, fila ou tópico. Esta regra consiste nos seguintes elementos:

* *Nome chave*: identifica a regra.
* *PrimaryKey*: uma chave criptográfica utilizada para assinar/validar tokens SAS.
* *SegundaTecla*: uma chave criptográfica utilizada para assinar/validar tokens SAS.
* *Direitos*: representa a recolha de **Direitos**de Escuta, **Envio**ou **Gestão** de Direitos concedidos.

As regras de autorização configuradas ao nível do espaço de nome podem conceder acesso a todas as entidades num espaço de nome para clientes com fichas assinadas utilizando a chave correspondente. Pode configurar até 12 tais regras de autorização num espaço de nome, fila ou tópico do Ônibus de serviço. Por predefinição, uma Regra de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos está configurada para cada espaço de nome quando é aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente requer um token SAS gerado através de uma regra específica de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de recursos que consiste no recurso URI a que o acesso é reivindicado, e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte de autenticação SAS para o Service Bus está incluído nas versões Azure.NET SDK 2.0 e posteriormente. O SAS inclui suporte para uma Regra de Autorização de [Acesso Partilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como parâmetro incluem suporte para cordas de ligação SAS.

## <a name="next-steps"></a>Passos seguintes

- Continuar a ler [Autenticação de ônibus de serviço com Assinaturas de Acesso Partilhado](service-bus-sas.md) para mais detalhes sobre sas.
- Como [migrar do Azure Ative Directory Access Control (ACS) para a autorização de assinatura](service-bus-migrate-acs-sas.md)de acesso partilhado .
- [Alterações para espaços de nome sinuosos ativados](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)por ACS .
- Para obter informações correspondentes sobre a autenticação e autorização do Relé Azure, consulte a [autenticação e autorização do Relé Azure.](../service-bus-relay/relay-authentication-and-authorization.md) 

