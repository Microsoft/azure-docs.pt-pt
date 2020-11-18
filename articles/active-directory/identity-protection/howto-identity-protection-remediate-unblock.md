---
title: Remediar riscos e desbloquear utilizadores na Proteção de Identidade AZure AD
description: Saiba mais sobre as opções que tem deteções de risco ativas próximas.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd918aef85deefc23771413c3eb7b92f1189d39
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835803"
---
# <a name="remediate-risks-and-unblock-users"></a>Remediar riscos e desbloquear utilizadores

Após concluir a sua [investigação,](howto-identity-protection-investigate-risk.md)irá querer tomar medidas para corrigir o risco ou desbloquear os utilizadores. As organizações também têm a opção de permitir a remediação automatizada utilizando as suas [políticas de risco.](howto-identity-protection-configure-risk-policies.md) As organizações devem tentar encerrar todas as deteções de risco com as qual são apresentadas num período de tempo com o qual a sua organização se sinta confortável. A Microsoft recomenda o encerramento dos eventos o mais rapidamente possível, porque o tempo é importante quando se trabalha com risco.

## <a name="remediation"></a>Remediação

Todas as deteções de risco ativas contribuem para o cálculo de um valor chamado nível de risco do utilizador. O nível de risco do utilizador é um indicador (baixo, médio, alto) para a probabilidade de uma conta ter sido comprometida. Como administrador, pretende encerrar todas as deteções de risco, para que os utilizadores afetados deixem de estar em risco.

Algumas deteções de riscos podem ser marcadas pela Proteção de Identidade como "Fechado (sistema)" porque os eventos já não estavam determinados como sendo arriscados.

Os administradores têm as seguintes opções para remediar:

- Auto-remediação com política de risco
- Reset da palavra-passe manual
- Descartar o risco do utilizador
- Fechar as deteções individuais de risco manualmente

### <a name="self-remediation-with-risk-policy"></a>Auto-remediação com política de risco

Se permitir que os utilizadores se auto-remediarem, com a Autenticação Multi-Factor AD (MFA) e a redefinição da palavra-passe de autosserviço (SSPR) nas suas políticas de risco, podem desbloquear-se quando o risco for detetado. Estas deteções são então consideradas fechadas. Os utilizadores devem ter-se registado previamente para Azure AD MFA e SSPR para poderem utilizar quando o risco for detetado.

Algumas deteções podem não elevar o risco para o nível em que seria necessária uma autorreparação do utilizador, mas os administradores devem ainda avaliar estas deteções. Os administradores podem determinar que são necessárias medidas adicionais como [bloquear o acesso a partir de locais](../conditional-access/howto-conditional-access-policy-location.md) ou reduzir o risco aceitável nas suas políticas.

### <a name="manual-password-reset"></a>Reset da palavra-passe manual

Se exigir uma redefinição de palavra-passe utilizando uma política de risco do utilizador não é uma opção, os administradores podem fechar todas as deteções de risco para um utilizador com uma palavra-passe manual reiniciada.

Aos administradores são dadas duas opções ao redefinir uma palavra-passe para os seus utilizadores:

- **Gerar uma palavra-passe temporária** - Ao gerar uma senha temporária, pode imediatamente trazer uma identidade de volta para um estado seguro. Este método requer contactar os utilizadores afetados porque precisam de saber qual é a senha temporária. Como a palavra-passe é temporária, o utilizador é solicitado a alterar a palavra-passe para algo novo durante o próximo s-in.

- **Exigir que o utilizador reinicie a palavra-passe** - Exigir que os utilizadores reiniciem as palavras-passe permite a autorrecuperação sem contactar o help desk ou um administrador. Este método aplica-se apenas aos utilizadores registados para Azure AD MFA e SSPR. Para os utilizadores que não tenham sido registados, esta opção não está disponível.

### <a name="dismiss-user-risk"></a>Descartar o risco do utilizador

Se uma redefinição de palavra-passe não for uma opção para si, porque por exemplo o utilizador foi eliminado, pode optar por rejeitar as deteções de risco do utilizador.

