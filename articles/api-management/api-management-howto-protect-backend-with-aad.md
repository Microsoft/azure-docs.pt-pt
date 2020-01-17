---
title: Proteger uma API usando o OAuth 2,0 com o AAD e o gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como proteger um back-end de API da Web com gerenciamento de API e Azure Active Directory.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 771257df12ee29c2bc8d32672c8e8b7d039a96ba
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120814"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteger uma API usando o OAuth 2,0 com gerenciamento de API e Azure Active Directory

Este guia mostra como configurar sua instância do gerenciamento de API do Azure para proteger uma API, usando o protocolo OAuth 2,0 com o Azure Active Directory (AD do Azure). 

> [!NOTE]
> Esse recurso está disponível nas camadas **Developer**, **Standard** e **Premium** do gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir as etapas neste artigo, você deve ter:
* Uma instância de gerenciamento de API
* Uma API que está sendo publicada que usa a instância de gerenciamento de API
* Um locatário do Azure AD

## <a name="overview"></a>Visão geral

Aqui está uma rápida visão geral das etapas:

1. Registrar um aplicativo (aplicativo de back-end) no Azure AD para representar a API.
2. Registrar outro aplicativo (cliente-aplicativo) no Azure AD para representar um aplicativo cliente que precisa chamar a API.
3. No Azure AD, conceda permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.
4. Configure o console do desenvolvedor para chamar a API usando a autorização de usuário OAuth 2,0.
5. Adicione a política **Validate-JWT** para validar o token OAuth para cada solicitação de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrar um aplicativo no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registrar um aplicativo no Azure AD que represente a API. 

