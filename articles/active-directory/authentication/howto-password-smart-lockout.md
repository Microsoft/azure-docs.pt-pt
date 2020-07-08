---
title: Prevenção de ataques com bloqueio inteligente - Azure Ative Directory
description: O bloqueio inteligente do Azure Ative Directory ajuda a proteger a sua organização de ataques de força bruta tentando adivinhar senhas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652572"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart lockout (Bloqueio inteligente do Azure Active Directory)

O bloqueio inteligente ajuda a bloquear os maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. Pode reconhecer os ins-ins provenientes de utilizadores válidos e tratá-los de forma diferente das dos atacantes e de outras fontes desconhecidas. O bloqueio inteligente bloqueia os atacantes, ao mesmo tempo que permite que os seus utilizadores continuem a aceder às suas contas e a ser produtivos.

Por predefinição, o bloqueio inteligente bloqueia a conta de tentativas de inscrição durante um minuto após 10 tentativas falhadas. A conta bloqueia novamente após cada tentativa falhada de inscrição, durante um minuto no início e mais tempo nas tentativas subsequentes.

O bloqueio inteligente rastreia os últimos três haques de palavra-passe estragados para evitar o incremento do contador de bloqueio para a mesma palavra-passe. Se alguém introduzir a mesma palavra-passe errada várias vezes, este comportamento não fará com que a conta bloqueie.

 > [!NOTE]
 > A funcionalidade de rastreio de haxixe não se encontra disponível para clientes com autenticação pass-through ativada, uma vez que a autenticação acontece no local e não na nuvem.

As implementações federadas utilizando o AD FS 2016 e o AF FS 2019 podem permitir benefícios semelhantes utilizando [o Bloqueio extranet AD FS e o bloqueio smart da extranet.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)

O bloqueio inteligente está sempre ligado para todos os clientes Azure AD com estas definições padrão que oferecem a mistura certa de segurança e usabilidade. A personalização das definições de bloqueio inteligente, com valores específicos da sua organização, requer licenças AD Azure pagas para os seus utilizadores.

A utilização de um bloqueio inteligente não garante que um utilizador genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de utilizador, tentamos o nosso melhor para não bloquear o utilizador genuíno. O serviço de bloqueio tenta garantir que os maus atores não possam ter acesso a uma conta de utilizador genuína.  

* Cada centro de dados do Azure Ative Directory rastreia o bloqueio de forma independente. Um utilizador terá (threshold_limit * datacenter_count) número de tentativas, se o utilizador atingir cada centro de dados.
* Smart Lockout usa localização familiar vs localização desconhecida para diferenciar entre um mau ator e o utilizador genuíno. Locais desconhecidos e familiares terão ambos balcões de bloqueio separados.

O bloqueio inteligente pode ser integrado com implementações híbridas, utilizando sincronização de haxixe de palavra-passe ou autenticação pass-through para proteger as contas do Ative Directory no local de serem bloqueadas pelos atacantes. Ao definir as políticas de bloqueio inteligentes em Azure AD adequadamente, os ataques podem ser filtrados antes de chegarem ao ative directy.

Ao utilizar a [autenticação pass-through,](../hybrid/how-to-connect-pta.md)tem de se certificar de que:

* O limiar de bloqueio Azure AD é **inferior** ao limiar de bloqueio da conta ative diretoria. De modo a definir os valores de modo a que o limiar de bloqueio da conta ative diretoria seja pelo menos duas ou três vezes maior do que o limiar de bloqueio Azure AD. 
* A duração do bloqueio Azure AD deve ser definida por mais tempo do que o contador de bloqueio de conta reset Ative Directory após a duração. Tenha em atenção que a duração AD AD do Azure é definida em segundos, enquanto a duração da AD é definida em minutos. 

Por exemplo, se quiser que o seu contador AD Azure seja superior ao da AD, então o Azure AD seria de 120 segundos (2 minutos) enquanto o seu AD no local está definido para 1 minuto (60 segundos).

> [!IMPORTANT]
> Atualmente, um administrador não pode desbloquear as contas na nuvem dos utilizadores se tiverem sido bloqueadas pela capacidade de Bloqueio Inteligente. O administrador deve aguardar que a duração do bloqueio expire. No entanto, o utilizador pode desbloquear utilizando o reset da palavra-passe de autosserviço (SSPR) a partir de um dispositivo ou localização fidedigno.

## <a name="verify-on-premises-account-lockout-policy"></a>Verifique a política de bloqueio de conta no local

Utilize as seguintes instruções para verificar a política de bloqueio de conta ative do Diretório ativo no local:

1. Abra a ferramenta de Gestão de Políticas de Grupo.
2. Editar a política de grupo que inclui a política de bloqueio de conta da sua organização, por exemplo, a **Política de Domínio Padrão.**
3. Consulte as políticas **de configuração do**  >  **Policies**  >  **computador, as definições**  >  **de segurança configuras de segurança**Política de conta de segurança Política de Bloqueio de  >  **Account Policies**  >  **Contas**.
4. Verifique o **limiar de bloqueio da** conta e **reinicie o contador de bloqueio de conta após** os valores.

![Modificar a política de bloqueio de conta ative no local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerir os valores de bloqueio inteligente Azure AD

Com base nos seus requisitos organizacionais, os valores de bloqueio inteligentes podem ter de ser personalizados. A personalização das definições de bloqueio inteligente, com valores específicos da sua organização, requer licenças AD Azure pagas para os seus utilizadores.

Para verificar ou modificar os valores de bloqueio inteligente para a sua organização, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione *Azure Active Directory*. Selecione **Security**  >  **métodos de autenticação de segurança**  >  **Proteção de senha**.
1. Descreva o **limiar de bloqueio,** com base no número de insuposições falhadas permitidas numa conta antes do seu primeiro bloqueio. O padrão é 10.
1. Desa estante a duração do **bloqueio em segundos,** ao comprimento em segundos de cada bloqueio. O predefinido é de 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro sinal após um bloqueio também falhar, a conta bloqueia novamente. Se uma conta se bloquear repetidamente, a duração do bloqueio aumenta.

![Personalize a política de bloqueio inteligente Azure AD no portal Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se a funcionalidade de bloqueio Smart está a funcionar ou não

Quando o limiar de bloqueio inteligente for acionado, receberá a seguinte mensagem enquanto a conta está bloqueada:

**A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente de novo mais tarde, e se ainda tiver problemas, contacte o seu administrador.**

## <a name="next-steps"></a>Próximos passos

* [Descubra como proibir más senhas na sua organização usando Azure AD.](howto-password-ban-bad.md)
* [Configure o reset da palavra-passe de autosserviço para permitir que os utilizadores desbloqueiem as suas próprias contas.](quickstart-sspr.md)
