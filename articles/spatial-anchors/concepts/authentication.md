---
title: Autenticação e autorização
description: Saiba mais sobre as várias formas de uma aplicação ou serviço poder autenticar para a Azure Spatial Anchors, e os níveis de controlo que tem de aceder às Âncoras Espaciais Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857729"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para âncoras espaciais Azure

Nesta secção, cobriremos as várias formas de autenticar a Azure Spatial Anchors a partir da sua aplicação ou serviço web, e as formas pelas quais pode utilizar Role-Based Controlo de Acesso em Diretório Azure (Azure AD) para controlar o acesso às suas contas de Âncoras Espaciais.

## <a name="overview"></a>Descrição geral

![Diagrama que mostra uma visão geral da autenticação para âncoras espaciais Azure.](./media/spatial-anchors-authentication-overview.png)

Para aceder a uma determinada conta Azure Spatial Anchors, os clientes precisam primeiro de obter um token de acesso do Azure Mixed Reality Security Token Service (STS). Os tokens obtidos a partir de STS vivem durante 24 horas, e contêm informações para os serviços de Âncoras Espaciais para tomar decisões de autorização na conta, e garantir que apenas os principais autorizados podem aceder a essa conta.

As fichas de acesso podem ser obtidas em troca de chaves de conta ou de fichas emitidas pela Azure.

As teclas de conta permitem-lhe começar rapidamente a utilizar o serviço Azure Spatial Anchors; no entanto, antes de implementar a sua aplicação para a produção, é aconselhável atualizar a sua aplicação para utilizar a autenticação baseada em Azure.

Os tokens de autenticação AD AZure podem ser obtidos de duas formas:

- Se estiver a construir uma aplicação empresarial e a sua empresa estiver a utilizar o Azure AD como seu sistema de identidade, poderá utilizar a autenticação Azure AD baseada no utilizador na sua aplicação e conceder acesso às suas contas de âncoras espaciais utilizando os seus grupos de segurança Azure AD existentes, ou diretamente aos utilizadores da sua organização.
- Caso contrário, recomenda-se que obtenha fichas AD Azure a partir de um serviço web que suporta a sua aplicação. A utilização de um serviço web de suporte é o método recomendado de autenticação para aplicações de produção, uma vez que evita incorporar as credenciais para aceder a Âncoras Espaciais Azure na sua aplicação ao cliente.

## <a name="account-keys"></a>Chaves de conta

Usar chaves de conta para aceder à sua conta Azure Spatial Anchors é a forma mais simples de começar. Encontrará as chaves da sua conta no portal Azure. Navegue na sua conta e selecione o separador "Chaves".

