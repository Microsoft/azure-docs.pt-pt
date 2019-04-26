---
title: Experiências de compreender o consentimento de aplicação do Azure AD | Documentos da Microsoft
description: Saiba que mais sobre o Azure AD experiências para ver como ele pode ser usado quando a gestão e desenvolvimento de aplicações no Azure AD de consentimento
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7de892143e1c69953cc60429ea0d24df194f0df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300285"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Compreender as experiências de consentimento da aplicação do Azure AD

Saiba que mais sobre a aplicação do Azure Active Directory (Azure AD) a experiência do usuário de consentimento. Por isso, pode inteligentemente gerir aplicações na sua organização e/ou desenvolvimento de aplicativos com uma experiência mais integrada de consentimento.

## <a name="consent-and-permissions"></a>Permissões e consentimento

Consentimento é o processo de um concessão de autorização para uma aplicação para aceder a recursos protegidos em seu nome de utilizador. Um administrador ou usuário pode ser solicitado consentimento permitir o acesso aos dados da organização/indivíduo.

A experiência do usuário real de conceder autorização irão variar consoante as políticas definidas no inquilino do utilizador, âmbito do utilizador de autoridade (ou funções) e o tipo de [permissões](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) sendo solicitado pela aplicação de cliente. Isso significa que os desenvolvedores de aplicativos e administradores de inquilinos manter algum controle sobre a experiência de consentimento. Os administradores têm a flexibilidade de definir e desativar as políticas num inquilino ou aplicação para controlar a experiência de consentimento no seu inquilino. Os desenvolvedores de aplicativos podem ditar estão sendo solicitados que tipos de permissões e se eles querem os orientará o utilizador consente flow ou o fluxo de consentimento de administrador.

