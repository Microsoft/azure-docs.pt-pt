---
title: Experiências de consentimento da aplicação AD AZure
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
ms.openlocfilehash: c4a4af81c6a216119ae2e1b0221c06ddc349452f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92478138"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Compreender as experiências de consentimento da aplicação do Azure AD

Saiba mais sobre a experiência do utilizador do consentimento do utilizador do Azure Ative (Azure AD). Assim, pode gerir inteligentemente aplicações para a sua organização e/ou desenvolver aplicações com uma experiência de consentimento mais perfeita.

## <a name="consent-and-permissions"></a>Consentimento e permissões

Consentimento é o processo de um utilizador que concede autorização a um pedido de acesso a recursos protegidos em seu nome. Um administrador ou utilizador pode ser solicitado consentimento para permitir o acesso à sua organização/dados individuais.

A experiência real do utilizador de conceder o consentimento diferirá consoante as políticas definidas no arrendatário do utilizador, o âmbito de autoridade (ou função) do utilizador e o tipo de [permissões solicitadas](../azuread-dev/v1-permissions-consent.md) pela aplicação do cliente. Isto significa que os desenvolvedores de aplicações e administradores inquilinos têm algum controlo sobre a experiência de consentimento. Os administradores têm a flexibilidade de definir e desativar políticas num inquilino ou app para controlar a experiência de consentimento no seu inquilino. Os desenvolvedores de aplicações podem ditar que tipos de permissões estão a ser solicitadas e se querem orientar os utilizadores através do fluxo de consentimento do utilizador ou do fluxo de consentimento de administração.

- **O fluxo de consentimento do utilizador** é quando um desenvolvedor de aplicações direciona os utilizadores para o ponto final da autorização com a intenção de registar o consentimento apenas para o utilizador atual.
- **O fluxo de consentimento da administração** é quando um desenvolvedor de aplicações direciona os utilizadores para o ponto final de consentimento administrativo com a intenção de registar o consentimento para todo o inquilino. Para garantir que o fluxo de consentimento administrativo funcione corretamente, os desenvolvedores de aplicações devem listar todas as permissões na `RequiredResourceAccess` propriedade no manifesto de aplicação. Para mais informações, consulte [o manifesto de aplicação.](./reference-app-manifest.md)

## <a name="building-blocks-of-the-consent-prompt"></a>Blocos de construção da solicitação de consentimento

O pedido de consentimento foi concebido para garantir que os utilizadores têm informações suficientes para determinar se confiam na aplicação do cliente para aceder a recursos protegidos em seu nome. Compreender os blocos de construção ajudará os utilizadores a conceder o consentimento a tomar decisões mais informadas e ajudará os desenvolvedores a construir melhores experiências de utilizador.

O diagrama e tabela seguintes fornecem informações sobre os blocos de construção do pedido de consentimento.

