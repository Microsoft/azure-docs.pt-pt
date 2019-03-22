---
title: Como delegar subscrição do produto e de registo de utilizador
description: Aprenda a delegar subscrição do produto e de registo de utilizador para uma aplicação de terceiros na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: c15dc83929aeaf6811f4d19bfca462abfacf4014
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892460"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar subscrição do produto e de registo de utilizador
A delegação permite-lhe utilizar o seu Web site existente para a manipulação de desenvolvedor início de sessão-em/inscrever-se e subscrição aos produtos em vez de utilizar a funcionalidade interna no portal do programador. Isto permite que o seu Web site para os dados de utilizador e realizar a validação dos seguintes passos de forma personalizada.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegar início de sessão do desenvolvedor sessão e inscrição
Para delegar o desenvolvedor de início de sessão e inscrição ao seu Web site existente, terá de criar um ponto de final de delegação especial no seu site, que atua como o ponto de entrada para qualquer esse pedido iniciado a partir do portal de programador da gestão de API.

O fluxo de trabalho final será o seguinte:

1. Desenvolvedor de cliques na ligação de início de sessão ou Inscreva-se no portal do desenvolvedor de gestão de API
2. Navegador é redirecionado para o ponto final de delegação
3. Ponto final de delegação em troca redireciona para ou apresenta a IU pedir ao utilizador para iniciar sessão no ou Inscreva-se
4. Em caso de êxito, o utilizador é redirecionado de volta para a página portal de gestão de API para programadores iniciadas a partir de

Para começar, vamos primeiro gestão de API de configuração para encaminhar pedidos através do seu ponto final de delegação. No portal do Editor de gestão de API, clique em **Security** e, em seguida, clique nas **delegação** separador. Clique na caixa de verificação para ativar a "Delegado de início de sessão e inscrição".

![Página de delegação][api-management-delegation-signin-up]

* Decidir o que o URL do seu ponto final de delegação especial será e introduza-a na **URL de ponto final de delegação** campo. 
* No campo chave de autenticação a delegação, introduza um segredo que será utilizado para calcular uma assinatura fornecida para a verificação para se certificar de que o pedido, de fato, vem de API Management do Azure. Pode clicar a **gerar** botão para que a gestão de API gerar aleatoriamente uma chave para.

Agora precisa para criar o **ponto final de delegação**. Tem de executar várias ações:

1. Receba um pedido no seguinte formato:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {URL da página de origem} & salt = {string} & sig = {string}*
   > 
   > 
   
    Consultar parâmetros para o início de sessão / inscrever-se caso:
   
   * **operação**: identifica o tipo de pedido de delegação é - apenas pode ser **SignIn** neste caso
   * **returnUrl**: o URL da página em que o usuário clicou num link de início de sessão ou Inscreva-se
   * **Salt**: uma cadeia de caracteres de salt especial usada para cálculo de um hash de segurança
   * **SIG**: um valor de hash calculada de segurança a ser utilizado para comparar com a sua própria de hash calculado
2. Certifique-se de que a solicitação é proveniente API Management do Azure (opcional, mas altamente recomendado de segurança)
   
   * Computar um hash HMAC-SHA512 de uma cadeia de caracteres com base na **returnUrl** e **salt** parâmetros de consulta ([código de exemplo fornecido abaixo]):
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Comparar o hash calculado acima para o valor do **sig** parâmetro de consulta. Se os dois hashes corresponderem, avançar para a próxima etapa, caso contrário, negar o pedido.
3. Certifique-se de que estão a receber um pedido de início de sessão no/início de sessão-cópia de segurança: os **operação** parâmetro de consulta será definido como "**SignIn**".
4. Apresentar ao usuário da interface do Usuário para iniciar sessão ou Inscreva-se
5. Se o utilizador é inscrever-se deve criar uma conta correspondente para os mesmos na gestão de API. [Criar um utilizador] com a API de REST de gestão de API. Ao fazê-lo, certifique-se de que defina o ID de utilizador para o mesmo é no seu arquivo de utilizador ou para um ID que pode manter o controle de.
6. Quando o utilizador é autenticado com êxito:
   
   * [solicitar um token de início de sessão único (SSO)] através da API de REST de gestão de API
   * acrescente um parâmetro de consulta de returnUrl para o URL de SSO recebeu da chamada de API acima:
     
     > Por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecionar o utilizador para o URL de produzidos acima