![Screenshot que mostra a página "Keys" com o botão "Copiar" para a "chave primária" realçada.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

São disponibilizadas duas chaves, ambas simultaneamente válidas para acesso à conta Das Âncoras Espaciais. Recomenda-se que atualize regularmente a chave que utiliza para aceder à conta; ter duas teclas válidas separadas permite tais atualizações sem tempo de inatividade; basta atualizar alternativamente a chave primária e a chave secundária.

O SDK tem suporte incorporado para autenticação com chaves de conta; basta definir a propriedade AccountKey no seu objeto cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Uma vez feito, o SDK tratará da troca da chave de conta para um token de acesso, e o caching necessário de fichas para a sua app.

> [!WARNING]
> Recomenda-se a utilização de chaves de conta para embarque rápido, mas apenas durante o desenvolvimento/prototipagem. Recomenda-se vivamente que não envie a sua aplicação para a produção utilizando uma chave de conta incorporada na própria, e que, em vez disso, utilize as abordagens de autenticação AD AZure baseadas no utilizador ou em serviço listadas em seguida.

## <a name="azure-ad-user-authentication"></a>Autenticação do utilizador Azure AD

Para aplicações direcionadas aos utilizadores do Azure Ative Directory, a abordagem recomendada é utilizar um token AD Azure para o utilizador, que pode obter através da [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Deve seguir os passos listados no [registo de um quickstart de aplicação,](../../active-directory/develop/quickstart-register-app.md)que inclui:

1. Configuração no portal Azure
    1.    Registe a sua candidatura na Azure AD como **candidatura nativa.** Como parte do registo, você precisará determinar se a sua aplicação deve ser multi-inquilino ou não, e fornecer os URLs de redirecionamento permitidos para a sua aplicação.
        1.  Mude para o separador **permissões API**
        2.  **Selecione Adicionar uma permissão**
            1.  Selecione **Fornecedor de Recursos de Realidade Mista** em **APIs a minha organização usa** separador
            2.  Selecione **permissões delegadas**
            3.  Verifique se a caixa é **de realidade mista.signin** em **misturão**
            4.  Selecione **Permissões adicionar**
        3.  Selecione **o consentimento de administração grant**
    2.    Conceder à sua aplicação ou aos seus utilizadores acesso ao seu recurso:
        1.    Navegue para o seu recurso De Âncoras Espaciais no portal Azure
        2.    Mudar para o **separador Controlo de Acesso (IAM)**
        3.    Hit **Add atribuição de função**
            1.    [Selecionar uma função](#role-based-access-control)
            2.    No campo **Select,** insira o nome do(s)(s)(s) do(s) do utilizador, grupo e/ou aplicações a que pretende atribuir acesso.
            3.    Prima **Guardar**.
2. No seu código:
    1.    Certifique-se de usar o ID da **aplicação** e **redirecionar Uri** da sua própria aplicação AD Azure como os parâmetros **de ID** e **RedirectUri** do cliente em MSAL
    2.    Desa esta medida de informação do arrendatário:
        1.    Se a sua candidatura apoiar **apenas a Minha organização,** substitua este valor pelo seu **ID** do Inquilino ou **nome de Inquilino** (por exemplo, contoso.microsoft.com)
        2.    Se a sua candidatura apoiar **contas em qualquer diretório organizacional,** substitua este valor por **Organizações**
        3.    Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** substitua este valor por **Common**
    3.    No seu pedido simbólico, desemosse o **âmbito** para https://sts.mixedreality.azure.com//.default " " Este âmbito indicará à Azure AD que a sua aplicação está a solicitar um sinal para o Serviço de Token de Segurança de Realidade Mista (STS).

Com isso, a sua aplicação deverá ser capaz de obter da MSAL um token AD Azure; pode definir o token AD AD do Azure como o **conjunto de autenticação** no seu objeto config de sessão de nuvem.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticação do serviço AZURE AD

A opção recomendada para implementar aplicações que alavancam as Âncoras Espaciais Azure para a produção é alavancar um serviço de backend que irá intermediar pedidos de autenticação. O regime geral deve ser descrito neste diagrama:

![Uma visão geral da autenticação para âncoras espaciais Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, presume-se que a sua aplicação utiliza o seu próprio mecanismo (por exemplo: conta Microsoft, PlayFab, Facebook, Google ID, username/password personalizado, etc.) para autenticar o seu serviço de backend. Assim que os seus utilizadores forem autenticados no seu serviço de backend, esse serviço pode recuperar um token AD Azure, trocá-lo por um token de acesso para Azure Spatial Anchors e devolvê-lo à sua aplicação cliente.

O token de acesso Azure AD é recuperado através da [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Deve seguir os passos listados no [registo de um quickstart de aplicação,](../../active-directory/develop/quickstart-register-app.md)que inclui:

1.    Configuração no portal Azure:
    1.    Registe a sua candidatura no Azure AD:
        1.    No portal Azure, navegue para **o Azure Ative Directory**e selecione **registos de aplicações**
        2.    Selecione **novo registo de candidaturas**
        3.    Introduza o nome da sua aplicação, selecione **Web app /API** como o tipo de aplicação e insira o URL auth para o seu serviço. Em seguida, bata **em Criar**.
        4.    Nessa aplicação, acerte **As Definições**e, em seguida, selecione o **separador Certificados e Segredos.** Crie um novo segredo de cliente, selecione uma duração e atinja **Add.** Certifique-se de guardar o valor secreto, pois terá de o incluir no código do seu serviço web.
    2.    Conceder à sua aplicação e/ou ao acesso dos utilizadores ao seu recurso:
        1.    Navegue para o seu recurso De Âncoras Espaciais no portal Azure
        2.    Mudar para o **separador Controlo de Acesso (IAM)**
        3.    Hit **Add atribuição de função**
        1.    [Selecionar uma função](#role-based-access-control)
        2.    No campo **selecionado,** insira o nome das(s) aplicações que criou e a que pretende atribuir acesso. Se quiser que os utilizadores da sua aplicação tenham papéis diferentes contra a conta Spatial Anchors, deverá registar várias aplicações em AD Azure e atribuir a cada um um papel diferente. Em seguida, implemente a sua lógica de autorização para utilizar o papel certo para os seus utilizadores.
        3.    Nota - Na seleção **de atribuição de funções Add** pretende que o acesso do **Atribua** seja definido para "Utilizador, grupo ou principal de serviço Azure".
    3.    Prima **Guardar**.
2.    No seu código (nota: pode utilizar a amostra de serviço incluída no GitHub):
    1.    Certifique-se de usar o ID da aplicação, o segredo da aplicação e redirecione Uri da sua própria aplicação AZure AD como os parâmetros de ID, segredo e RedirectUri do cliente em MSAL
    2.    Coloque a identificação do inquilino para o seu próprio ID do inquilino Azure ADD no parâmetro de autoridade na MSAL.
    3.    No seu pedido simbólico, desacordo o **âmbito** para https://sts.mixedreality.azure.com//.default "

Com isso, o seu serviço de backend pode recuperar um sinal AD Azure. Pode então trocá-lo por um sinal de MR que voltará ao cliente. A utilização de um token AD Azure para recuperar um token MR é feita através de uma chamada REST. Aqui está uma chamada de amostra:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Quando o cabeçalho de autorização for formatado da seguinte forma: `Bearer <Azure_AD_token>`

E a resposta contém o sinal mr em texto simples.

O sinal mr token é então devolvido ao cliente. A aplicação do seu cliente pode então defini-la como símbolo de acesso na sessão de cloud config.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ajudar a controlar o nível de acesso concedido a aplicações, serviços ou utilizadores AD Azure do seu serviço, foram criadas as seguintes funções para que possa atribuir conforme necessário contra as suas contas Azure Spatial Anchors:

- **Proprietário de conta de âncoras espaciais**: aplicações ou utilizadores com esta função são capazes de criar âncoras espaciais, consultar para elas e eliminá-las. Quando autenticar a sua conta utilizando as chaves da conta, a função **De Titular da Conta De Âncoras Espaciais** é atribuída ao principal autenticado.
- **Contribuinte de Conta De Âncoras Espaciais**: aplicações ou utilizadores com esta função são capazes de criar âncoras espaciais, consulta para elas, mas não podem eliminá-las.
- **Leitor de contas de âncoras espaciais**: aplicações ou utilizadores com esta função só podem consultar âncoras espaciais, mas não podem criar novas, eliminar as existentes ou atualizar metadados em âncoras espaciais. Isto é normalmente usado para aplicações onde alguns utilizadores curam o ambiente, enquanto outros só podem recordar âncoras anteriormente colocadas nesse ambiente.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação com âncoras espaciais Azure.

> [!div class="nextstepaction"]
> [Unidade (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unidade (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unidade (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
