---
title: Visão geral da verificação da editora - Plataforma de identidade da Microsoft Azure
description: Fornece uma visão geral do programa de verificação da editora (pré-visualização) para a plataforma de identidade da Microsoft. Lista os benefícios, requisitos do programa e perguntas frequentes. Quando uma aplicação é marcada como a editora verificada, significa que a editora verificou a sua identidade através de uma conta microsoft Partner Network que completou o processo de verificação e associou esta conta MPN ao seu registo de aplicação.
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
ms.openlocfilehash: 73a96f295d5dfa74130927e5096e9278a0e348e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682436"
---
# <a name="publisher-verification-preview"></a>Verificação da editora (pré-visualização)

A verificação da editora (pré-visualização) ajuda os administradores e os utilizadores finais a entender a autenticidade dos desenvolvedores de aplicações que se integram com a plataforma de identidade da Microsoft. Quando uma aplicação é marcada como a editora verificada, significa que a editora verificou a sua identidade através de uma conta [microsoft Partner Network](https://partner.microsoft.com/membership) que completou o processo de [verificação](/partner-center/verification-responses) e associou esta conta MPN ao seu registo de aplicação. 

Um crachá azul "verificado" aparece no pedido de consentimento da AD Azure e outros ecrãs: Solicitação de ![ consentimento](./media/publisher-verification-overview/consent-prompt.png)

Esta funcionalidade destina-se principalmente a desenvolvedores que construem aplicações multi-arrendatárias que aproveitem o [OAuth 2.0 e](active-directory-v2-protocols.md) o OpenID Connect com a plataforma de identidade [microsoft](v2-overview.md). Estas aplicações podem contratar utilizadores na utilização do OpenID Connect, ou podem utilizar o OAuth 2.0 para solicitar o acesso a dados usando APIs como [o Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Benefícios
A verificação do editor proporciona os seguintes benefícios:
- **Maior transparência e redução**de risco para os clientes - esta capacidade ajuda os clientes a entender em que aplicações são usadas nas suas organizações são publicadas por desenvolvedores em quem confiam. 

- **Marca melhorada**- um crachá "verificado" aparece no pedido de [consentimento](application-consent-experience.md)da AD Azure, página de Aplicações Empresariais e superfícies UX adicionais usadas por utilizadores finais e administradores. 

- **Adoção mais suave da empresa**- os administradores podem configurar novas Políticas de Consentimento do Utilizador, e o estado de verificação do editor será um dos principais critérios de política. 

- **Melhor avaliação**de risco - As deteções da Microsoft para pedidos de consentimento "arriscados" incluirão a verificação do editor como um sinal. 

## <a name="requirements"></a>Requisitos
Existem alguns pré-requisitos para a verificação da editora, alguns dos quais já terão sido concluídos por muitos parceiros da Microsoft. São: 

-  Um ID MPN para uma conta válida da [Microsoft Partner Network](https://partner.microsoft.com/membership) que concluiu o processo de [verificação.](/partner-center/verification-responses) Esta conta MPN deve ser a [conta Partner global (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) para a sua organização. 

-  Um inquilino da AD Azure com um [domínio personalizado](/azure/active-directory/fundamentals/add-custom-domain)verificado pelo DNS. O domínio personalizado deve corresponder ao domínio do endereço de e-mail utilizado durante a verificação na etapa anterior. 

-  Uma aplicação registada num inquilino da AD Azure, com um [Domínio editor](howto-configure-publisher-domain.md) configurado usando o mesmo domínio que anteriormente utilizado. 

-  O utilizador que efetua a verificação deve ser autorizado a efetuar alterações tanto no registo da aplicação em Azure AD como na conta MPN no Partner Center. 

    -  No Azure AD este utilizador deve ser o Proprietário da app ou ter uma das seguintes [funções](/azure/active-directory/users-groups-roles/directory-assign-admin-roles): Application Admin, Cloud Application Admin, Global Admin. 

    -  No Partner Center este utilizador deve ter as seguintes [funções](/partner-center/permissions-overview): MPN Admin, Accounts Admin ou um Global Admin (este é um papel partilhado dominado em Azure AD).
    
-  A editora concorda com a plataforma de identidade da [Microsoft para os desenvolvedores Termos de Utilização](/legal/microsoft-identity-platform/terms-of-use).

Os desenvolvedores que já cumpriram estes pré-requisitos podem ser verificados em questão de minutos. Se os requisitos não forem cumpridos, a criação é gratuita. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
Abaixo estão algumas perguntas frequentes sobre o programa de verificação da editora. Para as PERGUNTAS FaQrelacionadas com os requisitos e o processo, consulte [a marca de uma aplicação como a editora verificou](mark-app-as-publisher-verified.md).

- **Que informações __a__ verificação do editor não fornece?**  Quando uma aplicação é marcada, o editor verificou que isso não indica se a aplicação ou o seu editor obteve certificações específicas, cumpre os padrões do setor, adere às melhores práticas, etc. Outros programas da Microsoft fornecem estas informações, incluindo a Certificação de [Aplicações Microsoft 365.](/microsoft-365-app-certification/overview)

- **Quanto custa isto? Requer alguma licença?** A Microsoft não cobra aos programadores a verificação do editor e não necessita de nenhuma licença específica. 

- **O que é que isto tem a ver com o Microsoft 365 Publisher Attestation? E a Certificação de Aplicações Microsoft 365?** Estes são programas complementares que os desenvolvedores podem usar para criar aplicações confiáveis que podem ser adotadas com confiança pelos clientes. A verificação do editor é o primeiro passo neste processo, e deve ser completada por todos os desenvolvedores que criam apps que satisfaçam os critérios acima referidos. 

  Os desenvolvedores que também estão a integrar-se com o Microsoft 365 podem receber benefícios adicionais destes programas. Para mais informações, consulte o [Microsoft 365 Publisher Attestation](/microsoft-365-app-certification/docs/attestation) e a [Microsoft 365 App Certification](/microsoft-365-app-certification/docs/certification). 

- **Isto é a mesma coisa que a Galeria de Aplicação da AD Azure?** A verificação de não-editores é um programa complementar, mas separado, para a galeria de [aplicações do Azure Ative Directory.](/azure/active-directory/azuread-dev/howto-app-gallery-listing) Os desenvolvedores que se enquadram nos critérios acima devem completar o processo de verificação do editor independentemente da participação nesse programa. 

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [marcar uma aplicação como a editora verificou.](mark-app-as-publisher-verified.md)
* Verificação de editor [de problemas.](troubleshoot-publisher-verification.md)