---
title: Utilizar identidades geridas nos Serviços de Comunicação
titleSuffix: An Azure Communication Services quickstart
description: Identidades geridas permitem autorizar o acesso dos Serviços de Comunicação Azure a partir de aplicações em execução em VMs Azure, aplicações de funções e outros recursos.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657625"
---
# <a name="use-managed-identities"></a>Utilizar identidades geridas
Começa com os Serviços de Comunicação Azure utilizando identidades geridas. As bibliotecas de serviços de comunicação identidade e SMS apoiam a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este quickstart mostra-lhe como autorizar o acesso às bibliotecas de clientes Identidade e SMS a partir de um ambiente Azure que suporta identidades geridas. Também descreve como testar o seu código num ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

 - Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free)
 - Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](./create-communication-resource.md?pivots=platform-azp&tabs=windows)

## <a name="setting-up"></a>Configuração

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Ativar identidades geridas numa máquina virtual ou serviço de Aplicação

As identidades geridas devem ser ativadas sobre os recursos do Azure que está a autorizar. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de aplicações](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Atribuir funções a Azure com o portal Azure

1. Navegue para o portal do Azure.
1. Navegue para o recurso do Serviço de Comunicação Azure.
1. Navegue para o menu Controlo de Acesso (IAM) -> + Adicionar -> Adicionar função.
1. Selecione a função "Contribuinte" (esta é a única função suportada).
1. Selecione "Identidade gerida atribuída pelo utilizador" (ou um "Sistema atribuído identidade gerida") e, em seguida, selecione a identidade desejada. Salve a sua seleção.

![Papel de identidade gerido](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Atribuir funções a Azure com PowerShell

Para atribuir funções e permissões utilizando o PowerShell, consulte [adicionar ou remover atribuições de funções Azure utilizando a Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end