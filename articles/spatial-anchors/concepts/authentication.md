---
title: Autenticação e autorização para âncoras espaciais do Azure | Microsoft Docs
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar para âncoras espaciais do Azure e os níveis de controle que você precisa para portar acesso a âncoras espaciais do Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 850748462f0273f2dfb1522d900ce9f1b2156d2a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517074"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para âncoras espaciais do Azure

Nesta seção, abordaremos as várias maneiras pelas quais você pode autenticar as âncoras espaciais do Azure de seu aplicativo ou serviço Web e as maneiras como você pode usar o controle de acesso baseado em função no diretório do Azure (AD do Azure) para controlar o acesso às suas contas espaciais de âncoras.  

## <a name="overview"></a>Descrição geral

![Uma visão geral da autenticação para âncoras espaciais do Azure](./media/spatial-anchors-authentication-overview.png)

Para acessar uma determinada conta de âncoras espaciais do Azure, os clientes precisam primeiro obter um token de acesso do STS (serviço de token de segurança) da realidade mista do Azure. Os tokens obtidos do STS Live por 24 horas e contêm informações para os serviços de âncoras espaciais para tomar decisões de autorização na conta e garantir que apenas entidades de segurança autorizadas possam acessar essa conta. 

Tokens de acesso podem ser obtidos no Exchange a partir de chaves de conta ou de tokens emitidos pelo Azure AD. 

As chaves de conta permitem que você comece rapidamente a usar o serviço âncoras espaciais do Azure; no entanto, antes de implantar seu aplicativo para produção, é recomendável que você atualize seu aplicativo para usar a autenticação baseada no Azure AD. 

Os tokens de autenticação do Azure AD podem ser obtidos de duas maneiras:

- Se você estiver criando um aplicativo empresarial e sua empresa estiver usando o Azure AD como seu sistema de identidade, você poderá usar a autenticação do Azure AD baseada no usuário em seu aplicativo e conceder acesso às suas contas de âncoras espaciais usando seus grupos de segurança existentes do Azure AD ou diretamente aos usuários em sua organização. 
- Caso contrário, é recomendável que você obtenha tokens do Azure AD de um serviço Web que dê suporte ao seu aplicativo. Usar um serviço Web de suporte é o método de autenticação recomendado para aplicativos de produção, pois evita a inserção de credenciais para acessar âncoras espaciais do Azure em seu aplicativo cliente. 

## <a name="account-keys"></a>Chaves de conta

O uso de chaves de conta para acesso à sua conta de âncoras espaciais do Azure é a maneira mais simples de começar. Você encontrará suas chaves de conta no portal do Azure. Navegue até sua conta e selecione a guia "chaves".

