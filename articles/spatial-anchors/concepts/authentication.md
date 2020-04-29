---
title: Autenticação e autorização
description: Saiba mais sobre as várias formas como uma aplicação ou serviço pode autenticar as Âncoras Espaciais Azure, e os níveis de controlo que tem para aceder ao Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656992"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização às Âncoras Espaciais Azure

Nesta secção, cobriremos as várias formas de autenticar as Âncoras Espaciais Azure a partir da sua app ou serviço web, e as formas pelas quais poderá utilizar o Controlo de Acesso Baseado em Funções no Diretório Azure (Azure AD) para controlar o acesso às suas contas De âncoras espaciais.

## <a name="overview"></a>Descrição geral

![Uma visão geral da autenticação às Âncoras Espaciais Azure](./media/spatial-anchors-authentication-overview.png)

Para aceder a uma determinada conta Azure Spatial Anchors, os clientes precisam primeiro de obter um sinal de acesso do Azure Mixed Reality Security Token Service (STS). Os tokens obtidos do STS vivem durante 24 horas e contêm informações para os serviços de Âncoraespacial Espacial tomarem decisões de autorização na conta, e garantir que apenas os diretores autorizados podem aceder a essa conta.

As fichas de acesso podem ser obtidas em troca de chaves de conta ou de fichas emitidas pela Azure AD.

As chaves de conta permitem-lhe começar rapidamente a utilizar o serviço De âncoras espaciais Azure; no entanto, antes de implementar a sua aplicação para produção, é aconselhável atualizar a sua aplicação para utilizar a autenticação baseada em AD Azure.

Os tokens de autenticação Azure AD podem ser obtidos de duas formas:

- Se estiver a construir uma aplicação empresarial, e a sua empresa estiver a utilizar o Azure AD como seu sistema de identidade, poderá utilizar a autenticação Azure AD baseada no utilizador na sua aplicação e conceder acesso às suas contas de âncora espacial utilizando os grupos de segurança AD Azure existentes, ou diretamente para os utilizadores da sua organização.
- Caso contrário, recomenda-se que obtenha fichas AD Azure de um serviço web que suporte a sua aplicação. A utilização de um serviço web de suporte é o método recomendado de autenticação para aplicações de produção, uma vez que evita incorporar as credenciais de acesso às Âncoras Espaciais Azure na sua aplicação de cliente.

## <a name="account-keys"></a>Chaves de conta

Usar chaves de conta para acesso à sua conta Azure Spatial Anchors é a forma mais simples de começar. Encontrará as chaves da sua conta no portal Azure. Navegue na sua conta e selecione o separador "Chaves".

