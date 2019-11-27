---
title: Como delegar o registro de usuário e a assinatura do produto
description: Saiba como delegar o registro de usuário e a assinatura do produto a terceiros no gerenciamento de API do Azure.
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
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454351"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registro de usuário e a assinatura do produto

A delegação permite que você use seu site existente para lidar com a inscrição/inscrição e assinatura de desenvolvedores em produtos, em vez de usar a funcionalidade interna no portal do desenvolvedor. Ele permite que seu site seja proprietário dos dados do usuário e execute a validação dessas etapas de maneira personalizada.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegando a entrada e inscrição do desenvolvedor

Para delegar o desenvolvedor, entrar e se inscrever no seu site existente, você precisará criar um ponto de extremidade de delegação especial em seu site. Ele precisa atuar como o ponto de entrada para qualquer solicitação desse tipo iniciada no portal do desenvolvedor do gerenciamento de API.

O fluxo de trabalho final será o seguinte:

1. O desenvolvedor clica no link entrar ou inscrever-se no portal do desenvolvedor do gerenciamento de API
2. O navegador é redirecionado para o ponto de extremidade de delegação
3. O ponto de extremidade de delegação em retorno redireciona para ou apresenta a IU solicitando que o usuário entre ou se inscreva
4. Em caso de sucesso, o usuário é Redirecionado de volta para a página do portal do desenvolvedor do gerenciamento de API do qual ele começou

Para começar, vamos primeiro configurar o gerenciamento de API para rotear solicitações por meio de seu ponto de extremidade de delegação. No portal do Azure, procure **segurança** em seu recurso de gerenciamento de API e clique no item de **delegação** . Clique na caixa de seleção para habilitar ' delegar entrada & inscrever '.

![Página de delegação][api-management-delegation-signin-up]

* Decida qual será a URL do seu ponto de extremidade de delegação especial e insira-a no campo **URL do ponto de extremidade de delegação** . 
* No campo chave de autenticação de delegação, insira um segredo que será usado para computar uma assinatura fornecida para verificação para garantir que a solicitação seja realmente proveniente do gerenciamento de API do Azure. Você pode clicar no botão **gerar** para que o gerenciamento de API gere aleatoriamente uma chave para você.

Agora você precisa criar o **ponto de extremidade de delegação**. Ele precisa executar várias ações:

1. Receba uma solicitação no seguinte formato:
   
   > *http:\//www.yourwebsite.com/apimdelegation? Operation = SignIn & returnUrl = {URL da página de origem} & Salt = {String} & SIG = {String}*
   > 
   > 
   
    Parâmetros de consulta para o caso de entrada/inscrição:
   
   * **operação**: identifica que tipo de solicitação de delegação é-ela só pode ser **entrada** neste caso
   * **RETURNURL**: a URL da página em que o usuário clicou em um link de entrada ou inscrição
   * **Salt**: uma cadeia de caracteres de Salt especial usada para calcular um hash de segurança
   * **SIG**: um hash de segurança computado a ser usado para comparação com seu próprio hash computado
2. Verificar se a solicitação está vindo do gerenciamento de API do Azure (opcional, mas altamente recomendado para segurança)
   
   * Compute um hash HMAC-SHA512 de uma cadeia de caracteres com base nos parâmetros de consulta **RETURNURL** e **Salt** ([código de exemplo fornecido abaixo]):
     
     > HMAC (**Salt** + ' \n ' + **RETURNURL**)
     > 
     > 
   * Compare o hash calculado acima com o valor do parâmetro de consulta **SIG** . Se os dois hashes corresponderem, vá para a próxima etapa, caso contrário, negue a solicitação.
3. Verifique se você está recebendo uma solicitação para entrar/inscrever-se: o parâmetro de consulta de **operação** será definido como "**SignIn**".
4. Apresente o usuário com a interface de usuário para entrar ou se inscrever
5. Se o usuário estiver se inscrevendo, você precisará criar uma conta correspondente para eles no gerenciamento de API. [Crie um usuário] com a API REST de gerenciamento de API. Ao fazer isso, certifique-se de definir a ID de usuário com o mesmo valor que no armazenamento do usuário ou com uma ID que você pode controlar.
6. Quando o usuário for autenticado com êxito:
   
   * [solicitar um token de logon único (SSO)] por meio da API REST de gerenciamento de API
   * Acrescente um parâmetro de consulta returnUrl à URL de SSO que você recebeu da chamada à API acima:
     
     > por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecionar o usuário para a URL de produção acima

