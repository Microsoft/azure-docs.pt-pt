---
title: Autorização e autenticação de reencaminhamento do Azure | Documentos da Microsoft
description: Descrição geral da autenticação de assinatura de acesso partilhado (SAS) do reencaminhamento do Azure
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 206cca95c590a01f69d3664fb87398bc2fcb4ad9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445794"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autorização e autenticação de reencaminhamento do Azure

Aplicativos podem ser autenticado no reencaminhamento do Azure através da autenticação de assinatura de acesso partilhado (SAS). Autenticação de SAS permite que os aplicativos para se autenticar no serviço de reencaminhamento do Azure com uma chave de acesso configurada no espaço de nomes de reencaminhamento. Em seguida, pode utilizar esta chave para gerar um token de assinatura de acesso partilhado que os clientes podem utilizar para autenticar para o serviço de reencaminhamento.

## <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhada

[Autenticação de SAS](../service-bus-messaging/service-bus-sas.md) permite-lhe conceder um acesso de utilizador aos recursos de reencaminhamento do Azure com direitos específicos. Autenticação de SAS envolve a configuração de uma chave criptográfica com direitos associados de um recurso. Os clientes, em seguida, podem obter acesso a esse recurso através da apresentação de um token SAS, que consiste o URI que está sendo acessado do recurso e uma expiração assinada com a chave configurada.

Pode configurar as chaves SAs num espaço de nomes do reencaminhamento. Ao contrário das mensagens do Service Bus, [ligações híbridas de reencaminhamento](relay-hybrid-connections-protocol.md) suporta remetentes não autorizadas ou anónimos. Pode ativar o acesso anónimo para a entidade aquando da criação, conforme mostrado na seguinte captura de ecrã do portal:

![][0]

Para utilizar a SAS, pode configurar uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objeto num espaço de nomes de reencaminhamento que consiste no seguinte:

* *KeyName* que identifique a regra.
* *PrimaryKey* é uma chave criptográfica utilizada para início de sessão/validar os tokens SAS.
* *Secundária de regeneração* é uma chave criptográfica utilizada para início de sessão/validar os tokens SAS.
* *Direitos* que representa a coleção de escuta, enviar ou gerir os direitos concedidos.

Regras de autorização configuradas ao nível do espaço de nomes podem conceder acesso a todas as ligações de reencaminhamento num espaço de nomes para os clientes com tokens assinadas utilizando a chave correspondente. Até 12 tal autorização as regras podem ser configuradas num espaço de nomes do reencaminhamento. Por predefinição, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada espaço de nomes ao que está a ser aprovisionado pela primeira vez.

Para aceder a uma entidade, o cliente precisa de um token SAS gerado com um específico [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado com HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste o URI do recurso para o qual o acesso é reivindicado e uma expiração com uma chave de criptografia associada com a regra de autorização.

Suporte de autenticação de SAS para o reencaminhamento do Azure está incluído nas versões do SDK .NET do Azure 2.0 e posteriores. SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de ligação como um parâmetro incluem suporte para cadeias de ligação SAS.

## <a name="next-steps"></a>Passos Seguintes

- Continue a ler [autenticação do Service Bus com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md) para obter mais detalhes sobre a SAS.
- Consulte a [Guia do protocolo de ligações de híbridas de reencaminhamento do Azure](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a capacidade de ligações híbridas.
- Para obter informações correspondentes sobre mensagens do Service Bus autenticação e autorização, consulte [do Service Bus autenticação e autorização](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png