![Uma visão geral da autenticação às Âncoras Espaciais Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


São disponibilizadas duas chaves, ambas simultaneamente válidas para acesso à conta Deâncoras Espaciais. Recomenda-se que atualize regularmente a chave que utiliza para aceder à conta; Ter duas chaves válidas separadas permitem tais atualizações sem tempo de inatividade; só tem de atualizar alternativamente a chave principal e a chave secundária.

O SDK tem suporte incorporado para autenticação com chaves de conta; basta definir a propriedade AccountKey no seu objeto cloudSession.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Uma vez feito, o SDK tratará da troca da chave da conta por um token de acesso e o cache necessário de fichas para a sua aplicação.

> [!WARNING]
> Recomenda-se a utilização das chaves de conta para embarque rápido, mas apenas durante o desenvolvimento/prototipagem. Recomenda-se vivamente não enviar a sua aplicação para produção utilizando uma chave de conta incorporada na seleção e utilizar as abordagens de autenticação Azure AD baseadas no utilizador ou baseadas em serviços listadas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação do utilizador da AD Azure

Para aplicações dirigidas aos utilizadores do Azure Ative Directory, a abordagem recomendada é utilizar um token Azure AD para o utilizador, que pode obter através da [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Deve seguir os passos listados no [registo de uma aplicação quickstart](../../active-directory/develop/quickstart-register-app.md), que incluem:

1. Configuração no portal Azure
    1.  Registe a sua candidatura em Azure AD como **aplicação nativa.** Como parte do registo, terá de determinar se a sua candidatura deve ou não ser multi-arrendatária e fornecer os URLs redirecionados permitidos para a sua aplicação.
        1.  Mude para o separador de **permissões API**
        2.  Selecione **Adicionar uma permissão**
            1.  Selecione **Fornecedor de Recursos** de Realidade Mista sob **APIs a minha organização usa** o separador
            2.  Selecione **permissões delegadas**
            3.  Verifique a caixa para misturar **realidade.signin** sob **realidade mista**
            4.  Selecione **Adicionar permissões**
        3.  Selecione **o consentimento do administrador do Grant**
    2.  Conceda o acesso à sua aplicação ou utilizadores ao seu recurso:
        1.  Navegue para o seu recurso Spatial Anchors no portal Azure
        2.  Mude para o separador controlo de **acesso (IAM)**
        3.  Atribuição **de papel de Hit Add**
            1.  [Selecionar uma função](#role-based-access-control)
            2.  No campo **Select,** introduza o nome do(s) utilizador(s), grupo ou aplicação a que pretende atribuir acesso.
            3.  Prima **Guardar**.
2. No seu código:
    1.  Certifique-se de utilizar o ID da **aplicação** e **redirecionar Uri** da sua própria aplicação Azure AD como o ID do **cliente** e os parâmetros **RedirectUri** na ADAL
    2.  Detete a informação do inquilino:
        1.  Se a sua candidatura apoiar **apenas a minha organização,** substitua este valor pelo seu nome de **Id de Inquilino** ou **Inquilino** (por exemplo, contoso.microsoft.com)
        2.  Se a sua aplicação apoiar **Contas em qualquer diretório organizacional,** substitua este valor por **Organizações**
        3.  Se a sua aplicação apoiar **todos os utilizadores da conta microsoft,** substitua este valor por **Common**
    3.  No seu pedido simbólico, desloque o **recurso** para "https://sts.mixedreality.azure.com". Este "recurso" indicará à Azure AD que a sua aplicação está a solicitar um sinal para o serviço Deâncoras Espaciais Azure.

Com isso, a sua aplicação deverá poder obter a partir da MSAL um token Azure AD; pode definir o token Azure AD como a **autenticaçãoToken** no seu objeto config de sessão em nuvem.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

## <a name="azure-ad-service-authentication"></a>Autenticação do serviço Azure AD

A opção recomendada para implementar aplicações que alavancam as Âncoras Espaciais Azure para a produção é alavancar um serviço de backend que irá intermediar pedidos de autenticação. O regime geral deve ser descrito neste diagrama:

![Uma visão geral da autenticação às Âncoras Espaciais Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, assume-se que a sua aplicação utiliza o seu próprio mecanismo (por exemplo: conta Microsoft, PlayFab, Facebook, Google ID, nome de utilizador/palavra-passe personalizado, etc.) para autenticar o seu serviço de backend. Assim que os seus utilizadores forem autenticados no seu serviço backend, esse serviço pode recuperar um token Azure AD, trocá-lo por um sinal de acesso para Âncoras Espaciais Azure e devolvê-lo à sua aplicação de cliente.

O token de acesso Azure AD é recuperado utilizando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Deve seguir os passos listados no [registo de uma aplicação quickstart](../../active-directory/develop/quickstart-register-app.md), que incluem:

1.  Configuração no portal Azure:
    1.  Registe a sua candidatura em Azure AD:
        1.  No portal Azure, navegue para **o Azure Ative Directory**e selecione registos de **aplicações**
        2.  Selecione **novo registo de candidatura**
        3.  Introduza o nome da sua aplicação, selecione web **app / API** como o tipo de aplicação, e introduza o URL auth para o seu serviço. Em seguida, bater **Create**.
        4.  Nessa aplicação, toque em **Definições**, em seguida, selecione o separador **Teclas.** Introduza o nome da sua chave, selecione uma duração e bata **em Guardar**. Certifique-se de que guarda o valor-chave que é apresentado nesse momento, pois terá de o incluir no código do seu serviço web.
    2.  Conceda o acesso à sua aplicação e/ou utilizadores ao seu recurso:
        1.  Navegue para o seu recurso Spatial Anchors no portal Azure
        2.  Mude para o separador controlo de **acesso (IAM)**
        3.  Atribuição **de papel de Hit Add**
        1.  [Selecionar uma função](#role-based-access-control)
        2.  No campo **selecionado,** insira o nome das aplicações que criou e a que pretende atribuir acesso. Se pretender que os utilizadores da sua aplicação tenham diferentes funções em relação à conta Spatial Anchors, deverá registar várias aplicações em AD Azure e atribuir a cada uma uma função separada. Em seguida, implemente a sua lógica de autorização para usar o papel certo para os seus utilizadores.
    3.  Prima **Guardar**.
2.  No seu código (nota: pode utilizar a amostra de serviço incluída no GitHub):
    1.  Certifique-se de utilizar o ID da aplicação, o segredo da aplicação e redirecionar Uri da sua própria aplicação Azure AD como id do cliente, secret, e redirectUri parâmetros em ADAL
    2.  Defina o ID do inquilino para o seu próprio Id de inquilino AAAzure ADD no parâmetro de autoridade em ADAL
    3.  No seu pedido simbólico, desloque o **recurso** para "https://sts.mixedreality.azure.com

Com isso, o seu serviço de backend pode recuperar um símbolo da AD Azure. Pode então trocá-lo por um sinal de MR que voltará ao cliente. A utilização de um token Azure AD para recuperar um token MR é feita através de uma chamada REST. Aqui está uma chamada de amostra:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Quando o cabeçalho de autorização for formatado da seguinte forma:`Bearer <accoundId>:<accountKey>`

E a resposta contém o símbolo do MR em texto simples.

O sinal do Sr. é devolvido ao cliente. A sua aplicação de cliente pode então defini-la como o seu símbolo de acesso na config da sessão de nuvem.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Para ajudar a controlar o nível de acesso concedido a aplicações, serviços ou utilizadores de AD Azure do seu serviço, foram criadas as seguintes funções para que possa atribuir as medidas necessárias contra as suas contas Deâncoras Espaciais Azure:

- **Conta Âncoraespacial Espacial Proprietário**: aplicações ou utilizadores que tenham esta função são capazes de criar âncoras espaciais, consultas para elas e eliminá-las. Quando autentica na sua conta utilizando chaves de conta, a função proprietário da conta Spatial **Anchors** é atribuída ao comitente autenticado.
- **Contribuidor de Conta De Âncoras Espaciais**: aplicações ou utilizadores que tenham esta função são capazes de criar âncoras espaciais, consultas para elas, mas não podem apagá-las.
- **Leitor de conta de âncoras espaciais**: aplicações ou utilizadores que tenham esta função só podem consultar âncoras espaciais, mas não podem criar novas, eliminar as existentes ou atualizar metadados em âncoras espaciais. Isto é normalmente usado para aplicações onde alguns utilizadores curam o ambiente, enquanto outros só podem recordar âncoras anteriormente colocadas naquele ambiente.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação com âncoras espaciais Azure.

> [!div class="nextstepaction"]
> [Unidade (Hololens)](../quickstarts/get-started-unity-hololens.md)

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
