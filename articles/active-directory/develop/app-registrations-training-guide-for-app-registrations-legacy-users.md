---
title: Guia de treinamento de registros do aplicativo portal do Azure (Herdado) – Azure
description: Uma introdução à nova experiência de registro de aplicativo na plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905397"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>Guia de treinamento: Registros de aplicativo no portal do Azure (Herdado)

Você pode encontrar vários aprimoramentos na nova experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. Se você estiver mais familiarizado com a experiência herdada, use este guia de treinamento para começar a usar a nova experiência.

Em Azure Active Directory, a nova experiência de registro de aplicativo descrita aqui está disponível para o público geral (GA). No Azure Active Directory B2C (Azure AD B2C), essa experiência está em versão prévia.

## <a name="key-changes"></a>Principais alterações

- Registros de aplicativo não estão limitados a ser um **aplicativo Web/API** ou um aplicativo **nativo** . Você pode usar o mesmo registro de aplicativo para todos eles registrando os respectivos URIs de redirecionamento.
- Os aplicativos com suporte da experiência herdada que entram somente em contas organizacionais (Azure AD). Os aplicativos foram registrados como um único locatário (dando suporte apenas a contas organizacionais do diretório no qual o aplicativo foi registrado) e podem ser modificados para serem multilocatários (com suporte a todas as contas organizacionais). A nova experiência permite que você registre aplicativos que podem dar suporte a essas opções, bem como a terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.
- A experiência herdada só estava disponível quando entrava no portal do Azure usando uma conta organizacional. Com a nova experiência, você pode usar contas pessoais da Microsoft que não estão associadas a um diretório.

## <a name="list-of-applications"></a>Lista de aplicativos

