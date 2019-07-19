---
title: Noções básicas sobre experiências de consentimento de aplicativo do Azure AD | Microsoft Docs
description: Saiba mais sobre as experiências de consentimento do Azure AD para ver como você pode usá-las ao gerenciar e desenvolver aplicativos no Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac9d278053f05f9041a002b1c6ef3f873b0d9efb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324852"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Compreender as experiências de consentimento da aplicação do Azure AD

Saiba mais sobre a experiência do usuário de consentimento do aplicativo Azure Active Directory (Azure AD). Portanto, você pode gerenciar aplicativos de forma inteligente para sua organização e/ou desenvolver aplicativos com uma experiência de consentimento mais direta.

## <a name="consent-and-permissions"></a>Consentimento e permissões

O consentimento é o processo de um usuário que concede autorização a um aplicativo para acessar recursos protegidos em seu nome. Um administrador ou usuário pode ser solicitado a fornecer consentimento para permitir o acesso aos dados da organização/individuais.

A experiência real do usuário de conceder consentimento varia de acordo com as políticas definidas no locatário do usuário, o escopo de autoridade (ou função) do usuário e o tipo de [permissões](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) que estão sendo solicitadas pelo aplicativo cliente. Isso significa que os desenvolvedores de aplicativos e administradores de locatário têm algum controle sobre a experiência de consentimento. Os administradores têm a flexibilidade de definir e desabilitar políticas em um locatário ou aplicativo para controlar a experiência de consentimento em seu locatário. Os desenvolvedores de aplicativos podem determinar quais tipos de permissões estão sendo solicitados e se desejam guiar os usuários por meio do fluxo de consentimento do usuário ou do fluxo de consentimento do administrador.

