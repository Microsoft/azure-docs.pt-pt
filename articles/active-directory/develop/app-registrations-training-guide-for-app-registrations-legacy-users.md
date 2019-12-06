---
title: Guia de treinamento para fazer a transição de Registros de aplicativo (Herdado) para a nova experiência de Registros de aplicativo no portal do Azure
description: Uma introdução à nova experiência de registro de aplicativo no portal do Azure
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49cac5c8c49fb5c53d3320cb0ab52e0f61acda
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844265"
---
# <a name="transitioning-from-app-registrations-legacy-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Fazer a transição dos Registos de aplicações (Legado) para a nova experiência de Registos de aplicações no portal do Azure

Você pode encontrar muitos aprimoramentos na nova experiência de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. Se você estiver familiarizado com a experiência de Registros de aplicativo (herdada) no portal do Azure, use este guia de treinamento para começar a usar a nova experiência.

Em Azure Active Directory, a nova experiência de registro de aplicativo descrita aqui está disponível para o público geral (GA). No Azure Active Directory B2C (Azure AD B2C), essa experiência está em versão prévia.

## <a name="key-changes"></a>Principais alterações

- Registros de aplicativo não estão limitados a ser um *aplicativo Web/API* ou um aplicativo *nativo* . Você pode usar o mesmo registro de aplicativo para todos esses aplicativos registrando os respectivos URIs de redirecionamento.

- A experiência herdada tem suporte para aplicativos que se conectam usando apenas contas organizacionais (Azure AD). Os aplicativos foram registrados como um único locatário. Aplicativos com suporte apenas para contas organizacionais do diretório no qual o aplicativo foi registrado. Os aplicativos podem ser modificados para serem multilocatários e dar suporte a todas as contas organizacionais. A nova experiência permite que você registre aplicativos que podem dar suporte a essas opções, bem como a terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.

- A experiência herdada só estava disponível quando entrava no portal do Azure usando uma conta organizacional. Com a nova experiência, você pode usar contas pessoais da Microsoft que não estão associadas a um diretório.

## <a name="list-of-applications"></a>Lista de aplicativos

A nova lista de aplicativo mostra os aplicativos que foram registrados por meio da experiência de registros do aplicativo herdado no portal do Azure. Esses aplicativos entram usando contas do Azure AD. A nova lista de aplicativos também mostra os aplicativos registrados por meio do portal de registro de aplicativos. Esses aplicativos entram usando o Azure AD e contas pessoais da Microsoft.

>[!NOTE]
>O portal de registro de aplicativos foi preterido.

A nova lista de aplicativos não tem uma coluna de **tipo de aplicativo** porque um único registro de aplicativo pode ser de vários tipos. A lista tem duas colunas adicionais: **criadas em** e **certificados & segredos**. **Certificados & segredos** mostra o status das credenciais que foram registradas no aplicativo. Os status incluem **atual**, **expirando em breve**e **expiraram**.

## <a name="new-app-registration"></a>Registo de nova aplicação

Na experiência herdada, para registrar um aplicativo, você precisa fornecer: **nome**, **tipo de aplicativo**e URL de **entrada/URI de redirecionamento**. Os aplicativos que foram criados eram apenas aplicativos de locatário único do Azure AD. Eles só dão suporte a contas organizacionais do diretório no qual o aplicativo foi registrado.