Quando clica em **Rejeitar o risco do utilizador,** todos os eventos estão fechados e o utilizador afetado já não está em risco. No entanto, como este método não tem impacto na senha existente, não traz a identidade relacionada de volta a um estado seguro. 

### <a name="close-individual-risk-detections-manually"></a>Fechar as deteções individuais de risco manualmente

Pode fechar as deteções individuais de risco manualmente. Ao fechar as deteções de risco manualmente, pode baixar o nível de risco do utilizador. Normalmente, as deteções de risco são fechadas manualmente em resposta a uma investigação relacionada. Por exemplo, quando se fala com um utilizador revela que uma deteção de risco ativa já não é necessária. 
 
Ao fechar as deteções de risco manualmente, pode optar por tomar qualquer uma das seguintes ações para alterar o estado de uma deteção de risco:

- Confirme o utilizador comprometido
- Descartar o risco do utilizador
- Confirme o cofre de entrada
- Confirmar o insusição comprometido

## <a name="unblocking-users"></a>Desbloquear utilizadores

Um administrador pode optar por bloquear uma inscrição com base na sua política de risco ou nas suas investigações. Pode ocorrer um bloco com base no risco de inscrição ou de utilizador.

### <a name="unblocking-based-on-user-risk"></a>Desbloqueio com base no risco do utilizador

Para desbloquear uma conta bloqueada devido ao risco do utilizador, os administradores têm as seguintes opções:

1. **Redefinir a palavra-passe** - Pode redefinir a palavra-passe do utilizador.
1. **Rejeitar o risco do utilizador** - A política de risco do utilizador bloqueia um utilizador se o nível de risco do utilizador configurado para bloquear o acesso tiver sido atingido. Pode reduzir o nível de risco de um utilizador, descartando o risco do utilizador ou fechando manualmente as deteções de risco reportadas.
1. **Excluir o utilizador da política** - Se achar que a configuração atual da sua política de inscrição está a causar problemas a utilizadores específicos, pode excluir os utilizadores da presente. Para obter mais informações, consulte a secção Exclusões no artigo [Como: Configurar e permitir políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Política de desativar** - Se achar que a sua configuração de política está a causar problemas a todos os seus utilizadores, pode desativar a política. Para obter mais informações, consulte o artigo [Como: Configurar e permitir políticas de risco.](howto-identity-protection-configure-risk-policies.md)

### <a name="unblocking-based-on-sign-in-risk"></a>Desbloqueio com base no risco de inscrição

Para desbloquear uma conta baseada no risco de inscrição, os administradores têm as seguintes opções:

1. **Iniciar sposição a partir de um local ou dispositivo familiar** - Uma razão comum para iniciar sposições suspeitas bloqueadas são tentativas de inscrição de locais ou dispositivos desconhecidos. Os seus utilizadores podem determinar rapidamente se esta razão é a razão de bloqueio ao tentar entrar num local ou dispositivo familiar.
1. **Excluir o utilizador da política** - Se achar que a configuração atual da sua política de inscrição está a causar problemas a utilizadores específicos, pode excluir os utilizadores da presente. Para obter mais informações, consulte a secção Exclusões no artigo [Como: Configurar e permitir políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Política de desativar** - Se achar que a sua configuração de política está a causar problemas a todos os seus utilizadores, pode desativar a política. Para obter mais informações, consulte o artigo [Como: Configurar e permitir políticas de risco.](howto-identity-protection-configure-risk-policies.md)

## <a name="powershell-preview"></a>Pré-visualização PowerShell

Utilizando o módulo de pré-visualização SDK do Microsoft Graph PowerShell, as organizações podem gerir o risco utilizando o PowerShell. Os módulos de pré-visualização e o código de amostra podem ser encontrados no [repo Azure AD GitHub](https://github.com/AzureAD/IdentityProtectionTools).

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral da Proteção de Identidade AZure AD, consulte a visão geral da [Proteção de Identidade AZure AD](overview-identity-protection.md).
