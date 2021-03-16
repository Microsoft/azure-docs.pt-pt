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
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492358"
---
# <a name="use-managed-identities"></a>Utilizar identidades geridas
Começa com os Serviços de Comunicação Azure utilizando identidades geridas. As bibliotecas de serviços de comunicação identidade e SMS apoiam a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este quickstart mostra-lhe como autorizar o acesso às bibliotecas de clientes Identidade e SMS a partir de um ambiente Azure que suporta identidades geridas. Também descreve como testar o seu código num ambiente de desenvolvimento.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o controlo de acesso baseado em funções da Azure](../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../concepts/telephony-sms/concepts.md)
