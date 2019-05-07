---
title: Proteger uma API com OAuth 2.0 com o Azure Active Directory e gestão de API | Documentos da Microsoft
description: Saiba como proteger uma API da web back-end com o Azure Active Directory e gestão de API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: b5467711f06380ca61b4a9d5150b66c3f945c08c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141079"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteger uma API com OAuth 2.0 com o Azure Active Directory e gestão de API

Este guia mostra-lhe como configurar a sua instância de gestão de API do Azure para proteger uma API, utilizando o protocolo de OAuth 2.0 com o Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir os passos neste artigo, tem de ter:
* Uma instância de gestão de API
* Uma API que está a ser publicada que utiliza a instância de gestão de API
* Um inquilino do Azure AD

## <a name="overview"></a>Descrição geral

Aqui está uma rápida visão geral dos passos:

1. Registe uma aplicação (aplicação de back-end) no Azure AD para representar a API.
2. Registre-se outro aplicativo (aplicativo de cliente) no Azure AD para representar uma aplicação de cliente que precisa para chamar a API.
3. No Azure AD, conceda permissões para permitir que a aplicação de cliente chamar a aplicação de back-end.
4. Configure a consola de programador para utilizar a autorização de utilizador do OAuth 2.0.
5. Adicionar a **jwt validar** política para validar o token de OAuth para cada solicitação de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registar uma aplicação no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registar uma aplicação no Azure AD que representa a API. 

1. Navegue para o inquilino do Azure AD e, em seguida, navegue até **registos de aplicações (Legado)**.

2. Selecione **Novo registo de aplicação**. 

3. Forneça um nome da aplicação. (Neste exemplo, o nome é `backend-app`.)  

4. Escolher **aplicação Web / API** como o **tipo de aplicação**. 

5. Para **URL de início de sessão**, pode utilizar `https://localhost` como um marcador de posição.

6. Selecione **Criar**.

Quando a aplicação é criada, anote o **ID da aplicação**, para utilização num passo subsequente. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registe-se de outra aplicação no Azure AD para representar uma aplicação cliente

Todas as aplicações de cliente que chama a API tem de ser registado como uma aplicação no Azure AD também. Neste exemplo, o aplicativo de cliente de exemplo é a consola de Programador no portal do Programador de gestão de API. Eis como registar-se de outra aplicação no Azure AD para representar a consola de programador.

1. Enquanto estiver no **registos de aplicações (Legado)**, selecione **novo registo de aplicação**. 

2. Forneça um nome da aplicação. (Neste exemplo, o nome é `client-app`.)

3. Escolher **aplicação Web / API** como o **tipo de aplicação**.  

4. Para **URL de início de sessão**, pode utilizar `https://localhost` como um marcador de posição, ou utilize o início de sessão no URL da sua instância de gestão de API. (Neste exemplo, o URL é `https://contoso5.portal.azure-api.net/signin`.)

5. Selecione **Criar**.

Quando a aplicação é criada, anote o **ID da aplicação**, para utilização num passo subsequente. 

Agora, crie um segredo do cliente para esta aplicação, para utilização num passo subsequente.

1. Selecione **configurações** novamente e aceda à **chaves**.

2. Sob **palavras-passe**, forneça um **descrição da chave**. Escolha quando a chave deve expirar e selecione **guardar**.

Anote o valor da chave. 

## <a name="grant-permissions-in-azure-ad"></a>Conceder permissões no Azure AD

Agora que se registrou duas aplicações para representar a API e a consola de programador, tem de conceder permissões para permitir que a aplicação de cliente chamar a aplicação de back-end.  

1. Navegue até **registos de aplicação (Legado)**. 

2. Selecione `client-app`e aceda à **definições**.

3. Selecione **permissões obrigatórias** > **Add**.

4. Selecione **selecionar uma API**e procure `backend-app`.

5. Sob **permissões delegadas**, selecione `Access backend-app`. 

6. Selecione **selecionar**e, em seguida, selecione **feito**. 

> [!NOTE]
> Se **do Azure Active Directory** não está listado em permissões para outras aplicações, selecionadas **Add** para adicioná-lo a partir da lista.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Ativar a autorização de utilizador de OAuth 2.0 na consola do Programador

Neste ponto, ter criado as suas aplicações no Azure AD e concedeu permissões adequadas para permitir que a aplicação de cliente chamar a aplicação de back-end. 

Neste exemplo, a consola de programador é a aplicação de cliente. Os passos seguintes descrevem como ativar a autorização de utilizador de OAuth 2.0 na consola do programador. 

1. No Portal do Azure, navegue para a instância de gestão de API.

2. Selecione **OAuth 2.0** > **adicionar**.

3. Fornecer um **nome a apresentar** e **Descrição**.

4. Para o **URL de página de registo de cliente**, introduza um valor de marcador de posição, como `http://localhost`. O **URL de página de registo de cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar suas próprias contas para fornecedores de OAuth 2.0 que suportam esta operação. Neste exemplo, os utilizadores não criar e configurar suas próprias contas, pelo que utilizar em vez disso, um marcador de posição.

