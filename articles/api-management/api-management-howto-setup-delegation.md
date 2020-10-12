---
title: Como delegar o registo do utilizador e a subscrição do produto
description: Saiba como delegar o registo do utilizador e a subscrição de produto a terceiros na Azure API Management.
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
ms.date: 07/30/2020
ms.author: apimpm
ms.openlocfilehash: e7f2fb966aa323063220bc798706c8401745ba20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461005"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registo do utilizador e a subscrição do produto

A delegação permite-lhe utilizar o seu website existente para lidar com o sinal de desenvolvimento/inscrição e subscrição de produtos, em oposição à utilização da funcionalidade incorporada no portal do desenvolvedor. Permite que o seu website possua os dados do utilizador e realize a validação destes passos de forma personalizada.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegação de inscrição e inscrição de desenvolvedores

Para delegar o desenvolvedor, iniciar sessão e inscrever-se no seu website existente, terá de criar um ponto final especial da delegação no seu site. Tem de funcionar como ponto de entrada para qualquer pedido iniciado a partir do portal de desenvolvimento da API Management.

O fluxo de trabalho final será o seguinte:

1. Desenvolvedor clica no sinal ou insinusam link no portal de desenvolvimento da API Management
2. O navegador é redirecionado para o ponto final da delegação
3. Ponto final da delegação em reencaminhadores de retorno para ou apresenta UI pedindo ao utilizador para se inscrever ou se inscrever
4. No sucesso, o utilizador é redirecionado de volta para a página do portal de desenvolvedores da API Management que começaram a partir de

Para começar, vamos primeiro configurar a API Management para encaminhar os pedidos através do seu ponto final da delegação. No portal Azure, procure **segurança** no seu recurso de Gestão API e, em seguida, clique no item **Delegação.** Clique na caixa de verificação para ativar 'Delegado assinar em & inscrever-se'.

![Página da delegação][api-management-delegation-signin-up]

* Decida qual será o URL do seu ponto final especial da sua delegação e insira-o no campo **URL do ponto final da delegação.** 
* No âmbito do campo chave de autenticação da Delegação, insira um segredo que será utilizado para calcular uma assinatura que lhe será fornecida para verificação para garantir que o pedido é de facto proveniente da Azure API Management. Pode clicar no botão **gerar** para que a API Management gere aleatoriamente uma chave para si.

Agora precisa de criar o ponto final da **delegação.** Tem de realizar uma série de ações:

1. Receber um pedido no seguinte formulário:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL da página de origem}&salt={string}&sig={string}*
   
    Parâmetros de consulta para o caso de inscrição/inscrição:
   
   * **operação**: identifica que tipo de pedido de delegação é - só pode ser **SignIn** neste caso
   * **returnUrl**: o URL da página onde o utilizador clicou num link de sinal ou inscrição
   * **sal:** uma corda de sal especial usada para calcular um haxixe de segurança
   * **sig**: um haxixe de segurança computado para ser usado para comparação com o seu próprio haxixe computado
2. Verifique se o pedido vem da Azure API Management (opcional, mas altamente recomendado para a segurança)
   
   * Calcular um hash HMAC-SHA512 de uma cadeia com base nos parâmetros de consulta **de retornoUrl** e **sal** [(código de exemplo fornecido abaixo):]
     
     > HMAC(**sal** + '\n' + **returnUrl**)
     > 
     > 
   * Compare o haxixe acima computado com o valor do parâmetro de consulta **sig.** Se as duas hashes corresponderem, passe para o passo seguinte, caso contrário, negue o pedido.
3. Verifique se está a receber um pedido de inscrição/inscrição: o parâmetro de consulta de **operação** será definido como "**SignIn**".
4. Apresentar ao utilizador com UI para iniciar súm na súm nal ou inscrever-se
5. Se o utilizador estiver a inscrever-se, tem de criar uma conta correspondente para eles na Gestão da API. [Criar um utilizador] com a API Management REST API. Ao fazê-lo, certifique-se de que define o ID do utilizador ao mesmo valor que na sua loja de utilizadores ou a um ID que possa acompanhar.
6. Quando o utilizador é autenticado com sucesso:
   
   * [solicitar um sinal único (SSO)] através da API Management REST
   * apego um parâmetro de consulta de retornoUrl ao URL SSO que recebeu da chamada API acima:
     
     > Por exemplo `https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url` 
     > 
     > 
   * redirecionar o utilizador para o URL acima produzido

