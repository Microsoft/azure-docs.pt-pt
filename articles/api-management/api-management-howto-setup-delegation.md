---
title: Como delegar o registo do utilizador e a subscrição do produto
description: Saiba como delegar o registo do utilizador e a subscrição de produtos a terceiros na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: c28872e6cffa973f01b3f5a87c423d9dd93a2aa5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259107"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registo do utilizador e a subscrição do produto

A delegação permite-lhe utilizar o seu website existente para lidar com o acesso ao programador, inscreveu/inscreva-se e subscreva produtos, em oposição à utilização da funcionalidade incorporada no portal do desenvolvedor. Permite ao seu website possuir os dados do utilizador e realizar a validação destes passos de forma personalizada.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegar inscrição e inscrição no programador

Para delegar o programador, inscreva-se e inscreva-se no seu site existente, terá de criar um ponto final de delegação especial no seu site. Tem de funcionar como ponto de entrada para qualquer pedido deste tipo iniciado a partir do portal de desenvolvimento da API Management.

O fluxo de trabalho final será o seguinte:

1. Desenvolvedor clica no sinal de inscrição ou inscreva link no portal de desenvolvimento de gestão da API
2. O navegador é redirecionado para o ponto final da delegação
3. Ponto final da delegação em troca redireciona ou apresenta UI pedindo ao utilizador para se inscrever ou inscrever
4. Sobre o sucesso, o utilizador é redirecionado de volta para a página do portal de desenvolvimento da API Management que eles começaram a partir de

Para começar, vamos configurar a API Management para encaminhar pedidos através do seu ponto final de delegação. No portal Azure, procure **segurança** no seu recurso de Gestão API e, em seguida, clique no item **da Delegação.** Clique na caixa de verificação para ativar 'Desinsertar-se & inscrever-se'.

![Página de delegação][api-management-delegation-signin-up]

* Decida qual será o URL do seu ponto final de delegação especial e insira-o no campo URL do ponto final da **delegação.** 
* No âmbito do campo-chave de autenticação da Delegação, insira um segredo que será utilizado para calcular uma assinatura que lhe é fornecida para verificação para garantir que o pedido é, de facto, proveniente da Azure API Management. Pode clicar no botão **de geração** para que a API Management gere aleatoriamente uma chave para si.

Agora tem de criar o ponto final da **delegação.** Tem de realizar uma série de ações:

1. Receba um pedido no seguinte formulário:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&devoluçãoUrl={URL da página fonte}&sal={string}&sig={string}*
   > 
   > 
   
    Parâmetros de consulta para o caso de inscrição/inscrição:
   
   * **operação**: identifica que tipo de pedido de delegação é - só pode ser **SignIn** neste caso
   * **returnUrl**: o URL da página onde o utilizador clicou num link de inscrição ou de inscrição
   * **sal**: uma cadeia especial de sal utilizada para a computação de um hash de segurança
   * **sig**: um hash de segurança computorizado a ser usado para comparação com o seu próprio hash computado
2. Verifique se o pedido vem da Azure API Management (opcional, mas altamente recomendado para a segurança)
   
   * Calcular um hash HMAC-SHA512 de uma corda com base nos parâmetros **de retornoUrl** e consulta de **sal** [(código de exemplo fornecido abaixo]):
     
     > HMAC(**sal** + '\n' + **returnUrl**)
     > 
     > 
   * Compare o hash acima computado com o valor do parâmetro de consulta **sig.** Se os dois hashes corresponderem, siga para o próximo passo, caso contrário, negue o pedido.
3. Verifique se está a receber um pedido de inscrição/inscrição: o parâmetro de consulta de **funcionamento** será definido para "**SignIn**".
4. Apresentar o utilizador com UI para iniciar sessão ou inscrever-se
5. Se o utilizador estiver a inscrever-se, tem de criar uma conta correspondente para eles na Gestão de API. [Crie um utilizador] com a API Management REST API. Ao fazê-lo, certifique-se de que define o ID do utilizador para o mesmo valor que na sua loja de utilizadores ou para um ID que possa acompanhar.
6. Quando o utilizador for autenticado com sucesso:
   
   * [solicitar um sinal único (SSO) ficha] através da API Management REST API
   * anexar um parâmetro de consulta de retornoUrl ao URL SSO que recebeu da chamada API acima:
     
     > Por exemplohttps://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecionar o utilizador para o URL acima produzido

