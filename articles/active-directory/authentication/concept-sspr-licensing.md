---
title: Redefinição de senha de autoatendimento de licença-Azure Active Directory
description: Requisitos de licenciamento de redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848566"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisitos de licenciamento para redefinição de senha de autoatendimento do Azure AD

O Azure Active Directory (Azure AD) vem em várias edições: gratuita, Premium P1 e Premium P2. Há vários recursos diferentes que compõem a redefinição de senha de autoatendimento, incluindo alteração, redefinição, desbloqueio e Write-back, que estão disponíveis nas diferentes edições do Azure AD. Este artigo tenta explicar as diferenças. Mais detalhes dos recursos incluídos em cada edição do Azure AD podem ser encontrados na [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Comparar edições e recursos

A redefinição de senha de autoatendimento do Azure AD é licenciada por usuário, para manter as organizações de conformidade necessárias para atribuir a licença apropriada aos seus usuários.

* Alteração Personalizada de Palavra-passe para utilizadores em cloud
   * Sou um **usuário somente em nuvem** e sei minha senha.
      * Eu gostaria de **alterar** minha senha para algo novo.
   * Essa funcionalidade está incluída em todas as edições do Azure AD.

* Reposição Personalizada de Palavra-passe para utilizadores em cloud
   * Sou um **usuário somente em nuvem** e esqueci minha senha.
      * Eu gostaria de **Redefinir** minha senha para algo que eu sei.
   * Essa funcionalidade está incluída em Azure AD Premium P1 ou P2, Microsoft 365 Business ou Office 365.

* Redefinição/alteração/desbloqueio de senha de autoatendimento **com write-back local**
   * Sou um **usuário híbrido** meu local Active Directory conta de usuário está sincronizada com minha conta do Azure AD usando Azure ad Connect. Gostaria de alterar minha senha, esqueci minha senha ou foi bloqueada.
      * Gostaria de alterar minha senha ou redefini-la para algo que eu sei, ou desbloquear minha conta, **e** fazer com que essa alteração seja sincronizada para o local Active Directory.
   * Essa funcionalidade está incluída no Azure AD Premium P1 ou P2 ou Microsoft 365 Business.

> [!WARNING]
> Os planos de licenciamento do Office 365 autônomos *não dão suporte a "redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local"* e exigem um plano que inclua Azure ad Premium P1, Premium P2 ou Microsoft 365 Business para que essa funcionalidade funcione.
>

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas nas seguintes páginas:

* [Azure Active Directory site de preços](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Descrição do serviço de Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar o licenciamento baseado em grupo ou usuário

O Azure AD agora dá suporte ao licenciamento baseado em grupo. Os administradores podem atribuir licenças em massa a um grupo de usuários, em vez de atribuí-las um de cada vez. Para obter mais informações, consulte [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Para que uma licença possa ser atribuída a um usuário, o administrador deve especificar a propriedade **local de uso** no usuário. A atribuição de licenças pode ser feita na seção **configurações** do **usuário** > **perfil** > no portal do Azure. *Quando você usa a atribuição de licença de grupo, todos os usuários sem um local de uso especificado herdam o local do diretório.*

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