1. Acesse o [portal do Azure](https://portal.azure.com) para registrar seu aplicativo. Procure e selecione **registros de API**.

1. Selecione **novo registro**. 

1. Quando a **página Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação: 
    - Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo, como o aplicativo de *back-end*. 
    - Na seção **tipos de conta com suporte** , selecione uma opção que atenda ao seu cenário. 

1. Deixe a seção **Redirecionar URI** vazia.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde.

1. Selecione **expor uma API** e defina o **URI da ID do aplicativo** com o valor padrão. Registre esse valor para mais tarde.

1. Selecione o botão **Adicionar um escopo** para exibir a página **Adicionar um escopo** . Em seguida, crie um novo escopo com suporte da API (por exemplo, `Files.Read`). Por fim, selecione o botão **Adicionar escopo** para criar o escopo. Repita essa etapa para adicionar todos os escopos com suporte pela sua API.

1. Quando os escopos forem criados, anote-os para uso em uma etapa subsequente. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrar outro aplicativo no Azure AD para representar um aplicativo cliente

Cada aplicativo cliente que chama a API também precisa ser registrado como um aplicativo no Azure AD. Neste exemplo, o aplicativo cliente é o console do desenvolvedor no portal do desenvolvedor do gerenciamento de API. Veja como registrar outro aplicativo no Azure AD para representar o console do desenvolvedor.

1. Acesse o [portal do Azure](https://portal.azure.com) para registrar seu aplicativo. Procure e selecione **registros de API**.

1. Selecione **novo registro**.

1. Quando a **página Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação: 
    - Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo, como *aplicativo cliente*. 
    - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional (qualquer diretório do Azure ad-multilocatário)** . 

1. Na seção **URI de redirecionamento** , selecione `Web` e insira a URL `https://contoso5.portal.azure-api.net/signin`.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde.

Agora, crie um segredo do cliente para este aplicativo usar em uma etapa subsequente.

1. Na lista de páginas para seu aplicativo cliente, selecione **certificados & segredos**e selecione **novo segredo do cliente**.

1. Em **Adicionar um segredo do cliente**, forneça uma **Descrição**. Escolha quando a chave deve expirar e selecione **Adicionar**.

Quando o segredo for criado, observe o valor da chave para uso em uma etapa subsequente. 

## <a name="grant-permissions-in-azure-ad"></a>Conceder permissões no Azure AD

Agora que você registrou dois aplicativos para representar a API e o console do desenvolvedor, você precisa conceder permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.  

1. Vá para a [portal do Azure](https://portal.azure.com) para conceder permissões ao seu aplicativo cliente. Procure e selecione **registros de API**.

1. Escolha seu aplicativo cliente. Em seguida, na lista de páginas do aplicativo, selecione **permissões de API**.

1. Selecione **Adicionar uma permissão**.

1. Em **selecionar uma API**, selecione **minhas APIs**e, em seguida, localize e selecione seu aplicativo de back-end.

1. Em **permissões delegadas**, selecione as permissões apropriadas para seu aplicativo de back-end e, em seguida, selecione **adicionar permissões**.

1. Opcionalmente, na página **permissões de API** , selecione **conceder consentimento de administrador para \<seu nome de locatário >** para conceder consentimento em nome de todos os usuários neste diretório. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitar a autorização de usuário do OAuth 2,0 no console do desenvolvedor

Neste ponto, você criou seus aplicativos no Azure AD e concedeu as permissões adequadas para permitir que o aplicativo cliente chame o aplicativo de back-end. 

Neste exemplo, o console do desenvolvedor é o aplicativo cliente. As etapas a seguir descrevem como habilitar a autorização de usuário OAuth 2,0 no console do desenvolvedor. 

1. Em portal do Azure, navegue até sua instância de gerenciamento de API.

1. Selecione **OAuth 2,0** > **Adicionar**.

1. Forneça um **nome de exibição** e uma **Descrição**.

1. Para a **URL da página de registro do cliente**, insira um valor de espaço reservado, como `http://localhost`. A **URL da página de registro do cliente** aponta para uma página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2,0 que dão suporte a isso. Neste exemplo, os usuários não criam e configuram suas próprias contas, portanto, você usa um espaço reservado em vez disso.

1. Para **tipos de concessão de autorização**, selecione **código de autorização**.

1. Especifique a **URL do ponto** de extremidade de autorização e a **URL do ponto de extremidade do token** Recupere esses valores da página **pontos de extremidade** em seu locatário do Azure AD. Navegue até a página **registros de aplicativo** novamente e selecione **pontos de extremidade**.


1. Copie o **ponto de extremidade de autorização do OAuth 2,0**e cole-o na caixa de texto **URL do ponto de extremidade de autorização** . Selecione **postar** em método de solicitação de autorização.

1. Copie o **ponto de extremidade do token 2,0 do OAuth**e cole-o na caixa de texto **URL do ponto de extremidade do token** . 

    >[!IMPORTANT]
    > Você pode usar pontos de extremidade **v1** ou **v2** . No entanto, dependendo de qual versão você escolher, a etapa abaixo será diferente. É recomendável usar pontos de extremidade v2. 

1. Se você usar pontos de extremidade **v1** , adicione um parâmetro de corpo chamado **Resource**. Para o valor desse parâmetro, use a **ID de aplicativo** do aplicativo de back-end. 

1. Se você usar pontos de extremidade **v2** , use o escopo que você criou para o aplicativo de back-end no campo **escopo padrão** .

1. Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o aplicativo cliente.

1. Para **ID do cliente**, use a **ID do aplicativo** do aplicativo cliente.

1. Para o **segredo do cliente**, use a chave que você criou para o aplicativo cliente anteriormente. 

1. Imediatamente após o segredo do cliente está o **redirect_url** para o tipo de concessão de código de autorização. Anote essa URL.

1. Selecione **Criar**.

1. Volte para o aplicativo cliente e selecione **autenticação**.

1. Em **URIs de redirecionamento**, selecione o tipo como **Web**, Cole o **Redirect_url** em **URI de redirecionamento**e, em seguida, salve.

Agora que você configurou um servidor de autorização OAuth 2,0, o console do desenvolvedor pode obter tokens de acesso do Azure AD. 

A próxima etapa é habilitar a autorização de usuário OAuth 2,0 para sua API. Isso permite que o console do desenvolvedor saiba que ele precisa obter um token de acesso em nome do usuário antes de fazer chamadas para sua API.

1. Navegue até a instância de gerenciamento de API e vá para **APIs**.

2. Selecione a API que você deseja proteger. Por exemplo, você pode usar o `Echo API`.

3. Aceda a **definições**.

4. Em **segurança**, escolha **OAuth 2,0**e selecione o servidor OAuth 2,0 configurado anteriormente. 

5. Selecione **Guardar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar a API do portal do desenvolvedor com êxito

> [!NOTE]
> Esta seção não se aplica à camada de **consumo** , que não dá suporte ao portal do desenvolvedor.

Agora que a autorização de usuário do OAuth 2,0 está habilitada em sua API, o console do desenvolvedor obterá um token de acesso em nome do usuário, antes de chamar a API.

1. Navegue até qualquer operação na API no portal do desenvolvedor e selecione **experimentar**. Isso leva você ao console do desenvolvedor.

2. Observe um novo item na seção **autorização** , correspondente ao servidor de autorização que você acabou de adicionar.

3. Selecione **código de autorização** na lista suspensa autorização e você será solicitado a entrar no locatário do Azure AD. Se você já tiver entrado com a conta, talvez não seja solicitado.

4. Após a entrada bem-sucedida, um cabeçalho de `Authorization` é adicionado à solicitação, com um token de acesso do Azure AD. Este é um token de exemplo (codificado em Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selecione **Enviar**e você pode chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma política de validação de JWT para autorizar solicitações previamente

Neste ponto, quando um usuário tenta fazer uma chamada a partir do console do desenvolvedor, é solicitado que o usuário entre. O console do desenvolvedor Obtém um token de acesso em nome do usuário e inclui o token na solicitação feita à API.

No entanto, e se alguém chamar sua API sem um token ou com um token inválido? Por exemplo, tente chamar a API sem o cabeçalho `Authorization`, a chamada ainda passará. O motivo é que o gerenciamento de API não valida o token de acesso neste momento. Ele simplesmente passa o cabeçalho `Authorization` para a API de back-end.

Você pode usar a política [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar previamente as solicitações no gerenciamento de API, Validando os tokens de acesso de cada solicitação de entrada. Se uma solicitação não tiver um token válido, o gerenciamento de API o bloqueará. Por exemplo, adicione a seguinte política à seção de política de `<inbound>` do `Echo API`. Ele verifica a declaração de público em um token de acesso e retorna uma mensagem de erro se o token não for válido. Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID URI of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Esta `openid-config` URL corresponde ao ponto de extremidade v1. Para o ponto de extremidade v2 `openid-config`, use `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Criar um aplicativo para chamar a API

Neste guia, você usou o console do desenvolvedor no gerenciamento de API como o aplicativo cliente de exemplo para chamar o `Echo API` protegido pelo OAuth 2,0. Para saber mais sobre como criar um aplicativo e implementar o OAuth 2,0, confira [Azure Active Directory exemplos de código](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [Azure Active Directory e o OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o gerenciamento de API.
* Para obter outras maneiras de proteger seu serviço de back-end, consulte [autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).

* [Gerencie sua primeira API](import-and-publish.md).
