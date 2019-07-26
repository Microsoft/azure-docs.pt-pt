---
title: Acesso condicional para usuários de colaboração B2B-Azure Active Directory | Microsoft Docs
description: Azure Active Directory colaboração B2B dá suporte à autenticação multifator (MFA) para acesso seletivo aos seus aplicativos corporativos
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
ms.openlocfilehash: 5a54e5006a268347148945fbe7fc5f18cfa41036
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357128"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para usuários de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifator para usuários B2B
Com a colaboração B2B do Azure AD, as organizações podem impor políticas de MFA (autenticação multifator) para usuários B2B. Essas políticas podem ser impostas no nível de locatário, aplicativo ou usuário individual, da mesma maneira que são habilitadas para funcionários e membros de tempo integral da organização. As políticas de MFA são impostas na organização de recursos.

Exemplo:
1. O administrador ou operador de informações na empresa A convida o usuário da empresa B para um aplicativo *foo* na empresa a.
2. O aplicativo *foo* na empresa A é configurado para exigir MFA no acesso.
3. Quando o usuário da empresa B tenta acessar o aplicativo *foo* no locatário da empresa a, ele é solicitado a concluir um desafio de MFA.
4. O usuário pode configurar a MFA com a empresa A e escolhe sua opção de MFA.
5. Esse cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os usuários na empresa B autenticarem usando a ID social)
6. A empresa A deve ter licenças Premium do Azure AD suficientes que dão suporte a MFA. O usuário da empresa B consome essa licença da empresa A.

O aluguel de convite é sempre responsável pela MFA para usuários da organização parceira, mesmo que a organização parceira tenha recursos de MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurando a MFA para usuários de colaboração B2B
Para descobrir como é fácil configurar a MFA para usuários de colaboração B2B, consulte como no vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência de MFA de usuários B2B para resgate de oferta
Confira a animação a seguir para ver a experiência de resgate:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Redefinição de MFA para usuários de colaboração B2B
Atualmente, o administrador pode exigir que os usuários de colaboração B2B façam a verificação novamente somente usando os seguintes cmdlets do PowerShell:

1. Ligar ao Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Obter todos os usuários com métodos de verificação

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Segue-se um exemplo:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Redefina o método MFA de um usuário específico para exigir que o usuário de colaboração B2B defina os métodos de verificação novamente. Exemplo:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que executamos a MFA na locação de recursos?

Na versão atual, a MFA está sempre na locação de recursos, por motivos de previsibilidade. Por exemplo, digamos que um usuário da Contoso (Sally) seja convidado para a Fabrikam e a Fabrikam habilitou a MFA para usuários B2B.

Se a Contoso tiver a política de MFA habilitada para App1, mas não para App2, se olharmos a declaração de MFA da Contoso no token, poderemos ver o seguinte problema:

* Dia 1: Um usuário tem MFA na contoso e está acessando o App1; em seguida, nenhum prompt adicional do MFA é mostrado na Fabrikam.

* Dia 2: O usuário acessou o aplicativo 2 na contoso, portanto, agora, ao acessar a Fabrikam, ele deve se registrar para o MFA.

Esse processo pode ser confuso e pode levar a uma queda nas conclusões de entrada.

Além disso, mesmo que a contoso tenha capacidade de MFA, nem sempre é o caso em que a Fabrikam confiaria na política de MFA da contoso.

Por fim, o MFA do locatário de recursos também funciona para MSAs e IDs sociais e para organizações de parceiros que não têm a MFA configurada.

Portanto, a recomendação para MFA para usuários B2B é sempre exigir MFA no locatário que está convidando. Esse requisito pode levar à MFA dupla em alguns casos, mas sempre que acessar o locatário que está convidando, a experiência dos usuários finais é previsível: Sally deve se registrar para MFA com o locatário que está convidando.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional baseado no dispositivo, baseado no local e em risco para usuários B2B

Quando a contoso habilita políticas de acesso condicional com base no dispositivo para seus dados corporativos, o acesso é impedido de dispositivos que não são gerenciados pela contoso e não são compatíveis com as políticas de dispositivo da contoso.

Se o dispositivo do usuário B2B não for gerenciado pela contoso, o acesso de usuários B2B das organizações parceiras será bloqueado em qualquer contexto em que essas políticas forem impostas. No entanto, a Contoso pode criar listas de exclusão que contenham usuários de parceiros específicos para excluí-las da política de acesso condicional com base no dispositivo.

#### <a name="mobile-application-management-policies-for-b2b"></a>Políticas de gerenciamento de aplicativo móvel para B2B

As políticas de proteção de aplicativo de acesso condicional não podem ser aplicadas a usuários B2B porque a organização que está convidando não tem visibilidade da organização inicial do usuário B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional baseado em local para B2B

As políticas de acesso condicional com base no local podem ser impostas para usuários B2B se a organização que convida for capaz de criar um intervalo de endereços IP confiável que define suas organizações parceiras.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional com base em risco para B2B

Atualmente, as políticas de entrada baseadas em risco não podem ser aplicadas a usuários B2B porque a avaliação de risco é realizada na organização inicial do usuário B2B.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
