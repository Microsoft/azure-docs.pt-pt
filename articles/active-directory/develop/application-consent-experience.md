---
title: Experiências de consentimento da aplicação Azure AD
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as experiências de consentimento da AD Azure para ver como pode usá-lo ao gerir e desenvolver aplicações em Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: e96442be50a075ebf2cd81bf1b6fb0f58f883bad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885587"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Compreender as experiências de consentimento da aplicação do Azure AD

Saiba mais sobre a experiência de consentimento do utilizador do diretório Ativo Azure (Azure AD). Para que possa gerir inteligentemente aplicações para a sua organização e/ou desenvolver aplicações com uma experiência de consentimento mais perfeita.

## <a name="consent-and-permissions"></a>Consentimento e permissões

O consentimento é o processo de um utilizador que concede autorização a uma aplicação de acesso a recursos protegidos em seu nome. Um administrador ou utilizador pode ser solicitado o consentimento para permitir o acesso à sua organização/dados individuais.

A experiência real do utilizador em conceder o consentimento diferirá consoante as políticas definidas no inquilino do utilizador, no âmbito de autoridade (ou função) do utilizador e no tipo de [permissões solicitadas](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) pela aplicação do cliente. Isto significa que os desenvolvedores de aplicações e os administradores de inquilinos têm algum controlo sobre a experiência de consentimento. Os administradores têm a flexibilidade de definir e desativar políticas num inquilino ou app para controlar a experiência de consentimento no seu inquilino. Os desenvolvedores de aplicações podem ditar que tipos de permissões estão a ser solicitadas e se quiserem orientar os utilizadores através do fluxo de consentimento do utilizador ou do fluxo de consentimento do administrador.

