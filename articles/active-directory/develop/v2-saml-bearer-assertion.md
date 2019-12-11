---
title: Plataforma de identidade da Microsoft & fluxo de declaração de portador SAML | Azure
description: Saiba como buscar dados de Microsoft Graph sem solicitar credenciais ao usuário usando o fluxo de declaração de portador SAML.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7f5b983a00dfc0af2e7a40571ce58fafca5914e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964624"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidade da Microsoft e fluxo de declaração de portador SAML 2,0 do OAuth
O fluxo de asserção do portador SAML 2,0 do OAuth permite solicitar um token de acesso OAuth usando uma Asserção SAML quando um cliente precisa usar uma relação de confiança existente. A assinatura aplicada à declaração SAML fornece autenticação do aplicativo autorizado. Uma Asserção SAML é um token de segurança XML emitido por um provedor de identidade e consumido por um provedor de serviços. O provedor de serviços depende de seu conteúdo para identificar o assunto da declaração para fins relacionados à segurança.

A Asserção SAML é postada no ponto de extremidade do token OAuth.  O ponto de extremidade processa a asserção e emite um token de acesso com base na aprovação anterior do aplicativo. O cliente não precisa ter ou armazenar um token de atualização, nem o segredo do cliente deve ser passado para o ponto de extremidade do token.

O fluxo de declaração de portador SAML é útil ao buscar dados de APIs Microsoft Graph (que só dão suporte a permissões delegadas) sem solicitar credenciais ao usuário. Nesse cenário, a concessão de credenciais de cliente, que é preferida para processos em segundo plano, não funciona.

Para aplicativos que fazem logon interativo baseado em navegador para obter uma Asserção SAML e, em seguida, deseja adicionar acesso a uma API protegida por OAuth (como Microsoft Graph), você pode fazer uma solicitação OAuth para obter um token de acesso para a API. Quando o navegador é redirecionado para o Azure AD para autenticar o usuário, o navegador pega a sessão da entrada SAML e o usuário não precisa inserir suas credenciais.

O fluxo de declaração de portador SAML do OAuth também tem suporte para usuários que se autenticam com provedores de identidade, como Serviços de Federação do Active Directory (AD FS) (ADFS) federados para Azure Active Directory.  A Asserção SAML obtida do ADFS pode ser usada em um fluxo OAuth para autenticar o usuário.

![Fluxo do OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Grafo de chamada usando a asserção de portador SAML
Agora vamos entender como podemos realmente buscar a Asserção SAML de forma programática. Essa abordagem é testada com o ADFS. No entanto, isso funciona com qualquer provedor de identidade que dá suporte ao retorno da declaração SAML programaticamente. O processo básico é: obter uma Asserção SAML, obter um token de acesso e acessar Microsoft Graph.

### <a name="prerequisites"></a>Pré-requisitos

Estabeleça uma relação de confiança entre o servidor de autorização/ambiente (Microsoft 365) e o provedor de identidade ou o emissor da declaração de portador (ADFS) SAML 2,0. Para configurar o ADFS para logon único e como um provedor de identidade, você pode consultar [Este artigo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registre o aplicativo no [portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Entre na [folha de registro do aplicativo do portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Observe que estamos usando os pontos de extremidade v 2.0 para API do Graph e, portanto, precisamos registrar o aplicativo neste portal. Caso contrário, poderíamos ter usado os registros no Azure Active Directory). 
1. Selecione **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação: 
    1. **Nome** - Introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação.
    1. **Tipos de conta suportados** - Selecione as contas que quer que a sua aplicação suporte.
    1. **URI de redirecionamento (opcional)** – selecione o tipo de aplicativo que você está criando, Web ou cliente público (Mobile & Desktop) e, em seguida, insira o URI de redirecionamento (ou URL de resposta) para seu aplicativo.
    1. Quando terminar, selecione **Registar**.
1. Anote a ID do aplicativo (cliente).
1. No painel esquerdo, selecione **certificados & segredos**. Clique em **novo segredo do cliente** na seção **segredos do cliente** . Copie o novo segredo do cliente, você não poderá recuperar quando sair da folha.
1. No painel esquerdo, selecione **permissões de API** e, em seguida, **adicione uma permissão**. Selecione **Microsoft Graph**, **permissões delegadas**e, em seguida, selecione **tarefas. ler** , pois pretendemos usar o API do Graph do Outlook. 

Instale o [postmaster](https://www.getpostman.com/), uma ferramenta necessária para testar as solicitações de exemplo.  Posteriormente, você pode converter as solicitações em código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obter a Asserção SAML do ADFS
Crie uma solicitação POST para o ponto de extremidade do ADFS usando o envelope SOAP para buscar a Asserção SAML:

![Obter Asserção SAML](./media/v2-saml-bearer-assertion/2.png)

Valores de cabeçalho:

![Valores de cabeçalho](./media/v2-saml-bearer-assertion/3.png)

Corpo da solicitação do ADFS:

![Corpo da solicitação do ADFS](./media/v2-saml-bearer-assertion/4.png)

Depois que essa solicitação for lançada com êxito, você deverá receber uma Asserção SAML do ADFS. Somente os dados **SAML: Assertion** tag são necessários, converta-os na codificação Base64 para usar em solicitações posteriores.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obter o token OAuth2 usando a Asserção SAML 
Nesta etapa, busque um token OAuth2 usando a resposta de asserção do ADFS.

1. Crie uma solicitação POST, conforme mostrado abaixo, com os valores de cabeçalho:

    ![Solicitação POST](./media/v2-saml-bearer-assertion/5.png)
1. No corpo da solicitação, substitua **client_id**, **client_secret**e **asserção** (a declaração SAML codificada em base64 obteve a etapa anterior):

    ![Corpo do pedido](./media/v2-saml-bearer-assertion/6.png)
1. Após a solicitação bem-sucedida, você receberá um token de acesso do Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Obter os dados com o token OAuth

Depois de receber o token de acesso, chame as APIs do Graph (tarefas do Outlook neste exemplo). 

1. Crie uma solicitação GET com o token de acesso buscado na etapa anterior:

    ![OBTER solicitação](./media/v2-saml-bearer-assertion/7.png)

1. Após a solicitação bem-sucedida, você receberá uma resposta JSON.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md).