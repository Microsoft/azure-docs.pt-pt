---
title: Como integrar com o Azure Active Directory | Microsoft Docs
description: Conheça os benefícios de integrar seu aplicativo com o Azure Active Directory e obtenha recursos para recursos como entrada simplificada, gerenciamento de identidades, autenticação multifator e controle de acesso.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48be16827da21643af485d1fb94b9a7b76728325
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374039"
---
# <a name="integrating-with-azure-active-directory"></a>Integração com o Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Neste artigo, você aprende sobre os benefícios de integrar seu aplicativo com o Azure Active Directory (Azure AD) e obter recursos de integração. O Azure AD fornece às organizações o gerenciamento de identidades de nível empresarial para aplicativos em nuvem. A integração do AD do Azure oferece aos usuários uma experiência de entrada simplificada e ajuda seu aplicativo a estar em conformidade com a política de ti.

## <a name="how-to-integrate"></a>Como integrar

Há várias maneiras para seu aplicativo se integrar ao Azure AD. Aproveite o maior ou menor número desses cenários, conforme apropriado para seu aplicativo.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Suporte ao Azure AD como uma maneira de entrar em seu aplicativo

**Reduzir o conflito de entrada e reduzir os custos de suporte.** Usando o Azure AD para entrar em seu aplicativo, os usuários não terão mais um nome e senha para se lembrar. Como desenvolvedor, você terá uma senha a menos para armazenar e proteger. Não ter que lidar com redefinições de senha esquecida pode ser apenas uma economia significativa. O Azure AD liga para alguns dos aplicativos em nuvem mais populares do mundo, incluindo o Office 365 e Microsoft Azure. Com centenas de milhões de usuários de milhões de organizações, é provável que seu usuário já esteja conectado ao Azure AD. Saiba mais sobre como [Adicionar suporte para entrar no Azure ad](v1-authentication-scenarios.md).

