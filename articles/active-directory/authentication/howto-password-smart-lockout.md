---
title: Prevenção de ataques usando bloqueio inteligente - Diretório Ativo Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652572"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart lockout (Bloqueio inteligente do Azure Active Directory)

Smart lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou usar métodos de força bruta para entrar. Pode reconhecer inscrições provenientes de utilizadores válidos e tratá-los de forma diferente das de atacantes e outras fontes desconhecidas. O bloqueio inteligente bloqueia os atacantes, ao mesmo tempo que permite que os seus utilizadores continuem a aceder às suas contas e a serem produtivos.

Por defeito, o bloqueio inteligente bloqueia a conta das tentativas de inscrição durante um minuto após 10 tentativas falhadas. A conta volta a ser bloqueada após cada tentativa de inscrição falhada subsequente, durante um minuto, no início e mais tempo, nas tentativas subsequentes.

O bloqueio inteligente rastreia as últimas três hashes de senha para evitar incrementar o contador de bloqueio para a mesma senha. Se alguém introduzir a mesma senha má várias vezes, este comportamento não fará com que a conta se bloqueie.

 > [!NOTE]
 > A funcionalidade de rastreio de hash não está disponível para clientes com autenticação pass-through ativada, uma vez que a autenticação acontece no local não na nuvem.

As implementações federadas utilizando AD FS 2016 e AF FS 2019 podem permitir benefícios semelhantes usando [AD FS Extranet Lockout e Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

O bloqueio inteligente está sempre ligado para todos os clientes DaD Azure com estas configurações padrão que oferecem a mistura certa de segurança e usabilidade. A personalização das definições de bloqueio inteligente, com valores específicos da sua organização, requer licenças Azure AD pagas para os seus utilizadores.

A utilização de um bloqueio inteligente não garante que um utilizador genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de utilizador, tentamos o nosso melhor para não bloquear o utilizador genuíno. O serviço de bloqueio tenta garantir que os maus atores não possam ter acesso a uma conta de utilizador genuína.  

* Cada centro de dados azure Ative Directory rastreia o bloqueio de forma independente. Um utilizador terá (threshold_limit * datacenter_count) número de tentativas, se o utilizador atingir cada centro de dados.
* Smart Lockout usa localização familiar vs localização desconhecida para diferenciar entre um mau ator e o utilizador genuíno. Locais desconhecidos e familiares terão ambos balcões de bloqueio separados.

O bloqueio inteligente pode ser integrado com implementações híbridas, utilizando sincronização de hash de palavra-passe ou autenticação pass-through para proteger as contas do Ative Directory no local de serem bloqueadas pelos atacantes. Ao definir políticas inteligentes de bloqueio em Azure AD adequadamente, os ataques podem ser filtrados antes de chegarem ao diretório ativo no local.

Ao utilizar a [autenticação pass-through,](../hybrid/how-to-connect-pta.md)tem de se certificar de que:

* O limiar de bloqueio da AD Azure é **inferior** ao limiar de bloqueio da conta Ative Directy. Defixa os valores de modo a que o limiar de bloqueio da conta de diretório ativo seja pelo menos duas ou três vezes mais longo do que o limiar de bloqueio da AD Azure. 
* A duração do bloqueio da AD Azure deve ser definida mais tempo do que o contador de bloqueio da conta de reset do Diretório Ativo após a duração. Tenha em atenção que a duração do AD Azure é definida em segundos, enquanto a duração da AD é definida em minutos. 

Por exemplo, se quiser que o seu contador Azure AD seja superior ao da AD, então o Azure AD será de 120 segundos (2 minutos) enquanto o seu Anúncio no local está definido para 1 minuto (60 segundos).

> [!IMPORTANT]
> Atualmente, um administrador não pode desbloquear as contas na nuvem dos utilizadores se tiverem sido bloqueados pela capacidade de Smart Lockout. O administrador deve aguardar que a duração do bloqueio expire. No entanto, o utilizador pode desbloquear utilizando o reset de palavra-passe self-service (SSPR) a partir de um dispositivo ou localização fidedigno.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificar a política de bloqueio de conta no local

Utilize as seguintes instruções para verificar a sua política de bloqueio de conta ative Diretório no local:

1. Abra a ferramenta de Gestão de Políticas do Grupo.
2. Editar a política de grupo que inclui a política de bloqueio de conta da sua organização, por exemplo, a Política de **Domínio Padrão**.
3. Navegue nas**políticas** > de **configuração** > do computador**Definições de** > **segurança Definições** > de**segurança** > Política de bloqueio de conta Política de**bloqueio**.
4. Verifique o limiar de **bloqueio da conta** e **reset** o contador de bloqueio da conta após os valores.

![Modificar a política de bloqueio de conta de diretório ativo no local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerir os valores de bloqueio inteligente da Azure AD

Com base nos seus requisitos organizacionais, os valores inteligentes de bloqueio podem ter de ser personalizados. A personalização das definições de bloqueio inteligente, com valores específicos da sua organização, requer licenças Azure AD pagas para os seus utilizadores.

Para verificar ou modificar os valores inteligentes de bloqueio para a sua organização, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione *Azure Active Directory*. Selecione**Métodos** > de Autenticação **de Segurança** > **Proteção de palavras-passe**.
1. Estabeleça o limiar de **bloqueio**, com base no número de inscrições falhadas permitidas numa conta antes do primeiro bloqueio. O padrão é 10.
1. Detete a duração do **bloqueio em segundos,** até ao comprimento em segundos de cada bloqueio. A predefinição é de 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro início de sessão após um bloqueio também falhar, a conta bloqueia-se novamente. Se uma conta bloquear repetidamente, a duração do bloqueio aumenta.

![Personalize a política de bloqueio inteligente Azure AD no portal Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se a função de bloqueio Inteligente está a funcionar ou não

Quando o limiar de bloqueio inteligente for acionado, receberá a seguinte mensagem enquanto a conta está bloqueada:

**A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente mais tarde, e se ainda tiver problemas, contacte o seu administrador.**

## <a name="next-steps"></a>Passos seguintes

* [Descubra como proibir palavras-passe más na sua organização usando o Azure AD.](howto-password-ban-bad.md)
* [Configure o reset da palavra-passe de autosserviço para permitir que os utilizadores desbloqueiem as suas próprias contas.](quickstart-sspr.md)