![Uma visão geral da autenticação para âncoras espaciais do Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Duas chaves são disponibilizadas, que são simultaneamente válidas para acesso à conta de âncoras espaciais. É recomendável que você atualize regularmente a chave usada para acessar a conta; ter duas chaves válidas separadas habilita tais atualizações sem tempo de inatividade; Você só precisa atualizar a chave primária e a chave secundária como alternativa. 

O SDK tem suporte interno para autenticação com chaves de conta; Você simplesmente precisa definir a propriedade AccountKey em seu objeto cloudSession. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Quando isso for feito, o SDK manipulará a troca da chave de conta para um token de acesso e o cache de tokens necessário para seu aplicativo. 

> [!WARNING] 
> O uso de chaves de conta é recomendado para rápida integração, mas somente durante o desenvolvimento/protótipo somente. É altamente recomendável não enviar seu aplicativo para produção usando uma chave de conta incorporada e, em vez disso, usar as abordagens de autenticação do Azure AD baseadas em usuário ou em serviço listadas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação de usuário do Azure AD

Para aplicativos que visam Azure Active Directory usuários, a abordagem recomendada é usar um token do Azure ad para o usuário, que você pode obter usando a biblioteca Adal, conforme descrito na seguinte [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md)documentação:; você deve seguir as etapas listadas em "inícios rápidos", que incluem:

1. Configuração no portal do Azure
    1.  Registre seu aplicativo no Azure AD como **aplicativo nativo**. Como parte do registro, você precisará determinar se seu aplicativo deve ser multilocatário ou não e fornecer as URLs de redirecionamento permitidas para seu aplicativo.  
    2.  Conceda ao seu aplicativo ou usuários acesso ao seu recurso: 
        1.  Navegue até o recurso âncoras espaciais em portal do Azure
        2.  Alternar para a guia **controle de acesso (iam)**
        3.  Clique em **Adicionar atribuição de função**
            1.  [Selecionar uma função](#role-based-access-control)
            2.  No campo **selecionar** , insira o nome dos usuários, grupos e/ou os aplicativos aos quais você deseja atribuir o acesso de usuário (s). 
            3.  Clique em **salvar**.
2. Em seu código:
    1.  Certifique-se de usar **a ID do aplicativo** e o URI de redirecionamento do seu próprio aplicativo do Azure ad como a ID do **cliente** e os parâmetros **RedirectUri** no Adal
    2.  Defina as informações do locatário:
        1.  Se seu aplicativo der suporte **apenas à minha organização**, substitua esse valor pela sua **ID de locatário** ou nome de **locatário** (por exemplo, contoso.Microsoft.com)
        2.  Se seu aplicativo oferecer suporte a **contas em qualquer diretório organizacional**, substitua esse valor pelas **organizações**
        3.  Se seu aplicativo der suporte a **todos os conta Microsoft usuários**, substitua esse **valor por**
    3.  Em sua solicitação de token, defina o **recurso** como https://sts.mixedreality.azure.com"". Esse "recurso" indicará ao Azure AD que seu aplicativo está solicitando um token para o serviço âncoras espaciais do Azure.  

Com isso, seu aplicativo deve ser capaz de obter do ADAL um token do Azure AD; Você pode definir esse token do Azure AD como o **authenticationToken** em seu objeto de configuração de sessão de nuvem. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticação de serviço do Azure AD

A opção recomendada para implantar aplicativos que aproveitam âncoras espaciais do Azure para produção é utilizar um serviço de back-end que atenderá às solicitações de autenticação. O esquema geral deve ser conforme descrito neste diagrama:

![Uma visão geral da autenticação para âncoras espaciais do Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, supõe-se que seu aplicativo usa seu próprio mecanismo (por exemplo: Conta Microsoft, PlayFab, Facebook, Google ID, nome de usuário personalizado/senha, etc.) para autenticar em seu serviço de back-end. Depois que os usuários são autenticados em seu serviço de back-end, esse serviço pode recuperar um token do Azure AD, trocar o ti por um token de acesso para âncoras espaciais do Azure e retorná-lo de volta para o aplicativo cliente.

O token de acesso do AD do Azure é recuperado usando a biblioteca Adal, conforme descrito na [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md)seguinte documentação:. você deve seguir as etapas listadas em "inícios rápidos", que incluem:

1.  Configuração no portal do Azure:
    1.  Registrar seu aplicativo no Azure AD:
        1.  Em portal do Azure, navegue até **Azure Active Directory**e selecione **registros do aplicativo**
        2.  Selecione **novo registro de aplicativo**
        3.  Insira o nome do seu aplicativo, selecione **aplicativo Web/API** como o tipo de aplicativo e insira a URL de autenticação para seu serviço. Em seguida, clique em **criar**.
        4.  Nesse aplicativo, clique em **configurações**e selecione a guia **chaves** . Insira o nome da sua chave, selecione uma duração e clique em **salvar**. Certifique-se de salvar o valor de chave que é exibido nesse momento, pois você precisará incluí-lo no código do serviço Web.
    2.  Conceda ao seu aplicativo e/ou usuários acesso ao recurso:
        1.  Navegue até o recurso âncoras espaciais em portal do Azure
        2.  Alternar para a guia **controle de acesso (iam)**
        3.  Clique em **Adicionar atribuição de função**
        1.  [Selecionar uma função](#role-based-access-control)
        2.  No campo **selecionar** , insira o nome dos aplicativos que você criou e ao qual você deseja atribuir acesso. Se você quiser que os usuários do aplicativo tenham funções diferentes em relação à conta de âncoras espaciais, registre vários aplicativos no Azure AD e atribua a cada uma delas uma função separada. Em seguida, implemente sua lógica de autorização para usar a função certa para seus usuários.  
    3.  Clique em **salvar**.
2.  Em seu código (Observação: você pode usar o exemplo de serviço incluído no GitHub):
    1.  Certifique-se de usar a ID do aplicativo, o segredo do aplicativo e o URI de redirecionamento do seu próprio aplicativo do Azure AD como a ID do cliente, o segredo e os parâmetros RedirectUri no ADAL
    2.  Defina a ID do locatário como seu próprio AAAzure adicionar ID de locatário no parâmetro Authority na ADAL
    3.  Em sua solicitação de token, defina o **recurso** como https://sts.mixedreality.azure.com"" 

Com isso, o serviço de back-end pode recuperar um token do Azure AD. Em seguida, ele pode alterá-lo para um token MR que ele retornará ao cliente. Usar um token do Azure AD para recuperar um token MR é feito por meio de uma chamada REST. Aqui está uma chamada de exemplo:

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

Onde o cabeçalho de autorização é formatado da seguinte maneira:`Bearer <accoundId>:<accountKey>`

E a resposta contém o token MR em texto sem formatação.
 
Esse token Sr é retornado para o cliente. Seu aplicativo cliente pode defini-lo como seu token de acesso na configuração da sessão de nuvem.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ajudar a controlar o nível de acesso concedido a aplicativos, serviços ou usuários do Azure AD do seu serviço, as seguintes funções foram criadas para você atribuir conforme necessário em relação às suas contas de âncoras espaciais do Azure:

- **Proprietário da conta**de âncoras espaciais: os aplicativos ou usuários que têm essa função podem criar âncoras espaciais, consultá-los e excluí-los. Quando você se autentica em sua conta usando chaves de conta, a função de **proprietário da conta** de âncoras espaciais é atribuída à entidade de segurança autenticada. 
- **Colaborador de conta**de âncoras espaciais: os aplicativos ou usuários que têm essa função podem criar âncoras espaciais, consultá-los, mas não podem excluí-los. 
- **Leitor de conta**de âncoras espaciais: os aplicativos ou usuários que têm essa função só podem consultar âncoras espaciais, mas não podem criar novos, excluir ou atualizar metadados em âncoras espaciais. Normalmente, isso é usado para aplicativos em que alguns usuários separam o ambiente, enquanto outros só podem recuperar âncoras previamente colocadas nesse ambiente.

## <a name="next-steps"></a>Passos seguintes

Crie seu primeiro aplicativo com âncoras espaciais do Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
