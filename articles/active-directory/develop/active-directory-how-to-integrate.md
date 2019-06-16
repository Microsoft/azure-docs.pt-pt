---
title: Como integrar com o Azure Active Directory | Documentos da Microsoft
description: Conheça as vantagens de integrar a sua aplicação com o Azure Active Directory e obtenha recursos para funcionalidades como a gestão simplificada de início de sessão, identidade, autenticação multifator e o controlo de acesso.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4af8cdc1072678de7e34458fc4b54d52aae9eff1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111602"
---
# <a name="integrating-with-azure-active-directory"></a>Integração com o Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Neste artigo, saiba mais sobre os benefícios da integração da sua aplicação com o Azure Active Directory (Azure AD) e obtenha recursos para a integração. O Azure AD fornece as organizações com gestão de identidades de nível empresarial para aplicações na cloud. Integração do Azure AD fornece aos utilizadores uma experiência de início de sessão simplificada e ajuda a sua aplicação está em conformidade com a política de TI.

## <a name="how-to-integrate"></a>Como integrar

Existem várias formas para a sua aplicação integrar com o Azure AD. Tire partido de tantas ou, no mínimo um destes cenários, conforme apropriado para a sua aplicação.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Suporte do Azure AD como uma forma de iniciar sessão na sua aplicação

**Reduza a fricção de início de sessão e reduzir os custos de suporte.** Ao utilizar o Azure AD para iniciar sessão na sua aplicação, os utilizadores não terão um nome mais e a palavra-passe para se lembrar. Como desenvolvedor, terá uma menos palavra-passe para armazenar e proteger. Não ter de lidar com redefinições de senha esquecida pode ser uma economia significativa sozinha. Azure AD alimenta o início de sessão para algumas das mais populares aplicações na cloud do mundo, incluindo o Office 365 e o Microsoft Azure. Com centenas de milhões utilizadores de milhões de organizações, as chances são de seu usuário já tem sessão iniciado no Azure AD. Saiba mais sobre [adicionando suporte para início de sessão do Azure AD](authentication-scenarios.md).