Na nova experiência, você deve fornecer um **nome** para o aplicativo e escolher os **tipos de conta com suporte**. Opcionalmente, você pode fornecer um **URI de redirecionamento**. Se você fornecer um URI de redirecionamento, precisará especificar se ele é Web/público (móvel e área de trabalho). Para obter mais informações, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md). Para Azure AD B2C, consulte [registrar um aplicativo no Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Diferenças entre o portal de registro do aplicativo e a página Registros de aplicativo

### <a name="the-legacy-properties-page"></a>A página propriedades herdadas

A experiência herdada tinha uma página **Propriedades** . As **Propriedades** tinham os seguintes campos:

- **Nome**
- **ID do objeto**
- **ID da Aplicação**
- **URI do ID da Aplicação**
- **Logotipo**
- **URL da Home Page**
- **URL de logout**
- **URL dos termos de serviço**
- **URL de declaração de privacidade**
- **Tipo de Aplicação**
- **Multilocatário**

A nova experiência não tem essa página. Aqui está onde você pode encontrar a funcionalidade equivalente:

- **Nome**, **logotipo**, **URL da Home Page**, **URL dos termos de serviço**e **URL da política de privacidade** agora estão na página de **identidade visual** do aplicativo.
- A ID do **objeto** e a **ID do aplicativo (cliente)** estão na página **visão geral** .
- A funcionalidade controlada pela alternância **multilocatário** na experiência herdada foi substituída por tipos de **conta com suporte** na página de **autenticação** . Para obter mais informações, consulte [início rápido: modificar as contas com suporte em um aplicativo](quickstart-modify-supported-accounts.md).
- A **URL de logout** agora está na página de **autenticação** .
- O **tipo de aplicativo** não é mais um campo válido. Em vez disso, redirecione URIs, que podem ser encontrados na página **autenticação** , determine quais tipos de aplicativo têm suporte.
- O **URI da ID** do aplicativo agora é chamado de URI de ID do **aplicativo** e você pode encontrá-lo em **expor uma API**. Na experiência herdada, essa propriedade foi autoregistrada usando o seguinte formato: `https://{tenantdomain}/{appID}`, por exemplo, `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Na nova experiência, ele é gerado automaticamente como `api://{appID}`, mas precisa ser explicitamente salvo. Em locatários Azure AD B2C, o formato de `https://{tenantdomain}/{appID}` ainda é usado.

### <a name="reply-urlsredirect-urls"></a>URLs de resposta/URls de redirecionamento

Na experiência herdada, um aplicativo tinha uma página **URLs de resposta** . Na nova experiência, as URLs de resposta podem ser encontradas na página de **autenticação** de um aplicativo. Agora eles são chamados de **URIs de redirecionamento**.

O formato de URIs de redirecionamento foi alterado. Eles precisam ser associados a um tipo de aplicativo, seja Web ou público. Por motivos de segurança, não há suporte para curingas e esquemas de `http://`, exceto para *http://localhost* .

### <a name="keyscertificates--secrets"></a>Chaves/certificados & segredos

Na experiência herdada, um aplicativo tinha a página **chaves** . Na nova experiência, ele foi renomeado para **certificados & segredos**.

**As chaves públicas** agora são chamadas de **certificados**. **As senhas** agora são chamadas de **segredos do cliente**.

### <a name="required-permissionsapi-permissions"></a>Permissões necessárias/permissões de API

Na experiência herdada, um aplicativo tinha uma página **permissões necessárias** . Na nova experiência, ele foi renomeado para permissões de **API**.

Quando você selecionou uma API na experiência herdada, pode escolher uma pequena lista de APIs da Microsoft. Você também pode pesquisar por meio de entidades de serviço no locatário. Na nova experiência, você pode escolher entre várias guias: **APIs da Microsoft**, **APIs que minha organização usa**ou **minhas APIs**. A barra de pesquisa em **APIs minha organização** usa pesquisa de guias por meio de entidades de serviço no locatário.

> [!NOTE]
> Você não verá essa guia se seu aplicativo não estiver associado a um locatário. Para obter mais informações sobre como solicitar permissões, consulte [início rápido: configurar um aplicativo cliente para acessar APIs da Web](quickstart-configure-app-access-web-apis.md).

A experiência herdada tinha um botão **conceder permissões** na parte superior da página **permissões solicitadas** . Na nova experiência, a página **conceder consentimento** tem um botão de **consentimento conceder administrador** na seção permissões de **API** de um aplicativo. Há também algumas diferenças nas maneiras como os botões funcionam.

Na experiência herdada, a lógica variava dependendo do usuário conectado e das permissões que estão sendo solicitadas. A lógica foi:

- Se apenas as permissões de consentimento do usuário tiverem sido solicitadas e o usuário conectado não fosse um administrador, o usuário poderá conceder consentimento ao usuário para as permissões solicitadas.
- Se pelo menos uma permissão que exige o consentimento do administrador foi solicitada e o usuário conectado não era um administrador, o usuário recebeu um erro ao tentar conceder o consentimento.
- Se o usuário conectado foi um administrador, o consentimento do administrador foi concedido para todas as permissões solicitadas.

Na nova experiência, apenas um administrador pode conceder consentimento. Quando um administrador seleciona **conceder consentimento de administrador**, o consentimento do administrador é concedido a todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Excluindo um registro de aplicativo

Na experiência herdada, você pode excluir somente aplicativos de locatário único. O botão excluir foi desabilitado para aplicativos multilocatários. Na nova experiência, você pode excluir aplicativos em qualquer Estado, mas deve confirmar a ação. Para obter mais informações, consulte [início rápido: remover um aplicativo registrado com a plataforma de identidade da Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto de aplicação

As experiências herdadas e novas usam versões diferentes para o formato do JSON no editor de manifesto. Para obter mais informações, consulte [Azure Active Directory manifesto do aplicativo](reference-app-manifest.md).

## <a name="new-ui"></a>Nova interface do usuário

A nova experiência adiciona controles de interface do usuário para as seguintes propriedades:

- A página de **autenticação** tem o **fluxo de concessão implícito** (`oauth2AllowImplicitFlow`). Ao contrário da experiência herdada, você pode habilitar **tokens de acesso** ou **tokens de ID**, ou ambos.
- A página **expor uma API** contém **escopos definidos por essa API** (`oauth2Permissions`) e **aplicativos cliente autorizados** (`preAuthorizedApplications`). Para obter mais informações sobre como configurar um aplicativo para ser uma API da Web e expor permissões/escopos, consulte [início rápido: configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md).
- A página **identidade visual** contém o **domínio do Publicador**. O domínio do Publicador é exibido aos usuários no [prompt de consentimento do aplicativo](application-consent-experience.md). Para obter mais informações, consulte [como configurar um domínio do Publicador de um aplicativo](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- O formato dos segredos do cliente (senhas de aplicativo) é diferente daquele da experiência herdada e pode interromper a CLI.
- Não há suporte para a alteração do valor de contas com suporte na interface do usuário. Você precisa usar o manifesto do aplicativo, a menos que esteja alternando entre o Azure AD single-locatário e multilocatário.
