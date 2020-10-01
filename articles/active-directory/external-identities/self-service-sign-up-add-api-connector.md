---
title: Adicione conectores API aos fluxos de inscrição de autosserviço - Azure AD
description: Configurar uma API web para ser utilizada num fluxo de utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db68528a810ebc9cd61b205dd5167396d75db7f7
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613990"
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

   ![Configurar um novo conector API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Selecione **Guardar**.

> [!IMPORTANT]
> Anteriormente, tinha de configurar quais os atributos do utilizador para enviar à API ('Reclamações a enviar') e quais os atributos do utilizador para aceitar da API ('Reclamações a receber'). Agora, todos os atributos do utilizador são enviados por padrão se tiverem um valor e qualquer atributo de utilizador pode ser devolvido pela API numa resposta de "continuação".

## <a name="the-request-sent-to-your-api"></a>O pedido enviado à sua API
Um conector API materializa-se como um pedido **HTTP POST,** enviando atributos do utilizador ('claims') como pares de valor-chave num corpo JSON. Os atributos são serializados de forma semelhante às propriedades do utilizador [do Microsoft Graph.](https://docs.microsoft.com/graph/api/resources/user#properties) 

**Pedido de exemplo**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

Apenas as propriedades do utilizador e os atributos personalizados listados no **Azure Ative**  >  **Directory External Identities**Custom user  >  **experience** estão disponíveis para serem enviados no pedido.

Os atributos personalizados existem no formato ** \<extensions-app-id> _AttributeName extension_**  no diretório. A sua API deverá esperar receber reclamações neste mesmo formato serializado. Para obter mais informações sobre atributos personalizados, consulte [definir atributos personalizados para fluxos de inscrição de autosserviço](user-flow-add-custom-attributes.md).

Além disso, a reclamação **UI Locales ('ui_locales')** é enviada por padrão em todos os pedidos. Fornece a localização(s) de um utilizador como configurado no seu dispositivo que pode ser usado pela API para devolver respostas internacionalizadas.

> [!IMPORTANT]
> Se uma reclamação a enviar não tiver um valor no momento em que o ponto final da API é chamado, a reclamação não será enviada para a API. A sua API deve ser concebida para verificar explicitamente o valor que espera.

> [!TIP] 
> [**as identidades ('identidades')**](https://docs.microsoft.com/graph/api/resources/objectidentity) e as reclamações do **Email Address ('email')** podem ser utilizadas pela API para identificar um utilizador antes de terem uma conta no seu inquilino. A alegação de "identidades" é enviada quando um utilizador autentica com um fornecedor de identidade, como o Google ou o Facebook. 'e-mail' é sempre enviado.

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

## <a name="after-signing-in-with-an-identity-provider"></a>Depois de iniciar sessão com um fornecedor de identidade

Um conector API neste passo no processo de inscrição é invocado imediatamente após o utilizador autenticar com um fornecedor de identidade (Google, Facebook, Azure AD). Este passo antecede a ***página de recolha de atributos***, que é o formulário apresentado ao utilizador para recolher os atributos do utilizador. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Pedido de exemplo enviado à API neste passo
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de resposta esperados da API web neste passo

Quando a API web recebe um pedido HTTP da Azure AD durante um fluxo de utilizador, pode devolver estas respostas:

- Resposta de continuação
- Resposta de bloqueio

#### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do utilizador deve continuar para o passo seguinte: a página de recolha de atributos.

Numa resposta de continuação, a API pode devolver reclamações. Se uma reclamação for devolvida pela API, a reclamação faz o seguinte:

- Preencha o campo de entrada na página de recolha de atributos.

Veja um exemplo de [uma resposta de continuação](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Resposta de bloqueio

Uma resposta de bloqueio sai do fluxo do utilizador. Pode ser emitido propositadamente pela API para impedir a continuação do fluxo do utilizador exibindo uma página de bloco ao utilizador. A página do bloco apresenta o `userMessage` fornecido pela API.

Veja um exemplo de uma [resposta de bloqueio](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Antes de criar o utilizador

Um conector API neste passo no processo de inscrição é invocado após a página de recolha do atributo, se um estiver incluído. Este passo é sempre invocado antes de uma conta de utilizador ser criada em Azure AD. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Pedido de exemplo enviado à API neste passo

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de resposta esperados da API web neste passo

Quando a API web recebe um pedido HTTP da Azure AD durante um fluxo de utilizador, pode devolver estas respostas:

- Resposta de continuação
- Resposta de bloqueio
- Resposta de validação

#### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do utilizador deve continuar para o passo seguinte: criar o utilizador no diretório.

Numa resposta de continuação, a API pode devolver reclamações. Se uma reclamação for devolvida pela API, a reclamação faz o seguinte:

- Sobrepõe-se a qualquer valor que já tenha sido atribuído à reclamação a partir da página de recolha de atributos.

Veja um exemplo de [uma resposta de continuação](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Resposta de bloqueio
Uma resposta de bloqueio sai do fluxo do utilizador. Pode ser emitido propositadamente pela API para impedir a continuação do fluxo do utilizador exibindo uma página de bloco ao utilizador. A página do bloco apresenta o `userMessage` fornecido pela API.

Veja um exemplo de uma [resposta de bloqueio](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Resposta de erro de validação
 Quando a API responde com uma resposta de erro de validação, o fluxo do utilizador permanece na página de recolha do atributo e um `userMessage` é apresentado ao utilizador. O utilizador pode então editar e reenviar o formulário. Este tipo de resposta pode ser usado para validação de entrada.

Veja um exemplo de uma [resposta de erro de validação](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Respostas de exemplo

### <a name="example-of-a-continuation-response"></a>Exemplo de uma resposta de continuação

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

| Parâmetro                                          | Tipo              | Obrigatório | Descrição                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versão                                            | String            | Sim      | A versão da API.                                                                                                                                                                                                                                                                |
| ação                                             | String            | Sim      | O valor deve `Continue` ser.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Não       | Os valores podem ser armazenados no diretório se forem selecionados como Uma **Reivindicação para receber** na configuração do conector API e **nos atributos do Utilizador** para um fluxo de utilizador. Os valores podem ser devolvidos no token se forem selecionados como **reclamação de Candidatura.**                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Não       | A reclamação devolvida não precisa de `_<extensions-app-id>_` conter. Os valores são armazenados no diretório se forem selecionados como Uma **Reivindicação a receber** na configuração do conector API e **no atributo do Utilizador** para um fluxo de utilizador. Os atributos personalizados não podem ser enviados de volta no token. |

### <a name="example-of-a-blocking-response"></a>Exemplo de uma resposta de bloqueio

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

| Parâmetro   | Tipo   | Obrigatório | Descrição                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| versão     | String | Sim      | A versão da API.                                                    |
| ação      | String | Sim      | Valor deve ser `ShowBlockPage`                                              |
| userMessage | String | Sim      | A mensagem a apresentar ao utilizador.                                            |
| code        | String | Não       | Código de erro. Pode ser usado para depurar propósitos. Não apresentado ao utilizador. |

**Experiência de utilizador final com uma resposta de bloqueio**

![Página de bloco de exemplo](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exemplo de uma resposta de erro de validação

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

| Parâmetro   | Tipo    | Obrigatório | Descrição                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| versão     | String  | Sim      | A versão da API.                                                    |
| ação      | String  | Sim      | O valor deve `ValidationError` ser.                                           |
| status      | Número inteiro | Sim      | Deve ser valor `400` para uma resposta do ValidationError.                        |
| userMessage | String  | Sim      | A mensagem a apresentar ao utilizador.                                            |
| code        | String  | Não       | Código de erro. Pode ser usado para depurar propósitos. Não apresentado ao utilizador. |

**Experiência de utilizador final com uma resposta de erro de validação**

![Página de validação de exemplo](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Boas práticas e como resolver problemas

### <a name="using-serverless-cloud-functions"></a>Usando funções de nuvem sem servidor
As funções sem servidor, como os gatilhos HTTP em Funções Azure, fornecem uma forma simples de criar pontos finais da API para utilizar com o conector API. Pode utilizar a função de nuvem sem servidor para, [por exemplo,](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)executar lógica de validação e limitar inscrições a domínios específicos. A função de nuvem sem servidor também pode ligar e invocar outras APIs web, lojas de utilizadores e outros serviços na nuvem para cenários mais complexos.

### <a name="best-practices"></a>Melhores práticas
Certifique-se de que:
* A sua API está a seguir os contratos de pedido e resposta da API, tal como acima descrito. 
* O **URL endpoint** do conector API aponta para o ponto final API correto.
* A sua API verifica explicitamente os valores nulos dos créditos recebidos.
* A sua API responde o mais rapidamente possível para garantir uma experiência fluida do utilizador.
    * Se utilizar uma função sem servidor ou um serviço web escalável, utilize um plano de hospedagem que mantenha a API "acordada" ou "quente". Para funções Azure, recomenda-se a utilização do [plano Premium](../../azure-functions/functions-scale.md#premium-plan). 


### <a name="use-logging"></a>Utilizar registos madeireiros
Em geral, é útil utilizar as ferramentas de registo ativadas pelo seu serviço de API web, como [insights de aplicação,](../../azure-functions/functions-monitoring.md)para monitorizar a sua API para obter códigos de erro inesperados, exceções e mau desempenho.
* Monitorize códigos de estado HTTP que não sejam HTTP 200 ou 400.
* Um código de estado HTTP 401 ou 403 normalmente indica que há um problema com a sua autenticação. Verifique novamente a camada de autenticação da API e a configuração correspondente no conector API.
* Utilize níveis mais agressivos de exploração madeireira (por exemplo, "trace" ou "depurado") em desenvolvimento, se necessário.
* Monitorize a sua API para longos tempos de resposta.

## <a name="next-steps"></a>Passos seguintes
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Saiba como [adicionar um fluxo de trabalho de aprovação personalizado ao autosserviço](self-service-sign-up-add-approvals.md)
- Começa com as [nossas amostras de quickstart da Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