**Simplificar o início de sessão cópia de segurança para a sua aplicação.**  Durante a inscrição para a sua aplicação do Azure AD pode enviar informações essenciais sobre um usuário para que possa preencher previamente a formulário de inscrição ou eliminá-la completamente. Os utilizadores podem inscrever-se para a sua aplicação com a respetiva conta do Azure AD através de uma experiência de consentimento familiares semelhante àquelas encontradas em aplicações móveis e de redes sociais. Qualquer utilizador pode inscrever-se e iniciar sessão a uma aplicação que está integrada com o Azure AD sem a necessidade de envolvimento de TI. Saiba mais sobre [inscrever-se a sua aplicação para início de sessão de conta do Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Navegue para os utilizadores, gerir o aprovisionamento de utilizadores e controlar o acesso à sua aplicação

**Navegue para os utilizadores no diretório.**  Utilizar a Graph API para ajudar os utilizadores procurar e navegue para outras pessoas na sua organização ao convidar outras pessoas ou endereços de conceder acesso, em vez de exigi-los para o tipo de e-mail. Os utilizadores podem procurar através de uma interface de estilo de livro endereço familiares, incluindo ver os detalhes da hierarquia organizacional. Saiba mais sobre o [Graph API](active-directory-graph-api.md).

**Utilize novamente a grupos do Active Directory e listas de distribuição que já está a gerir o seu cliente.**  O Azure AD contém os grupos que seu cliente já está a utilizar para a distribuição de e-mail e gerir o acesso. Novamente com a Graph API, utilize estes grupos em vez de exigir o seu cliente criar e gerir um conjunto separado de grupos em seu aplicativo. Informações de grupo também podem ser enviadas ao seu aplicativo no início de sessão nos tokens. Saiba mais sobre o [Graph API](active-directory-graph-api.md).

**Utilize o Azure AD para controlar quem tem acesso à sua aplicação.**  Os administradores e os proprietários da aplicação no Azure AD podem atribuir acesso a aplicações para utilizadores e grupos específicos. Com a Graph API, pode ler esta lista e utilizá-lo para controlar o provisionamento e desprovisionamento de recursos e de acesso na sua aplicação.

**Utilize o Azure AD para funções de controlo de acesso baseado em.**  Os administradores e os proprietários de aplicativos podem atribuir utilizadores e grupos a funções que definem quando registar a sua aplicação no Azure AD. Informações de função são enviadas ao seu aplicativo no tokens de início de sessão e também podem ser lidos com a Graph API. Saiba mais sobre [utilizar o Azure AD para autorização](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obtenha acesso do utilizador perfil, calendário, correio eletrónico, contactos, ficheiros e muito mais

**O Azure AD é o servidor de autorização para o Office 365 e outros serviços empresariais da Microsoft.**  Se o suporte do Azure AD para início de sessão para a sua aplicação ou o suporte de ligação suas contas de utilizador atual para contas de utilizador do Azure AD com o OAuth 2.0, pode pedir uma leitura e de acesso de gravação a um perfil de usuário, calendário, correio eletrónico, contactos, ficheiros e outras informações. Pode facilmente escrever eventos no calendário do usuário e ler ou gravar arquivos no seu OneDrive. Saiba mais sobre [aceder a APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promova a sua aplicação no Azure e do Office 365 Marketplaces

**Promova a sua aplicação a milhões de organizações que já estão a utilizar o Azure AD.**  Os utilizadores que pesquisam e procurar esses mercados já estiver a utilizar um ou mais serviços cloud, tornando-os qualificado clientes do serviço cloud. Saiba mais sobre como promover a sua aplicação num [no Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os utilizadores inscrever-se para a sua aplicação, irá aparecer no seu painel de acesso do Azure AD e o iniciador de aplicações do Office 365.**  Os utilizadores serão capazes de forma rápida e fácil regressar à sua aplicação mais tarde, melhorando a interação do utilizador. Saiba mais sobre o [painel de acesso do Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Proteger a comunicação de serviço de dispositivos e de serviços

**Utilizar o Azure AD para a gestão de identidades de dispositivos e serviços reduz o código que necessário para escrever e permite que as TI para gerir o acesso.**  Serviços e dispositivos, podem obter os tokens do Azure AD através de OAuth e utilizar esses tokens para aceder a web APIs. Utilizar o Azure AD pode evitar escrever código de autenticação complexa. Uma vez que as identidades dos serviços e dispositivos são armazenadas no Azure AD, IT pode gerir as chaves e a revogação num único local em vez de precisar fazê-lo em separado na sua aplicação.

## <a name="benefits-of-integration"></a>Vantagens da integração

Integração com o Azure AD é fornecido com os benefícios que não necessitam de escrever código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com o gerenciamento de identidades empresariais

**Ajudar a sua aplicação está em conformidade com as políticas de TI.**  As organizações a integram seus sistemas de gestão de identidade empresarial no Azure AD, para quando uma pessoa sai de uma organização, eles serão automaticamente perder o acesso à sua aplicação sem terem de seguir passos adicionais de IT. IT pode gerir quem pode aceder à sua aplicação e determinar que políticas de acesso são necessárias - exemplo multi-factor Authentication - reduzindo a necessidade de escrever código para estar em conformidade com as políticas empresariais complexas. O Azure AD fornece aos administradores um log de auditoria detalhados de que iniciou sessão no seu aplicativo então IT pode controlar a utilização.

**O Azure AD amplia o Active Directory para a cloud, para que seu aplicativo pode integrar com o AD.**  Muitas organizações em todo o mundo utilizam o Active Directory como seus o início de sessão principal e o sistema de gestão de identidades e necessitam de seus aplicativos para funcionarem com o AD. Integração com o Azure AD integra-se a aplicação com o Active Directory.

### <a name="advanced-security-features"></a>Funcionalidades de segurança avançadas

**Autenticação multifator.**  O Azure AD fornece nativo multi-factor authentication. Os administradores de TI podem exigir a autenticação multifator para aceder à sua aplicação, para que não é necessário código este suporte sozinho. Saiba mais sobre [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Início de sessão anómalos em deteção.**  O Azure AD processa mais de um bilhão-inícios de sessão por dia, ao utilizar os algoritmos de machine learning para detetar atividade suspeita e notificar os administradores de TI de possíveis problemas. Ao suportar o início de sessão no Azure AD, a aplicação obtém o benefício dessa proteção. Saiba mais sobre [visualizar o relatório de acesso do Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Para além da autenticação multifator, os administradores podem exigir condições específicas de ser cumpridos antes dos utilizadores podem iniciar sessão no seu aplicativo. As condições que podem ser definidas incluem o intervalo de endereços IP de dispositivos de cliente, a associação a grupos especificados e o estado do dispositivo que está a ser utilizado para acesso. Saiba mais sobre [do Azure Active Directory condicional acesso](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Fácil desenvolvimento

**Protocolos de norma da indústria.**  A Microsoft está empenhada em apoiar a padrões da indústria. A plataforma de identidade da Microsoft suporta os protocolos padrão da indústria de OAuth 2.0 e OpenID Connect 1.0. Saiba mais sobre [protocolos de autenticação do Microsoft identity platform](active-directory-v2-protocols.md).

**Bibliotecas de código-fonte aberto.**  A Microsoft fornece as bibliotecas de código-fonte aberto com suporte completo para idiomas populares e plataformas para acelerar o desenvolvimento. O código-fonte é licenciado ao abrigo do Apache 2.0, e é livre para bifurcar e contribuir para os projetos. Saiba mais sobre [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença em todo o mundo e de elevada disponibilidade

**O Azure AD é implementado em datacenters em todo o mundo e é gerido e monitorizado ininterruptamente.**  Azure AD é o sistema de gestão de identidade do Microsoft Azure e do Office 365 e é implementado em 28 datacenters em todo o mundo. Dados do diretório são garantidos a ser replicados para os datacenters, pelo menos, três. Balanceadores de carga global Certifique-se os utilizadores acedam a cópia mais próxima do Azure AD que contém os seus dados e automaticamente novamente encaminham os pedidos para noutros datacenters se for detetado um problema.

## <a name="next-steps"></a>Passos Seguintes

[Começar a escrever código](v2-overview.md#getting-started).

[Iniciar sessão dos utilizadores com a plataforma de identidade da Microsoft](authentication-scenarios.md)

