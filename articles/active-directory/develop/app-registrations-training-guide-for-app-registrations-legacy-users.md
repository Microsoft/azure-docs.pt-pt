---
title: Novo guia de preparação de registos de aplicações do portal do Azure
description: Introduz a nova experiência de registo do portal Azure App
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154581"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Novo guia de preparação de registos de aplicações do portal do Azure

Pode encontrar [muitas melhorias](https://go.microsoft.com/fwlink/?linkid=2083908) na experiência de registos da nova App no portal Azure. Se estiver familiarizado com a experiência de registos da App (legado) no portal Azure, utilize este guia de formação para começar a utilizar a nova experiência.

No Azure Ative Diretório, a nova experiência de registo de candidaturas aqui descrita está geralmente disponível (GA). No Azure Ative Directory B2C (Azure AD B2C), esta experiência está em pré-visualização.

## <a name="key-changes"></a>Alterações-chave

- Os registos de aplicações não se limitam a ser uma *aplicação web/API* ou uma aplicação *nativa.* Pode utilizar o mesmo registo de aplicações para todas estas aplicações, registando as respetivas URIs redirecionais.

- A experiência do legado apoiou apps que se inscrevem usando apenas contas organizacionais (Azure AD). As aplicações foram registadas como inquilinos únicos. As aplicações suportavam apenas contas organizacionais do diretório em que a app estava registada. As aplicações podem ser modificadas para serem multi-inquilinos e apoiar todas as contas organizacionais. A nova experiência permite-lhe registar aplicações que podem suportar ambas as opções, bem como uma terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.

- A experiência do legado só estava disponível quando assinada no portal Azure usando uma conta organizacional. Com a nova experiência, pode utilizar contas pessoais da Microsoft que não estão associadas a um diretório.

## <a name="list-of-applications"></a>Lista de candidaturas

A nova lista de aplicações mostra aplicações que foram registadas através da experiência de registos de aplicações no portal Azure. Estas aplicações entram em sessão utilizando contas Azure AD. A nova lista de aplicações também mostra aplicações registadas no Portal de Registo de Aplicações. Estas aplicações entram em sessão utilizando contas Azure AD e pessoal da Microsoft.

>[!NOTE]
>O Portal de Registo de Candidaturas foi depretido.

A nova lista de aplicações não tem uma coluna **do tipo Aplicação** porque um único registo de aplicações pode ser de vários tipos. A lista tem duas colunas adicionais: **Criadas** e **Certificados & segredos.** **Os certificados & segredos** mostram o estado das credenciais que foram registadas na aplicação. Os estados incluem **Corrente,** **Expiração em breve,** e **Expirado.**

## <a name="new-app-registration"></a>Registo de nova aplicação

Na experiência do legado, para registar uma app que foi obrigado a fornecer: **Nome,** Tipo de **Aplicação,** e **URL/Redirect URI**. As aplicações criadas foram apenas aplicações de um único inquilino da Azure AD. Apenas apoiaram contas organizacionais do diretório em que a app estava registada.

Na nova experiência, deve fornecer um **Nome** para a app e escolher os tipos de **conta suportados**. Você pode opcionalmente fornecer um **Redirect URI**. Se fornecer um URI redirecionado, terá de especificar se é web/público (mobile e desktop). Para mais informações, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md). Para o Azure AD B2C, consulte [Registar uma aplicação no Diretório Ativo Azure B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Diferenças entre o Portal de Registo de Aplicações e a página de registos de aplicações

### <a name="the-legacy-properties-page"></a>A página legacy Properties

A experiência do legado tinha uma página **de Propriedades.** **As propriedades** tinham os seguintes campos:

- **Nome**
- **ID de objeto**
- **ID de aplicação**
- **URI do ID da Aplicação**
- **Logótipo**
- **URL de página inicial**
- **Logout URL**
- **Termos de URL de serviço**
- **URL de declaração de privacidade**
- **Tipo de aplicação**
- **Multi-inquilino**

A nova experiência não tem aquela página. Aqui é onde você pode encontrar a funcionalidade equivalente:

- **Nome**, **Logotipo,** URL da **página inicial,** **Termos de URL de serviço,** e URL de declaração de **privacidade** estão agora na página de **Branding** da aplicação.
- **Id de objeto** e **id de aplicação (cliente)** estão na página **de visão geral.**
- A funcionalidade controlada pelo **multi-inquilino** na experiência do legado foi substituída por tipos de **conta suportados** na página **de Autenticação.** Para mais informações, consulte [Quickstart: Modifique as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md).
- **O URL** de logout está agora na página **de Autenticação.**
- **O tipo de aplicação** já não é um campo válido. Em vez disso, redirecione os URIs, que pode encontrar na página **de Autenticação,** determina quais os tipos de aplicações suportados.
- **O ID da aplicação URI** chama-se ID URI de **aplicação** e pode encontrá-lo na **Expose a API**. Na experiência do legado, esta propriedade foi `https://{tenantdomain}/{appID}`autoregistrada `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`usando o seguinte formato: , por exemplo, . Na nova experiência, é autogerado `api://{appID}`como, mas precisa de ser explicitamente guardado. Nos inquilinos Azure AD B2C, o `https://{tenantdomain}/{appID}` formato ainda é utilizado.

### <a name="reply-urlsredirect-urls"></a>URLs de resposta/URls redirecionados

Na experiência do legado, uma aplicação tinha uma página **de UrLs de Resposta.** Na nova experiência, os URLs de resposta podem ser encontrados na página de **Autenticação** de uma aplicação. Agora são referidos como **URIs redirecionais.**

O formato de redirecionamento de URIs mudou. São obrigados a associar-se a um tipo de aplicação, web ou público. Por razões de `http://` segurança, os wildcards e *http://localhost*os esquemas não são suportados, exceto por .

### <a name="keyscertificates--secrets"></a>Chaves/Certificados & segredos

Na experiência do legado, uma aplicação tinha página **de Keys.** Na nova experiência, foi renomeado para **Certificados & segredos.**

**As chaves públicas** são agora referidas como **Certificados.** **As palavras-passe** são agora referidas como **segredos do Cliente.**

### <a name="required-permissionsapi-permissions"></a>Permissões/Permissões API necessárias

Na experiência do legado, uma aplicação tinha uma página de **permissões necessárias.** Na nova experiência, foi renomeado para **permissões DaPI.**

Quando selecionou uma API na experiência do legado, pode escolher entre uma pequena lista de APIs da Microsoft. Você também pode pesquisar através de diretores de serviço no inquilino. Na nova experiência, pode escolher entre vários separadores: **Microsoft APIs**, **APIs que**a minha organização utiliza , ou **As Minhas APIs**. A barra de pesquisa em **APIs a minha organização** usa pesquisas de separadores através de diretores de serviço no inquilino.

> [!NOTE]
> Não verá esta conta se o seu pedido não estiver associado a um inquilino. Para obter mais informações sobre como solicitar permissões, consulte [Quickstart: Configure uma aplicação de cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md).

A experiência do legado tinha um botão de **permissões Grant** no topo da página de **permissões solicitadas.** Na nova experiência, a página de consentimento do **Grant** tem um botão de consentimento de administrador da **Grant** na secção de **permissões API** de uma aplicação. Há também algumas diferenças na forma como os botões funcionam.

Na experiência do legado, a lógica variava consoante o assinado no utilizador e as permissões que estão a ser solicitadas. A lógica era:

- Se apenas fossem solicitadas permissões de consentimento do utilizador e o utilizador não fosse um administrador, o utilizador poderia conceder o consentimento do utilizador para as permissões solicitadas.
- Se pelo menos uma permissão que requer consentimento administrativo foi solicitada e o utilizador assinado não foi um administrador, o utilizador teve um erro ao tentar conceder o consentimento.
- Se o utilizador assinado fosse administrador, o consentimento administrativo foi concedido para todas as permissões solicitadas.

Na nova experiência, apenas um administrador pode conceder o consentimento. Quando um administrador seleciona o consentimento do **administrador grant,** o consentimento do administrador é concedido a todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Apagar um registo de aplicações

Na experiência do legado, você poderia eliminar apenas aplicativos de um único inquilino. O botão de eliminação foi desativado para aplicações multi-inquilinos. Na nova experiência, pode eliminar aplicações em qualquer estado, mas tem de confirmar a ação. Para mais informações, consulte [Quickstart: Remova uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto de aplicação

O legado e as novas experiências utilizam diferentes versões para o formato da JSON no manifesto editor. Para mais informações, consulte o manifesto da [aplicação Azure Ative Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nova UI

A nova experiência adiciona controlos de UI para as seguintes propriedades:

- A página **de Autenticação** tem **fluxo de subvenção implícita** .`oauth2AllowImplicitFlow` Ao contrário da experiência do legado, você pode ativar **fichas** de acesso ou **fichas de identificação**, ou ambos.
- A página **Expor uma página API** contém`oauth2Permissions` **âmbitos definidos por esta API** ( ) e **aplicações autorizadas** do cliente ().`preAuthorizedApplications` Para obter mais informações sobre como configurar uma aplicação para ser uma API web e expor permissões/âmbitos, consulte [Quickstart: Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).
- A página **Branding** contém o **domínio Publisher**. O domínio da editora é apresentado aos utilizadores no [pedido de consentimento da aplicação](application-consent-experience.md). Para mais informações, consulte [Como: Configurar o domínio de editor de uma aplicação](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- O formato de segredos de cliente (palavras-passe de aplicação) é diferente do da experiência do legado e pode quebrar o CLI.
- A alteração do valor das contas apoiadas não é suportada na UI. Você precisa usar o manifesto da aplicação a menos que você esteja trocando entre Azure AD single-tenant e multi-inquilino.
