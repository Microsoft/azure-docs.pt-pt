---
title: A política de linha de base requer MFA para gerenciamento de serviços (versão prévia)-Azure Active Directory
description: Política de acesso condicional para exigir MFA para Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b3a3f12b2955ae97c43b0caf6a4ac13c9b770a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086736"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Política de linha de base: Exigir MFA para gerenciamento de serviços (versão prévia)

Você pode estar usando uma variedade de serviços do Azure em sua organização. Esses serviços podem ser gerenciados por meio da API Azure Resource Manager:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

O uso de Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. Azure Resource Manager pode alterar configurações de todo o locatário, como configurações de serviço e cobrança de assinatura. A autenticação de fator único é vulnerável a uma variedade de ataques, como phishing e spray de senha. Portanto, é importante verificar a identidade dos usuários que desejam acessar Azure Resource Manager e atualizar as configurações, exigindo a autenticação multifator antes de permitir o acesso.

**Exigir MFA para gerenciamento de serviços** é uma [política de linha de base](concept-baseline-protection.md) que exigirá MFA para qualquer usuário que acessa portal do Azure, Azure PowerShell ou CLI do Azure. Essa política se aplica a todos os usuários que acessam Azure Resource Manager, independentemente de serem um administrador.

Depois que essa política estiver habilitada em um locatário, todos os usuários que fizerem logon nos recursos de gerenciamento do Azure serão desafiados com a autenticação multifator. Se o usuário não estiver registrado para MFA, o usuário será solicitado a se registrar usando o aplicativo Microsoft Authenticator para continuar.

Para executar a entrada interativa usando o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), use o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

```PowerShell
Connect-AzAccount
```

Quando é executado, este cmdlet apresenta uma cadeia de token. Para entrar, copie essa cadeia de caracteres e cole- [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) a em um navegador. A sua sessão do PowerShell será autenticada para se ligar ao Azure.

Para executar a entrada interativa usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), execute o comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, você precisa abrir uma página do navegador e seguir as instruções na linha de comando para inserir um código de autorização depois [https://aka.ms/devicelogin](https://aka.ms/devicelogin) de navegar até no navegador. Em seguida, entre com suas credenciais de conta no navegador.

## <a name="deployment-considerations"></a>Considerações sobre implementação

A política **exigir MFA para gerenciamento de serviços** se aplica a todos os Azure Resource Manager usuários.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **de linha de base de política: Exigir MFA para gerenciamento de serviços (versão** prévia) vem pré-configurado e aparecerá na parte superior quando você navegar para a folha acesso condicional em portal do Azure.

Para habilitar essa política e proteger seus administradores:

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir MFA para gerenciamento de serviços (versão**prévia).
1. Defina **habilitar política** para **usar a política imediatamente**.
1. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