Além da operação de **entrada** , você também pode executar o gerenciamento de conta seguindo as etapas anteriores e usando uma das seguintes operações:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Você deve passar os seguintes parâmetros de consulta para operações de gerenciamento de conta.

* **operação**: identifica o tipo de solicitação de delegação que é (ChangePassword, ChangeProfile ou CloseAccount)
* **userid**: a ID de usuário da conta a ser gerenciada
* **Salt**: uma cadeia de caracteres de Salt especial usada para calcular um hash de segurança
* **SIG**: um hash de segurança computado a ser usado para comparação com seu próprio hash computado

## <a name="delegate-product-subscription"> </a>Delegando assinatura do produto
A delegação de assinatura do produto funciona de forma semelhante à delegação de entrada/ativação do usuário. O fluxo de trabalho final seria o seguinte:

1. O desenvolvedor seleciona um produto no portal do desenvolvedor de gerenciamento de API e clica no botão assinar.
2. O navegador é redirecionado para o ponto de extremidade de delegação.
3. O ponto de extremidade de delegação executa as etapas de assinatura do produto necessárias. Cabe a você criar as etapas. Eles podem incluir o redirecionamento para outra página para solicitar informações de cobrança, fazer perguntas adicionais ou simplesmente armazenar as informações e não exigir nenhuma ação do usuário.

Para habilitar a funcionalidade, na página **delegação** , clique em **delegar assinatura do produto**.

Em seguida, verifique se o ponto de extremidade de delegação faz as seguintes ações:

1. Receba uma solicitação no seguinte formato:
   
   > *http:\//www.yourwebsite.com/apimdelegation? Operation = {Operation} & productId = {produto para assinar} & userId = {usuário que faz a solicitação} & Salt = {String} & SIG = {String}*
   >
   
    Parâmetros de consulta para o caso de assinatura do produto:
   
   * **operação**: identifica o tipo de solicitação de delegação. Para solicitações de assinatura de produto, as opções válidas são:
     * "Assinar": uma solicitação para assinar o usuário para um determinado produto com a ID fornecida (veja abaixo)
     * "Cancelar assinatura": uma solicitação para cancelar a assinatura de um usuário de um produto
     * "Renovar": uma solicitação para renovar uma assinatura (por exemplo, que pode estar expirando)
   * **ProductID**: a ID do produto que o usuário solicitou para assinar
   * **SubscriptionId**: em *cancelar assinatura* e *renovar* -a ID da assinatura do produto
   * **userid**: a ID do usuário para o qual a solicitação é feita
   * **Salt**: uma cadeia de caracteres de Salt especial usada para calcular um hash de segurança
   * **SIG**: um hash de segurança computado a ser usado para comparação com seu próprio hash computado

2. Verificar se a solicitação está vindo do gerenciamento de API do Azure (opcional, mas altamente recomendado para segurança)
   
   * Compute um HMAC-SHA512 de uma cadeia de caracteres com base nos parâmetros de consulta **ProductID**, **userid**e **Salt** :
     
     > HMAC (**Salt** + ' \n ' + **ProductID** + ' \n ' + **userid**)
     > 
     > 
   * Compare o hash calculado acima com o valor do parâmetro de consulta **SIG** . Se os dois hashes corresponderem, vá para a próxima etapa, caso contrário, negue a solicitação.
3. Processar a assinatura do produto com base no tipo de operação solicitada na **operação** – por exemplo, cobrança, perguntas adicionais etc.
4. Ao assinar com êxito o usuário para o produto no seu lado, assine o usuário para o produto de gerenciamento de API [chamando a API REST para assinaturas].

## <a name="delegate-example-code"></a> Código de exemplo

Estes exemplos de código mostram como:

* Obter a *chave de validação de delegação*, que é definida na tela de delegação do portal do editor
* Crie um HMAC, que é usado para validar a assinatura, comprovando a validade da returnUrl passada.

O mesmo código funciona para productId e userId com ligeira modificação.

**C#código para gerar hash de returnUrl**

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

**Código NodeJS para gerar hash de returnUrl**

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
> Você precisa [republicar o portal do desenvolvedor](api-management-howto-developer-portal-customize.md#publish) para que as alterações de delegação entrem em vigor.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre delegação, consulte o vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar um token de logon único (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Crie um usuário]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[chamando a API REST para assinaturas]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
