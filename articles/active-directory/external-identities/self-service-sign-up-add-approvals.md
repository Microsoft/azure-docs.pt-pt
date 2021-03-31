---
title: Adicionar aprovações personalizadas aos fluxos de inscrição de self-service - Azure AD
description: Adicione conectores API para fluxos de trabalho de homologação personalizados em identidades externas self-service - Azure Ative Directy (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41d7d45fd11f2dc26fc50182a7649b23cd21196
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008761"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Adicione um fluxo de trabalho de aprovação personalizado à inscrição de self-service

Com [conectores API,](api-connectors-overview.md)pode integrar-se com os seus próprios fluxos de trabalho de aprovação personalizada com inscrição de self-service para que possa gerir quais as contas de utilizador do hóspede criadas no seu inquilino.

Este artigo dá um exemplo de como integrar-se com um sistema de aprovação. Neste exemplo, o fluxo de utilizador de inscrição de autosserviço recolhe dados do utilizador durante o processo de inscrição e passa-os para o seu sistema de aprovação. Então, o sistema de aprovação pode:

- Aprove automaticamente o utilizador e permita que a Azure AD crie a conta do utilizador.
- Desencadeie uma revisão manual. Se o pedido for aprovado, o sistema de aprovação utiliza o Microsoft Graph para forrê-lo na conta de utilizador. O sistema de aprovação também pode notificar o utilizador de que a sua conta foi criada.

> [!IMPORTANT]
>**A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="register-an-application-for-your-approval-system"></a>Registar um pedido para o seu sistema de aprovação

Tem de registar o seu sistema de aprovação como uma aplicação no seu inquilino Azure AD para que possa autenticar com a Azure AD e ter permissão para criar utilizadores. Saiba mais sobre [o básico de autenticação e autorização para o Microsoft Graph](/graph/auth/auth-concepts).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
4. Introduza um **Nome** para a aplicação, por exemplo, _Aprovações de inscrição_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Selecione **Registar**. Pode deixar outros campos à sua revelia.

   ![Screenshot que realça o botão Registar.](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. Em **Gerir** no menu esquerdo, selecione **permissões API** e, em seguida, selecione **Adicionar uma permissão**.
7. Na página de **permissões da API do Pedido,** selecione **o Gráfico do Microsoft** e, em seguida, selecione **permissões de aplicação**.
8. Sob **permissões Selecione,** expanda o **Utilizador** e, em seguida, selecione a caixa de verificação **User.ReadWrite.All.** Esta permissão permite que o sistema de aprovação crie o utilizador mediante aprovação. Em seguida, **selecione Adicionar permissões**.

   ![Registar uma página de inscrição](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Na página de permissões da **API,** selecione **o consentimento de administração grant para (o nome do seu inquilino)** e, em seguida, selecione **Sim**.
10. Em **Gerir** no menu esquerdo, selecione **Certificados & segredos,** e, em seguida, selecione **Novo segredo do cliente**.
11. Insira uma **Descrição** para o segredo, por _exemplo, aprova_ o segredo do cliente, e selecione a duração para quando o segredo do cliente **expirar.** Em seguida, selecione **Adicionar**.
12. Copie o valor do segredo do cliente.

    ![Copie o segredo do cliente para uso no sistema de aprovação](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Configure o seu sistema de aprovação para utilizar o ID da **aplicação** como ID do cliente e o segredo do **cliente** que gerou para autenticar com Azure AD.

## <a name="create-the-api-connectors"></a>Criar os conectores API

Em seguida, [irá criar os conectores API](self-service-sign-up-add-api-connector.md#create-an-api-connector) para o fluxo de utilizador de inscrição de autosserviço. O seu sistema de aprovação API precisa de dois conectores e pontos finais correspondentes, como os exemplos apresentados abaixo. Estes conectores API fazem o seguinte:

- **Verifique o estado de aprovação**. Envie uma chamada para o sistema de aprovação imediatamente após um utilizador entrar em sing-in com um fornecedor de identidade para verificar se o utilizador tem um pedido de aprovação existente ou já foi negado. Se o seu sistema de aprovação apenas fizer decisões de aprovação automáticas, este conector API pode não ser necessário. Exemplo de um conector API "Verificar o estado de aprovação".

  ![Verifique a configuração do conector API do estado de aprovação](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Solicitar aprovação** - Enviar uma chamada para o sistema de aprovação depois de um utilizador completar a página de recolha de atributos, mas antes da conta de utilizador ser criada, para solicitar a aprovação. O pedido de aprovação pode ser concedido automaticamente ou revisto manualmente. Exemplo de um conector API de "Pedido de aprovação". 

  ![Solicitação de configuração do conector API de aprovação](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Para criar estes conectores, siga os passos para [criar um conector API](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Ativar os conectores API num fluxo de utilizador

Agora irá adicionar os conectores API a um fluxo de utilizador de inscrição de autosserviço com estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos do Utilizador** e, em seguida, selecione o fluxo do utilizador para o conector API.
5. Selecione **conectores API** e, em seguida, selecione os pontos finais da API que pretende invocar nos seguintes passos no fluxo do utilizador:

   - **Depois de iniciar sessão com um fornecedor de identidade**: Selecione o seu conector API de estado de aprovação, por _exemplo, Verifique o estado de aprovação_.
   - **Antes de criar o utilizador**: Selecione o seu conector API de pedido de aprovação, por exemplo, _Solicite a aprovação._

   ![Adicionar APIs ao fluxo de utilizador](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Selecione **Guardar**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Controlar o fluxo de inscrição com respostas API

O seu sistema de aprovação pode utilizar as suas respostas quando é chamado para controlar o fluxo de inscrição. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Pedido e respostas para o conector API "Verificar o estado de aprovação"

Exemplo do pedido recebido pela API a partir do conector API "Verificar o estado de aprovação":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

As reclamações exatas enviadas à API dependem da informação fornecida pelo fornecedor de identidade. 'e-mail' é sempre enviado.

#### <a name="continuation-response-for-check-approval-status"></a>Resposta de continuação para "Verificar o estado de aprovação"

O ponto final **do estado de aprovação** do Cheque API deve devolver uma resposta de continuação se:

- O utilizador não solicitou previamente uma aprovação.

Exemplo da resposta de continuação:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Resposta de bloqueio para "Verificar o estado de aprovação"

O ponto final **do estado de aprovação** do Cheque API deve devolver uma resposta de bloqueio se:

- A aprovação do utilizador está pendente.
- O utilizador foi negado e não deve ser autorizado a pedir aprovação novamente.

Seguem-se exemplos de respostas de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Pedido e respostas para o conector API "Pedido de aprovação"

Exemplo de um pedido HTTP recebido pela API a partir do conector API "Pedido de aprovação":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

As reclamações exatas enviadas à API dependem de que informações são recolhidas junto do utilizador ou são fornecidas pelo fornecedor de identidade.

#### <a name="continuation-response-for-request-approval"></a>Resposta de continuação para "Pedido de aprovação"

O ponto final da **API de aprovação** do Pedido deve devolver uma resposta de continuação se:

- O utilizador pode ser **_automaticamente aprovado_**.

Exemplo da resposta de continuação:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Se for recebida uma resposta de continuação, a Azure AD cria uma conta de utilizador e direciona o utilizador para a aplicação.

#### <a name="blocking-response-for-request-approval"></a>Resposta de bloqueio para "Solicitar aprovação"

O ponto final da **API de aprovação** do Pedido deve devolver uma resposta de bloqueio se:

- Foi criado um pedido de aprovação do utilizador e está agora pendente.
- Um pedido de aprovação do utilizador foi automaticamente negado.

Seguem-se exemplos de respostas de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

A `userMessage` resposta é apresentada ao utilizador, por exemplo:

![Exemplo pendente página de aprovação](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Criação de conta de utilizador após aprovação manual

Após obter aprovação manual, o sistema de aprovação personalizado cria uma conta [de utilizador](/graph/azuread-users-concept-overview) utilizando o [Microsoft Graph](/graph/use-the-api). A forma como o seu sistema de aprovação prevê a conta de utilizador depende do fornecedor de identidade que foi utilizado pelo utilizador.

### <a name="for-a-federated-google-or-facebook-user-and-email-one-time-passcode"></a>Para um utilizador federado do Google ou Facebook e código de acesso de e-mail

> [!IMPORTANT]
> O sistema de aprovação deve verificar explicitamente que `identities` , e que estejam `identities[0]` `identities[0].issuer` presentes e que seja igual a `identities[0].issuer` 'facebook', 'google' ou 'mail' para utilizar este método.

Se o seu utilizador se inscreve com uma conta do Google ou facebook ou enviar uma senha de e-mail, pode utilizar a [API de criação](/graph/api/user-post-users?tabs=http)do Utilizador.

1. O sistema de aprovação recebe o pedido HTTP do fluxo do utilizador.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. O sistema de aprovação utiliza o Microsoft Graph para criar uma conta de utilizador.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parâmetro                                           | Obrigatório | Descrição                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Yes      | Pode ser gerado tomando a `email` reclamação enviada para a API, substituindo o `@` personagem por `_` , e pré-pendente para `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Yes      | Deve ser definido para `true` .                                                                                                                                                 |
| correio                                                | Yes      | Equivalente à `email` reclamação enviada à API.                                                                                                               |
| userType                                            | Yes      | Deve `Guest` ser. Designa este utilizador como utilizador convidado.                                                                                                                 |
| identidades                                          | Yes      | A informação de identidade federada.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | No       | Outros atributos incorporados `displayName` `city` como, e outros. Os nomes dos parâmetros são os mesmos que os parâmetros enviados pelo conector API.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | No       | Atributos personalizados sobre o utilizador. Os nomes dos parâmetros são os mesmos que os parâmetros enviados pelo conector API.                                                            |

### <a name="for-a-federated-azure-active-directory-user-or-microsoft-account-user"></a>Para um utilizador federado do Azure Ative Directory ou utilizador de conta Microsoft

Se um utilizador entrar com uma conta federada do Azure Ative Directory ou uma conta Microsoft, deve utilizar o [convite API](/graph/api/invitation-post) para criar o utilizador e, em seguida, opcionalmente, a [API de atualização](/graph/api/user-update) do utilizador para atribuir mais atributos ao utilizador.

1. O sistema de aprovação recebe o pedido HTTP do fluxo do utilizador.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. O sistema de aprovação cria o convite utilizando o `email` conector API fornecido.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress": "johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Exemplo da resposta:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. O sistema de aprovação utiliza o ID do utilizador convidado para atualizar a conta do utilizador com atributos de utilizador recolhidos (opcional).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Passos seguintes

- Começa com as [nossas amostras de quickstart da Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Check-out o [autosserviço de inscrição para utilizadores convidados com amostra de aprovação manual](code-samples-self-service-sign-up.md#custom-approval-workflows).
