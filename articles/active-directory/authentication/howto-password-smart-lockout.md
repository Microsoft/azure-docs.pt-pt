---
title: Impedindo ataques usando o bloqueio inteligente-Azure Active Directory
description: Azure Active Directory bloqueio inteligente ajuda a proteger sua organização contra ataques de força bruta tentando adivinhar senhas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c4cb598d9a8c14ab5d6ee893376266e104d15
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381534"
---
# <a name="azure-active-directory-smart-lockout"></a>Bloqueio inteligente de Azure Active Directory

O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. Ele pode reconhecer entradas provenientes de usuários válidos e tratá-los de forma diferente daqueles de invasores e outras fontes desconhecidas. O bloqueio inteligente bloqueia os invasores, enquanto permite que os usuários continuem acessando suas contas e sejam produtivos.

Por padrão, o bloqueio inteligente bloqueia a conta de tentativas de entrada por um minuto após 10 tentativas com falha. A conta é bloqueada novamente após cada tentativa de entrada com falha subsequente, por um minuto a primeiro e mais longa em tentativas subsequentes.

O bloqueio inteligente rastreia os três últimos hashes de senha inválidos para evitar incrementar o contador de bloqueios para a mesma senha. Se alguém inserir a mesma senha inválida várias vezes, esse comportamento não causará o bloqueio da conta.

 > [!NOTE]
 > A funcionalidade de controle de hash não está disponível para clientes com autenticação de passagem habilitada, pois a autenticação ocorre localmente, não na nuvem.

Implantações federadas usando AD FS 2016 e AF FS 2019 podem permitir benefícios semelhantes usando [AD FS bloqueio de extranet e bloqueio inteligente de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

O bloqueio inteligente está sempre ativado para todos os clientes do Azure AD com essas configurações padrão que oferecem a combinação certa de segurança e usabilidade. A personalização das configurações de bloqueio inteligente, com valores específicos para sua organização, requer licenças pagas do Azure AD para seus usuários.

O uso do bloqueio inteligente não garante que um usuário original nunca será bloqueado. Quando o bloqueio inteligente bloqueia uma conta de usuário, nós experimentamos o melhor para não bloquear o usuário original. O serviço de bloqueio tenta garantir que os atores ruins não possam obter acesso a uma conta de usuário original.  

* Cada Azure Active Directory data center rastreia o bloqueio de forma independente. Um usuário terá o número de tentativas (threshold_limit * datacenter_count), se o usuário atingir cada data center.
* O bloqueio inteligente usa um local familiar versus um local desconhecido para diferenciar entre um ator insatisfatório e o usuário original. Os locais desconhecidos e familiares terão contadores de bloqueio separados.

O bloqueio inteligente pode ser integrado a implantações híbridas, usando a sincronização de hash de senha ou a autenticação de passagem para proteger as contas de Active Directory locais sejam bloqueadas pelos invasores. Ao definir as políticas de bloqueio inteligente no Azure AD adequadamente, os ataques podem ser filtrados antes de alcançarem Active Directory locais.

Ao usar [a autenticação de passagem](../hybrid/how-to-connect-pta.md), você precisa certificar-se de que:

* O limite de bloqueio do Azure AD é **menor** que o limite de bloqueio de conta de Active Directory. Defina os valores para que o limite de bloqueio da conta de Active Directory seja pelo menos duas ou três vezes mais tempo do que o limite de bloqueio do Azure AD. 
* A duração do bloqueio do Azure AD deve ser definida por mais tempo do que o Active Directory reiniciar o contador de bloqueios de conta após a duração. Lembre-se de que a duração do Azure AD é definida em segundos, enquanto a duração do anúncio é definida em minutos. 

Por exemplo, se você quiser que seu contador do Azure AD seja maior que o AD, o Azure AD será de 120 segundos (2 minutos) enquanto seu AD local for definido como 1 minuto (60 segundos).

> [!IMPORTANT]
> Atualmente, um administrador não poderá desbloquear as contas de nuvem dos usuários se elas tiverem sido bloqueadas pelo recurso de bloqueio inteligente. O administrador deve aguardar a duração do bloqueio expirar. No entanto, o usuário pode desbloquear usando a SSPR (redefinição de senha de autoatendimento) de um dispositivo ou local confiável.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificar a política de bloqueio de conta local

Use as instruções a seguir para verificar sua política de bloqueio de conta Active Directory local:

1. Abra a ferramenta de gerenciamento de Política de Grupo.
2. Edite a política de grupo que inclui a política de bloqueio de conta da sua organização, por exemplo, a **política de domínio padrão**.
3. Navegue até **configuração do computador** > **políticas** > **configurações do Windows** > **configurações de segurança** > políticas de **conta** > política de **bloqueio de conta**.
4. Verifique seu **limite de bloqueio de conta** e **redefina o contador de bloqueios de conta após** os valores.

![Modificar a política de bloqueio de conta Active Directory local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerenciar valores de bloqueio inteligente do Azure AD

Com base nos requisitos organizacionais, os valores de bloqueio inteligente podem precisar ser personalizados. A personalização das configurações de bloqueio inteligente, com valores específicos para sua organização, requer licenças pagas do Azure AD para seus usuários.

Para verificar ou modificar os valores de bloqueio inteligente para sua organização, use as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Procure e selecione *Azure Active Directory*. Selecione os **métodos de autenticação** > proteção por **senha**.
1. Defina o **limite de bloqueio**, com base em quantas entradas com falha são permitidas em uma conta antes de seu primeiro bloqueio. O padrão é 10.
1. Defina a **duração do bloqueio em segundos**, para o comprimento em segundos de cada bloqueio. O padrão é 60 segundos (um minuto).

> [!NOTE]
> Se a primeira entrada após um bloqueio também falhar, a conta é bloqueada novamente. Se uma conta for bloqueada repetidamente, a duração do bloqueio aumentará.

![Personalizar a política de bloqueio inteligente do Azure AD no portal do Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se o recurso de bloqueio inteligente está funcionando ou não

Quando o limite de bloqueio inteligente for disparado, você receberá a seguinte mensagem enquanto a conta estiver bloqueada:

**Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente mais tarde e, se você ainda tiver problemas, entre em contato com seu administrador.**

## <a name="next-steps"></a>Passos seguintes

* [Descubra como proibir senhas ruins em sua organização usando o Azure AD.](howto-password-ban-bad.md)
* [Configure a redefinição de senha de autoatendimento para permitir que os usuários desbloqueiem suas próprias contas.](quickstart-sspr.md)