- O **fluxo de consentimento do usuário** é quando um desenvolvedor de aplicativo direciona os usuários para o ponto de extremidade de autorização com a intenção de registrar o consentimento somente para o usuário atual.
- O **fluxo de consentimento do administrador** é quando um desenvolvedor de aplicativos direciona os usuários para o ponto de extremidade de consentimento do administrador com a intenção de registrar o consentimento para todo o locatário. Para garantir que o fluxo de consentimento do administrador funcione corretamente, os desenvolvedores de aplicativos devem `RequiredResourceAccess` listar todas as permissões na propriedade no manifesto do aplicativo. Para obter mais informações, consulte [manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocos de construção da solicitação de consentimento

A solicitação de consentimento foi projetada para garantir que os usuários tenham informações suficientes para determinar se confiam no aplicativo cliente para acessar recursos protegidos em seu nome. Entender os blocos de construção ajudará os usuários a conceder consentimento a tomar decisões mais informadas e ajudará os desenvolvedores a criarem melhores experiências do usuário.

O diagrama e a tabela a seguir fornecem informações sobre os blocos de construção do prompt de consentimento.

![Blocos de construção da solicitação de consentimento](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Objetivo |
| ----- | ----- | ----- |
| 1 | Identificador de usuário | Esse identificador representa o usuário que o aplicativo cliente está solicitando para acessar recursos protegidos em nome de. |
| 2 | Cargo | O título é alterado com base em se os usuários estão passando pelo fluxo de consentimento do usuário ou do administrador. No fluxo de consentimento do usuário, o título será "permissões solicitadas" enquanto estiver no fluxo de consentimento do administrador, o título terá uma linha adicional "aceitar para sua organização". |
| 3 | Logotipo do aplicativo | Essa imagem deve ajudar os usuários a ter uma indicação visual de se esse aplicativo é o aplicativo que pretende acessar. Essa imagem é fornecida por desenvolvedores de aplicativos e a propriedade dessa imagem não é validada. |
| 4 | Nome da aplicação | Esse valor deve informar aos usuários qual aplicativo está solicitando acesso aos seus dados. Observe que esse nome é fornecido pelos desenvolvedores e a propriedade desse nome de aplicativo não é validada. |
| 5 | Domínio de publicador | Esse valor deve fornecer aos usuários um domínio que eles podem ser capazes de avaliar a confiabilidade. Esse domínio é fornecido pelos desenvolvedores e a propriedade desse domínio do Publicador é validada. |
| 6 | Permissões | Essa lista contém as permissões que estão sendo solicitadas pelo aplicativo cliente. Os usuários sempre devem avaliar os tipos de permissões que estão sendo solicitadas para entender quais dados o aplicativo cliente será autorizado a acessar em seu nome, se eles aceitarem. Como um desenvolvedor de aplicativos, é melhor solicitar acesso, às permissões com privilégios mínimos. |
| 7 | Descrição da permissão | Esse valor é fornecido pelo serviço expondo as permissões. Para ver as descrições de permissão, você deve alternar a divisa ao lado da permissão. |
| 8 | Termos do aplicativo | Esses termos contêm links para os termos de serviço e a política de privacidade do aplicativo. O Publicador é responsável por estruturar suas regras em seus termos de serviço. Além disso, o Publicador é responsável por revelar a maneira como eles usam e compartilhar dados de usuário em sua política de privacidade. Se o Publicador não fornecer links para esses valores para aplicativos multilocatário, haverá um aviso em negrito no prompt de consentimento. |
| 9 | https://myapps.microsoft.com | Esse é o link onde os usuários podem revisar e remover qualquer aplicativo que não seja da Microsoft que tenha acesso aos seus dados no momento. |

## <a name="common-consent-scenarios"></a>Cenários comuns de autorização

Aqui estão as experiências de consentimento que um usuário pode ver nos cenários comuns de consentimento:

1. Indivíduos que acessam um aplicativo que os direciona para o fluxo de autorização do usuário, exigindo um conjunto de permissões que esteja dentro de seu escopo de autoridade.
    
    1. Os administradores verão um controle adicional na solicitação de consentimento tradicional que lhes permitirá consentir em nome do locatário inteiro. O controle será padronizado como desativado, portanto, somente quando os administradores marcarem explicitamente a caixa, o consentimento será concedido em nome do locatário inteiro. A partir de hoje, essa caixa de seleção só será mostrada para a função de administrador global, portanto, o administrador de nuvem e o administrador de aplicativos não verão essa caixa de seleção.

        ![Prompt de consentimento para o cenário 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Os usuários verão o prompt de consentimento tradicional.

        ![Prompt de consentimento para o cenário 1B](./media/application-consent-experience/consent_prompt_1b.png)

2. Pessoas que acessam um aplicativo que exige pelo menos uma permissão que está fora de seu escopo de autoridade.
    1. Os administradores verão a mesma solicitação que 1. eu mostrei acima.
    2. Os usuários serão impedidos de conceder consentimento ao aplicativo e serão instruídos a solicitar o acesso do seu administrador ao aplicativo. 
                
        ![Prompt de consentimento para o cenário 1B](./media/application-consent-experience/consent_prompt_2b.png)

3. Indivíduos que navegam ou são direcionados para o fluxo de consentimento do administrador.
    1. Os usuários administradores verão o prompt de consentimento do administrador. O título e as descrições de permissão foram alteradas nesse prompt, as alterações realçam o fato de que a aceitação desse prompt concederá ao aplicativo acesso aos dados solicitados em nome do locatário inteiro.
        
        ![Prompt de consentimento para o cenário 1B](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Os usuários não administradores verão a mesma tela que 2. II mostrada acima.

## <a name="next-steps"></a>Passos seguintes
- Obtenha uma visão geral passo a passo de [como a estrutura de consentimento do Azure ad implementa o consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Para obter mais detalhes, saiba [como um aplicativo multilocatário pode usar a estrutura de consentimento](active-directory-devhowto-multi-tenant-overview.md) para implementar o consentimento de "usuário" e "administrador", dando suporte a padrões de aplicativos multicamadas mais avançados.
- Saiba [como configurar o domínio do Publicador do aplicativo](howto-configure-publisher-domain.md).