Para além da **SignIn** operação, também pode efetuar gestão de contas seguindo os passos anteriores e usando uma das seguintes operações:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Tem de passar os parâmetros de consulta seguinte para operações de gestão de conta.

* **operação**: identifica o tipo de pedido de delegação é (ChangePassword, ChangeProfile ou CloseAccount)
* **ID de utilizador**: o id de utilizador da conta a gerir
* **Salt**: uma cadeia de caracteres de salt especial usada para cálculo de um hash de segurança
* **SIG**: um valor de hash calculada de segurança a ser utilizado para comparar com a sua própria de hash calculado

## <a name="delegate-product-subscription"> </a>Delegar subscrição do produto
Delegar subscrição do produto funciona da mesma forma para delegar o utilizador iniciar sessão/cópia de segurança. O fluxo de trabalho final seria da seguinte forma:

1. Desenvolvedor seleciona um produto no portal do Programador de gestão de API e clica no botão inscrever-se
2. Navegador é redirecionado para o ponto final de delegação
3. Ponto final de delegação efetua os passos de subscrição de produto necessária – isso cabe a e pode envolve a redirecionar para outra página para pedir informações de faturação, fazer perguntas adicionais, ou simplesmente armazenar as informações e que não requerem qualquer ação do utilizador

Para ativar a funcionalidade, o **delegação** página clique **delegar subscrição do produto**.

Em seguida, certifique-se do que ponto final de delegação executa as seguintes ações:

1. Receba um pedido no seguinte formato:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {product para subscrever} & userId = {user fazer pedido} & salt = {string} & sig = {string}*
   > 
   > 
   
    Parâmetros de consulta para o caso de subscrição do produto:
   
   * **operação**: identifica o tipo de pedido de delegação é. Para a subscrição do produto pedidos as opções válidas são:
     * "Assine": fornecidos de um pedido para subscrever o utilizador para um determinado produto com o ID (ver abaixo)
     * "Unsubscribe": um pedido para anular a subscrição de um utilizador a partir de um produto
     * "Renovar": um pedido para renovar uma assinatura (por exemplo, que pode ser prestes a expirar)
   * **productId**: o ID do produto, o utilizador solicitou para subscrever
   * **ID de utilizador**: o ID do utilizador para o qual a solicitação é feita
   * **Salt**: uma cadeia de caracteres de salt especial usada para cálculo de um hash de segurança
   * **SIG**: um valor de hash calculada de segurança a ser utilizado para comparar com a sua própria de hash calculado
2. Certifique-se de que a solicitação é proveniente API Management do Azure (opcional, mas altamente recomendado de segurança)
   
   * Um HMAC-SHA512 de uma cadeia de caracteres com base de computação a **productId**, **userId**, e **salt** parâmetros de consulta:
     
     > HMAC (**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Comparar o hash calculado acima para o valor do **sig** parâmetro de consulta. Se os dois hashes corresponderem, avançar para a próxima etapa, caso contrário, negar o pedido.
3. Executar qualquer processamento de subscrição do produto com base no tipo de operação pedida na **operação** – por exemplo, faturação, ainda mais perguntas, etc.
4. Sobre a inscrição com êxito ao utilizador para o produto no seu lado, subscrever o utilizador para o produto de gestão de API por [chamar a API REST para a subscrição do produto].

## <a name="delegate-example-code"> </a> Código de exemplo
Estas amostras de código mostram como aproveitar o *chave de validação de delegação*, que está definido no ecrã do portal do publicador, a delegação para criar um HMAC, que, em seguida, é usado para validar a assinatura, provar a validade da com êxito returnUrl. O mesmo código funciona para o productId e o ID de utilizador com uma leve modificação.

**C#código para gerar o hash do returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Código de NodeJS para gerar o hash do returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a delegação, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar um token de início de sessão único (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Criar um utilizador]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[chamar a API REST para a subscrição do produto]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
