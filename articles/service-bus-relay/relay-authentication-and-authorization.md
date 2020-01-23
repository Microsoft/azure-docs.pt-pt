---
title: Autenticação e autorização de retransmissão do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da autenticação SAS (assinatura de acesso compartilhado) com o serviço de retransmissão do Azure.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514583"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticação e autorização de retransmissão do Azure

Os aplicativos podem se autenticar na retransmissão do Azure usando a autenticação SAS (assinatura de acesso compartilhado). A autenticação SAS permite que os aplicativos se autentiquem no serviço de retransmissão do Azure usando uma chave de acesso configurada no namespace de retransmissão. Você pode usar essa chave para gerar um token de assinatura de acesso compartilhado que os clientes podem usar para se autenticar no serviço de retransmissão.

## <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso compartilhado

A [autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite conceder a um usuário acesso aos recursos de retransmissão do Azure com direitos específicos. A autenticação SAS envolve a configuração de uma chave criptográfica com direitos associados em um recurso. Os clientes podem obter acesso a esse recurso apresentando um token SAS, que consiste no URI do recurso que está sendo acessado e uma expiração assinada com a chave configurada.

Você pode configurar chaves para SAS em um namespace de retransmissão. Ao contrário das mensagens do barramento de serviço, a [retransmissão conexões híbridas](relay-hybrid-connections-protocol.md) dá suporte a remetentes não autorizados ou anônimos. Você pode habilitar o acesso anônimo para a entidade ao criá-la, conforme mostrado na seguinte captura de tela do portal:

![][0]

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace de retransmissão que consiste no seguinte:

* *KeyName* que identifica a regra.
* *PrimaryKey* é uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey* é uma chave de criptografia usada para assinar/validar tokens SAS.
* *Direitos* que representam a coleção de direitos de escuta, envio ou gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as conexões de retransmissão em um namespace para clientes com tokens assinados usando a chave correspondente. Até 12 regras de autorização podem ser configuradas em um namespace de retransmissão. Por padrão, um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurado para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)específico. O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é reivindicado e uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para retransmissão do Azure está incluído no SDK do .NET do Azure versões 2,0 e posteriores. A SAS inclui suporte para um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como um parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Passos seguintes

- Continue lendo [a autenticação do barramento de serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md) para obter mais detalhes sobre SAS.
- Consulte o [Guia do protocolo conexões híbridas de retransmissão do Azure](relay-hybrid-connections-protocol.md) para obter informações detalhadas sobre a capacidade de conexões híbridas.
- Para obter informações correspondentes sobre autenticação e autorização de mensagens do barramento de serviço, consulte [autenticação e autorização do barramento de serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png