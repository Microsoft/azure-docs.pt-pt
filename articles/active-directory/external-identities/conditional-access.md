---
title: Acesso Condicional para utilizadores de colaboração B2B - Azure AD
description: Saiba como impor políticas de autenticação de vários fatores para utilizadores do Azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: eccbbb22814788aaf06fa6fd10d8c376203c1d49
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892456"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso Condicional para utilizadores de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multi-factor para utilizadores de B2B
Com a colaboração Azure AD B2B, as organizações podem impor políticas de autenticação de vários fatores (MFA) para utilizadores de B2B. Estas políticas podem ser aplicadas ao nível do arrendatário, app ou utilizador individual, da mesma forma que são ativadas para funcionários a tempo inteiro e membros da organização. As políticas de MFA são aplicadas na organização de recursos.

Exemplo:
1. A Administração ou trabalhador da informação na Empresa A convida o utilizador da empresa B a uma aplicação *Foo* na empresa A.
2. A Aplicação *Foo* na empresa A está configurada para exigir MFA no acesso.
3. Quando o utilizador da empresa B tenta aceder à app *Foo* na empresa A inquilina, é-lhes pedido que completem um desafio de MFA.
4. O utilizador pode configurar o seu MFA com a empresa A, e escolhe a sua opção MFA.
5. Este cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os utilizadores da Empresa B autenticar usando iD social)
6. A Empresa A deve ter licenças AD Premium Azure suficientes que suportem MFA. O utilizador da empresa B consome esta licença da empresa A.

O arrendamento convidativo é sempre responsável pelo MFA para os utilizadores da organização parceira, mesmo que a organização parceira tenha capacidades de MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Criação de MFA para utilizadores de colaboração B2B
Para descobrir como é fácil configurar MFA para utilizadores de colaboração B2B, veja como no seguinte vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência MFA dos utilizadores B2B para resgate de ofertas
Confira a seguinte animação para ver a experiência de redenção:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Reposição de MFA para utilizadores de colaboração B2B
Atualmente, o administrador pode exigir que os utilizadores de colaboração B2B voltem a ser revistos apenas utilizando os seguintes cmdlets PowerShell:

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

3. Repor o método MFA para que um utilizador específico exija que o utilizador de colaboração B2B volte a definir métodos de impermeabilização. Exemplo:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que realizamos MFA no arrendamento de recursos?

Na versão atual, o MFA está sempre no arrendamento de recursos, por razões de previsibilidade. Por exemplo, digamos que um utilizador do Contoso (Sally) é convidado para a Fabrikam e a Fabrikam permitiu que o MFA para utilizadores de B2B.

Se o Contoso tiver uma política de MFA ativada para a App1 mas não para a App2, então se olharmos para a reivindicação do Contoso MFA no token, poderemos ver o seguinte problema:

* Dia 1: Um utilizador tem MFA em Contoso e está a aceder à App1, então não é mostrado nenhum pedido adicional de MFA em Fabrikam.

* Dia 2: O utilizador acedeu à App 2 em Contoso, pelo que agora, ao aceder a Fabrikam, tem de se registar para mFA lá.

Este processo pode ser confuso e pode levar à queda nas conclusões de inscrição.

Além disso, mesmo que Contoso tenha capacidade de MFA, nem sempre é o caso de o Fabrikam confiar na política do MFA de Contoso.

Finalmente, o inquilino de recursos MFA também trabalha para MSAs e IDs sociais e para orgs parceiros que não têm MFA configurado.

Por conseguinte, a recomendação para os utilizadores de B2B é exigir sempre MFA no inquilino convidado. Esta exigência pode levar a um duplo MFA em alguns casos, mas sempre que acede ao inquilino convidado, a experiência dos utilizadores finais é previsível: a Sally deve inscrever-se na MFA com o inquilino convidado.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso Condicional baseado em dispositivos, baseado em localização e risco para utilizadores B2B

Quando a Contoso permite políticas de acesso condicional baseadas em dispositivos para os seus dados corporativos, o acesso é impedido de dispositivos que não são geridos pela Contoso e não estão em conformidade com as políticas do dispositivo Contoso.

Se o dispositivo do utilizador B2B não for gerido pela Contoso, o acesso dos utilizadores B2B das organizações parceiras é bloqueado em qualquer contexto que estas políticas sejam aplicadas. No entanto, a Contoso pode criar listas de exclusão que contenham utilizadores de parceiros específicos para os excluir da política de Acesso Condicional baseada no dispositivo.

#### <a name="mobile-application-management-policies-for-b2b"></a>Políticas de gestão de aplicações móveis para B2B

As políticas de proteção de aplicações de acesso condicional não podem ser aplicadas aos utilizadores B2B porque a organização convidativa não tem visibilidade na organização do utilizador B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional baseado na localização para B2B

As políticas de acesso condicional baseadas em localização podem ser aplicadas aos utilizadores B2B se a organização convidada for capaz de criar uma gama de endereços IP fidedigna que define as suas organizações parceiras.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional baseado no risco para B2B

Atualmente, as políticas de inscrição baseadas no risco não podem ser aplicadas aos utilizadores de B2B porque a avaliação do risco é realizada na organização do utilizador B2B.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Preços das Identidades Externas](external-identities-pricing.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
