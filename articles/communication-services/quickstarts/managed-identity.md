---
title: Utilizar identidades geridas nos Serviços de Comunicação
titleSuffix: An Azure Communication Services quickstart
description: Identidades geridas permitem autorizar o acesso dos Serviços de Comunicação Azure a partir de aplicações em execução em VMs Azure, aplicações de funções e outros recursos.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307444"
---
# <a name="use-managed-identities"></a>Utilizar identidades geridas
Começa com os Serviços de Comunicação Azure utilizando identidades geridas. Os Serviços de Comunicação Identidade e SMS SDKs apoiam a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este quickstart mostra-lhe como autorizar o acesso aos SDKs de Identidade e SMS a partir de um ambiente Azure que suporta identidades geridas. Também descreve como testar o seu código num ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free)
- Um recurso ativo dos Serviços de Comunicação Azure, consulte [criar um recurso de Serviços de Comunicação](./create-communication-resource.md) se não tiver um.
- Para enviar um SMS, necessitará de um [número de telefone.](./telephony-sms/get-phone-number.md)
- Uma configuração gerida identidade para um ambiente de desenvolvimento, ver [Acesso autorizado com identidade gerida](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o controlo de acesso baseado em funções da Azure](../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../concepts/telephony-sms/concepts.md)
