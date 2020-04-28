---
title: Remediar riscos e desbloquear utilizadores na Proteção de Identidade da AD Azure
description: Conheça as opções que tem detetações de risco ativas próximas.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74382105"
---
# <a name="remediate-risks-and-unblock-users"></a>Remediar riscos e desbloquear utilizadores

Depois de concluir a sua [investigação,](howto-identity-protection-investigate-risk.md)irá querer tomar medidas para remediar o risco ou desbloquear os utilizadores. As organizações também têm a opção de permitir a reparação automatizada utilizando as suas políticas de [risco.](howto-identity-protection-configure-risk-policies.md) As organizações devem tentar encerrar todas as deteções de risco com as que são apresentadas num período de tempo com a sua organização. A Microsoft recomenda o encerramento dos eventos o mais rapidamente possível, porque o tempo importa quando se trabalha com risco.

## <a name="remediation"></a>Remediação

Todas as deteções de risco ativas contribuem para o cálculo de um valor chamado nível de risco do utilizador. O nível de risco do utilizador é um indicador (baixo, médio, elevado) para a probabilidade de uma conta ter sido comprometida. Como administrador, pretende encerrar todas as deteções de risco, para que os utilizadores afetados deixem de estar em risco.

Alguns riscos de deteção podem ser marcados pela Proteção de Identidade como "Fechado (sistema)" porque os eventos já não estavam determinados como arriscados.

Os administradores têm as seguintes opções para remediar:

- Auto-remediação com política de risco
- Reset manual da palavra-passe
- Descartar o risco de utilizador
- Encerrar as deteções individuais de risco manualmente

### <a name="self-remediation-with-risk-policy"></a>Auto-remediação com política de risco

Se permitir que os utilizadores se auto-remediam, com a Autenticação Multi-Factor Azure (MFA) e o reset de palavra-passe self-service (SSPR) nas suas políticas de risco, podem desbloquear-se quando o risco é detetado. Estas deteções são então consideradas encerradas. Os utilizadores devem ter registado previamente para o Azure MFA e SSPR para poderem utilizar quando o risco for detetado.

Algumas deteções podem não aumentar o risco para o nível em que seria necessária uma autorreparação do utilizador, mas os administradores devem ainda avaliar essas deteções. Os administradores podem determinar que são necessárias medidas adicionais como [bloquear o acesso a partir de locais](../conditional-access/howto-conditional-access-policy-location.md) ou reduzir o risco aceitável nas suas políticas.

### <a name="manual-password-reset"></a>Reset manual da palavra-passe

Se exigir uma redefinição da palavra-passe utilizando uma política de risco do utilizador não é uma opção, os administradores podem fechar todas as deteções de risco para um utilizador com uma redefinição manual da palavra-passe.

Os administradores recebem duas opções ao redefinir uma palavra-passe para os seus utilizadores:

- **Gerar uma senha temporária** - Ao gerar uma senha temporária, pode imediatamente trazer uma identidade de volta para um estado seguro. Este método requer contactar os utilizadores afetados porque precisam de saber qual é a senha temporária. Como a palavra-passe é temporária, o utilizador é solicitado a alterar a palavra-passe para algo novo durante o próximo sessão.

- **Exigir que o utilizador reset password** - Exigir que os utilizadores reporem as palavras-passe permite a autorrecuperação sem contactar o balcão de ajuda ou um administrador. Este método aplica-se apenas aos utilizadores registados para o Azure MFA e SSPR. Para utilizadores que não tenham sido registados, esta opção não está disponível.

### <a name="dismiss-user-risk"></a>Descartar o risco de utilizador

Se um reset de palavra-passe não for uma opção para si, porque, por exemplo, o utilizador foi eliminado, pode optar por descartar as deteções de risco do utilizador.

Quando clicar em Descartar o risco do **utilizador,** todos os eventos estão fechados e o utilizador afetado já não está em risco. No entanto, como este método não tem impacto na senha existente, não traz a identidade relacionada de volta para um estado seguro. 

### <a name="close-individual-risk-detections-manually"></a>Encerrar as deteções individuais de risco manualmente

Pode encerrar as deteções individuais de risco manualmente. Ao fechar as deteções de risco manualmente, pode baixar o nível de risco do utilizador. Tipicamente, as deteções de risco são fechadas manualmente em resposta a uma investigação relacionada. Por exemplo, quando se fala com um utilizador revela que já não é necessária uma deteção de risco ativa. 
 
Ao fechar as deteções de risco manualmente, pode optar por tomar qualquer uma das seguintes ações para alterar o estado de uma deteção de risco:

- Confirmar o utilizador comprometido
- Descartar o risco de utilizador
- Confirmar o cofre de inscrição
- Confirmar o compromisso de inscrição

## <a name="unblocking-users"></a>Desbloquear utilizadores

Um administrador pode optar por bloquear um sessão com base na sua política de risco ou investigações. Pode ocorrer um bloco com base no risco de iniciar sessão ou de utilizador.

### <a name="unblocking-based-on-user-risk"></a>Desbloqueio com base no risco do utilizador

Para desbloquear uma conta bloqueada devido ao risco do utilizador, os administradores têm as seguintes opções:

1. **Redefinir a palavra-passe** - Pode redefinir a palavra-passe do utilizador.
1. **Descartar o risco** do utilizador - A política de risco do utilizador bloqueia um utilizador se tiver atingido o nível de risco configurado para bloquear o acesso. Pode reduzir o nível de risco de um utilizador, descartando o risco do utilizador ou fechando manualmente as deteções de risco reportadas.
1. **Exclua o utilizador da política** - Se acha que a configuração atual da sua política de sessão está a causar problemas a utilizadores específicos, pode excluir os utilizadores da sua política. Para mais informações, consulte a secção Exclusões no artigo [Como Configurar e ativar políticas](howto-identity-protection-configure-risk-policies.md#exclusions)de risco .
1. **Política de desativação** - Se pensa que a sua configuração de política está a causar problemas a todos os seus utilizadores, pode desativar a política. Para mais informações, consulte o artigo [Como: Configurar e ativar políticas](howto-identity-protection-configure-risk-policies.md)de risco.

### <a name="unblocking-based-on-sign-in-risk"></a>Desbloqueio com base no risco de inscrição

Para desbloquear uma conta com base no risco de inscrição, os administradores têm as seguintes opções:

1. **Iniciar sessão** a partir de um local ou dispositivo familiar - Uma razão comum para inscrições suspeitas bloqueadas são tentativas de iniciar sessão de locais ou dispositivos desconhecidos. Os seus utilizadores podem determinar rapidamente se esta razão é a razão de bloqueio ao tentar iniciar sessão a partir de um local ou dispositivo familiar.
1. **Exclua o utilizador da política** - Se acha que a configuração atual da sua política de sessão está a causar problemas a utilizadores específicos, pode excluir os utilizadores da sua política. Para mais informações, consulte a secção Exclusões no artigo [Como Configurar e ativar políticas](howto-identity-protection-configure-risk-policies.md#exclusions)de risco .
1. **Política de desativação** - Se pensa que a sua configuração de política está a causar problemas a todos os seus utilizadores, pode desativar a política. Para mais informações, consulte o artigo [Como: Configurar e ativar políticas](howto-identity-protection-configure-risk-policies.md)de risco.

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral da Proteção de Identidade Azure AD, consulte a visão geral da [Proteção de Identidade da AD Azure](overview-identity-protection.md).