- **O fluxo de consentimento** do utilizador é quando um desenvolvedor de aplicações direciona os utilizadores para o ponto final da autorização com a intenção de registar o consentimento apenas para o utilizador atual.
- **O fluxo de consentimento** da Administração é quando um desenvolvedor de aplicações direciona os utilizadores para o ponto final do consentimento do administrador com a intenção de registar o consentimento para todo o inquilino. Para garantir que o fluxo de consentimento da administração funciona `RequiredResourceAccess` corretamente, os desenvolvedores de aplicações devem listar todas as permissões na propriedade no manifesto de aplicação. Para mais informações, consulte [O manifesto da Aplicação.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

## <a name="building-blocks-of-the-consent-prompt"></a>Blocos de construção do pedido de consentimento

O pedido de consentimento foi concebido para garantir que os utilizadores têm informações suficientes para determinar se confiam na aplicação do cliente para aceder a recursos protegidos em seu nome. Compreender os blocos de construção ajudará os utilizadores que concedem o consentimento a tomar decisões mais informadas e ajudará os desenvolvedores a construir melhores experiências de utilizador.

O diagrama e a tabela seguintes fornecem informações sobre os blocos de construção do pedido de consentimento.

![Blocos de construção do pedido de consentimento](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Objetivo |
| ----- | ----- | ----- |
| 1 | Identificador de utilizador | Este identificador representa o utilizador que a aplicação do cliente está a solicitar para aceder a recursos protegidos em nome de. |
| 2 | Título | O título muda com base no facto de os utilizadores estarem a passar pelo fluxo de consentimento do utilizador ou do administrador. No fluxo de consentimento do utilizador, o título será "Permissões solicitadas" enquanto no fluxo de consentimento da administração o título terá uma linha adicional "Aceitar para a sua organização". |
| 3 | Logótipo da aplicação | Esta imagem deve ajudar os utilizadores a ter uma pista visual sobre se esta aplicação é a app a que pretendiam aceder. Esta imagem é fornecida por desenvolvedores de aplicações e a propriedade desta imagem não é validada. |
| 4 | Nome da aplicação | Este valor deve informar os utilizadores de qual aplicação está a solicitar o acesso aos seus dados. Note que este nome é fornecido pelos desenvolvedores e a propriedade deste nome da aplicação não é validada. |
| 5 | Domínio de publicador | Este valor deve fornecer aos utilizadores um domínio que possam avaliar para fiabilidade. Este domínio é fornecido pelos desenvolvedores e a propriedade deste domínio de editor é validada. |
| 6 | Permissões | Esta lista contém as permissões que estão a ser solicitadas pelo pedido do cliente. Os utilizadores devem sempre avaliar os tipos de permissões que estão a ser solicitadas para perceber quais os dados que a aplicação do cliente será autorizada a aceder em seu nome se aceitarem. Como desenvolvedor de aplicações, o melhor é solicitar acesso às permissões com menos privilégios. |
| 7 | Descrição da permissão | Este valor é fornecido pelo serviço expondo as permissões. Para ver as descrições da permissão, deve alternar o chevron ao lado da permissão. |
| 8 | Termos da aplicação | Estes termos contêm links para os termos de serviço e declaração de privacidade da aplicação. A editora é responsável por delinear as suas regras nos seus termos de serviço. Além disso, a editora é responsável por divulgar a forma como utilizam e partilhar dados dos utilizadores na sua declaração de privacidade. Se a editora não fornecer ligações a estes valores para aplicações multi-arrendatárias, haverá um aviso arrojado sobre o pedido de consentimento. |
| 9 | https://myapps.microsoft.com | Este é o link onde os utilizadores podem rever e remover quaisquer aplicações não Microsoft que tenham atualmente acesso aos seus dados. |

## <a name="common-consent-scenarios"></a>Cenários de consentimento comuns

Aqui estão as experiências de consentimento que um utilizador pode ver nos cenários comuns de consentimento:

1. Os indivíduos que acedem a uma aplicação que os direciona para o fluxo de consentimento do utilizador, exigindo um conjunto de permissão que esteja dentro do seu âmbito de autoridade.
    
    1. Os administradores verão um controlo adicional sobre o pedido de consentimento tradicional que lhes permitirá consentir em nome de todo o inquilino. O controlo será indefinido, pelo que só quando os administradores verificarem explicitamente que a caixa será concedida em nome de todo o inquilino. A partir de hoje, esta caixa de verificação só irá mostrar para o papel de Administrador Global, por isso a Cloud Admin e a App Admin não verão esta caixa de verificação.

        ![Pedido de consentimento para o cenário 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Os utilizadores verão o pedido de consentimento tradicional.

        ![Pedido de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Indivíduos que acedem a uma aplicação que requer pelo menos uma permissão que está fora do seu âmbito de autoridade.
    1. Os administradores verão o mesmo pedido que 1.i mostrado acima.
    2. Os utilizadores serão impedidos de conceder o consentimento à aplicação, e serão informados para pedir ao seu administrador o acesso à app. 
                
        ![Pedido de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Indivíduos que navegam ou são direcionados para o fluxo de consentimento da administração.
    1. Os utilizadores de administrador verão o pedido de consentimento do administrador. O título e as descrições de permissões alteradas neste pedido, as alterações destacam o facto de que aceitar este pedido irá conceder à app acesso aos dados solicitados em nome de todo o inquilino.
        
        ![Pedido de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Os utilizadores não administrativos verão o mesmo ecrã que 2.ii mostrado acima.

## <a name="next-steps"></a>Passos seguintes
- Obtenha uma visão geral passo a passo de como o quadro de [consentimento da AD Azure implementa o consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Para obter mais profundidade, saiba [como uma aplicação multi-arrendatária pode usar o quadro de consentimento](active-directory-devhowto-multi-tenant-overview.md) para implementar o consentimento de "utilizador" e "administrador", suportando padrões de aplicação multi-nível mais avançados.
- Saiba [como configurar o domínio de editor da aplicação.](howto-configure-publisher-domain.md)
