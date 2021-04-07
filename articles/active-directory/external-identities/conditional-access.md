---
title: Acesso Condicional para utilizadores de colaboração B2B - Azure AD
description: Saiba como impor políticas de autenticação de vários fatores para utilizadores do Azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646287"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso Condicional para utilizadores de colaboração B2B

Este artigo descreve como as organizações podem âmbito das políticas de Acesso Condicional (CA) para os utilizadores convidados B2B acederem aos seus recursos.
>[!NOTE]
>Este fluxo de autenticação ou autorização é um pouco diferente para os utilizadores convidados do que para os utilizadores existentes desse fornecedor de Identidade (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Fluxo de autenticação para utilizadores convidados B2B a partir de um diretório externo

O seguinte diagrama ilustra o fluxo: ![ a imagem mostra o fluxo de autenticação para utilizadores convidados B2B a partir de um diretório externo](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Passo | Description |
|--------------|-----------------------|
| 1. | O utilizador convidado B2B solicita o acesso a um recurso. O recurso redireciona o utilizador para o seu inquilino de recursos, um IdP de confiança.|
| 2. | O inquilino identifica o utilizador como externo e redireciona o utilizador para o IdP do utilizador convidado B2B. O utilizador realiza a autenticação primária no IdP.
| 3. | O IdP do utilizador convidado B2B emite um símbolo ao utilizador. O utilizador é redirecionado de volta para o inquilino de recurso com o token. O inquilino de recursos valida o token e avalia o utilizador contra as suas políticas de CA. Por exemplo, o inquilino de recursos pode exigir que o utilizador realize a Autenticação Multi-Factor Azure Ative(AD).
| 4. | Uma vez que todas as políticas de inquilino de recursos CA estão satisfeitas, o inquilino de recursos emite o seu próprio token e redireciona o utilizador para o seu recurso.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Fluxo de autenticação para utilizadores convidados B2B com senha única

O diagrama a seguir ilustra o fluxo: ![ a imagem mostra o fluxo de autenticação para utilizadores convidados B2B com uma senha de tempo](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Passo | Description |
|--------------|-----------------------|
| 1. |O utilizador solicita o acesso a um recurso noutro inquilino. O recurso redireciona o utilizador para o seu inquilino de recursos, um IdP de confiança.|
| 2. | O inquilino identifica o utilizador como um [utilizador de senha única (OTP) e](./one-time-passcode.md) envia um e-mail com a OTP ao utilizador.|
| 3. | O utilizador recupera o OTP e envia o código. O inquilino de recursos avalia o utilizador contra as suas políticas de CA.
| 4. | Uma vez que todas as políticas de CA estão satisfeitas, o inquilino de recursos emite um símbolo e redireciona o utilizador para o seu recurso. |

>[!NOTE]
>Se o utilizador for de um inquilino de recursos externos, não é possível avaliar também as políticas IdP CA do utilizador B2B. A partir de hoje, apenas as políticas de CA do inquilino de recursos se aplicam aos seus hóspedes.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Autenticação multi-factor Azure AD para utilizadores B2B

As organizações podem impor várias políticas de autenticação multi-factor Azure AD para os seus utilizadores convidados B2B. Estas políticas podem ser aplicadas ao nível do inquilino, app ou utilizador individual da mesma forma que estão habilitadas para funcionários a tempo inteiro e membros da organização.
O inquilino de recursos é sempre responsável pela Autenticação Multi-Factor AZure AD para os utilizadores, mesmo que a organização do utilizador convidado tenha capacidades de autenticação multi-factor. Aqui está um exemplo...

1. Um administrador ou trabalhador de informação de uma empresa chamada Fabrikam convida o utilizador de outra empresa chamada Contoso a usar a sua aplicação Woodgrove.

2. A aplicação Woodgrove em Fabrikam está configurada para exigir autenticação multi-factor Azure AD no acesso.

3. Quando o utilizador convidado B2B da Contoso tenta aceder à Woodgrove no inquilino de Fabrikam, é-lhes pedido que completem o desafio de autenticação multi-factor Azure AD.

4. O utilizador convidado pode então configurar a sua Autenticação Multi-Factor Azure AD com a Fabrikam e selecionar as opções.

5. Este cenário funciona para qualquer identidade – Azure AD ou Personal Microsoft Account (MSA). Por exemplo, se o utilizador em Contoso autenticar a utilização de ID social.

6. A Fabrikam deve ter licenças AD AZure premium suficientes que suportem a autenticação multi-factor Azure AD. O utilizador de Contoso consome então esta licença da Fabrikam. Consulte [o modelo de faturação das identidades externas da Azure AD](./external-identities-pricing.md) para obter informações sobre o licenciamento B2B.

>[!NOTE]
>A autenticação multi-factor Azure AD é feita no arrendamento de recursos para garantir a previsibilidade.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Configurar a autenticação multi-factor Azure AD para utilizadores B2B

Para configurar a autenticação multi-factor Azure AD para os utilizadores de colaboração B2B, veja este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Utilizadores B2B Azure AD Multi-Factor Autenticação para resgate de oferta

Para saber mais sobre a experiência de redenção de autenticação multi-factor Azure AD, veja este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Reset de autenticação multi-factor Azure AD para utilizadores de B2B

Agora, os seguintes cmdlets PowerShell estão disponíveis para comprovar os utilizadores convidados B2B:

1. Ligar ao Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Obtenha todos os utilizadores com métodos de prova

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Segue-se um exemplo:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Reinicie o método de autenticação multi-factor Azure AD para que um utilizador específico exija que o utilizador de colaboração B2B volte a configurar métodos de impermeabilização. 
   Segue-se um exemplo:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Acesso Condicional para utilizadores B2B

Existem vários fatores que influenciam as políticas de CA para os utilizadores convidados B2B.

### <a name="device-based-conditional-access"></a>Acesso Condicional com base em dispositivos

Na AC, existe uma opção para exigir que o dispositivo de um utilizador [seja conforme ou a AD Híbrido Azure aderido](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Os utilizadores de hóspedes B2B só podem satisfazer a conformidade se o arrendatário de recursos puder gerir o seu dispositivo. Os dispositivos não podem ser geridos por mais de uma organização de cada vez. Os utilizadores de hóspedes B2B não conseguem satisfazer a ad AD híbrida Azure porque não têm uma conta AD no local. Só se o dispositivo do utilizador convidado não for gerido, podem registar ou inscrever o seu dispositivo no arrendatário de recursos e, em seguida, tornar o dispositivo conforme. O utilizador pode então satisfazer o controlo da concessão.

>[!Note]
>Não é aconselhável exigir um dispositivo gerido para utilizadores externos.

### <a name="mobile-application-management-policies"></a>Políticas de gestão de aplicações móveis

Os controlos de concessão da AC, tais como **requerer aplicações de clientes aprovadas** e exigir políticas de proteção de **aplicações** precisam que o dispositivo seja registado no arrendatário. Estes controlos só podem ser aplicados em [dispositivos iOS e Android.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) No entanto, nenhum destes controlos pode ser aplicado aos utilizadores de hóspedes B2B se o dispositivo do utilizador já estiver a ser gerido por outra organização. Um dispositivo móvel não pode ser registado em mais de um inquilino de cada vez. Se o dispositivo móvel for gerido por outra organização, o utilizador será bloqueado. Só se o dispositivo do utilizador convidado não for gerido, podem registar o seu dispositivo no arrendatário de recursos. O utilizador pode então satisfazer o controlo da concessão.  

>[!NOTE]
>Não é aconselhável exigir uma política de proteção de aplicações para utilizadores externos.

### <a name="location-based-conditional-access"></a>Acesso Condicional baseado em localização

A [política baseada na localização](../conditional-access/concept-conditional-access-conditions.md#locations) baseada em intervalos IP pode ser aplicada se a organização convidativa puder criar um intervalo de endereços IP confiável que define as suas organizações parceiras.

As políticas também podem ser aplicadas com base em **localizações geográficas.**

### <a name="risk-based-conditional-access"></a>Acesso Condicional baseado em riscos

A [política de risco de inscrição](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) é aplicada se o utilizador convidado B2B satisfizer o controlo da concessão. Por exemplo, uma organização pode exigir autenticação multi-factor Azure AD para risco médio ou alto de sinalização. No entanto, se um utilizador não tiver registado previamente a autenticação multi-factor Azure AD no arrendatário de recursos, o utilizador será bloqueado. Isto é feito para evitar que utilizadores maliciosos registem as suas próprias credenciais de autenticação multi-factor Azure AD no caso de comprometerem a palavra-passe de um utilizador legítimo.

No [entanto, a política de risco de utilizador](../conditional-access/concept-conditional-access-conditions.md#user-risk) não pode ser resolvida no arrendatário de recursos. Por exemplo, se necessitar de uma alteração de palavra-passe para utilizadores de hóspedes de alto risco, eles serão bloqueados devido à incapacidade de redefinir palavras-passe no diretório de recursos.

### <a name="conditional-access-client-apps-condition"></a>Condição de aplicações de cliente de acesso condicional

[As condições das aplicações](../conditional-access/concept-conditional-access-conditions.md#client-apps) do cliente comportam-se da mesma forma para os utilizadores convidados B2B, como para qualquer outro tipo de utilizador. Por exemplo, pode impedir que os utilizadores convidados utilizem protocolos de autenticação antigas.

### <a name="conditional-access-session-controls"></a>Controlos de sessão de acesso condicional

[Os controlos de](../conditional-access/concept-conditional-access-session.md) sessão comportam-se da mesma forma para os utilizadores convidados B2B, como para qualquer outro tipo de utilizador.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](./what-is-b2b.md)
- [Utilizadores de proteção de identidade e B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Preços das Identidades Externas](https://azure.microsoft.com/pricing/details/active-directory/)
- [Perguntas frequentes (Perguntas Frequentes)](./faq.md)