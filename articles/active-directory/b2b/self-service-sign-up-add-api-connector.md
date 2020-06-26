---
title: Adicione conectores API aos fluxos de inscrição de autosserviço - Azure AD
description: Configurar uma API web para ser utilizada num fluxo de utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386747"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Adicione um conector API a um fluxo de utilizador

Para utilizar um [conector API,](api-connectors-overview.md)primeiro cria o conector API e depois ativa-o num fluxo de utilizador.

## <a name="create-an-api-connector"></a>Criar um conector API

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **todos os conectores API (Pré-visualização)** e, em seguida, selecione **novo conector API**.

   ![Adicione um novo conector API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Forneça um nome de exibição para a chamada. Por exemplo, **Verifique o estado de aprovação**.
6. Forneça o **URL endpoint** para a chamada da API.
7. Forneça a informação de autenticação para a API.

   - Apenas a Autenticação Básica é suportada atualmente. Se desejar utilizar uma API sem Autenticação Básica para fins de desenvolvimento, basta introduzir um nome de **utilizador** e **palavra-passe** falso que a sua API possa ignorar. Para ser utilizado com uma Função Azure com uma chave API, pode incluir o código como parâmetro de consulta no **URL endpoint** (por exemplo, https://contoso.azurewebsites.net/api/endpoint []() <b>?code=0123456789).</b>

   ![Adicione um novo conector API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selecione as reclamações que pretende enviar para a API.
9. Selecione quaisquer reclamações que pretenda receber de volta da API.

   ![Definir reclamações de conector API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Selecione **Guardar**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Ativar o conector API num fluxo de utilizador

Siga estes passos para adicionar um conector API a um fluxo de utilizador de inscrição de autosserviço.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos do Utilizador (Pré-visualização)** e, em seguida, selecione o fluxo do utilizador a que pretende adicionar o conector API.
5. Selecione **conectores API**e, em seguida, selecione os pontos finais da API que pretende invocar nos seguintes passos no fluxo do utilizador:

   - **Depois de iniciar sessão com um fornecedor de identidade**
   - **Antes de criar o utilizador**

   ![Adicionar APIs ao fluxo de utilizador](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selecione **Guardar**.

Saiba [onde pode ativar um conector API num fluxo de utilizador.](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)

## <a name="request-sent-to-the-api"></a>Pedido enviado à API

Um conector API materializa-se como um pedido HTTP POST, enviando reclamações selecionadas como pares de valor-chave num corpo JSON. A resposta também deve ter o cabeçalho HTTP `Content-Type: application/json` . Os atributos são serializados de forma semelhante aos atributos do utilizador do Microsoft Graph. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Pedido de exemplo

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

A **reclamação UI Locales ('ui_locales')** é enviada por padrão em todos os pedidos. Fornece a localizado(s) local(s) de um utilizador e pode ser usado pela API para devolver respostas internacionalizadas. Não aparece no painel de configuração da API.

Se uma reclamação a enviar não tiver um valor no momento em que o ponto final da API é chamado, a reclamação não será enviada para a API.

Os atributos personalizados podem ser criados para o utilizador utilizando o formato **_AttributeName \<extensions-app-id> extension_.** A sua API deverá esperar receber reclamações neste mesmo formato serializado. A sua API pode devolver reclamações com ou sem `<extensions-app-id>` . Para obter mais informações sobre atributos personalizados, consulte [definir atributos personalizados para fluxos de inscrição de autosserviço](user-flow-add-custom-attributes.md).

> [!TIP] 
> [**as identidades ('identidades')**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) e as reclamações do **Email Address ('email_address')** podem ser usadas para identificar um utilizador antes de terem uma conta no seu inquilino. A alegação de "identidades" é enviada quando um utilizador autentica com um Google ou Facebook e 'email_address' é sempre enviada.

## <a name="expected-response-types-from-the-web-api"></a>Tipos de resposta esperados da API web

Quando a API web recebe um pedido HTTP da Azure AD durante um fluxo de utilizador, pode devolver estas respostas:

- [Resposta de continuação](#continuation-response)
- [Resposta de bloqueio](#blocking-response)
- [Resposta de erro de validação](#validation-error-response)

### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do utilizador deve continuar para o passo seguinte. Numa resposta de continuação, a API pode devolver reclamações.

Se uma reclamação for devolvida pela API e selecionada como **Reclamação a receber,** a reclamação faz o seguinte:

- Preencha os campos de entrada na página de recolha de atributos se o conector API for invocado antes da página ser apresentada.
- Sobrepõe-se a qualquer valor que já tenha sido atribuído à reclamação.
- Atribui um valor à reclamação se anteriormente foi nulo.

#### <a name="example-of-a-continuation-response"></a>Exemplo de uma resposta de continuação

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parâmetro                                          | Tipo              | Necessário | Descrição                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versão                                            | String            | Yes      | A versão da API.                                                                                                                                                                                                                                                                |
| action                                             | String            | Yes      | O valor deve `Continue` ser.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Os valores podem ser armazenados no diretório se forem selecionados como Uma **Reivindicação para receber** na configuração do conector API e **nos atributos do Utilizador** para um fluxo de utilizador. Os valores podem ser devolvidos no token se forem selecionados como **reclamação de Candidatura.**                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | A reclamação devolvida pode, opcionalmente, não `_<extensions-app-id>_` conter. Os valores são armazenados no diretório se forem selecionados como Uma **Reivindicação a receber** na configuração do conector API e **no atributo do Utilizador** para um fluxo de utilizador. Os atributos personalizados não podem ser enviados de volta no token. |

### <a name="blocking-response"></a>Resposta de bloqueio

Uma resposta de bloqueio sai do fluxo do utilizador. Pode ser emitido propositadamente pela API para impedir a continuação do fluxo do utilizador exibindo uma página de bloco ao utilizador. A página do bloco apresenta o `userMessage` fornecido pela API.

Segue-se um exemplo da resposta de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parâmetro   | Tipo   | Necessário | Descrição                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| versão     | String | Yes      | A versão da API.                                                    |
| action      | String | Yes      | Valor deve ser`ShowBlockPage`                                              |
| userMessage | String | Yes      | A mensagem a apresentar ao utilizador.                                            |
| code        | String | No       | Código de erro. Pode ser usado para depurar propósitos. Não apresentado ao utilizador. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Experiência de utilizador final com uma resposta de bloqueio

![Página de bloco de exemplo](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Resposta de erro de validação

Uma chamada de API invocada após uma página de recolha de atributos pode devolver uma resposta de erro de validação. Ao fazê-lo, o fluxo do utilizador permanece na página de recolha do atributo e o `userMessage` é apresentado ao utilizador. O utilizador pode então editar e reenviar o formulário. Este tipo de resposta pode ser usado para validação de entrada.

#### <a name="example-of-a-validation-error-response"></a>Exemplo de uma resposta de erro de validação

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parâmetro   | Tipo    | Necessário | Descrição                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| versão     | String  | Yes      | A versão da API.                                                    |
| action      | String  | Yes      | O valor deve `ValidationError` ser.                                           |
| status      | Número inteiro | Yes      | Deve ser valor `400` para uma resposta do ValidationError.                        |
| userMessage | String  | Yes      | A mensagem a apresentar ao utilizador.                                            |
| code        | String  | No       | Código de erro. Pode ser usado para depurar propósitos. Não apresentado ao utilizador. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Experiência final do utilizador com uma resposta de erro de validação

![Página de validação de exemplo](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integração com as Funções do Azure
Pode utilizar um gatilho HTTP em Funções Azure como uma forma simples de criar uma API para usar com o conector API. Utiliza a Função Azure [para, por exemplo,](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)executar lógica de validação e limitar inscrições a domínios específicos. Também pode ligar e invocar outras APIs web, lojas de utilizadores e outros serviços na nuvem.

## <a name="next-steps"></a>Passos seguintes

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Saiba como [adicionar um fluxo de trabalho de aprovação personalizado ao autosserviço](self-service-sign-up-add-approvals.md)
- Começa com as [nossas amostras de quickstart da Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
