---
title: portal do Azure campos de registro para aplicativos desenvolvidos com personalização
description: Diretrizes para registrar um aplicativo personalizado desenvolvido com o Azure AD
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
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ded0ba36a8eef7adb1be5b5f07408b9f79359f44
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965641"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>portal do Azure campos de registro para aplicativos desenvolvidos com personalização

Este artigo fornece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registrar um novo aplicativo, navegue até a [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory.**

-   Escolha **registros de aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de registro do aplicativo

| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome da aplicação. Ele deve ter um mínimo de quatro caracteres.                |
| Tipos de conta suportados| Selecione as contas às quais você deseja que seu aplicativo dê suporte: contas neste diretório organizacional somente, contas em qualquer diretório organizacional ou contas em qualquer diretório organizacional e contas pessoais da Microsoft.  |
| URI de Redirecionamento (opcional) | Selecione o tipo de aplicativo que você está criando, cliente **Web** ou **público (Mobile & Desktop)** e, em seguida, insira o URI de redirecionamento (ou URL de resposta) para seu aplicativo. Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, http://localhost:31544 pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Insira um valor específico para seu aplicativo, como myapp://auth. Para ver exemplos específicos para aplicativos Web ou aplicativos nativos, Confira nossos [guias de início rápido](https://docs.microsoft.com/azure/active-directory/develop).|

Depois de preencher os campos acima, o aplicativo será registrado na portal do Azure e você será redirecionado para a página Visão geral do aplicativo. As páginas de configurações no painel esquerdo em **gerenciar** têm mais campos para personalizar seu aplicativo. As tabelas a seguir descrevem todos os campos. Você só veria um subconjunto desses campos, dependendo se você criou um aplicativo Web ou um aplicativo cliente público.

### <a name="overview"></a>Visão geral

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Quando você registra um aplicativo, o AD do Azure atribui à sua aplicação uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente seu aplicativo em solicitações de autenticação para o Azure AD, bem como para acessar recursos como o API do Graph.                                                          |
| URI da ID do aplicativo      | Ele deve ser um URI exclusivo, geralmente do formato **https://&lt;\_nome do locatário&gt;/** &lt;do\_de nome do aplicativo. Isso é usado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também se torna a declaração ' AUD ' no token de acesso emitido. |

### <a name="branding"></a>Personalização

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logotipo | Você pode usar isso para carregar um logotipo para seu aplicativo. O logotipo deve estar no formato. bmp,. jpg ou. png e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com dimensões de imagem central de pixels de 94x94.|
| URL da Home Page   | Esta é a URL de logon especificada durante o registro do aplicativo.|

### <a name="authentication"></a>Autenticação

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL de fim de sessão      | Esta é a URL de logoff de logoff único. O Azure AD envia uma solicitação de logoff para essa URL quando o usuário limpa sua sessão com o AD do Azure usando qualquer outro aplicativo registrado.|
| Tipos de conta suportados  | Essa opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que as organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.|
| URLs de Redirecionamento      | As URLs de redirecionamento, ou de resposta são os pontos de extremidade onde o Azure AD retorna quaisquer tokens que seu aplicativo solicita. Para aplicativos nativos, é aqui que o usuário é enviado após a autorização bem-sucedida. O Azure AD verifica se o URI de redirecionamento que seu aplicativo fornece na solicitação OAuth 2,0 corresponde a um dos valores registrados no Portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos de cliente            | Você pode criar segredos do cliente, ou chaves, para acessar programaticamente APIs da Web protegidas pelo Azure AD sem nenhuma interação do usuário. Na página **novo segredo do cliente** , insira uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-lo em algum lugar seguro, pois você não poderá acessá-lo mais tarde.             |

## <a name="next-steps"></a>Passos seguintes

[Managing Applications with Azure Active Directory](../manage-apps/what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