![Blocos de construção da solicitação de consentimento](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Objetivo |
| ----- | ----- | ----- |
| 1 | Identificador de utilizador | Este identificador representa o utilizador que a aplicação do cliente está a solicitar o acesso aos recursos protegidos em nome de. |
| 2 | Título | O título muda com base no facto de os utilizadores estarem a passar pelo fluxo de consentimento do utilizador ou administração. No fluxo de consentimento do utilizador, o título será "Permissões solicitadas" enquanto no fluxo de consentimento administrativo o título terá uma linha adicional "Aceitar para a sua organização". |
| 3 | Logótipo da aplicação | Esta imagem deve ajudar os utilizadores a ter uma pista visual sobre se esta aplicação é a app a que se destinavam a aceder. Esta imagem é fornecida pelos desenvolvedores de aplicações e a propriedade desta imagem não é validada. |
| 4 | Nome da aplicação | Este valor deve informar os utilizadores sobre qual a aplicação que solicita o acesso aos seus dados. Note que este nome é fornecido pelos desenvolvedores e a propriedade deste nome da aplicação não é validada. |
| 5 | Domínio de publicador | Este valor deve fornecer aos utilizadores um domínio que possam avaliar para a fiabilidade. Este domínio é fornecido pelos desenvolvedores e a propriedade deste domínio de editor é validada. |
| 6 | Permissões | Esta lista contém as permissões solicitadas pela aplicação do cliente. Os utilizadores devem sempre avaliar os tipos de permissões que são solicitadas para entender que dados a aplicação do cliente será autorizada a aceder em seu nome se aceitar. Como desenvolvedor de aplicações, o melhor é solicitar acesso às permissões com menos privilégio. |
| 7 | Descrição da permissão | Este valor é fornecido pelo serviço expondo as permissões. Para ver as descrições da permissão, deve alternar o chevron ao lado da permissão. |
| 8 | Termos de aplicação | Estes termos contêm links para os termos de serviço e declaração de privacidade da aplicação. A editora é responsável por delinear as suas regras nos seus termos de serviço. Além disso, a editora é responsável por divulgar a forma como utilizam e partilham os dados dos utilizadores na sua declaração de privacidade. Se a editora não fornecer ligações a estes valores para aplicações multi-inquilinos, haverá um aviso ousado sobre o pedido de consentimento. |
| 9 | https://myapps.microsoft.com | Este é o link onde os utilizadores podem rever e remover quaisquer aplicações não Microsoft que tenham atualmente acesso aos seus dados. |

## <a name="common-consent-scenarios"></a>Cenários de consentimento comum

Aqui estão as experiências de consentimento que um utilizador pode ver nos cenários comuns de consentimento:

1. Os indivíduos que acedem a uma aplicação que os direciona para o fluxo de consentimento do utilizador, exigindo um conjunto de permissão que esteja dentro do seu âmbito de autoridade.
    
    1. Os administradores verão um controlo adicional sobre o consentimento tradicional que lhes permitirá consentir em nome de todo o inquilino. O controlo será incumprido, por isso só quando os administradores verificarem explicitamente a caixa será concedido em nome de todo o inquilino. A partir de hoje, esta caixa de verificação só será mostrada para o papel de Administrador Global, para que o Cloud Admin e o App Admin não vejam esta caixa de verificação.

        ![Pedido de consentimento para o cenário 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Os utilizadores verão o pedido de consentimento tradicional.

        ![Screenshot que mostra o pedido de consentimento tradicional.](./media/application-consent-experience/consent_prompt_1b.png)

2. Os indivíduos que acedem a uma aplicação que requer pelo menos uma permissão que está fora do seu âmbito de autoridade.
    1. Os administradores verão a mesma solicitação que 1.i mostrada acima.
    2. Os utilizadores ficarão impedidos de conceder o consentimento à aplicação, e serão aconselhados a pedir ao seu administrador acesso à app. 
                
        ![Screenshot do consentimento pronta a dizer ao utilizador para pedir a um administrador acesso à app.](./media/application-consent-experience/consent_prompt_2b.png)

3. Indivíduos que navegam ou são direcionados para o fluxo de consentimento administrativo.
    1. Os utilizadores de administração verão o pedido de consentimento administrativo. O título e as descrições de permissão alteradas neste pedido, as alterações destacam o facto de que aceitar esta solicitação dará acesso à app aos dados solicitados em nome de todo o arrendatário.
        
        ![Pedido de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Os utilizadores não administrativos verão o mesmo ecrã que o 2.ii acima mostrado.

## <a name="next-steps"></a>Passos seguintes
- Obtenha uma visão geral passo a passo de como o quadro de [consentimento Azure AD implementa o consentimento](./quickstart-register-app.md).
- Para obter mais profundidade, saiba [como uma aplicação multi-inquilino pode usar o quadro de consentimento](./howto-convert-app-to-be-multi-tenant.md) para implementar o consentimento "utilizador" e "administrador", suportando padrões de aplicação de vários níveis mais avançados.
- Saiba [como configurar o domínio de editor da aplicação.](howto-configure-publisher-domain.md)