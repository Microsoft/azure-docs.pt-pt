---
title: Como preencher campos específicos para um aplicativo de desenvolvimento personalizado | Microsoft Docs
description: Orientação sobre como preencher campos específicos ao registrar um aplicativo personalizado desenvolvido com o Azure AD
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
ms.openlocfilehash: 46bc6d2ebbb22ffc37ca73a3fff3b7b5a3a534b8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325342"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Como preencher campos específicos para um aplicativo desenvolvido de forma personalizada

Este artigo fornece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registrar um novo aplicativo, navegue até a [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory.**

-   Escolha **registros de aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de registro do aplicativo


| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome do aplicativo. Ele deve ter um mínimo de quatro caracteres.                |
| Tipos de conta suportados| Selecione as contas às quais você deseja que seu aplicativo dê suporte: contas neste diretório organizacional somente, contas em qualquer diretório organizacional ou contas em qualquer diretório organizacional e contas pessoais da Microsoft.  |
| URI de redirecionamento (opcional) | Selecione o tipo de aplicativo que você está criando, cliente **Web** ou **público (Mobile & Desktop)** e, em seguida, insira o URI de redirecionamento (ou URL de resposta) para seu aplicativo. Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, http://localhost:31544 pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Insira um valor específico para seu aplicativo, como myapp://auth. Para ver exemplos específicos de aplicações Web ou nativas, veja os nossos [inícios rápidos](https://docs.microsoft.com/azure/active-directory/develop).|

Depois de preencher os campos acima, o aplicativo será registrado na portal do Azure e você será redirecionado para a página Visão geral do aplicativo. As páginas de configurações no painel esquerdo em **gerenciar** têm mais campos para personalizar seu aplicativo. As tabelas a seguir descrevem todos os campos. Você só veria um subconjunto desses campos, dependendo se você criou um aplicativo Web ou um aplicativo cliente público.

### <a name="overview"></a>Descrição geral
| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Quando você registra um aplicativo, o AD do Azure atribui à sua aplicação uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente seu aplicativo em solicitações de autenticação para o Azure AD, bem como para acessar recursos como o API do Graph.                                                          |
| URI da ID do aplicativo      | Deve ser um URI exclusivo, geralmente do formato **&lt;https://nome do locatário\_&gt;&lt;&gt;/do aplicativo\_.** Isso é usado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também se torna a declaração ' AUD ' no token de acesso emitido. |

### <a name="branding"></a>Identidade visual

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logotipo | Você pode usar isso para carregar um logotipo para seu aplicativo. O logotipo deve estar no formato. bmp,. jpg ou. png e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com dimensões de imagem central de pixels de 94x94.|
| URL da Home Page   | Esta é a URL de logon especificada durante o registro do aplicativo.|

### <a name="authentication"></a>Authentication

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL de fim de sessão      | Esta é a URL de logoff de logoff único. O Azure AD envia uma solicitação de logoff para essa URL quando o usuário limpa sua sessão com o AD do Azure usando qualquer outro aplicativo registrado.|
| Tipos de conta suportados  | Essa opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que as organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.|
| Redirecionar URLs      | As URLs de redirecionamento, ou de resposta são os pontos de extremidade onde o Azure AD retorna quaisquer tokens que seu aplicativo solicita. Para aplicativos nativos, é aqui que o usuário é enviado após a autorização bem-sucedida. O Azure AD verifica se o URI de redirecionamento que seu aplicativo fornece na solicitação OAuth 2,0 corresponde a um dos valores registrados no Portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos do cliente            | Você pode criar segredos do cliente, ou chaves, para acessar programaticamente APIs da Web protegidas pelo Azure AD sem nenhuma interação do usuário. Na página **novo segredo do cliente** , insira uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-lo em algum lugar seguro, pois você não poderá acessá-lo mais tarde.             |

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](../manage-apps/what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
