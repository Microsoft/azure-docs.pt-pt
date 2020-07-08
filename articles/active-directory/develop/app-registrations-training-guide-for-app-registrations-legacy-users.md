---
title: Novo guia de preparação de registos de aplicações do portal do Azure
description: Apresenta a nova experiência de registo do portal Azure App
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: f72e16afd61d364a2dad634f3502c801f1b99380
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478149"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Novo guia de preparação de registos de aplicações do portal do Azure

Pode encontrar muitas melhorias na experiência de [registos](https://go.microsoft.com/fwlink/?linkid=2083908) de novas Apps no portal Azure. Se está familiarizado com a experiência de registos (legado) da App no portal Azure, utilize este guia de formação para começar a usar a nova experiência.

No Azure Ative Directory, a nova experiência de registo de aplicações aqui descrita está geralmente disponível (GA). No Azure Ative Directory B2C (Azure AD B2C), esta experiência está em pré-visualização.

## <a name="key-changes"></a>Alterações-chave

- Os registos de aplicações não se limitam a ser uma *aplicação web/API* ou uma aplicação *nativa.* Pode utilizar o mesmo registo de aplicações para todas estas aplicações registando os respetivos URIs de redirecionamento.

- A experiência do legado apoiou aplicações que se inscrevem usando apenas contas organizacionais (Azure AD). As aplicações foram registadas como inquilinos únicos. As aplicações suportavam apenas contas organizacionais do diretório em que a aplicação estava registada. As aplicações podem ser modificadas para serem multi-inquilinos e suportar todas as contas organizacionais. A nova experiência permite registar aplicações que podem suportar tanto essas opções como uma terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.

- A experiência do legado só estava disponível quando assinou no portal Azure usando uma conta organizacional. Com a nova experiência, pode utilizar contas pessoais da Microsoft que não estejam associadas a um diretório.

## <a name="list-of-applications"></a>Lista de candidaturas

A nova lista de aplicações mostra aplicações que foram registadas através da experiência de registos de aplicações antigas no portal Azure. Estas aplicações insinuam-se utilizando as contas AZure AD. A nova lista de aplicações também mostra aplicações registadas no Portal de Registo de Aplicações. Estas aplicações insinuam-se utilizando a Azure AD e contas pessoais da Microsoft.

>[!NOTE]
>O Portal de Registo de Candidaturas foi deprectado.

A nova lista de aplicações não tem uma coluna **tipo Aplicação** porque um registo de aplicações únicas pode ser vários tipos. A lista tem duas colunas adicionais: **Criados** e **certificados & segredos**. **Os certificados & segredos** mostram o estado das credenciais que foram registadas na aplicação. Os estados incluem **Corrente,** **Expiração em breve**e **Expiração**.

## <a name="new-app-registration"></a>Registo de nova aplicação

Na experiência do legado, para registar uma aplicação foi-lhe exigido fornecer: **Nome,** **tipo de aplicação**e **URL/Redireccionamento URI.** As aplicações que foram criadas foram a Azure AD apenas aplicações de inquilino único. Só suportavam contas organizacionais do diretório em que a aplicação estava registada.

Na nova experiência, deve fornecer um **Nome** para a aplicação e escolher os **tipos de conta suportado.** Pode opcionalmente fornecer um **URI de redirecionamento.** Se fornecer um URI de redirecionamento, terá de especificar se é web/público (mobile e desktop). Para obter mais informações, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md). Para Azure AD B2C, consulte [registar uma aplicação no Azure Ative Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Diferenças entre o Portal de Registo de Aplicações e a página de registos de aplicações

### <a name="the-legacy-properties-page"></a>A página legacy Properties

A experiência do legado tinha uma página **de Propriedades.** **As propriedades** tinham os seguintes campos:

- **Nome**
- **ID de objeto**
- **ID da Aplicação**
- **URI do ID da Aplicação**
- **Logótipo**
- **URL de página inicial**
- **Logout URL**
- **Termos de SERVIÇO URL**
- **URL de declaração de privacidade**
- **Tipo de aplicação**
- **Multi-inquilino**

A nova experiência não tem esta página. Aqui é onde você pode encontrar a funcionalidade equivalente:

- **Nome**, **Logotipo**, **URL de página inicial,** **URL de serviço**e URL de declaração de **privacidade** estão agora na página de Branding da **aplicação.**
- **O ID** do Objeto e **o ID de aplicação (cliente)** estão na página **de Visão Geral.**
- A funcionalidade controlada pelo **multi-inquilino** na experiência do legado foi substituída por **tipos de conta suportados** na página **de Autenticação.** Para obter mais informações, consulte [Quickstart: Modifique as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md).
- **O URL logout** está agora na página **de Autenticação.**
- **O tipo de aplicação** já não é um campo válido. Em vez disso, redirecione os URIs, que pode encontrar na página **de Autenticação,** determina quais os tipos de aplicações suportados.
- **App ID URI** é agora chamado **de ID URI de aplicação** e você pode encontrá-lo em **Expor uma API.** Na experiência do legado, esta propriedade foi registada automaticamente utilizando o seguinte formato: `https://{tenantdomain}/{appID}` , por exemplo, `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b` . Na nova experiência, é autogerado como `api://{appID}` , mas precisa ser explicitamente salvo. Nos inquilinos Azure AD B2C, o `https://{tenantdomain}/{appID}` formato ainda é utilizado.

### <a name="reply-urlsredirect-urls"></a>URLs de resposta/redireccionamento de URLs

Na experiência do legado, uma aplicação tinha uma página **de URLs de resposta.** Na nova experiência, os URLs de resposta podem ser encontrados na página de Autenticação de uma **aplicação.** São agora referidos como **Redirecionar URIs.**

O formato para uris de redirecionamento mudou. São obrigados a ser associados a um tipo de aplicação, seja web ou público. Por razões de segurança, os wildcards e `http://` esquemas não são apoiados, *http://localhost* exceto.

### <a name="keyscertificates--secrets"></a>Chaves/Certificados & segredos

Na experiência do legado, uma aplicação tinha página **Keys.** Na nova experiência, foi renomeado para **Certificados & segredos.**

**As chaves públicas** são agora referidas como **Certificados.** **As palavras-passe** são agora referidas como segredos do **Cliente.**

### <a name="required-permissionsapi-permissions"></a>Permissões necessárias/permissões API

Na experiência do legado, uma aplicação tinha uma página **de permissões necessárias.** Na nova experiência, foi renomeado para **permissões API.**

Quando selecionou uma API na experiência do legado, pode escolher entre uma pequena lista de APIs da Microsoft. Você também pode pesquisar através de diretores de serviço no inquilino. Na nova experiência, pode escolher entre vários separadores: **APIs da Microsoft,** **APIs que a minha organização utiliza,** ou **As Minhas APIs**. A barra de pesquisa em **APIs a minha organização** usa pesquisas de separadores através de diretores de serviço no inquilino.

> [!NOTE]
> Não verá este separador se o seu pedido não estiver associado a um inquilino. Para obter mais informações sobre como solicitar permissões, consulte [Quickstart: Configure uma aplicação do cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md).

A experiência do legado tinha um botão **de permissões Grant** no topo da página de **permissões solicitadas.** Na nova experiência, a página **de consentimento grant** tem um botão de consentimento administrativo **Grant** na secção de **permissões API** de uma aplicação. Há também algumas diferenças na forma como os botões funcionam.

Na experiência do legado, a lógica variou consoante o assinado no utilizador e as permissões solicitadas. A lógica era:

- Se apenas as permissões de consentimento do utilizador fossem solicitadas e o assinado no utilizador não fosse um administrador, o utilizador poderia conceder o consentimento do utilizador para as permissões solicitadas.
- Se pelo menos uma permissão que requer consentimento administrativo foi solicitada e o assinado no utilizador não era um administrador, o utilizador teve um erro ao tentar conceder o consentimento.
- Se o contratado no utilizador fosse um administrador, o consentimento administrativo foi concedido para todas as permissões solicitadas.

Na nova experiência, apenas um administrador pode conceder o consentimento. Quando um administrador seleciona **o consentimento administrativo do Grant,** o consentimento administrativo é concedido a todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Eliminação de um registo de aplicações

Na experiência do legado, você poderia apagar apenas aplicações de inquilino único. O botão de exclusão foi desativado para aplicações multi-inquilinos. Na nova experiência, pode eliminar aplicações em qualquer estado, mas tem de confirmar a ação. Para obter mais informações, consulte [Quickstart: Remova uma aplicação registada na plataforma de identidade da Microsoft.](quickstart-remove-app.md)

## <a name="application-manifest"></a>Manifesto de aplicação

O legado e as novas experiências utilizam diferentes versões para o formato do JSON no editor manifesto. Para mais informações, consulte [o manifesto da aplicação Azure Ative Directory.](reference-app-manifest.md)

## <a name="new-ui"></a>Nova UI

A nova experiência adiciona controlos de UI para as seguintes propriedades:

- A página **de autenticação** tem **fluxo de subvenção implícito** `oauth2AllowImplicitFlow` (). Ao contrário da experiência do legado, você pode ativar **tokens** de acesso ou **fichas de ID**, ou ambos.
- A página **Expor uma API** contém **Âmbitos definidos por esta API** `oauth2Permissions` () e **aplicações de clientes autorizados** `preAuthorizedApplications` (). Para obter mais informações sobre como configurar uma aplicação para ser uma API web e expor permissões/âmbitos, consulte [Quickstart: Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).
- A página **de Branding** contém o **domínio Editor.** O domínio da editora é apresentado aos utilizadores no [pedido de consentimento da aplicação](application-consent-experience.md). Para obter mais informações, consulte [Como: Configurar o domínio de uma aplicação.](howto-configure-publisher-domain.md)

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- O formato dos segredos dos clientes (palavras-passe de aplicações) é diferente do da experiência do legado e pode quebrar o CLI.
- A alteração do valor das contas suportadas não é suportada na UI. Você precisa usar o manifesto da aplicação a menos que você esteja trocando entre Azure AD single-tenant e multi-inquilino.
