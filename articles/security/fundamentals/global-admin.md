---
title: Habilitar MFA para todos os administradores do Azure
description: Diretrizes para habilitar o administrador global
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 702feded450aed7368836c7eff799e969b9f396b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727307"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Impor a MFA (autenticação multifator) para administradores de assinatura

Ao criar seus administradores, incluindo sua conta de administrador global, é essencial que você use métodos de autenticação muito fortes.

Você pode executar a administração diária atribuindo funções de administrador específicas, como administrador do Exchange ou administrador de senha, às contas de usuário da equipe de ti, conforme necessário.
Além disso, a habilitação da [MFA (autenticação multifator) do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para seus administradores adiciona uma segunda camada de segurança a entradas e transações do usuário. O Azure MFA também ajuda a reduzir a probabilidade de que uma credencial comprometida tenha acesso aos dados da organização.

Por exemplo: Você impõe o Azure MFA para seus usuários e o configura para usar uma chamada telefônica ou uma mensagem de texto como verificação. Se as credenciais do usuário estiverem comprometidas, o invasor não poderá acessar nenhum recurso, pois não terá acesso ao telefone do usuário. As organizações que não adicionam camadas extras de proteção de identidade são mais suscetíveis a ataques de roubo de credenciais, o que pode levar a um comprometimento de dados.

Uma alternativa para as organizações que desejam manter todo o controle de autenticação local é usar o [servidor de autenticação multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), também chamado de "MFA local". Usando esse método, você ainda poderá impor a autenticação multifator e, ao mesmo tempo, manter o servidor MFA local.

Para verificar quem em sua organização tem privilégios administrativos, você pode verificar usando o seguinte comando do PowerShell Microsoft Azure AD v2:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Habilitando MFA

Examine como a [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) funciona antes de continuar.

Desde que os utilizadores tenham licenças que incluam a Multi-Factor Authentication do Azure, não é necessário fazer nada para ativar a MFA do Azure. Pode requerer a verificação de dois passos para cada utilizador. As licenças que permitem a MFA do Azure são:

- Multi-Factor Authentication do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação de dois passos para os utilizadores

Use um dos procedimentos listados em [como exigir a verificação em duas etapas](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) para um usuário ou grupo para começar a usar o Azure MFA. Você pode optar por impor a verificação em duas etapas para todas as entradas ou pode criar políticas de acesso condicional para exigir a verificação em duas etapas somente quando for importante para você.

