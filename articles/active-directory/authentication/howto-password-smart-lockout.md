---
title: Prevenir ataques usando bloqueio inteligente - Azure Ative Directory
description: Saiba como o bloqueio inteligente do Azure Ative Directory ajuda a proteger a sua organização de ataques de força bruta que tentam adivinhar as palavras-passe dos utilizadores.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255378"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Proteja as contas dos utilizadores de ataques com o bloqueio inteligente do Azure Ative Directory

O bloqueio inteligente ajuda a bloquear os maus atores que tentam adivinhar as palavras-passe dos seus utilizadores ou usam métodos de força bruta para entrar. O bloqueio inteligente pode reconhecer as entradas que vêm de utilizadores válidos e tratá-las de forma diferente das de atacantes e outras fontes desconhecidas. Os atacantes ficam bloqueados, enquanto os seus utilizadores continuam a aceder às suas contas e a ser produtivos.

## <a name="how-smart-lockout-works"></a>Como funciona o bloqueio inteligente

Por predefinição, o bloqueio inteligente bloqueia a conta de tentativas de inscrição durante um minuto após 10 tentativas falhadas. A conta bloqueia novamente após cada tentativa falhada de inscrição, durante um minuto no início e mais tempo nas tentativas subsequentes. Para minimizar as formas como um intruso pode trabalhar em torno deste comportamento, não divulgamos a taxa a que o período de bloqueio cresce ao longo de tentativas adicionais de insu certo.

O bloqueio inteligente rastreia os últimos três haques de palavra-passe estragados para evitar o incremento do contador de bloqueio para a mesma palavra-passe. Se alguém introduzir a mesma palavra-passe errada várias vezes, este comportamento não fará com que a conta bloqueie.

> [!NOTE]
> A funcionalidade de rastreio de haxixe não está disponível para clientes com autenticação pass-through ativada, uma vez que a autenticação acontece no local e não na nuvem.

As implementações federadas que utilizam O FS FS 2016 e AF FS 2019 podem permitir benefícios semelhantes utilizando [o Bloqueio extranet AD FS e o bloqueio smart da extranet.](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)

O bloqueio inteligente está sempre ligado, para todos os clientes Azure AD, com estas definições padrão que oferecem a mistura certa de segurança e usabilidade. A personalização das configurações de bloqueio inteligente, com valores específicos da sua organização, requer licenças Azure AD Premium P1 ou licenças superiores para os seus utilizadores.

A utilização de um bloqueio inteligente não garante que um utilizador genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de utilizador, tentamos o nosso melhor para não bloquear o utilizador genuíno. O serviço de bloqueio tenta garantir que os maus atores não possam ter acesso a uma conta de utilizador genuína. As seguintes considerações são aplicáveis:

* Cada centro de dados AZure AD rastreia o bloqueio de forma independente. Um utilizador tem (*threshold_limit * datacenter_count*) número de tentativas, se o utilizador atingir cada centro de dados.
* Smart Lockout usa localização familiar vs localização desconhecida para diferenciar entre um mau ator e o utilizador genuíno. Locais desconhecidos e familiares ambos têm balcões de bloqueio separados.

O bloqueio inteligente pode ser integrado com implementações híbridas que utilizam sincronização de haxixe de palavra-passe ou autenticação pass-through para proteger as contas de Serviços de Domínio de Diretório Ativo (DS) no local de serem bloqueadas por atacantes. Ao definir as políticas de bloqueio inteligentes em Azure AD adequadamente, os ataques podem ser filtrados antes de chegarem ao local AD DS.

Ao utilizar [a autenticação transitativa, aplicam-se](../hybrid/how-to-connect-pta.md)as seguintes considerações:

* O limiar de bloqueio Azure AD é **inferior** ao limiar de bloqueio da conta AD DS. De modo a definir os valores de modo a que o limiar de bloqueio da conta AD DS seja pelo menos duas ou três vezes maior do que o limiar de bloqueio Azure AD.
* A duração do bloqueio Azure AD deve ser definida por mais tempo do que o contador de bloqueio de conta de reposição AD DS após a duração. A duração AD AZure é definida em segundos, enquanto a duração da AD é definida em minutos.