- A nova lista de aplicativo mostra os aplicativos que foram registrados por meio da experiência de registros do aplicativo herdado no portal do Azure (aplicativos que entram nas contas do Azure AD), bem como aplicativos registrados no [portal de registro de aplicativos](https://apps.dev.microsoft.com/) (aplicativos que entram Contas pessoais e da Microsoft do Azure AD).
- A nova lista de aplicativos não tem uma coluna de **tipo de aplicativo** (pois um único registro de aplicativo pode ser de vários tipos) e tem duas colunas adicionais: uma coluna **criada em** e um **certificado & coluna segredos** que mostra o status (atual, Expirando em breve ou expirado) de credenciais que foram registradas no aplicativo.

## <a name="new-app-registration"></a>Novo registro de aplicativo

Na experiência herdada, para registrar um aplicativo, você precisa fornecer: **nome**, **tipo de aplicativo**e URL de **entrada/URI de redirecionamento**. Os aplicativos que foram criados eram o Azure AD apenas aplicativos de locatário único, o que significa que eles só dão suporte a contas organizacionais do diretório no qual o aplicativo foi registrado.

Na nova experiência, você deve fornecer um **nome** para o aplicativo e escolher os **tipos de conta com suporte**. Opcionalmente, você pode fornecer um **URI de redirecionamento**. Se você fornecer um URI de redirecionamento, será necessário especificar se ele é Web/público (móvel e área de trabalho). Para obter mais informações sobre como registrar um aplicativo usando a nova experiência de registros de aplicativo, consulte [registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md). Para Azure AD B2C, consulte [registrar um aplicativo no Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>A página propriedades herdadas

A experiência herdada tinha uma página de **Propriedades** que a nova experiência não tem. A folha **Propriedades** tinha os seguintes campos: **nome**, **ID de objeto**, ID do **aplicativo**, Uri da **ID do aplicativo**, **logotipo**, **URL da Home Page**, **URL de logout**, **URL dos termos de serviço**, **política de privacidade URL**, **tipo de aplicativo**e **multilocatário.**

Aqui está onde você pode encontrar a funcionalidade equivalente na nova experiência:

- **Nome**, **logotipo**, **URL da Home Page**, **URL dos termos de serviço**e **URL da política de privacidade** agora está na página de **identidade visual** do aplicativo.
- A ID do **objeto** e a **ID do aplicativo (cliente)** estão na página **visão geral** .
- A funcionalidade controlada pela alternância **multilocatário** na experiência herdada foi substituída por tipos de **conta com suporte** na página de **autenticação** . Para obter mais informações sobre como o multi-locatário é mapeado para as opções de tipo de conta com suporte, consulte este guia de [início rápido](quickstart-modify-supported-accounts.md).
- A **URL de logout** agora está na página de **autenticação** .
- O **tipo de aplicativo** não é mais um campo válido. Em vez disso, redirecionar URIs (que podem ser encontrados na página de **autenticação** ) determinam quais tipos de aplicativo têm suporte.
- O **URI da ID** do aplicativo agora é chamado de URI de ID do **aplicativo** e você pode encontrá-lo na folha **expor uma API** . Na experiência herdada, essa propriedade foi registrada automaticamente usando o seguinte formato: `https://{tenantdomain}/{appID}` (por exemplo, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Na nova experiência, ele é gerado automaticamente como `api://{appID}`, mas precisa ser explicitamente salvo. Em locatários Azure AD B2C, o formato de `https://{tenantdomain}/{appID}` ainda é usado.

## <a name="reply-urlsredirect-urls"></a>URLs de resposta/URls de redirecionamento

Na experiência herdada, um aplicativo tinha uma página **URLs de resposta** . Na nova experiência, as URLs de resposta podem ser encontradas na seção **autenticação** de um aplicativo. Além disso, eles são chamados de **URIs de redirecionamento**. Além disso, o formato de URIs de redirecionamento foi alterado. Eles devem ser associados a um tipo de aplicativo (Web ou público). Além disso, por motivos de segurança, não há suporte para curingas e esquemas http://(com exceção de http://localhost).

## <a name="keyscertificates--secrets"></a>Chaves/certificados & segredos

Na experiência herdada, um aplicativo tinha a página **chaves** . Na nova experiência, ele foi renomeado para **certificados & segredos**. Além disso, **as chaves públicas** são conhecidas como **certificados** e **as senhas** são chamadas de **segredos do cliente**.

## <a name="required-permissionsapi-permissions"></a>Permissões necessárias/permissões de API

- Na experiência herdada, um aplicativo tinha uma página **permissões necessárias** . Na nova experiência, ele foi renomeado para permissões de **API**.
- Ao selecionar uma API na experiência herdada, você pode escolher entre uma pequena lista de APIs da Microsoft ou pesquisar por entidades de serviço no locatário. Na nova experiência, você pode escolher entre várias guias: **APIs da Microsoft**, **APIs que minha organização usa**ou **minhas APIs**. A barra de pesquisa em **APIs minha organização** usa pesquisa de guias por meio de entidades de serviço no locatário.

   > [!NOTE]
   > Você não verá essa guia se seu aplicativo não estiver associado a um locatário. Para obter mais informações sobre como solicitar permissões usando a nova experiência, consulte [este guia de início rápido](quickstart-configure-app-access-web-apis.md).

- A experiência herdada tinha um botão **conceder permissões** na parte superior da página **permissões solicitadas** . Na nova experiência, há uma seção **conceder consentimento** com um botão **conceder consentimento do administrador** na seção permissões de **API** de um aplicativo. Além disso, há algumas diferenças nas maneiras como os botões funcionam:
   - Na experiência herdada, a lógica variava dependendo do usuário conectado e das permissões que estão sendo solicitadas. A lógica foi:
      - Se apenas as permissões de consentimento do usuário tiverem sido solicitadas e o usuário conectado não era um administrador, o usuário foi capaz de conceder consentimento ao usuário para as permissões solicitadas.
      - Se pelo menos uma permissão que exige o consentimento do administrador estava sendo solicitada e o usuário conectado não era um administrador, o usuário recebeu um erro ao tentar conceder o consentimento.
      - Se o usuário conectado foi um administrador, o consentimento do administrador foi concedido para todas as permissões solicitadas.
   - Na nova experiência, apenas um administrador pode conceder consentimento. Quando um administrador seleciona o botão **conceder consentimento do administrador** , o consentimento do administrador é concedido a todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Excluindo um registro de aplicativo

Na experiência herdada, um aplicativo tinha que ser excluído por um único locatário. O botão excluir foi desabilitado para aplicativos multilocatários. Na nova experiência, os aplicativos podem ser excluídos em qualquer Estado, mas você deve confirmar a ação. Para obter mais informações sobre como excluir registros de aplicativo, consulte este guia de [início rápido](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto de aplicação

As experiências herdadas e novas usam versões diferentes para o formato do JSON no editor de manifesto. Para obter mais informações, consulte [manifesto do aplicativo](reference-app-manifest.md).

## <a name="new-ui"></a>Nova interface do usuário

Há uma nova interface do usuário para propriedades que, anteriormente, só poderia ser definida usando o editor de manifesto ou a API, ou não existia.

- O oauth2AllowImplicitFlow ( **fluxo de concessão implícita** ) pode ser encontrado na página de **autenticação** . Ao contrário da experiência herdada, você pode habilitar **tokens de acesso** ou **tokens de ID**, ou ambos.
- Os **escopos definidos por essa API** (oauth2Permissions) e preAuthorizedApplications ( **aplicativos cliente autorizados** ) podem ser configurados por meio da página **expor uma API** . Para obter mais informações sobre como configurar um aplicativo para ser uma API da Web e expor permissões/escopos, consulte este guia de [início rápido](quickstart-configure-app-expose-web-apis.md).
- O **domínio do Publicador** (exibido para os usuários no [prompt de consentimento do aplicativo](application-consent-experience.md)) pode ser encontrado na página da **folha de identidade visual** . Para obter mais informações sobre como configurar um domínio do Publicador, consulte [este "como](howto-configure-publisher-domain.md)".

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- O formato dos segredos do cliente (senhas de aplicativo) é diferente daquele da experiência herdada e pode interromper a CLI.
- Não há suporte para a alteração do valor de contas com suporte na interface do usuário. Você precisa usar o manifesto do aplicativo, a menos que esteja alternando entre o Azure AD single-locatário e multilocatário.