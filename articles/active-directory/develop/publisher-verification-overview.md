---
title: Visão geral de verificação do editor - Plataforma de identidade da Microsoft Rio Azure
description: Fornece uma visão geral do programa de verificação de editores para a plataforma de identidade da Microsoft. Lista os benefícios, requisitos do programa e perguntas frequentes. Quando uma aplicação é marcada como editora verificada, significa que a editora verificou a sua identidade utilizando uma conta da Microsoft Partner Network que concluiu o processo de verificação e associou esta conta MPN ao seu registo de candidatura.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 53861bbff9ba328ca97063df59cfedc6e886212d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258154"
---
# <a name="publisher-verification"></a>Verificação do editor

A verificação do editor ajuda os administradores e os utilizadores finais a compreender a autenticidade dos desenvolvedores de aplicações que se integram na plataforma de identidade da Microsoft. Quando uma aplicação é marcada como editora verificada, significa que a editora verificou a sua identidade utilizando uma conta [da Microsoft Partner Network](https://partner.microsoft.com/membership) que concluiu o processo de [verificação](/partner-center/verification-responses) e associou esta conta MPN ao seu registo de candidatura. 

Um crachá azul "verificado" aparece na solicitação de consentimento Azure AD e outros ecrãs: ![ Pedido de consentimento](./media/publisher-verification-overview/consent-prompt.png)

Esta funcionalidade destina-se principalmente a programadores que couem aplicações multi-arrendadas que alavancam [o OAuth 2.0 e](active-directory-v2-protocols.md) o OpenID Connect com a plataforma de identidade da [Microsoft.](v2-overview.md) Estas aplicações podem assinar utilizadores na utilização do OpenID Connect, ou podem usar o OAuth 2.0 para solicitar o acesso a dados usando APIs como [o Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Benefícios
A verificação do editor proporciona os seguintes benefícios:
- **Maior transparência e redução de risco para os clientes**- esta capacidade ajuda os clientes a entender quais as aplicações que estão a ser usadas nas suas organizações são publicadas pelos desenvolvedores em que confiam. 

- **Melhor marca**- um crachá "verificado" aparece no [pedido](application-consent-experience.md)de consentimento Azure AD, página de Aplicações empresariais e superfícies UX adicionais usadas pelos utilizadores finais e administradores. 

- **Adoção mais suave da empresa**- os administradores podem configurar políticas de consentimento do [utilizador,](../manage-apps/configure-user-consent.md)com o estado de verificação do editor como um dos principais critérios de política.

> [!NOTE]
> A partir de novembro de 2020, os utilizadores finais deixarão de poder conceder o consentimento à maioria das aplicações multi-inquilinos recentemente registadas sem editores verificados. Isto aplica-se a aplicações registadas a partir de 8 de novembro de 2020, utilizará o OAuth2.0 para solicitar permissões para além do perfil básico do utilizador e lerá o consentimento dos utilizadores em diferentes inquilinos do que aquele em que a app está registada. Será exibido um aviso no ecrã de consentimento informando os utilizadores de que estas aplicações são arriscadas e são de editoras não verificadas.    

## <a name="requirements"></a>Requisitos
Existem alguns pré-requisitos para a verificação de editores, alguns dos quais já terão sido concluídos por muitos parceiros da Microsoft. A saber: 

-  Um ID MPN para uma conta válida [da Microsoft Partner Network](https://partner.microsoft.com/membership) que tenha concluído o processo de [verificação.](/partner-center/verification-responses) Esta conta MPN deve ser a [conta global partner (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) para a sua organização. 

-  Uma aplicação registada num inquilino AZure AD, com um [Domínio editor](howto-configure-publisher-domain.md) configurado.

-  O domínio do endereço de e-mail utilizado durante a verificação da conta MPN deve corresponder ao domínio do editor configurado na aplicação ou um [domínio personalizado](../fundamentals/add-custom-domain.md) verificado pelo DNS adicionado ao inquilino AD Azure. 

-  O utilizador que efetua a verificação deve ser autorizado a efetuar alterações tanto no registo da aplicação no AZure AD como na conta MPN no Partner Center. 

    -  Em Azure AD este utilizador deve ser membro de uma das [seguintes funções](../users-groups-roles/directory-assign-admin-roles.md): Administrador de Aplicação, Cloud Application Admin ou Global Admin. 

    -  No Partner Center este utilizador deve ter as seguintes [funções](/partner-center/permissions-overview): MPN Admin, Accounts Admin ou um Administrador Global (este é um papel partilhado dominado em Azure AD).
    
-  A verificação de efetuações do utilizador deve iniciar sação com a [autenticação de vários fatores](../authentication/howto-mfa-getstarted.md).

-  A editora concorda com a plataforma de identidade da [Microsoft para os desenvolvedores Termos de Utilização.](/legal/microsoft-identity-platform/terms-of-use)

Os desenvolvedores que já cumpriram estes pré-requisitos podem ser verificados em questão de minutos. Se os requisitos não tiverem sido cumpridos, a instalação é gratuita. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 
Abaixo estão algumas perguntas frequentes sobre o programa de verificação de editores. Para perguntas frequentes relacionadas com os requisitos e o processo, consulte [uma aplicação como editor verificado.](mark-app-as-publisher-verified.md)

- **Que informações a verificação do editor __não__ fornece?**  Quando uma aplicação é marcada, o editor verificado não indica se a aplicação ou o seu editor obteve certificações específicas, cumpre as normas do setor, adere às melhores práticas, etc. Outros programas da Microsoft fornecem esta informação, incluindo [a Certificação de Aplicações Microsoft 365.](/microsoft-365-app-certification/overview)

- **Quanto custa isto? Requer licença?** A Microsoft não cobra aos desenvolvedores a verificação do editor e não necessita de nenhuma licença específica. 

- **O que é que isto tem a ver com a Microsoft 365 Publisher Attestation? E a Certificação de Aplicações Microsoft 365?** Estes são programas complementares que os desenvolvedores podem usar para criar aplicações confiáveis que podem ser adotadas com confiança pelos clientes. A verificação do editor é o primeiro passo neste processo, e deve ser concluída por todos os desenvolvedores que criam apps que satisfaçam os critérios acima referidos. 

  Os desenvolvedores que também estão a integrar-se com o Microsoft 365 podem receber benefícios adicionais destes programas. Para mais informações, consulte a [Microsoft 365 Publisher Attestation](/microsoft-365-app-certification/docs/attestation) e [a Microsoft 365 App Certification](/microsoft-365-app-certification/docs/certification). 

- **É a mesma coisa que a Galeria de Aplicações AD AZure?** A verificação de não editores é um programa complementar, mas separado, para a galeria de candidaturas do [Azure Ative Directory.](v2-howto-app-gallery-listing.md) Os desenvolvedores que se enquadrarem nos critérios acima devem completar o processo de verificação do editor independentemente da participação nesse programa. 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [marcar uma aplicação como editor verificado.](mark-app-as-publisher-verified.md)
* [Verificação de](troubleshoot-publisher-verification.md) editor de resolução de problemas.