Além da operação **SignIn,** pode ainda efetuar a gestão da conta seguindo os passos anteriores e utilizando uma das seguintes operações:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **SignOut**

Deve passar os seguintes parâmetros de consulta para operações de gestão de conta.

* **funcionamento**: identifica o tipo de pedido de delegação (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: o ID do utilizador da conta a gerir
* **sal:** uma corda de sal especial usada para calcular um haxixe de segurança
* **sig**: um haxixe de segurança computado para ser usado para comparação com o seu próprio haxixe computado

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegando a subscrição do produto

Delegar a subscrição do produto funciona da mesma forma para delegar o sinal do utilizador dentro/-up. O fluxo de trabalho final seria o seguinte:

1. O desenvolvedor seleciona um produto no portal de desenvolvimento da API Management e clica no botão Subscrever.
2. O navegador é redirecionado para o ponto final da delegação.
3. O ponto final da delegação executa as etapas de subscrição do produto necessárias. Cabe-lhe a si desenhar os degraus. Podem incluir redirecionamento para outra página para solicitar informações de faturação, fazer perguntas adicionais ou simplesmente armazenar as informações e não exigir qualquer ação do utilizador.

Para ativar a funcionalidade, na página **delegada** clique na **subscrição do produto Delegado**.

Em seguida, certifique-se de que o ponto final da delegação faz as seguintes ações:

1. Receber um pedido no seguinte formulário:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation={operation}&produtoD={produto para subscrever a}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parâmetros de consulta para o caso da subscrição do produto:
   
   * **funcionamento**: identifica o tipo de pedido de delegação. Para pedidos de subscrição de produtos, as opções válidas são:
     * "Subscrever": um pedido de subscrição do utilizador a um determinado produto com ID fornecido (ver abaixo)
     * "Cancelar a subscrição": um pedido para cancelar a subscrição de um utilizador de um produto
     * "Renovar": um pedido de renovação de uma subscrição (por exemplo, que pode estar a expirar)
   * **productId**: on *Subscribe* - o ID do produto que o utilizador solicitou para subscrever
   * **subscriçãoId**: em *Cancelar* e *Renovar* - o ID da subscrição do produto
   * **userId**: on *Subscribe* - o ID do utilizador o pedido é feito para
   * **sal:** uma corda de sal especial usada para calcular um haxixe de segurança
   * **sig**: um haxixe de segurança computado para ser usado para comparação com o seu próprio haxixe computado

2. Verifique se o pedido vem da Azure API Management (opcional, mas altamente recomendado para a segurança)
   
   * Computação de um HMAC-SHA512 de uma cadeia baseada no **produtoId,** **userId**e parâmetros de consulta de **sal:**
     
     > HMAC(**sal** + '\n' + **produtoId** + '\n' + **userId**)
     > 
     > 
   * Compare o haxixe acima computado com o valor do parâmetro de consulta **sig.** Se as duas hashes corresponderem, passe para o passo seguinte, caso contrário, negue o pedido.
3. Processar a subscrição do produto com base no tipo de operação solicitada em **funcionamento** - por exemplo, faturação, outras questões, etc.
4. Ao subscrever com sucesso o utilizador ao produto do seu lado, subscreva o utilizador no produto API [Management, ligando para a API REST para subscrições.]

## <a name="example-code"></a><a name="delegate-example-code"> </a> Código exemplo

Estas amostras de código mostram como:

* Pegue a chave de validação da *delegação,* que está definida no ecrã da delegação do portal da editora
* Crie um HMAC, que é depois utilizado para validar a assinatura, comprovando a validade do returnUrl passado.

O mesmo código funciona para o produtoId e userId com ligeira modificação.

**Código C# para gerar haxixe de retornoUrl**

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

**Código NodeJS para gerar haxixe de retornoUrl**

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
> É necessário [reeditar o portal do programador](api-management-howto-developer-portal-customize.md#publish) para que as alterações da delegação entrem em vigor.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a delegação, consulte o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar um sinal único (SSO) token]: /rest/api/apimanagement/2019-12-01/user/generatessourl
[criar um utilizador]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[chamando a API REST para subscrições]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
