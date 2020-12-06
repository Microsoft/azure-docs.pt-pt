---
title: Azure AD Multi-Factor Authentication solicita e vida útil da sessão
description: Saiba mais sobre a configuração recomendada para pedidos de reautorização com autenticação multi-factor AZure AD e como a vida útil da sessão é aplicada.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14a65b83195bff48934668ff029f925e13884bad
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743705"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Otimizar as solicitações de reautorização e compreender a vida útil da sessão para autenticação multi-factor Azure AD

O Azure Ative Directory (Azure AD) tem várias configurações que determinam a frequência com que os utilizadores precisam de reautorização. Esta reauthentication pode ser com um primeiro fator, como palavra-passe, FIDO ou Autenticador Microsoft sem palavras-passe, ou para realizar a autenticação multi-factor (MFA). Pode configurar estas definições de reautorca necessárias para o seu próprio ambiente e a experiência do utilizador que deseja.

A configuração padrão AD AZure para a frequência de inscrição do utilizador é uma janela rolante de 90 dias. Pedir credenciais aos utilizadores muitas vezes parece uma coisa sensata a fazer, mas pode dar errado. Se os utilizadores forem treinados para introduzir as suas credenciais sem pensar, podem fornecer-lhes involuntariamente uma solicitação de credencial maliciosa.

