---
title: Como integrar-se com o Azure Ative Directory
description: Aprenda os benefícios de integrar a sua aplicação com o Azure Ative Directory e obtenha recursos para funcionalidades como sing-in simplificado, gestão de identidade, autenticação de vários fatores e controlo de acesso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 4eaca7350ac006a2fcf2eed8d3eb0cb8ef274841
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885706"
---
# <a name="integrating-with-azure-active-directory"></a>Integrar no Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Neste artigo, aprende sobre os benefícios de integrar a sua aplicação com o Azure Ative Directory (Azure AD) e obter recursos para integração. A Azure AD fornece às organizações uma gestão de identidade de nível empresarial para aplicações em nuvem. A integração AD AD dá aos seus utilizadores uma experiência de inscrição simplificada e ajuda a sua aplicação a cumprir a política de TI.

## <a name="how-to-integrate"></a>Como integrar

Existem várias formas de a sua aplicação se integrar com a Azure AD. Aproveite quantos ou poucos destes cenários for apropriado para a sua aplicação.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Apoiar a Azure AD como forma de iniciar seduca na sua aplicação

**Reduza o sinal de atrito e reduza os custos de suporte.** Ao utilizar o Azure AD para iniciar sação na sua aplicação, os seus utilizadores não terão mais um nome e senha para se lembrarem. Como desenvolvedor, terá uma senha a menos para armazenar e proteger. Não ter de lidar com resets de palavras-passe esquecidos pode ser uma poupança significativa por si só. Os poderes da AD AD azure insinuam-se em algumas das aplicações em nuvem mais populares do mundo, incluindo o Office 365 e o Microsoft Azure. Com centenas de milhões de utilizadores de milhões de organizações, é provável que o seu utilizador já esteja inscrito no Azure AD. Saiba mais sobre [a adição de suporte para o sinal AZure AD em](authentication-scenarios.md).

**Simplifique o seu pedido.**  Durante o pré-inscrição para a sua aplicação, o Azure AD pode enviar informações essenciais sobre um utilizador para que possa pré-preencher o formulário de inscrição ou eliminá-lo completamente. Os utilizadores podem inscrever-se na sua aplicação utilizando a sua conta Azure AD através de uma experiência de consentimento familiar semelhante à encontrada nas redes sociais e aplicações móveis. Qualquer utilizador pode inscrever-se e inscrever-se numa aplicação que está integrada com a Azure AD sem necessitar de envolvimento de TI. Saiba mais sobre [a inscrição na sua candidatura para o login da Conta AD Azure.](../../app-service/configure-authentication-provider-aad.md)

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Navegue para utilizadores, gerencie o fornecimento de utilizadores e controle o acesso à sua aplicação

