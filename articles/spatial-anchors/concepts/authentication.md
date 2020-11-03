---
title: Autenticação e autorização
description: Conheça as várias formas de uma aplicação ou serviço poder autenticar para a Azure Spatial Anchors, e os níveis de controlo que tem de aceder às Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: a3d88c8d5d42e3dec2142df1ede7a9ee50898e92
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242352"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para âncoras espaciais Azure

Neste artigo, você vai aprender as várias maneiras que você pode autenticar para Azure Spatial Anchors a partir da sua app ou serviço web. Você também vai aprender sobre as formas de usar o controlo de acesso baseado em funções Azure (Azure RBAC) em Azure Ative Directory (Azure AD) para controlar o acesso às suas contas De Âncoras Espaciais.

## <a name="overview"></a>Descrição geral

![Diagrama que mostra uma visão geral da autenticação para âncoras espaciais Azure.](./media/spatial-anchors-authentication-overview.png)

Para aceder a uma determinada conta Azure Spatial Anchors, os clientes precisam primeiro de obter um token de acesso do Azure Mixed Reality Security Token Service (STS). As fichas obtidas da STS têm uma vida útil de 24 horas. Contêm informações que os serviços da Spatial Anchors utilizam para tomar decisões de autorização na conta e garantem que apenas os principais autorizados podem aceder à conta.

As fichas de acesso podem ser obtidas em troca de chaves de conta ou fichas emitidas pela Azure AD.

As teclas de conta permitem-lhe começar rapidamente com a utilização do serviço Azure Spatial Anchors. Mas antes de implementar a sua aplicação para a produção, recomendamos que atualize a sua aplicação para utilizar a autenticação AZure AD.

Pode obter fichas de autenticação AD Azure de duas formas:

- Se estiver a construir uma aplicação empresarial e a sua empresa estiver a utilizar o Azure AD como seu sistema de identidade, pode utilizar a autenticação Azure AD baseada no utilizador na sua aplicação. Em seguida, concede acesso às suas contas De Âncoras Espaciais utilizando os seus grupos de segurança Azure AD existentes. Também pode conceder acesso diretamente aos utilizadores da sua organização.
- Caso contrário, recomendamos que obtenha fichas AD Azure de um serviço web que suporte a sua aplicação. Recomendamos este método para aplicações de produção porque permite evitar incorporar as credenciais de acesso a Âncoras Espaciais Azure na sua aplicação ao cliente.

## <a name="account-keys"></a>Chaves de conta

A maneira mais fácil de começar é usar chaves de conta para aceder à sua conta Azure Spatial Anchors. Pode obter as chaves da sua conta no portal Azure. Vá à sua conta e selecione o separador **Chaves:**

