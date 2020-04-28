---
title: Acesso Condicional para utilizadores de colaboração B2B - Azure AD
description: A colaboração azure Ative Directory B2B apoia a autenticação de vários fatores (MFA) para acesso seletivo às suas aplicações corporativas
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
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74273005"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso Condicional para utilizadores de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifactor para utilizadores B2B
Com a colaboração azure AD B2B, as organizações podem impor políticas de autenticação multifactor (MFA) para utilizadores B2B. Estas políticas podem ser aplicadas ao nível do arrendatário, app ou utilizador individual, da mesma forma que são habilitadas para funcionários a tempo inteiro e membros da organização. As políticas de MFA são aplicadas na organização de recursos.

Exemplo:
1. Administrador ou trabalhador da informação na Empresa A convida utilizador da empresa B para uma aplicação *Foo* na empresa A.
2. Aplicação *Foo* na empresa A está configurada para exigir MFA no acesso.
3. Quando o utilizador da empresa B tenta aceder à aplicação *Foo* na empresa Um inquilino, é-lhes pedido que completem um desafio MFA.
4. O utilizador pode configurar o seu MFA com a empresa A e escolher a sua opção MFA.
5. Este cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os utilizadores da Empresa B autenticarem usando ID social)
6. A Empresa A deve ter licenças Premium Azure AD suficientes que suportem o MFA. O utilizador da empresa B consome esta licença da empresa A.

O arrendamento convidativo é sempre responsável pelo MFA para utilizadores da organização parceira, mesmo que a organização parceira tenha capacidades de MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Criação de MFA para utilizadores de colaboração B2B
Para descobrir como é fácil configurar mFA para utilizadores de colaboração B2B, veja como no seguinte vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência mfa dos utilizadores B2B para oferecer resgate
Confira a seguinte animação para ver a experiência de redenção:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Reset de MFA para utilizadores de colaboração B2B
Atualmente, o administrador pode exigir que os utilizadores de colaboração B2B recomporem novamente usando os seguintes cmdlets PowerShell:

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

3. Redefinir o método MFA para um utilizador específico para exigir ao utilizador de colaboração B2B que volte a configurar métodos de comprovação. Exemplo:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que executamos MFA no arrendamento de recursos?

Na versão atual, o MFA está sempre no arrendamento de recursos, por razões de previsibilidade. Por exemplo, digamos que um utilizador de Contoso (Sally) é convidado para Fabrikam e Fabrikam permitiu mFA para utilizadores B2B.

Se Contoso tem a política de MFA ativada para app1 mas não App2, então se olharmos para a reivindicação do MFA Contoso no símbolo, poderemos ver a seguinte questão:

* Dia 1: Um utilizador tem MFA em Contoso e está a aceder à App1, então não é mostrado nenhum pedido adicional de MFA em Fabrikam.

* Dia 2: O utilizador acedeu à App 2 em Contoso, pelo que agora, ao aceder à Fabrikam, deve inscrever-se no MFA.

Este processo pode ser confuso e pode levar à queda das conclusões de inscrição.

Além disso, mesmo que Contoso tenha capacidade de MFA, nem sempre é o caso de fabrikam confiar na política do MFA de Contoso.

Finalmente, o inquilino de recursos MFA também trabalha para MSAs e IDs sociais e para o parceiro orgs que não tem MFA criado.

Por isso, a recomendação para os utilizadores de MFA para utilizadores b2B é sempre exigir MFA no inquilino convidativo. Esta exigência pode levar a um duplo MFA em alguns casos, mas sempre que acede ao inquilino convidativo, a experiência dos utilizadores finais é previsível: A Sally deve inscrever-se no MFA com o inquilino convidado.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional baseado em dispositivos, baseado na localização e baseado em riscos para utilizadores B2B

Quando a Contoso permite políticas de Acesso Condicional baseadas em dispositivos para os seus dados corporativos, o acesso é impedido de dispositivos que não são geridos pela Contoso e não estão em conformidade com as políticas do dispositivo Contoso.

Se o dispositivo do utilizador B2B não for gerido pela Contoso, o acesso dos utilizadores B2B das organizações parceiras está bloqueado em qualquer contexto que estas políticas sejam aplicadas. No entanto, a Contoso pode criar listas de exclusão contendo utilizadores parceiros específicos para os excluir da política de acesso condicional baseada no dispositivo.

#### <a name="mobile-application-management-policies-for-b2b"></a>Políticas móveis de gestão de aplicações para B2B

As políticas de proteção de aplicações de acesso condicional não podem ser aplicadas aos utilizadores B2B porque a organização convidativa não tem visibilidade na organização doméstica do utilizador B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso Condicional baseado na localização para B2B

As políticas de acesso condicional baseadas na localização podem ser aplicadas aos utilizadores B2B se a organização convidativa for capaz de criar uma gama de endereços IP confiáveis que defina as suas organizações parceiras.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso Condicional baseado no risco para B2B

Atualmente, as políticas de inscrição baseadas no risco não podem ser aplicadas aos utilizadores b2B porque a avaliação de risco é realizada na organização doméstica do utilizador B2B.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