Pode parecer alarmante não pedir a um utilizador que volte a entrar, embora qualquer violação das políticas de TI revogue a sessão. Alguns exemplos incluem uma mudança de palavra-passe, um dispositivo incompatível ou uma operação de desativação de conta. Também pode revogar explicitamente [as sessões dos utilizadores utilizando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Este artigo detalha configurações recomendadas e como diferentes configurações funcionam e interagem entre si.

## <a name="recommended-settings"></a>Configurações recomendadas

Para dar aos seus utilizadores o equilíbrio certo de segurança e facilidade de utilização, pedindo-lhes que entrem na frequência certa, recomendamos as seguintes configurações:

* Se tiver Azure AD Premium:
    * Ativar um único sinal de sso através de aplicações utilizando [dispositivos geridos](../devices/overview.md) ou [SSO sem costura](../hybrid/how-to-connect-sso.md).
    * Se for necessária a reauthentication, utilize uma [política de frequência de acesso](../conditional-access/howto-conditional-access-session-lifetime.md)condicional .
    * Para os utilizadores que iniciarem sessão a partir de dispositivos não geridos ou cenários de dispositivos móveis, utilizem o Acesso Condicional para permitir sessões persistentes de navegador e políticas de frequência de acesso.
* Se tiver licenças de aplicações microsoft 365 ou o nível AD Azure gratuito:
    * Ativar um único sinal de sso através de aplicações utilizando [dispositivos geridos](../devices/overview.md) ou [SSO sem costura](../hybrid/how-to-connect-sso.md).
    * Mantenha a opção *de insusitada Do Remain* e guie os seus utilizadores a aceitá-la.
* Para cenários de dispositivos móveis, certifique-se de que os seus utilizadores utilizam a aplicação Microsoft Authenticator. Esta aplicação é utilizada como corretor de outras aplicações federadas Azure AD, e reduz as solicitações de autenticação no dispositivo.

Nossa pesquisa mostra que estes cenários são adequados para a maioria dos inquilinos. Algumas combinações destas configurações, como *Remember MFA* e *Remain singed-in,* podem resultar em solicitações para que os seus utilizadores autentem demasiadas vezes. As solicitações regulares de reautorca são más para a produtividade dos utilizadores e podem torná-las mais vulneráveis a ataques.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Definições de configuração de vida de sessão Ad Azure

Para otimizar a frequência de pedidos de autenticação para os seus utilizadores, pode configurar opções de vida útil da sessão Azure. Compreenda as necessidades do seu negócio e utilizadores e configufique as configurações que proporcionam o melhor equilíbrio para o seu ambiente.

### <a name="evaluate-session-lifetime-policies"></a>Avaliar as políticas de vida útil da sessão

Sem configurações de vida útil da sessão, não existem cookies persistentes na sessão do navegador. Sempre que um utilizador fecha e abre o navegador, obtém-se um pedido de reautorsação. Nos clientes do Office, o período de tempo padrão é uma janela rolante de 90 dias. Com esta configuração padrão do Office, se o utilizador tiver reiniciado a sua palavra-passe ou se tiver havido inatividade de mais de 90 dias, o utilizador é obrigado a reautorizar com todos os fatores necessários (primeiro e segundo fator).

Um utilizador pode ver várias solicitações de MFA num dispositivo que não tem identidade no AZure AD. Várias solicitações resultam quando cada aplicação tem o seu próprio OAuth Refresh Token que não é partilhado com outras aplicações do cliente. Neste cenário, o MFA solicita várias vezes que cada aplicação solicite um Token OAuth Refresh para ser validado com MFA.

Em Azure AD, a política mais restritiva para a vida útil da sessão determina quando o utilizador precisa de reautorização. Considere o seguinte cenário:

* Você ativa *o Remain signed-in,* que usa um cookie de navegador persistente, e
* Também *ativar O MFA de ressoem 14 dias*

Neste cenário de exemplo, o utilizador precisa de reautorar a cada 14 dias. Este comportamento segue a política mais restritiva, mesmo que o *Keep me assinado* por si só não exija o utilizador para reautorciação no navegador.

### <a name="managed-devices"></a>Dispositivos geridos

Os dispositivos ligados ao Azure AD utilizando a Azure AD Join ou a Hybrid Azure AD Join recebem um [Tokens de Atualização Primária (PRT)](../devices/concept-primary-refresh-token.md) para utilizar um único sinal de sinalização (SSO) através de aplicações. Este PRT permite que um utilizador assine uma vez no dispositivo e permite que o pessoal de TI certifique-se de que as normas de segurança e conformidade são cumpridas. Se um utilizador precisar de ser convidado a iniciar seduções com mais frequência num dispositivo associado para algumas aplicações ou cenários, isso pode ser alcançado usando [a frequência de acesso condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)

### <a name="show-option-to-remain-signed-in"></a>Mostrar opção para permanecer inscrito

Quando um utilizador seleciona **Sim** na opção *De permanência assinada?* Este cookie persistente lembra-se do primeiro e segundo fator, e aplica-se apenas a pedidos de autenticação no navegador.

![Screenshot de exemplo pronta para permanecer assinado em](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Se tiver uma licença Azure AD Premium 1, recomendamos a utilização da política de acesso condicional para *a sessão de navegador persistente.* Esta política substitui a *definição de Stay e* proporciona uma melhor experiência do utilizador. Se não tiver uma licença Azure AD Premium 1, recomendamos que a estadia seja assinada na definição para os seus utilizadores.

Para obter mais informações sobre a configuração da opção de permitir que os utilizadores permaneçam inscritos, consulte [Personalizar a sua página de sing-in Azure AD](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Lembre-se da autenticação multi-factor  

Esta definição permite configurar valores entre 1-365 dias e define um cookie persistente no navegador quando um utilizador seleciona a opção **X days no** início da sposição.

![Screenshot de exemplo pronta para aprovar um pedido de inscrição](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Embora esta definição reduza o número de autenticações em aplicações web, aumenta o número de autenticações para clientes de autenticação moderna, como clientes do Office. Estes clientes normalmente só solicitam após a redefinição da palavra-passe ou inatividade de 90 dias. No entanto, a fixação deste valor para menos de 90 dias encurta as indicações padrão do MFA para os clientes do Office e aumenta a frequência de reauthentication. Quando utilizado em combinação com as políticas de Acesso **Assinado ou** Condicional, pode aumentar o número de pedidos de autenticação.

Se utilizar *O MFA* e tiver licenças Azure AD Premium 1, considere migrar estas definições para a Frequência de Acesso Condicional. Caso contrário, considere usar *manter-me inscrito?*

Mais informações, consulte [Remember Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Gestão de sessão de autenticação com Acesso Condicional

**A frequência de login** permite ao administrador escolher a frequência de login que se aplica tanto para o primeiro como para o segundo fator tanto no cliente como no navegador. Recomendamos a utilização destas definições, juntamente com a utilização de dispositivos geridos, em cenários em que tenha necessidade de restringir a sessão de autenticação, como por exemplo para aplicações empresariais críticas.

**A sessão de navegador persistente** permite que os utilizadores permaneçam assinados após o fecho e reabertura da janela do navegador. Semelhante à definição *de inscrito do Remain,* define um cookie persistente no navegador. No entanto, uma vez que é configurado pela administração, não requer que o utilizador selecione **Sim** na opção *de inscrição de Permanência?* Se utilizar a opção *Remain?* **Persistent browser session**

Para obter mais informações. ver [Configurar gestão de sessão de autenticação com Acesso Condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Vidas simbólicas configuráveis

Esta definição permite a configuração de vida útil para token emitido pelo Azure Ative Directory. Esta política é substituída pela *gestão da sessão de autenticação com acesso condicional.* Se você estiver usando *vidas de símbolos configurais* hoje, recomendamos iniciar a migração para as políticas de Acesso Condicional.

## <a name="review-your-tenant-configuration"></a>Reveja a configuração do seu inquilino  

Agora que compreende como funcionam diferentes configurações e a configuração recomendada, é hora de verificar a configuração dos seus inquilinos e fazer alterações em conformidade:

Para configurar ou rever a opção *Remain,* complete os seguintes passos:

1. No portal AD AZure, procure e selecione *O Diretório Ativo Azure*.
1. Selecione **Branding da Empresa,** em seguida, para cada local, escolha **a opção Show para permanecer assinado em**.
1. Escolha *Sim* e, em seguida, selecione **Guardar**.

Para lembrar as definições de autenticação de vários fatores, complete os seguintes passos:

1. No portal AD AZure, procure e selecione *O Diretório Ativo Azure*.
1. Selecione **Segurança,** em **seguida, MFA**.
1. Em **Configuração**, selecione **definições adicionais de MFA baseadas na nuvem**.
1. Na página *de definições de autenticação multi-factor, desloque-se* para **lembrar as definições de autenticação de vários fatores**. Desative a definição desmarcando a caixa de verificação.

Para configurar as políticas de acesso condicional para a frequência de inscrição e sessão de navegador persistente, complete os seguintes passos:

1. No portal AD AZure, procure e selecione *O Diretório Ativo Azure*.
1. Selecione **Segurança,** em **seguida, Acesso Condicional**.
1. Configure uma política utilizando as opções de gestão de sessão recomendadas detalhadas neste artigo.

Para rever as vidas dos token, [use a Azure AD PowerShell para consultar quaisquer políticas de AD Azure](../develop/configure-token-lifetimes.md#prerequisites). Desative todas as políticas que tiver em vigor.

Se estiver ativado mais de uma definição no seu inquilino, recomendamos que atualizámos as suas definições com base no licenciamento disponível para si. Por exemplo, se tiver licenças premium Azure AD, deve utilizar apenas a política de acesso condicional da *sessão de frequência de entrada* e navegador *persistente.* Se tiver aplicações microsoft 365 ou licenças gratuitas Azure AD, deve utilizar a configuração *remain-in?*

Se tiver ativado vidas de token configuráveis, esta capacidade será removida em breve. Planeie uma migração para uma política de acesso condicional.

O quadro que se segue resume as recomendações baseadas em licenças:

|              | Azure AD Free e Microsoft 365 apps | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [A azure AD junta-se](../devices/concept-azure-ad-join.md) ou [Ad Hybrid Ad ,](../devices/concept-azure-ad-join-hybrid.md)ou [SSO sem emenda](../hybrid/how-to-connect-sso.md) para dispositivos não geridos. | Associação ao Azure AD<br />Associação ao Azure AD Híbrido |
| **Definições de reauthentication** | Mantenha-se inscrito                  | Utilize políticas de acesso condicional para a frequência de inscrição e sessão de navegador persistente |

## <a name="next-steps"></a>Passos seguintes

Para começar, preencha o tutorial para [Garantir eventos de entrada de utilizador com autenticação multi-factor AD Azure](tutorial-enable-azure-mfa.md) ou [utilize deteções de risco para iniciar insusentuques para ativar a autenticação multi-factor Azure AD](tutorial-risk-based-sspr-mfa.md).