**Navegue para os utilizadores no diretório.**  Utilize a API do Microsoft Graph para ajudar os utilizadores a procurar e procurar outras pessoas na sua organização quando convidam outras pessoas ou concedem acesso, em vez de os exigir a escrever endereços de e-mail. Os utilizadores podem navegar usando uma interface familiar do estilo do livro de endereços, incluindo visualizar os detalhes da hierarquia organizacional. Saiba mais sobre a [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Reutilizar grupos de Diretório Ativo e listas de distribuição que o seu cliente já está a gerir.**  O Azure AD contém os grupos que o seu cliente já está a usar para distribuição de email e gestão de acesso. Utilizando a API do Gráfico microsoft, reutilizar estes grupos em vez de exigir que o seu cliente crie e gere um conjunto separado de grupos na sua aplicação. As informações do grupo também podem ser enviadas para o seu pedido em sinal de tokens. Saiba mais sobre a [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Utilize a Azure AD para controlar quem tem acesso à sua aplicação.**  Os administradores e proprietários de aplicações em Azure AD podem atribuir acesso a aplicações a utilizadores e grupos específicos. Utilizando a API do Gráfico microsoft, pode ler esta lista e usá-la para controlar o fornecimento e desavisionamento de recursos e acesso dentro da sua aplicação.

**Utilize Azure AD para funções baseados no controlo de acesso.**  Os administradores e os proprietários de aplicações podem atribuir utilizadores e grupos às funções que define quando regista a sua aplicação no Azure AD. As informações de função são enviadas para a sua aplicação em tokens de sinal e também podem ser lidas usando a API do Gráfico microsoft. Saiba mais sobre [a utilização do Azure AD para autorização.](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Tenha acesso ao perfil do utilizador, calendário, e-mail, contactos, ficheiros e muito mais

**Azure AD é o servidor de autorização do Office 365 e outros serviços comerciais da Microsoft.**  Se apoiar o Azure AD para iniciar sessão na sua aplicação ou suporte que ligue as suas contas de utilizador corrente às contas de utilizadores da Azure AD utilizando o OAuth 2.0, pode solicitar a leitura e escrever acesso ao perfil de um utilizador, calendário, e-mail, contactos, ficheiros e outras informações. Pode escrever eventos perfeitamente para o calendário do utilizador e ler ou escrever ficheiros para o seu OneDrive. Saiba mais sobre [o acesso às APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promover a sua candidatura nos Mercados Azure e Office 365

**Promova a sua aplicação aos milhões de organizações que já utilizam a Azure AD.**  Os utilizadores que pesquisam e navegam nestes mercados já estão a utilizar um ou mais serviços na nuvem, tornando-os clientes qualificados de serviços de nuvem. Saiba mais sobre a promoção da sua candidatura [no Azure Marketplace.](https://azure.microsoft.com/marketplace/partner-program/)

**Quando os utilizadores se inscreverem para a sua aplicação, aparecerá no painel de acesso AD AZure e no launcher da aplicação Office 365.**  Os utilizadores poderão voltar de forma rápida e fácil à sua aplicação mais tarde, melhorando o envolvimento dos utilizadores. Saiba mais sobre o [painel de acesso AZure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura de dispositivo a serviço e serviço-a-serviço

**A utilização do Azure AD para a gestão de identidade de serviços e dispositivos reduz o código que precisa de escrever e permite que a TI gere o acesso.**  Os serviços e dispositivos podem obter fichas do Azure AD usando OAuth e usar esses tokens para aceder a APIs web. Utilizando a Azure AD pode evitar escrever código de autenticação complexo. Uma vez que as identidades dos serviços e dispositivos são armazenados no Azure AD, o TI pode gerir as chaves e a revogação num só local em vez de ter de o fazer separadamente na sua aplicação.

## <a name="benefits-of-integration"></a>Benefícios da integração

A integração com o Azure AD vem com benefícios que não exigem que você escreva código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com a gestão de identidade empresarial

**Ajude a sua aplicação a cumprir as políticas de TI.**  As organizações integram os seus sistemas de gestão de identidade empresarial com a Azure AD, por isso, quando uma pessoa deixa uma organização, perderá automaticamente o acesso à sua aplicação sem que a TI precise de tomar medidas adicionais. O TI pode gerir quem pode aceder à sua aplicação e determinar quais as políticas de acesso necessárias - por exemplo, a autenticação de vários fatores - reduzindo a sua necessidade de escrever código para cumprir as complexas políticas corporativas. A Azure AD fornece aos administradores um registo de auditoria detalhado de quem assinou a sua aplicação para que a TI possa acompanhar o uso.

**A Azure AD estende o Ative Directory à nuvem para que a sua aplicação possa integrar-se com AD.**  Muitas organizações em todo o mundo usam o Ative Directory como o seu principal sistema de gestão de assinaturas e identidades, e exigem que as suas aplicações trabalhem com a AD. A integração com o Azure AD integra a sua app com o Ative Directory.

### <a name="advanced-security-features"></a>Recursos avançados de segurança

**Autenticação multi-factor.**  A Azure AD fornece autenticação de vários fatores nativos. Os administradores de TI podem exigir a autenticação de vários fatores para aceder à sua aplicação, para que não tenha de codificar este suporte por si mesmo. Saiba mais sobre [a Autenticação Multi-Factor.](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)

**Sinal anómalo na deteção.**  O Azure AD processa mais de mil milhões de inscrições por dia, enquanto utiliza algoritmos de aprendizagem automática para detetar atividades suspeitas e notificar os administradores de TI de possíveis problemas. Ao apoiar a inscrição do Azure AD, a sua aplicação beneficia desta proteção. Saiba mais sobre [a visualização do relatório de acesso ao Azure Ative Directory](../active-directory-view-access-usage-reports.md).

**Acesso Condicional.**  Além da autenticação multi-fator, os administradores podem exigir condições específicas antes de os utilizadores poderem iniciar sação na sua aplicação. As condições que podem ser definidas incluem a gama de endereços IP de dispositivos clientes, a adesão em grupos especificados e o estado do dispositivo a ser utilizado para acesso. Saiba mais sobre [o Acesso Condicional do Diretório Ativo Azure](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão da indústria.**  A Microsoft está empenhada em apoiar os padrões da indústria. A plataforma de identidade da Microsoft suporta os protocolos OAuth 2.0 e OpenID Connect 1.0 padrão da indústria. Saiba mais sobre [os protocolos de autenticação da plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

**Bibliotecas de código aberto.**  A Microsoft fornece bibliotecas de código aberto totalmente suportadas para idiomas e plataformas populares para acelerar o desenvolvimento. O código fonte é licenciado sob Apache 2.0, e você é livre para fork e contribuir de volta para os projetos. Saiba mais sobre [a Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e alta disponibilidade

**O Azure AD é implantado em datacenters em todo o mundo e é gerido e monitorizado 24 horas por dia.**  Azure AD é o sistema de gestão de identidade do Microsoft Azure e do Office 365 e está implantado em 28 datacenters em todo o mundo. É garantido que os dados do diretório sejam replicados em pelo menos três centros de dados. Os equilibradores de carga globais garantem que os utilizadores acedem à cópia mais próxima do AZure AD contendo os seus dados e reencaminham automaticamente os pedidos para outros centros de dados se for detetado um problema.

## <a name="next-steps"></a>Próximos passos

[Começa a escrever código.](v2-overview.md#getting-started)

[Inscreva utilizadores na utilização da plataforma de identidade microsoft](authentication-scenarios.md)