- **Fluxo de consentimento do utilizador** é quando um programador de aplicações direciona os utilizadores para o ponto final de autorização com a intenção de consentimento de registo para apenas o utilizador atual.
- **Fluxo de consentimento de administração** é quando um programador de aplicações direciona os utilizadores para o administrador de consentir ponto final com a intenção de consentimento de registo para todo o inquilino. Para garantir que o fluxo de consentimento do admin funciona corretamente, os desenvolvedores de aplicativos devem listar todas as permissões no `RequiredResourceAccess` propriedade no manifesto do aplicativo. Para mais informações, veja [manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocos de construção da solicitação de consentimento

A solicitação de consentimento foi concebida para garantir que os utilizadores têm informações suficientes para determinar se eles confiam a aplicação de cliente para aceder a recursos protegidos em seu nome. Compreender os blocos de construção ajudará os usuários a concessão de consentimento marca decisões mais informadas e ajudará os desenvolvedores a criar melhores experiências de utilizador.

O diagrama e tabela seguintes fornecem informações sobre os blocos de construção da solicitação de consentimento.

![Blocos de construção da solicitação de consentimento](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Objetivo |
| ----- | ----- | ----- |
| 1 | Identificador de utilizador | Este identificador representa o que o aplicativo cliente está a pedir para aceder a recursos protegidos em nome de utilizador. |
| 2 | Título | As alterações de título com base em se os utilizadores passam pelo fluxo de consentimento do utilizador ou administrador. No fluxo de consentimento do utilizador, o título será "Permissões solicitadas" enquanto o fluxo de consentimento de administrador o título terá uma linha adicional "Aceitar para a sua organização". |
| 3 | Logótipo da aplicação | Esta imagem deve ajudar os utilizadores têm uma indicação visual se esta aplicação é a aplicação pretendem aceder. Esta imagem é fornecida por desenvolvedores de aplicativos e a propriedade desta imagem não é validada. |
| 4 | Nome da aplicação | Este valor deve informar os utilizadores que aplicação está a pedir acesso aos respetivos dados. Tenha em atenção que este nome é fornecido pelos desenvolvedores e a propriedade deste nome de aplicação não é validada. |
| 5 | Domínio de publicador | Este valor deve fornecer aos utilizadores com um domínio pode ser capazes de avaliar a confiabilidade. Este domínio é fornecido pelos desenvolvedores e a propriedade deste domínio de publicador é validada. |
| 6 | Permissões | Esta lista inclui as permissões que está a ser solicitadas pela aplicação de cliente. Os usuários sempre devem avaliar os tipos de permissões que está sendo solicitadas para compreender os dados que a aplicação de cliente vai ser autorizada a aceder em seu nome, caso aceitem. Como desenvolvedor de aplicativos é melhor para pedir acesso, para as permissões com o menor privilégio. |
| 7 | Descrição da permissão | Este valor é fornecido pelo serviço de expor as permissões. Para ver as descrições de permissão, tem de ativar/desativar de divisa junto a permissão. |
| 8 | Termos de aplicação | Estes termos contêm links para os termos de serviço e declaração de privacidade da aplicação. O publicador é responsável por que descreva as respetivas regras nos seus termos de serviço. Além disso, o publicador é responsável por divulgação da forma que utilizam e partilham dados de utilizador na respetiva declaração de privacidade. Se o publicador não fornece links para esses valores para aplicações multi-inquilino, haverá um aviso em negrito no prompt de consentimento. |
| 9 | https://myapps.microsoft.com | Esta é a ligação em que os utilizadores podem rever e remover todas as aplicações não são da Microsoft que atualmente têm acesso aos respetivos dados. |

## <a name="common-consent-scenarios"></a>Cenários comuns de consentimento

Aqui estão as experiências de consentimento que um utilizador poderá ver nos cenários comuns de consentimento:

1. Indivíduos aceder a uma aplicação que os direcione para o usuário consentimento fluxo, exigindo um conjunto de permissões que esteja dentro do respetivo âmbito de autoridade.
    
    1. Os administradores vão ver um controle adicional sobre a solicitação de consentimento tradicionais que lhes permite consentimento em nome de todo o inquilino. O controle será predefinido para desativado, isso apenas quando os administradores verificar explicitamente a caixa irá dar consentimento ser concedido em nome de todo o inquilino. Até hoje, esta caixa de verificação mostrará apenas para a função de Administrador Global, para que o administrador da nuvem e Admin da aplicação não verá esta caixa de verificação.

        ![Solicitação de consentimento para cenário 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Os utilizadores verão a solicitação de consentimento tradicional.

        ![Solicitação de consentimento para cenário 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Aceder a uma aplicação que requer, pelo menos, uma permissão que está fora do âmbito da autoridade de indivíduos.
    1. Os administradores vão ver a linha de comandos da mesmo como 1.i mostrado acima.
    2. Os utilizadores serão impedidos de conceder permissão à aplicação e irá ser informados para peça ao seu administrador de acesso à aplicação. 
                
        ![Solicitação de consentimento para cenário 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Fluxo de consentimento de indivíduos que naveguem ou são direcionados para o administrador.
    1. Os utilizadores administradores Verão a solicitação de consentimento de administrador. O título e as descrições de permissão alteradas nesta linha de comandos, o realce de alterações, o fato de que ao aceitarem este pedido irá garantir a aplicação de acesso para os dados solicitados em nome de todo o inquilino.
        
        ![Solicitação de consentimento para cenário 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Utilizadores não administradores verão o mesmo ecrã como ii 2. mostrado acima.

## <a name="next-steps"></a>Passos Seguintes
- Obtenha uma visão geral passo a passo dos [como o framework de consentimento do Azure AD implementa consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Para obter mais detalhes, saiba [como uma aplicação de multi-inquilino pode utilizar a estrutura do consentimento](active-directory-devhowto-multi-tenant-overview.md) implementar "utilizador" e "admin" consentimento, suporte a mais avançada padrões de aplicação de várias camadas.
- Saiba mais [como configurar o domínio de publicador da aplicação](howto-configure-publisher-domain.md).
