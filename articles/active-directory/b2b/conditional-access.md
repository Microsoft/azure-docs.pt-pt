---
title: Acesso condicional para utilizadores de colaboração do B2B - Azure Active Directory | Documentos da Microsoft
description: A colaboração B2B do Active Directory do Azure suporta a autenticação multifator (MFA) para acesso seletivo às suas aplicações empresariais
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: f51b42780a4a3500a5f5e8385d0973a335c2b78e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670184"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para utilizadores de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifator para utilizadores B2B
Com a colaboração B2B do Azure AD, as organizações podem impor políticas de autenticação multifator (MFA) para utilizadores B2B. Estas políticas podem ser impostas no inquilino, a aplicação ou o nível de usuário individual, da mesma forma que eles estão ativados para funcionários a tempo inteiro e membros da organização. As políticas MFA são impostas na organização de recursos.

Exemplo:
1. Trabalhador de informações ou administrador de empresa A convida utilizadores da empresa B para um aplicativo *Foo* na empresa A.
2. Aplicativo *Foo* da empresa A está configurada para exigir a MFA no acesso.
3. Quando o utilizador da empresa B tenta aceder à aplicação *Foo* na empresa um inquilino, é-lhes pedido para concluir um desafio MFA.
4. O utilizador pode definir a respetiva MFA com a empresa e escolhe terão a opção de MFA.
5. Esse cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os utilizadores na empresa B autenticar com o ID de redes sociais)
6. Empresa A tem de ter licenças suficientes do Premium do Azure AD que suportam a MFA. O utilizador da empresa B consome esta licença da empresa A.

O inquilino de convite é responsável por MFA para os utilizadores da organização parceira, sempre, mesmo que a organização parceira tenha capacidades MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurar a MFA para utilizadores de colaboração B2B
Para descobrir como é fácil configurar a MFA para utilizadores de colaboração B2B, consulte como no vídeo seguinte:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Utilizadores B2B MFA experiência para oferecem resgate
Confira a animação seguinte para ver o resgate experiência:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA repor para utilizadores de colaboração B2B
Atualmente, o administrador pode exigir utilizadores de colaboração do B2B para prova de cópia de segurança novamente apenas ao utilizar os seguintes cmdlets do PowerShell:

1. Ligar ao Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obter todos os utilizadores com prova de métodos

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Segue-se um exemplo:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Repor o método MFA de um utilizador específico exigir que o utilizador de colaboração do B2B definir métodos de verificação de segurança novamente. Exemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que podemos executar MFA em inquilinos do recurso?

Na versão atual, o MFA está sempre em inquilinos de recursos, por motivos de previsibilidade. Por exemplo, vamos supor que um utilizador Contoso (Sally) é convidado a Fabrikam e Fabrikam tiver ativado a MFA para utilizadores B2B.

Se a Contoso tiver a política MFA ativada para App1, mas não App2, em seguida, se observarmos a afirmação de MFA de Contoso no token, poderá vemos o seguinte problema:

* Dia 1: Um utilizador tiver a MFA na Contoso e está a aceder ao App1, em seguida, sem MFA adicional prompt é mostrado na Fabrikam.

* Dia 2: O usuário acessa aplicação 2 na Contoso, portanto, agora ao aceder a Fabrikam, tem de se registar para MFA lá.

Este processo pode ser confuso e poderia levar a remover o início de sessão conclusões.

Além disso, mesmo que a Contoso tem capacidade MFA, nem sempre é que o caso, a Fabrikam seria confiar a política da MFA de Contoso.

Por fim, o inquilino de recursos MFA também funciona para MSAs e identidades sociais e para organizações de parceiros que não têm MFA que configurar.

Portanto, a recomendação para a MFA para utilizadores B2B é sempre exigir a MFA no inquilino do convite. Este requisito pode levar a MFA duplo em alguns casos, mas sempre que aceder ao inquilino de convite, a experiência de usuários finais é previsível: Sally tem de registar para MFA ao inquilino de convite.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional com base no dispositivo, com base na localização e com base no risco para utilizadores B2B

Quando Contoso permite que as políticas de acesso condicional com base no dispositivo para os seus dados empresariais, o acesso é impedido de dispositivos que não são geridos pelo Contoso e não em conformidade com as políticas de dispositivos da Contoso.

Se o dispositivo do utilizador B2B não é gerido pelo Contoso, acesso de utilizadores B2B das organizações de parceiro é bloqueado no qualquer contexto que estas políticas são impostas. No entanto, a Contoso pode criar listas de exclusão que contém utilizadores de parceiros específicos para os excluir da política de acesso condicional com base no dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Com base na localização de acesso condicional para B2B

Políticas de acesso condicional com base na localização podem ser impostas para utilizadores B2B, se a organização que o convidou é capaz de criar um intervalo de endereços IP fidedigno que define suas organizações de parceiro.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional com base no risco para B2B

Atualmente, não não possível aplicar políticas baseadas no risco de início de sessão para utilizadores B2B, porque a avaliação de risco é efetuada na organização da página principal do utilizador B2B.

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
