---
title: Reset de senha de autosserviço de licença - Diretório Ativo Azure
description: Palavra-passe autosserviço Azure AD repõe requisitos de licenciamento
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848566"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisitos de licenciamento para reset de senha de autosserviço da AD Azure

O Azure Ative Directory (Azure AD) vem em várias edições: Grátis, Premium P1 e Premium P2. Existem várias funcionalidades diferentes que compõem o reset da palavra-passe self-service, incluindo alterações, reset, desbloqueio e reescrita, que estão disponíveis nas diferentes edições do Azure AD. Este artigo tenta explicar as diferenças. Mais detalhes sobre as funcionalidades incluídas em cada edição da AD Azure podem ser encontrados na página de [preços do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="compare-editions-and-features"></a>Compare edições e funcionalidades

O reset de palavra-passe autosserviço Azure AD é licenciado por utilizador, para manter as organizações de conformidade são obrigados a atribuir a licença adequada aos seus utilizadores.

* Alteração de senha de autosserviço para utilizadores de nuvem
   * Sou um **utilizador só na nuvem** e sei a minha senha.
      * Gostaria de **mudar** a minha senha para algo novo.
   * Esta funcionalidade está incluída em todas as edições da Azure AD.

* Reset de palavra-passe self-service para utilizadores em nuvem
   * Sou um utilizador só em nuvem e **esqueci-me** da minha senha.
      * Gostaria de **redefinir** a minha senha para algo que sei.
   * Esta funcionalidade está incluída no Azure AD Premium P1 ou P2, Microsoft 365 Business ou Office 365.

* Reset/Change/Unlock com reprodução de palavra-passe self-service/Change/Unlock **with on-premis**
   * Sou um **utilizador híbrido,** a minha conta de utilizador ative diretório está sincronizada com a minha conta Azure AD utilizando o Azure AD Connect. Gostaria de mudar a minha senha, esqueci-me da minha senha, ou fui bloqueado.
      * Gostaria de mudar a minha palavra-passe ou redefini-la para algo que sei, ou desbloquear a minha conta, **e** fazer com que essa mudança seja sincronizada de volta para o Ative Directory no local.
   * Esta funcionalidade está incluída no Azure AD Premium P1 ou P2, ou no Microsoft 365 Business.

> [!WARNING]
> Os planos de licenciamento autónomos do Office 365 *não suportam "Reset/Change/Unlock with in-premis"* e exigem um plano que inclua O Azure AD Premium P1, Premium P2 ou Microsoft 365 Business para que esta funcionalidade funcione.
>

Informações adicionais de licenciamento, incluindo custos, podem ser encontradas nas seguintes páginas:

* [Site de preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funcionalidades e capacidades do Diretório Ativo Azure](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Descrição do serviço empresarial Microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Ativar licenciamento baseado em grupo ou ao utilizador

A Azure AD apoia agora o licenciamento baseado em grupo. Os administradores podem atribuir licenças a granel a um grupo de utilizadores, em vez de as atribuir uma de cada vez. Para mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença poder ser atribuída a um utilizador, o administrador deve especificar a propriedade de **localização de utilização** no utilizador. A atribuição de licenças pode ser feita no âmbito da secção**Definições** de**Perfil** > do **Utilizador** > no portal Azure. *Quando utiliza a atribuição de licença de grupo, quaisquer utilizadores sem localização de utilização especificada herdam a localização do diretório.*

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