Por exemplo, se pretender que o seu contador AD Azure seja superior ao AD DS, então o Azure AD seria de 120 segundos (2 minutos) enquanto o seu AD no local está definido para 1 minuto (60 segundos).

> [!IMPORTANT]
> Atualmente, um administrador não pode desbloquear as contas na nuvem dos utilizadores se tiverem sido bloqueadas pela capacidade de Bloqueio Inteligente. O administrador deve aguardar que a duração do bloqueio expire. No entanto, o utilizador pode desbloquear utilizando o reset da palavra-passe de autosserviço (SSPR) a partir de um dispositivo ou localização fidedigno.

## <a name="verify-on-premises-account-lockout-policy"></a>Verifique a política de bloqueio de conta no local

Para verificar a política de bloqueio de conta AD DS no local, complete os seguintes passos de um sistema ligado ao domínio com privilégios de administrador:

1. Abra a ferramenta de Gestão de Políticas de Grupo.
2. Editar a política de grupo que inclui a política de bloqueio de conta da sua organização, como, por exemplo, a **Política de Domínio Padrão.**
3. Consulte as políticas **de configuração do**  >    >  **computador, as definições**  >  **de segurança configuras de segurança** Política de conta de segurança Política de Bloqueio de  >    >  **Contas**.
4. Verifique o **limiar de bloqueio da** conta e **reinicie o contador de bloqueio de conta após** os valores.

![Modificar a política de bloqueio de conta ative no local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerir os valores de bloqueio inteligente Azure AD

Com base nos seus requisitos organizacionais, pode personalizar os valores de bloqueio inteligente AZure AD. A personalização das configurações de bloqueio inteligente, com valores específicos da sua organização, requer licenças Azure AD Premium P1 ou licenças superiores para os seus utilizadores.

Para verificar ou modificar os valores de bloqueio inteligente para a sua organização, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione *Azure Ative Directory* e, em seguida, selecione   >  **métodos de autenticação de segurança**  >  **Proteção de passwords**.
1. Descreva o **limiar de bloqueio,** com base no número de insuposições falhadas permitidas numa conta antes do seu primeiro bloqueio.

    O padrão é 10.

1. Desa estante a duração do **bloqueio em segundos,** ao comprimento em segundos de cada bloqueio.

    O predefinido é de 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro sinal após um bloqueio também falhar, a conta bloqueia novamente. Se uma conta se bloquear repetidamente, a duração do bloqueio aumenta.

![Personalize a política de bloqueio inteligente Azure AD no portal Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se a funcionalidade de bloqueio Smart está a funcionar ou não

Quando o limiar de bloqueio inteligente for acionado, receberá a seguinte mensagem enquanto a conta está bloqueada:

*A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente de novo mais tarde, e se ainda tiver problemas, contacte o seu administrador.*

Ao testar o bloqueio inteligente, os seus pedidos de inscrição podem ser tratados por diferentes centros de dados devido à natureza geo-distribuída e equilibrada da carga do serviço de autenticação AD AD Azure. Nesse cenário, uma vez que cada datacenter AZure AD rastreia o bloqueio de forma independente, pode ser necessário mais do que o número de tentativas definidas de bloqueio para causar um bloqueio. Um utilizador tem (*threshold_limit * datacenter_count*) um número de tentativas erradas se o utilizador atingir cada datacenter antes de ocorrer um bloqueio.

## <a name="next-steps"></a>Passos seguintes

Para personalizar ainda mais a experiência, pode [configurar senhas proibidas personalizadas para proteção de senha AD AZure](tutorial-configure-custom-password-protection.md).

Para ajudar os utilizadores a redefinir ou alterar a sua palavra-passe a partir de um navegador web, pode [configurar a palavra-passe de autosserviço Azure.](tutorial-enable-sspr.md)
