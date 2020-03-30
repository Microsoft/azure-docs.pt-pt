---
title: Como integrar-se com o Diretório Ativo Azure [ Microsoft Docs
description: Conheça os benefícios de integrar a sua aplicação com o Azure Ative Directory e obtenha recursos para funcionalidades como entrada simplificada, gestão de identidade, autenticação multifactor e controlo de acesso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 5e4c6fb91b93b1035c68350a5f77a3c16db7323f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300117"
---
# <a name="integrating-with-azure-active-directory"></a>Integração com Diretório Ativo Azure

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Neste artigo, você aprende sobre os benefícios de integrar a sua aplicação com o Azure Ative Directory (Azure AD) e obter recursos para integração. A Azure AD fornece às organizações uma gestão de identidade de nível empresarial para aplicações na nuvem. A integração do Azure AD proporciona aos seus utilizadores uma experiência de entrada simplificada e ajuda a sua aplicação a conformar-se com a política de TI.

## <a name="how-to-integrate"></a>Como integrar

Existem várias formas de a sua aplicação se integrar com a Azure AD. Aproveite o máximo ou o que for adequado para a sua aplicação.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Apoie a AD Azure como forma de iniciar sessão na sua candidatura

**Reduza o sinal de atrito e reduza os custos de suporte.** Ao utilizar o Azure AD para iniciar sessão na sua aplicação, os seus utilizadores não terão mais um nome e senha para recordar. Como desenvolvedor, terá uma palavra-passe a menos para armazenar e proteger. Não ter de lidar com resets de senhas esquecidas pode ser uma poupança significativa por si só. Os poderes da Azure AD assinam algumas das aplicações de nuvem mais populares do mundo, incluindo o Office 365 e o Microsoft Azure. Com centenas de milhões de utilizadores de milhões de organizações, é provável que o seu utilizador já esteja inscrito no Azure AD. Saiba mais sobre [adicionar suporte para o signo da AD Azure .](authentication-scenarios.md)

**Simplifique a inscrição para a sua candidatura.**  Durante a inscrição para a sua aplicação, o Azure AD pode enviar informações essenciais sobre um utilizador para que possa pré-preencher o formulário de inscrição ou eliminá-lo completamente. Os utilizadores podem inscrever-se na sua aplicação através da sua conta Azure AD através de uma experiência de consentimento familiar semelhante à encontrada nas redes sociais e aplicações móveis. Qualquer utilizador pode inscrever-se e iniciar sessão numa aplicação integrada com a Azure AD sem necessitar de envolvimento de TI. Saiba mais sobre [a inscrição da sua candidatura para login da Conta AD Azure](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procure por utilizadores, gerencie o fornecimento de utilizadores e controle o acesso à sua aplicação

**Procure os utilizadores no diretório.**  Utilize a API do Microsoft Graph para ajudar os utilizadores a pesquisar e navegar por outras pessoas na sua organização ao convidar outras pessoas ou conceder acesso, em vez de exigir que escrevam endereços de e-mail. Os utilizadores podem navegar usando uma interface familiar de estilo de livro de endereços, incluindo visualizar os detalhes da hierarquia organizacional. Saiba mais sobre a [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Reutilizar os grupos de Diretório Ativo e listas de distribuição que o seu cliente já está a gerir.**  A Azure AD contém os grupos que o seu cliente já está a utilizar para distribuição de email e gestão de acesso. Utilizando o Microsoft Graph API, reutilize estes grupos em vez de exigir que o seu cliente crie e gere um conjunto separado de grupos na sua aplicação. As informações do grupo também podem ser enviadas para a sua aplicação em fichas de inscrição. Saiba mais sobre a [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Utilize a AD Azure para controlar quem tem acesso à sua aplicação.**  Administradores e proprietários de aplicações na AD Azure podem atribuir acesso a aplicações a utilizadores e grupos específicos. Utilizando a API do Microsoft Graph, pode ler esta lista e usá-la para controlar o fornecimento e desprovisionamento de recursos e acesso dentro da sua aplicação.

**Utilize o Azure AD para controlo de acesso baseado em funções.**  Os administradores e proprietários de aplicações podem atribuir utilizadores e grupos a funções que define quando regista a sua aplicação em Azure AD. As informações sobre as funções são enviadas para a sua aplicação em fichas de sessão e também podem ser lidas através da Microsoft Graph API. Saiba mais sobre a utilização de [Anúncios Azure para autorização.](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Tenha acesso ao perfil do utilizador, calendário, e-mail, contactos, ficheiros e muito mais

**A Azure AD é o servidor de autorização do Office 365 e de outros serviços empresariais da Microsoft.**  Se apoiar o Azure AD para iniciar sessão na sua aplicação ou suporte que ligue as suas contas de utilizador correntes a contas de utilizadores DaA Azure utilizando o OAuth 2.0, pode solicitar a leitura e o acesso ao perfil de um utilizador, calendário, e-mail, contactos, ficheiros e outras informações. Pode escrever eventos sem problemas no calendário do utilizador e ler ou escrever ficheiros para o seu OneDrive. Saiba mais sobre [o acesso ao Office 365 APIs](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promova a sua candidatura nos Mercados Azure e Office 365

**Promova a sua aplicação aos milhões de organizações que já estão a utilizar o Azure AD.**  Os utilizadores que pesquisam e navegam nestes mercados já estão a utilizar um ou mais serviços na nuvem, tornando-os clientes qualificados de serviços na nuvem. Saiba mais sobre a promoção da sua aplicação [no Azure Marketplace.](https://azure.microsoft.com/marketplace/partner-program/)

**Quando os utilizadores se inscreverem para a sua aplicação, esta aparecerá no seu painel de acesso Azure AD e no launcher da aplicação Office 365.**  Os utilizadores poderão voltar à sua aplicação de forma rápida e fácil, melhorando o envolvimento do utilizador. Saiba mais sobre o [painel de acesso azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação de dispositivo-serviço seguro e serviço-a-serviço

**A utilização da Azure AD para a gestão de identidade de serviços e dispositivos reduz o código que precisa para escrever e permite que a TI gere o acesso.**  Os serviços e dispositivos podem obter fichas da Azure AD usando a OAuth e usar esses tokens para aceder a APIs web. Utilizando o Azure AD, pode evitar escrever código de autenticação complexo. Uma vez que as identidades dos serviços e dispositivos estão armazenadas em Azure AD, a TI pode gerir chaves e revogação num só local em vez de ter de o fazer separadamente na sua aplicação.

## <a name="benefits-of-integration"></a>Benefícios da integração

A integração com a Azure AD vem com benefícios que não exigem que escreva código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com a gestão de identidade empresarial

**Ajude a sua aplicação a cumprir as políticas de TI.**  As organizações integram os seus sistemas de gestão de identidade empresarial com a Azure AD, pelo que quando uma pessoa deixa uma organização, perderá automaticamente o acesso à sua aplicação sem que a TI precise de tomar medidas adicionais. O TI pode gerir quem pode aceder à sua aplicação e determinar quais as políticas de acesso necessárias - por exemplo, a autenticação de vários fatores - reduzindo a sua necessidade de escrever código para cumprir com políticas corporativas complexas. A Azure AD fornece aos administradores um registo de auditoria detalhado de quem assinou a sua aplicação para que a TI possa acompanhar o uso.

**A Azure AD estende o Diretório Ativo à nuvem para que a sua aplicação possa integrar-se com a AD.**  Muitas organizações em todo o mundo usam o Ative Directory como o seu principal sistema de sign-in e gestão de identidade, e exigem que as suas aplicações trabalhem com a AD. Integrando com a Azure AD integra a sua app com diretório ativo.

### <a name="advanced-security-features"></a>Funcionalidades avançadas de segurança

**Autenticação multi-factor.**  A Azure AD fornece autenticação nativa de vários fatores. Os administradores de TI podem exigir a autenticação de vários fatores para aceder à sua aplicação, para que não tenha de codificar este suporte por si mesmo. Saiba mais sobre [a autenticação multi-factor.](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)

**Sinal anómalo na deteção.**  A Azure AD processa mais de mil milhões de inscrições por dia, enquanto utiliza algoritmos de aprendizagem automática para detetar atividades suspeitas e notificar os administradores de TI de possíveis problemas. Ao apoiar o sign-in Azure AD, a sua aplicação obtém o benefício desta proteção. Saiba mais sobre a visualização do relatório de acesso ao [Diretório Ativo azure.](../active-directory-view-access-usage-reports.md)

**Acesso Condicional.**  Além da autenticação de vários fatores, os administradores podem exigir que sejam satisfeitas condições específicas antes de os utilizadores poderem iniciar sessão na sua aplicação. As condições que podem ser definidas incluem a gama de endereços IP dos dispositivos clientes, a adesão em grupos especificados e o estado do dispositivo a ser utilizado para acesso. Saiba mais sobre o Acesso Condicional do [Diretório Ativo Azure.](../active-directory-conditional-access-azure-portal.md)

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão da indústria.**  A Microsoft está empenhada em apoiar os padrões da indústria. A plataforma de identidade da Microsoft suporta os protocolos OAuth 2.0 e OpenID Connect 1.0. Saiba mais sobre os protocolos de [autenticação da plataforma de identidade microsoft.](active-directory-v2-protocols.md)

**Bibliotecas de código aberto.**  A Microsoft fornece bibliotecas de código aberto totalmente suportadas para idiomas e plataformas populares para acelerar o desenvolvimento. O código fonte é licenciado ao abrigo do Apache 2.0, e você é livre de bifurcar e contribuir de volta para os projetos. Saiba mais sobre a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e elevada disponibilidade

**A Azure AD é implantada em datacenters em todo o mundo e é gerida e monitorizada 24 horas por dia.**  A Azure AD é o sistema de gestão de identidade saca do Microsoft Azure e office 365 e está implantado em 28 datacenters em todo o mundo. Os dados do diretório são garantidos para serem replicados em pelo menos três datacenters. Os equilibradores globais de carga garantem que os utilizadores acedem à cópia mais próxima da AD Azure contendo os seus dados e redirecionam automaticamente os pedidos para outros centros de dados se for detetado um problema.

## <a name="next-steps"></a>Passos seguintes

[Começar a escrever código.](v2-overview.md#getting-started)

[Inscreva utilizadores na utilização da plataforma de identidade da Microsoft](authentication-scenarios.md)

