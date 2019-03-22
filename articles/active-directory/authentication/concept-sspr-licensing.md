---
title: Reposição de palavra-passe self-service de licença - Azure Active Directory
description: Requisitos de licenciamento de redefinição de senha de autoatendimento de AD do Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e4e32aef97d406fa47a2ccfe8cddb12b97ff088
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316852"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Os requisitos de palavra-passe self-service do Azure AD de licenciamento de reposição

O Azure Active Directory (Azure AD) está disponível em quatro edições: Gratuito, Básico, Premium P1 e Premium P2. Existem vários recursos diferentes que tornam a reposição de palavra-passe self-service, incluindo a alteração, repor, desbloquear e repetição de escrita, que estão disponíveis nas edições diferentes do Azure AD. Este artigo tenta explicar as diferenças. Podem encontrar mais detalhes dos recursos incluídos em cada edição do Azure AD na [Azure Active Directory, página de preços](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Compare as edições e funcionalidades

O Azure AD self-service palavra-passe reposição é licenciada por usuário, para manter a conformidade, as organizações são obrigadas a atribuir a licença adequada aos seus utilizadores.

* Alteração Personalizada de Palavra-passe para utilizadores em cloud
   * Sou um **utilizador apenas na cloud** e saiba minha senha.
      * Eu gostaria **alterar** minha palavra-passe para algo novo.
   * Esta funcionalidade está incluída em todas as edições do Azure AD.

* Reposição Personalizada de Palavra-passe para utilizadores em cloud
   * Sou um **utilizador apenas na cloud** e ter esquecido a minha palavra-passe.
      * Eu gostaria **repor** minha palavra-passe para algo que sei.
   * Esta funcionalidade está incluída no Azure AD básico, Premium P1 ou P2 ou Microsoft 365 empresas.

* Self-Service palavra-passe reposição/alteração/desbloqueio **com repetição de escrita no local**
   * Sou um **utilizador híbrido** minha conta de utilizador do Active Directory no local está sincronizada com a minha conta do Azure AD com o Azure AD Connect. Eu gostaria de alterar a minha palavra-passe, tenha esquecido a minha palavra-passe ou foi bloqueada.
      * Eu gostaria de alterar a minha palavra-passe ou redefini-lo para algo que sei ou desbloquear a minha conta **e** ter que alterar sincronizada de volta no local do Active Directory.
   * Esta funcionalidade está incluída no Azure AD Premium P1 ou P2 ou Microsoft 365 empresas.

> [!WARNING]
> Autónomo Office 365, planos de licenciamento *não suportam "Self-Service palavra-passe reposição/alteração/desbloqueio com repetição de escrita no local"* e requerem um plano que inclui o Azure AD Premium P1, Premium P2 ou Microsoft 365 empresas para isso funcionalidade funcionar.
>

Informações de licenciamento adicionais, incluindo os custos, podem ser encontradas nas seguintes páginas:

* [O Azure Active Directory, preços do site](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funcionalidades do Active Directory e capacidades do Azure](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Descrição do serviço Microsoft 365 empresas](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Ativar o grupo ou licenciamento baseado no utilizador

Agora o Azure AD suporta o licenciamento baseado em grupo. Os administradores podem atribuir licenças em massa a um grupo de utilizadores, em vez de atribuir um de cada vez. Para obter mais informações, consulte [atribuir, certifique-se e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar o **localização de utilização** propriedade no utilizador. Atribuição de licenças que pode ser feita sob o **usuário** > **perfil** > **definições** secção no portal do Azure. *Quando utilizar a atribuição de licenças de grupo, todos os utilizadores sem uma localização de utilização definida herdam a localização do diretório.*

## <a name="next-steps"></a>Passos Seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