![Screenshot que mostra o separador Chaves com o botão Copiar para a tecla Principal realçada.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Duas chaves estão disponíveis. Ambos são simultaneamente válidos para acesso à conta De âncoras espaciais. Recomendamos que atualize regularmente a chave que utiliza para aceder à conta. Ter duas teclas válidas separadas permite estas atualizações sem tempo de inatividade. Basta atualizar a chave primária e a chave secundária em alternativa.

O SDK tem suporte incorporado para autenticação através de chaves de conta. Só precisa de definir a `AccountKey` propriedade no seu `cloudSession` objeto:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Depois de definir essa propriedade, o SDK tratará da troca da chave de conta para um token de acesso e o caching necessário de fichas para a sua app.

> [!WARNING]
> Recomendamos que utilize chaves de conta para embarque rápido, mas apenas durante o desenvolvimento/prototipagem. Não recomendamos que envie a sua aplicação para a produção com uma chave de conta incorporada. Em vez disso, utilize as abordagens de autenticação AD Azure baseadas no utilizador ou baseadas em serviço descritas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação do utilizador Azure AD

Para aplicações que visam os utilizadores do Azure Ative Directory, recomendamos que utilize um token AD Azure para o utilizador. Pode obter este símbolo utilizando o [MSAL](../../active-directory/develop/msal-overview.md). Siga os passos no [quickstart sobre o registo de uma aplicação,](../../active-directory/develop/quickstart-register-app.md)que inclui:

**No portal Azure**
1.    Registe a sua candidatura no Azure AD como uma aplicação nativa. Como parte do registo, terá de determinar se a sua aplicação deve ser multitenante. Também terá de fornecer os URLs de redirecionamento permitidos para a sua aplicação.
1.  Vá ao separador permissões da **API.**
2.  **Selecione Adicionar uma permissão** .
    1.  Selecione **Fornecedor de Recursos de Realidade Mista** nas **APIs que a minha organização utiliza.**
    2.  Selecione **permissões delegadas** .
    3.  Selecione **mixedreality.signin** em **mixedreality** .
    4.  **Selecione Permissões de adicionar** .
3.  Selecione **o consentimento administrativo grant** .

2. Conceder à sua aplicação ou aos seus utilizadores acesso ao seu recurso:
   1.    Vá ao seu recurso De Âncoras Espaciais no portal Azure.
   2.    Aceda ao **separador Controlo de Acesso (IAM).**
   3.    Selecione **Adicionar atribuição de função** .
   1.    [Selecione uma função](#azure-role-based-access-control).
   2.    Na caixa **Select,** insira os nomes dos utilizadores, grupos e/ou aplicações às quais pretende atribuir acesso.
   3.    Selecione **Guardar** .

**No seu código**
1.    Certifique-se de usar o ID da aplicação e redirecionar URI da sua própria aplicação Azure AD para os parâmetros **de ID** e **RedirectUri** do cliente em MSAL.
2.    Desa esta medida de informação do arrendatário:
        1.    Se a sua candidatura apoiar **apenas a Minha organização,** substitua este valor pelo seu nome **de ID** ou **Inquilino.** Por exemplo, contoso.microsoft.com.
        2.    Se a sua candidatura suportar **Contas em qualquer diretório organizacional,** substitua este valor por **Organizações.**
        3.    Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** substitua este valor pelo **Common** .
3.    No seu pedido simbólico, desacordo o **âmbito** para **" `https://sts.<account-domain>//.default` ",** onde `<account-domain>` é substituído pelo Domínio de **Conta** para a sua conta Azure Spatial Anchors. Um exemplo de espaço para uma conta Azure Spatial Anchors no domínio da conta East US 2 é **`https://sts.mixedreality.azure.com//.default` "** . Este âmbito indicará à Azure AD que a sua aplicação está a solicitar um sinal para o Serviço de Token de Segurança de Realidade Mista (STS).

Depois de completar estes passos, a sua aplicação deverá ser capaz de obter da MSAL um token AD Azure. Pode definir o token AD do Azure como o objeto de configuração da `authenticationToken` sessão em nuvem:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticação do serviço AZURE AD

Para implementar aplicações que utilizem âncoras espaciais Azure para produção, recomendamos que utilize um serviço back-end que irá intermediar pedidos de autenticação. Aqui está uma visão geral do processo:

![Diagrama que fornece uma visão geral da autenticação às âncoras espaciais Azure.](./media/spatial-anchors-aad-authentication.png)

Aqui, presume-se que a sua aplicação utiliza o seu próprio mecanismo para autenticar o seu serviço back-end. (Por exemplo, uma conta Microsoft, PlayFab, Facebook, um ID do Google ou um nome de utilizador personalizado e senha.)  Depois de os seus utilizadores serem autenticados no seu serviço back-end, esse serviço pode recuperar um token AD Azure, trocá-lo por um token de acesso para Azure Spatial Anchors e devolvê-lo à sua aplicação cliente.

O token de acesso Azure AD é recuperado através do [MSAL](../../active-directory/develop/msal-overview.md). Siga os passos no [registo de uma aplicação de arranque rápido,](../../active-directory/develop/quickstart-register-app.md)que inclui:

**No portal Azure**
1.    Registe a sua candidatura no Azure AD:
        1.    No portal Azure, selecione **Azure Ative Directory** e, em seguida, selecione **registos de Aplicações** .
        2.    Selecione **Novo registo** .
        3.    Introduza o nome da sua aplicação, selecione **Web app /API** como o tipo de aplicação e insira o URL auth para o seu serviço. Selecione **Criar** .
2.    Na aplicação, selecione **Definições** e, em seguida, selecione o **separador Certificados e Segredos.** Crie um novo segredo de cliente, selecione uma duração e, em seguida, **selecione Add** . Certifique-se de guardar o valor secreto. Terá de incluí-lo no código do seu serviço web.
3.    Conceder à sua aplicação e/ou ao acesso dos utilizadores ao seu recurso:
        1.    Vá ao seu recurso De Âncoras Espaciais no portal Azure.
        2.    Aceda ao **separador Controlo de Acesso (IAM).**
        3.    Selecione **Adicionar atribuição de função** .
        4.    [Selecione uma função](#azure-role-based-access-control).
        5.    Na caixa **Select,** insira o nome ou os nomes das aplicações às quais pretende atribuir acesso. Se quiser que os utilizadores da sua aplicação tenham papéis diferentes contra a conta Spatial Anchors, registe várias aplicações em AD Azure e atribua um papel separado a cada um. Em seguida, implemente a sua lógica de autorização para utilizar o papel certo para os seus utilizadores.

              > [!NOTE]
              > No painel de atribuição de **funções Add,** em **Atribuir acesso a** , selecione utilizador **AD AD, grupo ou principal de serviço** .

        6.    Selecione **Guardar** .

**No seu código**

>[!NOTE]
> Pode utilizar a amostra de serviço disponível no GitHub.

1.    Certifique-se de usar o ID da aplicação, o segredo da aplicação e redirecionar a URI da sua própria aplicação AZure AD como o ID do **cliente,** **segredo** e **redirectUri** parâmetros msal.
2.    Coloque a identificação do inquilino para o seu próprio ID de inquilino Azure no parâmetro **da autoridade** na MSAL.
3.    No seu pedido simbólico, desacordo o **âmbito** para **" `https://sts.<account-domain>//.default` ",** onde `<account-domain>` é substituído pelo Domínio de **Conta** para a sua conta Azure Spatial Anchors. Um exemplo de espaço para uma conta Azure Spatial Anchors no domínio da conta East US 2 é **`https://sts.mixedreality.azure.com//.default` "** .

Depois de completar estes passos, o seu serviço de back-end pode recuperar um token AD Azure. Pode então trocá-lo por um sinal de MR que voltará ao cliente. A utilização de um token AD Azure para recuperar um token MR é feita através de uma chamada REST. Aqui está uma amostra:

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

O cabeçalho de autorização é formatado da seguinte forma: `Bearer <Azure_AD_token>`

A resposta contém o sinal mr em texto simples.

O sinal mr token é então devolvido ao cliente. A aplicação do seu cliente pode então defini-la como símbolo de acesso na configuração da sessão em nuvem:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

Para ajudá-lo a controlar o nível de acesso concedido aos utilizadores de aplicações, serviços ou Azure AD do seu serviço, pode atribuir estas funções pré-existentes conforme necessário contra as suas contas Azure Spatial Anchors:

- **Proprietário de conta de âncoras espaciais.** As aplicações ou utilizadores que tenham esta função podem criar âncoras espaciais, consultar e eliminá-las. Quando autentica na sua conta utilizando as chaves da conta, a função De Titular da Conta De Âncoras Espaciais é atribuída ao principal autenticado.
- **Contribuinte da Conta De Âncoras Espaciais** . As aplicações ou utilizadores que tenham esta função podem criar âncoras espaciais e consultar-se para elas, mas não podem eliminá-las.
- **Leitor de conta de âncoras espaciais** . As aplicações ou utilizadores que tenham esta função só podem consultar âncoras espaciais. Não podem criar novos, eliminar os existentes ou atualizar metadados sobre eles. Esta função é normalmente utilizada para aplicações onde alguns utilizadores curam o ambiente, mas outros só conseguem recordar âncoras anteriormente colocadas no ambiente.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação com âncoras espaciais Azure:

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
