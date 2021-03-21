---
title: Adicione conectores API aos fluxos de inscrição de autosserviço - Azure AD
description: Configurar uma API web para ser utilizada num fluxo de utilizador.
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
ms.openlocfilehash: 703e3b4c951bc4c3a22f82b9faa31789d1abf868
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008727"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Adicione um conector API a um fluxo de utilizador

Para utilizar um [conector API,](api-connectors-overview.md)primeiro cria o conector API e depois ativa-o num fluxo de utilizador.

> [!IMPORTANT]
>**A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Criar um conector API

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **todos os conectores API** e, em seguida, selecione **novo conector API**.

   ![Adicione um novo conector API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Forneça um nome de exibição para a chamada. Por exemplo, **Verifique o estado de aprovação**.
6. Forneça o **URL endpoint** para a chamada da API.
7. Escolha o **tipo de autenticação** e configuure as informações de autenticação para ligar para a sua API. Consulte a secção abaixo para obter opções para garantir a sua API.

    ![Configurar um conector API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selecione **Guardar**.

## <a name="securing-the-api-endpoint"></a>Assegurar o ponto final da API
Pode proteger o seu ponto final da API utilizando a autenticação básica HTTP ou a autenticação do certificado de cliente HTTPS (pré-visualização). Em qualquer dos casos, fornece as credenciais que o Azure Ative Directory utilizará ao ligar para o seu ponto final da API. O seu ponto final da API verifica as credenciais e executa as decisões de autorização.

### <a name="http-basic-authentication"></a>Autenticação básica HTTP
A autenticação básica HTTP é definida no [RFC 2617](https://tools.ietf.org/html/rfc2617). O Azure Ative Directory envia um pedido HTTP com as credenciais do cliente `username` `password` (e) no `Authorization` cabeçalho. As credenciais são formatadas como a cadeia codificada `username:password` base64. A sua API verifica então estes valores para determinar se rejeita ou não uma chamada da API.

### <a name="https-client-certificate-authentication-preview"></a>Autenticação de certificado de cliente HTTPS (pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização e é fornecida sem um acordo de nível de serviço. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A autenticação do certificado de cliente é uma autenticação baseada em certificados mútuos, onde o cliente fornece um certificado de cliente ao servidor para provar a sua identidade. Neste caso, o Azure Ative Directory utilizará o certificado que carrega como parte da configuração do conector API. Isto acontece como parte do aperto de mão SSL. Apenas os serviços que possuam certificados adequados podem aceder ao seu serviço API. O certificado de cliente é um certificado digital X.509. Em ambientes de produção, deve ser assinado por uma autoridade de certificados. 

Para criar um certificado, pode utilizar [o Azure Key Vault,](../../key-vault/certificates/create-certificate.md)que tem opções para certificados auto-assinados e integrações com fornecedores de emitentes de certificados para certificados assinados. Em seguida, pode [exportar o certificado](../../key-vault/certificates/how-to-export-certificate.md) e carregá-lo para utilização na configuração dos conectores API. Note que a palavra-passe só é necessária para ficheiros de certificados protegidos por uma palavra-passe. Também pode utilizar o [cmdlet New-SelfSignedCertificate da PowerShell](../../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) para gerar um certificado auto-assinado.

Para o Azure App Service e Azure Functions, consulte [a autenticação mútua de configuração TLS](../../app-service/app-service-web-configure-tls-mutual-auth.md) para aprender a ativar e validar o certificado a partir do seu ponto final da API.

Recomenda-se que desista de alertas para quando o seu certificado expirar. Para enviar um novo certificado para um conector API existente, selecione o conector API em **todos os conectores API** e clique no **Upload novo certificado**. O certificado mais recentemente carregado que não está expirado e já passou a data de início será automaticamente utilizado pelo Azure Ative Directory.

### <a name="api-key"></a>Chave de API
Alguns serviços utilizam um mecanismo de "chave API" para obstacular o acesso aos seus pontos finais HTTP durante o desenvolvimento. Para [funções Azure,](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)pode fazê-lo incluindo o `code` parâmetro de consulta no URL **endpoint**. Por exemplo, `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Este não é um mecanismo que deve ser utilizado apenas na produção. Portanto, a configuração para a autenticação básica ou certificado é sempre necessária. Se não pretender implementar nenhum método de autenticação (não recomendado) para fins de desenvolvimento, pode escolher a autenticação básica e utilizar valores temporários `username` para e que a sua `password` API possa ignorar enquanto implementa a autorização na sua API.

## <a name="the-request-sent-to-your-api"></a>O pedido enviado à sua API
Um conector API materializa-se como um pedido **HTTP POST,** enviando atributos do utilizador ('claims') como pares de valor-chave num corpo JSON. Os atributos são serializados de forma semelhante às propriedades do utilizador [do Microsoft Graph.](/graph/api/resources/user#properties) 

**Pedido de exemplo**
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

Apenas as propriedades do utilizador e os atributos personalizados listados no **Azure Ative**  >  **Directory External Identities** Custom user  >  **experience** estão disponíveis para serem enviados no pedido.

Os atributos personalizados existem no formato **\<extensions-app-id> _AttributeName extension_**  no diretório. A sua API deverá esperar receber reclamações neste mesmo formato serializado. Para obter mais informações sobre atributos personalizados, consulte [definir atributos personalizados para fluxos de inscrição de autosserviço](user-flow-add-custom-attributes.md).

Além disso, a reclamação **UI Locales ('ui_locales')** é enviada por padrão em todos os pedidos. Fornece a localização(s) de um utilizador como configurado no seu dispositivo que pode ser usado pela API para devolver respostas internacionalizadas.

> [!IMPORTANT]
> Se uma reclamação não tiver um valor no momento em que o ponto final da API é chamado, a reclamação não será enviada para a API. A sua API deve ser concebida para verificar e lidar explicitamente com o caso em que uma reclamação não está no pedido.

> [!TIP] 
> [**as identidades ('identidades')**](/graph/api/resources/objectidentity) e as reclamações do **Email Address ('email')** podem ser utilizadas pela API para identificar um utilizador antes de terem uma conta no seu inquilino. A alegação de "identidades" é enviada quando um utilizador autentica com um fornecedor de identidade, como o Google ou o Facebook. 'e-mail' é sempre enviado.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Ativar o conector API num fluxo de utilizador

Siga estes passos para adicionar um conector API a um fluxo de utilizador de inscrição de autosserviço.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos do Utilizador** e, em seguida, selecione o fluxo do utilizador a que pretende adicionar o conector API.
5. Selecione **conectores API** e, em seguida, selecione os pontos finais da API que pretende invocar nos seguintes passos no fluxo do utilizador:

   - **Depois de iniciar sessão com um fornecedor de identidade**
   - **Antes de criar o utilizador**

   ![Adicionar APIs ao fluxo de utilizador](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selecione **Guardar**.

## <a name="after-signing-in-with-an-identity-provider"></a>Depois de iniciar sessão com um fornecedor de identidade

Um conector API neste passo no processo de inscrição é invocado imediatamente após o utilizador autenticar com um fornecedor de identidade (como Google, Facebook, & Azure AD). Este passo antecede a ***página de recolha de atributos***, que é o formulário apresentado ao utilizador para recolher os atributos do utilizador. Este passo não é invocado se um utilizador estiver a registar-se numa conta local.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Pedido de exemplo enviado à API neste passo
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

### <a name="example-request-sent-to-the-api-at-this-step"></a>Pedido de exemplo enviado à API neste passo

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

| Parâmetro                                          | Tipo              | Necessário | Descrição                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versão                                            | String            | Yes      | A versão da API.                                                                                                                                                                                                                                                                |
| ação                                             | String            | Yes      | O valor deve `Continue` ser.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Os valores podem ser armazenados no diretório se forem selecionados como Uma **Reivindicação para receber** na configuração do conector API e **nos atributos do Utilizador** para um fluxo de utilizador. Os valores podem ser devolvidos no token se forem selecionados como **reclamação de Candidatura.**                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | A reclamação devolvida não precisa de `_<extensions-app-id>_` conter. Os valores devolvidos podem substituir valores recolhidos por um utilizador. Também podem ser devolvidos no token se configurados como parte da aplicação.  |

### <a name="example-of-a-blocking-response"></a>Exemplo de uma resposta de bloqueio

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parâmetro   | Tipo   | Necessário | Descrição                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| versão     | String | Yes      | A versão da API.                                                    |
| ação      | String | Yes      | Valor deve ser `ShowBlockPage`                                              |
| userMessage | String | Yes      | A mensagem a apresentar ao utilizador.                                            |

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
}
```

| Parâmetro   | Tipo    | Necessário | Descrição                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| versão     | String  | Yes      | A versão da sua API.                                                    |
| ação      | String  | Yes      | O valor deve `ValidationError` ser.                                           |
| status      | Número inteiro | Yes      | Deve ser valor `400` para uma resposta do ValidationError.                        |
| userMessage | String  | Yes      | A mensagem a apresentar ao utilizador.                                            |

> [!NOTE]
> O código de estado HTTP tem de ser "400" para além do valor de "estado" no corpo da resposta.

**Experiência de utilizador final com uma resposta de erro de validação**

![Página de validação de exemplo](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Boas práticas e como resolver problemas

### <a name="using-serverless-cloud-functions"></a>Usando funções de nuvem sem servidor
As funções sem servidor, como os gatilhos HTTP em Funções Azure, fornecem uma forma simples de criar pontos finais da API para utilizar com o conector API. Pode utilizar a função de nuvem sem servidor para, [por exemplo,](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)executar lógica de validação e limitar inscrições a domínios de e-mail específicos. A função de nuvem sem servidor também pode ligar e invocar outras APIs web, lojas de utilizadores e outros serviços na nuvem para cenários mais complexos.

### <a name="best-practices"></a>Melhores práticas
Certifique-se de que:
* A sua API está a seguir os contratos de pedido e resposta da API, tal como acima descrito. 
* O **URL endpoint** do conector API aponta para o ponto final API correto.
* A sua API verifica explicitamente os valores nulos dos créditos recebidos.
* A sua API responde o mais rapidamente possível para garantir uma experiência fluida do utilizador.
    * Se utilizar uma função sem servidor ou um serviço web escalável, utilize um plano de hospedagem que mantenha a API "acordada" ou "quente". na produção. Para funções Azure, recomenda-se a utilização do [plano Premium](../../azure-functions/functions-scale.md)

### <a name="use-logging"></a>Utilizar registos madeireiros
Em geral, é útil utilizar as ferramentas de registo ativadas pelo seu serviço de API web, como [insights de aplicação,](../../azure-functions/functions-monitoring.md)para monitorizar a sua API para obter códigos de erro inesperados, exceções e mau desempenho.
* Monitorize códigos de estado HTTP que não sejam HTTP 200 ou 400.
* Um código de estado HTTP 401 ou 403 normalmente indica que há um problema com a sua autenticação. Verifique novamente a camada de autenticação da API e a configuração correspondente no conector API.
* Utilize níveis mais agressivos de exploração madeireira (por exemplo, "trace" ou "depurado") em desenvolvimento, se necessário.
* Monitorize a sua API para longos tempos de resposta.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [adicionar um fluxo de trabalho de aprovação personalizado ao autosserviço](self-service-sign-up-add-approvals.md)
- Começa com [as nossas amostras de arranque rápido.](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)