Além da operação **SignIn,** também pode realizar a gestão da conta seguindo os passos anteriores e utilizando uma das seguintes operações:

* **Alterar Palavra-passe**
* **Perfil de mudança**
* **CloseAccount**

Deve passar os seguintes parâmetros de consulta para operações de gestão de conta.

* **operação**: identifica o tipo de pedido de delegação (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: o ID do utilizador da conta para gerir
* **sal**: uma cadeia especial de sal utilizada para a computação de um hash de segurança
* **sig**: um hash de segurança computorizado a ser usado para comparação com o seu próprio hash computado

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegar a subscrição do produto
Delegar a subscrição do produto funciona da mesma forma para delegar o utilizador a inscrever/a subir. O fluxo de trabalho final seria o seguinte:

1. O desenvolvedor seleciona um produto no portal de desenvolvimento de gestão da API e clica no botão Subscrever.
2. O navegador é redirecionado para o ponto final da delegação.
3. O ponto final da delegação executa as etapas de subscrição do produto necessárias. Cabe-lhe a si desenhar os passos. Podem incluir redirecionar para outra página para solicitar informações de faturação, fazer perguntas adicionais, ou simplesmente armazenar a informação e não exigir qualquer ação do utilizador.

Para ativar a funcionalidade, na página **da Delegação** clique na **subscrição**do produto Delegado .

Em seguida, assegurar que o ponto final da delegação faça as seguintes ações:

1. Receba um pedido no seguinte formulário:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parâmetros de consulta para o caso da subscrição do produto:
   
   * **operação**: identifica o tipo de pedido de delegação. Para pedidos de subscrição de produtos, as opções válidas são:
     * "Subscrever": um pedido de subscrição do utilizador a um determinado produto com identificação fornecida (ver abaixo)
     * "Cancelar a subscrição": um pedido para cancelar a subscrição de um utilizador de um produto
     * "Renovar": um pedido de renovação de uma subscrição (por exemplo, que pode estar a expirar)
   * **produtoId**: o ID do produto que o utilizador solicitou para subscrever
   * **subscriçãoId**: on *Unsubscribe* and *Renew* - o ID da subscrição do produto
   * **userId**: o ID do utilizador para o que o pedido é feito
   * **sal**: uma cadeia especial de sal utilizada para a computação de um hash de segurança
   * **sig**: um hash de segurança computorizado a ser usado para comparação com o seu próprio hash computado

2. Verifique se o pedido vem da Azure API Management (opcional, mas altamente recomendado para a segurança)
   
   * Calcular um HMAC-SHA512 de uma cadeia baseada no **produtoId,** **userId,** e parâmetros de consulta de **sal:**
     
     > HMAC(**sal** + '\n' + **produtoId** + '\n' + **userId**)
     > 
     > 
   * Compare o hash acima computado com o valor do parâmetro de consulta **sig.** Se os dois hashes corresponderem, siga para o próximo passo, caso contrário, negue o pedido.
3. Processe a subscrição do produto com base no tipo de operação solicitada em **funcionamento** - por exemplo, faturação, outras questões, etc.
4. Ao subscrever com sucesso o utilizador ao produto do seu lado, subscreva o utilizador no produto API [Management, chamando a API REST para subscrições.]

## <a name="example-code"></a><a name="delegate-example-code"> </a> Código de Exemplo

Estas amostras de código mostram como:

* Pegue a chave de validação da *delegação*, que está definida no ecrã da delegação do portal da editora
* Crie um HMAC, que é então usado para validar a assinatura, comprovando a validade do retorno passadoUrl.

O mesmo código funciona para o id e userId do produto com ligeira modificação.

**C# código para gerar hash de retornoUrl**

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

**Código NodeJS para gerar hash de retornoUrl**

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

> [!IMPORTANT]
> É necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) de desenvolvimento para que as alterações da delegação entrem em vigor.

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre a delegação, consulte o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar um símbolo de inscrição única (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/User/GenerateSsoUrl
[criar um utilizador]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user/createorupdate
[chamando a API REST para subscrições]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[código exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
