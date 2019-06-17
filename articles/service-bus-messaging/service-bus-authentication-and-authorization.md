---
title: Autorização e autenticação do Service Bus do Azure | Documentos da Microsoft
description: Autenticar aplicações para o Service Bus com a autenticação de assinatura de acesso partilhado (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7c5a45504b7c44d97ff2250663ef9c47ef6e3595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60714511"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Service Bus

Aplicativos obtém acesso aos recursos do Azure Service Bus utilizando a autenticação de token de assinatura de acesso partilhado (SAS). Com o SAS, os aplicativos apresentam um token para o Service Bus assinado com uma chave simétrica conhecido para o emissor de tokens e do Service Bus (, por conseguinte, "partilhado") e essa chave é diretamente associado a uma regra de conceder direitos de acesso específicos, como a permissão para receber/escutar ou enviar mensagens. Regras SAS são que seja configurado no espaço de nomes ou diretamente em entidades, como uma fila ou tópico, permitindo que para controlo de acesso detalhada.

SAS tokens podem ser gerados por um cliente do Service Bus diretamente, ou podem ser gerados por algumas token intermediário emitir o ponto final com a qual o cliente interage. Por exemplo, um sistema pode exigir o cliente chamar um Active Directory protegido web service ponto final de autorização para provar a sua identidade e direitos de acesso do sistema e o serviço web, em seguida, devolve o barramento de serviço apropriado token. Este token SAS pode ser facilmente gerada com o fornecedor do token do Service Bus incluído no SDK do Azure. 

> [!IMPORTANT]
> Se estiver a utilizar o Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) com o Service Bus, tenha em atenção de que o suporte para esse método é agora limitado e deve migrar a sua aplicação para utilizar a SAS. Para obter mais informações, consulte [nesta mensagem de blogue](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e [este artigo](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhada

[Autenticação de SAS](service-bus-sas.md) permite-lhe conceder um acesso de utilizador aos recursos do Service Bus, com direitos específicos. Autenticação de SAS no Service Bus envolve a configuração de uma chave criptográfica com direitos associados de um recurso do Service Bus. Os clientes, em seguida, podem obter acesso a esse recurso através da apresentação de um token SAS, que consiste o URI que está sendo acessado do recurso e uma expiração assinada com a chave configurada.

Pode configurar as chaves SAs num espaço de nomes do Service Bus. A chave se aplica a todas as entidades de mensagens dentro desse namespace. Também pode configurar as chaves em tópicos e filas do Service Bus. Também é suportada o SAS [reencaminhamento do Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Para utilizar a SAS, pode configurar uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto num espaço de nomes, fila ou tópico. Esta regra é composta pelos seguintes elementos:

* *KeyName*: identifica a regra.
* *PrimaryKey*: uma chave criptográfica utilizada para início de sessão/validar tokens SAS.
* *Secundária de regeneração*: uma chave criptográfica utilizada para início de sessão/validar tokens SAS.
* *Direitos*: representa a coleção de **escutar**, **enviar**, ou **gerir** direitos concedidos.

Regras de autorização configuradas ao nível do espaço de nomes podem conceder acesso a todas as entidades de um espaço de nomes para os clientes com tokens assinadas utilizando a chave correspondente. Pode configurar até 12 dessas regras de autorização num espaço de nomes, fila ou tópico do Service Bus. Por predefinição, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada espaço de nomes ao que está a ser aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente precisa de um token SAS gerado com um específico [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado com HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste o URI do recurso para o qual o acesso é reivindicado e uma expiração com uma chave de criptografia associada com a regra de autorização.

Suporte de autenticação de SAS do Service Bus está incluído nas versões do SDK .NET do Azure 2.0 e posteriores. SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como um parâmetro incluem suporte para cadeias de ligação SAS.

## <a name="next-steps"></a>Passos Seguintes

- Continue a ler [autenticação do Service Bus com assinaturas de acesso partilhado](service-bus-sas.md) para obter mais detalhes sobre a SAS.
- Como [migrar a partir do Azure Active Directory controlo de acesso (ACS) para autorização de assinatura de acesso partilhado](service-bus-migrate-acs-sas.md).
- [Espaços de nomes de alterações para ACS ativado](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Para obter informações correspondentes sobre o reencaminhamento do Azure de autenticação e autorização, consulte [reencaminhamento do Azure de autenticação e autorização](../service-bus-relay/relay-authentication-and-authorization.md). 