5. Para **tipos de concessão de autorização**, selecione **código de autorização**.

6. Especifique a **URL de ponto final de autorização** e **URL de ponto final do Token**. Obter estes valores a partir da **pontos de extremidade** página no seu inquilino do Azure AD. Navegue para o **registos de aplicações** página novamente e selecione **pontos de extremidade**.

    >[!NOTE]
    > Utilize o **v1** pontos de extremidade aqui

7. Copiar o **ponto final de autorização do OAuth 2.0**e cole-o para o **URL de ponto final de autorização** caixa de texto.

8. Cópia a **ponto final de Token do OAuth 2.0**e cole-o para o **URL de ponto final do Token** caixa de texto. Além de colar no ponto final do token, adicionar um parâmetro de corpo chamado **recursos**. Para o valor deste parâmetro, utilize o **ID da aplicação** para a aplicação de back-end.

9. Em seguida, especifique as credenciais de cliente. Estas são as credenciais para a aplicação de cliente.

10. Para **ID de cliente**, utilize o **ID de aplicação** para a aplicação de cliente.

11. Para **segredo do cliente**, utilize a chave que criou para a aplicação de cliente anteriormente. 

12. Imediatamente após o segredo do cliente é o **redirect_url** para a autorização de tipo de concessão de código. Tome nota deste URL.

13. Selecione **Criar**.

14. Volte para o **definições** página da sua aplicação de cliente.

15. Selecione **URLs de resposta**e cole o **redirect_url** na primeira linha. Neste exemplo, substituídos `https://localhost` com o URL na primeira linha.  

Agora que configurou um servidor de autorização de OAuth 2.0, a consola de programador pode obter os tokens de acesso do Azure AD. 

A próxima etapa é ativar a autorização de utilizador de OAuth 2.0 para a sua API. Isto permite que a consola de programador para saber o que é necessário obter um token de acesso em nome do utilizador, antes de fazer chamadas para a sua API.

1. Navegue para a instância de gestão de API e aceda a **APIs**.

2. Selecione a API que pretende proteger. Neste exemplo, usa o `Echo API`.

3. Aceda a **definições**.

4. Sob **Security**, escolha **OAuth 2.0**e selecione o servidor de OAuth 2.0 que configurou anteriormente. 

5. Selecione **Guardar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar com êxito a API a partir do portal do Programador

> [!NOTE]
> Esta secção não se aplica para o **consumo** escalão, o que não suporta o portal do programador.

Agora que a autorização de utilizador do OAuth 2.0 está ativada no `Echo API`, a consola de programador obtém um token de acesso em nome do utilizador, antes de chamar a API.

1. Navegar para qualquer operação sob a `Echo API` no developer portal e selecione **experimente**. Isso nos leva para a consola de programador.

2. Tenha em atenção de um novo item na **autorização** secção, correspondente para o servidor de autorização que acabou de adicionar.

3. Selecione **código de autorização** da autorização na lista pendente e são-lhe pedido que inicie sessão no inquilino do Azure AD. Se já tiver sessão iniciada com a conta, não pode ser pedido.

4. Após o início de sessão com êxito, um `Authorization` cabeçalho é adicionado ao pedido, com um token de acesso do Azure AD. Segue-se um token de exemplo (codificado por Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selecione **enviar**, e pode chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma política de validação do JWT para autorizar previamente pedidos

Neste momento, quando um usuário tentar fazer uma chamada a partir da consola de programador, é pedido ao utilizador para iniciar sessão. A consola de programador obtém um token de acesso em nome do utilizador.

Mas e se alguém liga para sua API sem um token ou com um token inválido? Por exemplo, pode ainda chamar a API, mesmo se eliminar o `Authorization` cabeçalho. O motivo é que a gestão de API não valida o token de acesso neste momento. Ele simplesmente passa a `Authorization` cabeçalho para a API de back-end.

Pode utilizar o [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) política para autorizar previamente pedidos na gestão de API ao validar os tokens de acesso de cada pedido recebido. Se um pedido não tem um token válido, a gestão de API bloqueia-lo. Por exemplo, pode adicionar a seguinte política para o `<inbound>` secção política do `Echo API`. Verifica a afirmação de audiência num token de acesso e devolve uma mensagem de erro se o token não é válido. Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Criar um aplicativo para chamar a API

Neste guia, usou a consola de programador na gestão de API como o exemplo de aplicação de cliente para chamar o `Echo API` protegidos pelo OAuth 2.0. Para saber mais sobre como criar uma aplicação e implementar o OAuth 2.0, veja [exemplos de código do Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [Azure Active Directory e OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a gestão de API.
* Para outras formas de proteger o seu serviço de back-end, consulte [autenticação de certificados mútuos](api-management-howto-mutual-certificates.md).

* [Criar uma instância de serviço de gestão de API](get-started-create-service-instance.md).

* [Gerir a sua primeira API](import-and-publish.md).