**Simplifique a inscrição para seu aplicativo.**  Durante a inscrição para seu aplicativo, o Azure AD pode enviar informações essenciais sobre um usuário para que você possa preencher previamente seu formulário de inscrição ou eliminá-lo completamente. Os usuários podem se inscrever no seu aplicativo usando sua conta do Azure AD por meio de uma experiência de consentimento familiar semelhante àquelas encontradas em mídia social e aplicativos móveis. Qualquer usuário pode se inscrever e entrar em um aplicativo integrado ao Azure AD sem a necessidade de envolvimento de ti. Saiba mais sobre como [inscrever seu aplicativo para o logon da conta do Azure ad](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procurar usuários, gerenciar o provisionamento de usuários e controlar o acesso ao seu aplicativo

**Procure usuários no diretório.**  Use o API do Graph para ajudar os usuários a Pesquisar e procurar outras pessoas em sua organização ao convidar outros ou conceder acesso, em vez de exigir que eles digitem endereços de email. Os usuários podem navegar usando uma interface familiar de estilo de catálogo de endereços, incluindo a exibição dos detalhes da hierarquia organizacional. Saiba mais sobre o [API do Graph](active-directory-graph-api.md).

**Reuse Active Directory grupos e listas de distribuição que seu cliente já está gerenciando.**  O AD do Azure contém os grupos que o cliente já está usando para distribuição de email e gerenciamento de acesso. Usando o API do Graph, reutilize esses grupos em vez de exigir que o cliente crie e gerencie um conjunto separado de grupos em seu aplicativo. As informações do grupo também podem ser enviadas para seu aplicativo em tokens de entrada. Saiba mais sobre o [API do Graph](active-directory-graph-api.md).

**Use o Azure AD para controlar quem tem acesso ao seu aplicativo.**  Os administradores e os proprietários de aplicativos no Azure AD podem atribuir acesso a aplicativos a usuários e grupos específicos. Usando o API do Graph, você pode ler essa lista e usá-la para controlar o provisionamento e desprovisionamento de recursos e o acesso em seu aplicativo.

**Use o Azure AD para controle de acesso baseado em funções.**  Os administradores e os proprietários de aplicativos podem atribuir usuários e grupos a funções que você define ao registrar seu aplicativo no Azure AD. As informações de função são enviadas para seu aplicativo em tokens de entrada e também podem ser lidas usando o API do Graph. Saiba mais sobre como [usar o Azure ad para autorização](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obtenha acesso ao perfil do usuário, calendário, email, contatos, arquivos e muito mais

**O Azure AD é o servidor de autorização para o Office 365 e outros serviços comerciais da Microsoft.**  Se você oferecer suporte ao Azure AD para entrar no seu aplicativo ou oferecer suporte à vinculação de suas contas de usuário atuais às contas de usuário do Azure AD usando o OAuth 2,0, você poderá solicitar acesso de leitura e gravação para o perfil, calendário, email, contatos, arquivos e outras informações de um usuário. Você pode gravar eventos diretamente no calendário do usuário e ler ou gravar arquivos em seu OneDrive. Saiba mais sobre como [acessar as APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promova seu aplicativo nos Marketplaces do Azure e do Office 365

**Promova seu aplicativo para milhões de organizações que já estão usando o Azure AD.**  Os usuários que pesquisam e procuram esses Marketplaces já estão usando um ou mais serviços de nuvem, tornando-os clientes qualificados do serviço de nuvem. Saiba mais sobre como promover seu aplicativo no [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os usuários se inscrevem em seu aplicativo, ele aparecerá no painel de acesso do Azure AD e no iniciador de aplicativos do Office 365.**  Os usuários poderão retornar de forma rápida e fácil ao seu aplicativo mais tarde, melhorando o envolvimento do usuário. Saiba mais sobre o [painel de acesso do AD do Azure](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura de dispositivo para serviço e serviço a serviço

**Usar o Azure AD para gerenciamento de identidade de serviços e dispositivos reduz o código que você precisa para escrever e permite que ele gerencie o acesso.**  Serviços e dispositivos podem obter tokens do Azure AD usando o OAuth e usar esses tokens para acessar APIs da Web. Usando o Azure AD, você pode evitar escrever código de autenticação complexo. Como as identidades dos serviços e dispositivos são armazenadas no Azure AD, ele pode gerenciar chaves e revogação em um único lugar em vez de ter que fazer isso separadamente em seu aplicativo.

## <a name="benefits-of-integration"></a>Benefícios da integração

A integração com o Azure AD vem com benefícios que não exigem que você escreva código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com o Enterprise Identity Management

**Ajude seu aplicativo a estar em conformidade com as políticas de ti.**  As organizações integram seus sistemas de gerenciamento de identidades empresariais ao Azure AD, portanto, quando uma pessoa sai de uma organização, ele perde automaticamente o acesso ao seu aplicativo sem a necessidade de executar etapas adicionais. Ele pode gerenciar quem pode acessar seu aplicativo e determinar quais políticas de acesso são necessárias-por exemplo, autenticação multifator – reduzindo a necessidade de escrever código para obedecer a suas políticas corporativas complexas. O Azure AD fornece aos administradores um log de auditoria detalhado de quem se conectou ao seu aplicativo para que ele possa acompanhar o uso.

**O Azure AD estende Active Directory para a nuvem para que seu aplicativo possa ser integrado ao AD.**  Muitas organizações em todo o mundo usam Active Directory como seu principal sistema de entrada e gerenciamento de identidade e exigem que seus aplicativos trabalhem com o AD. A integração com o Azure AD integra seu aplicativo ao Active Directory.

### <a name="advanced-security-features"></a>Funcionalidades de segurança avançadas

**Autenticação multifator.**  O Azure AD fornece autenticação multifator nativa. Os administradores de ti podem exigir a autenticação multifator para acessar seu aplicativo, para que você não precise codificar esse suporte por conta própria. Saiba mais sobre [a autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Detecção de entrada anômala.**  O Azure AD processa mais de um bilhão de entradas por dia, enquanto usa algoritmos de aprendizado de máquina para detectar atividades suspeitas e notificar os administradores de ti de possíveis problemas. Ao dar suporte à entrada do Azure AD, seu aplicativo obtém o benefício dessa proteção. Saiba mais sobre como [exibir Azure Active Directory relatório de acesso](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Além da autenticação multifator, os administradores podem exigir que condições específicas sejam atendidas antes que os usuários possam entrar no seu aplicativo. As condições que podem ser definidas incluem o intervalo de endereços IP de dispositivos cliente, a associação em grupos especificados e o estado do dispositivo que está sendo usado para acesso. Saiba mais sobre [Azure Active Directory acesso condicional](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão do setor.**  A Microsoft está comprometida em dar suporte aos padrões do setor. A plataforma de identidade da Microsoft dá suporte aos protocolos OAuth 2,0 e OpenID Connect 1,0 padrão do setor. Saiba mais sobre os [protocolos de autenticação da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

**Bibliotecas de código-fonte aberto.**  A Microsoft fornece bibliotecas de software livre totalmente suportadas para linguagens e plataformas populares para agilizar o desenvolvimento. O código-fonte é licenciado sob o Apache 2,0 e você está livre para bifurcar e contribuir de volta para os projetos. Saiba mais sobre a [MSAL (biblioteca de autenticação da Microsoft)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e alta disponibilidade

**O Azure AD é implantado em data centers em todo o mundo e é gerenciado e monitorado em todo o relógio.**  O Azure AD é o sistema de gerenciamento de identidade para Microsoft Azure e Office 365 e é implantado em 28 datacenters em todo o mundo. Os dados do diretório são garantidos para serem replicados em pelo menos três datacenters. Os balanceadores de carga global garantem que os usuários acessem a cópia mais próxima do AD do Azure que contém seus dados e reroteiam automaticamente as solicitações para outros data centers se um problema for detectado.

## <a name="next-steps"></a>Passos seguintes

Comece a [escrever código](v2-overview.md#getting-started).

[Conectar usuários usando a plataforma de identidade da Microsoft](v1-authentication-scenarios.md)

