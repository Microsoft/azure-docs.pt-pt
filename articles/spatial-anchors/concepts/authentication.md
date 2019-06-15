---
title: Autenticação e autorização a âncoras espaciais do Azure | Documentos da Microsoft
description: Saiba mais sobre as várias formas que pode autenticar uma aplicação ou serviço de âncoras espaciais do Azure e os níveis de controlo que seja preciso impeçam o acesso a âncoras espaciais do Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2b3f4cf4099459b655fc0e370935ddc8079de810
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073948"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização a âncoras espaciais do Azure

Nesta seção, abordaremos as várias formas como pode autenticar a âncoras espaciais do Azure da sua aplicação ou serviço da web e as formas em que pode usar o controle de acesso baseado em funções no Azure Directory (Azure AD) para controlar o acesso às suas contas de âncoras espaciais.  

## <a name="overview"></a>Descrição geral

![Uma descrição geral da autenticação a âncoras espaciais do Azure](./media/spatial-anchors-authentication-overview.png)

Para aceder a uma determinada conta de âncoras espaciais do Azure, os clientes precisam de obter primeiro um token de acesso do Azure misto realidade serviço STS (Security Token). Tokens de obteve do STS live durante 24 horas e contêm informações para os serviços de âncoras geográficos para tomar decisões de autorização na conta e certifique-se de que apenas os principais de autorizados podem aceder a essa conta. 

Tokens de acesso podem ser obtidos no exchange de qualquer uma das chaves de conta ou a partir do Azure tokens emitidos em AD. 

Chaves de conta permitem-lhe começar rapidamente a utilizar o serviço de âncoras espaciais do Azure; No entanto, antes de implementar a sua aplicação para produção, recomenda-se que atualizar a sua aplicação para utilizar a autenticação baseada no AD do Azure. 

Tokens de autenticação do Azure AD podem ser obtidos de duas formas:

- Se estiver a criar uma aplicação empresarial e sua empresa está a utilizar o Azure AD como respetivo sistema de identidade, pode utilizar o Azure com base no utilizador autenticação do AD na sua aplicação e conceder acesso às suas contas de âncoras espaciais com seus grupos de segurança do Azure AD existentes, ou diretamente para os utilizadores na sua organização. 
- Caso contrário, é recomendável que obtenha tokens do AD do Azure de um serviço web que suporta a sua aplicação. Utilizar um serviço web de suporte é o método de autenticação recomendado para aplicações de produção, pois evita a incorporar as credenciais para aceder ao Azure âncoras de geográficos na aplicação de cliente. 

## <a name="account-keys"></a>Chaves de conta

Utilizar chaves de conta para acesso à sua conta de âncoras espaciais do Azure é a forma mais simples para começar a utilizar. Encontrará as chaves da conta no portal do Azure. Navegue para a sua conta e selecione o separador "Chaves".

![Uma descrição geral da autenticação a âncoras espaciais do Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Duas chaves são disponibilizadas, ambas ao mesmo tempo válidas para o acesso à conta de âncoras espaciais. Recomenda-se que Atualize regularmente a chave que utiliza para aceder a conta. ter dois separar ativar chaves válido, como atualizações sem tempo de inatividade; apenas terá de atualizar, em alternativa, a chave primária e a chave secundária. 

O SDK tem suporte incorporado para autenticar com chaves de conta; basta definir a propriedade de AccountKey em seu objeto cloudSession. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

Depois disso, o SDK processará a troca da chave de conta para um token de acesso e a necessário colocação em cache de tokens para a sua aplicação. 

> [!WARNING] 
> Utilização de chaves de conta é recomendada para introdução rápida, mas durante o desenvolvimento/criação de protótipos apenas. É altamente recomendável não para enviar a sua aplicação para produção com uma chave de conta incorporado no mesmo e, em vez disso, utilizar o Azure baseado no utilizador ou serviço autenticação AD aproxima próxima listada.

## <a name="azure-ad-user-authentication"></a>Autenticação de utilizador do Azure AD

Para aplicações destinadas aos utilizadores do Azure Active Directory, a abordagem recomendada é usar um token do Azure AD para o utilizador, o que pode utilizar a biblioteca ADAL, conforme descrito na documentação do seguinte: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); deve seguir os passos listados na "Quickstarts", que incluem:

1. Configuração no portal do Azure
    1.  Registar a sua aplicação no Azure AD como **aplicativo nativo**. Como parte do Registro, terá de determinar se a aplicação deve ser multi-inquilino ou não e fornecer o redirecionamento de URLs permitidos para a sua aplicação.  
    2.  Conceda o acesso de aplicação ou os utilizadores ao seu recurso: 
        1.  Navegue até ao seu recurso de âncoras geográficos no portal do Azure
        2.  Mude para o **controlo de acesso (IAM)** separador
        3.  Pressionar **adicionar atribuição de função**
            1.  [Selecione uma função](#role-based-access-control)
            2.  Na **selecione** , insira o nome de utilizador (es), o grupo (s) e/ou as aplicações aos quais pretende atribuir acesso. 
            3.  Pressionar **guardar**.
2. Em seu código:
    1.  Certifique-se utilizar o **ID da aplicação** e **Uri de redirecionamento** da sua própria aplicação do Azure AD como o **ID de cliente** e **RedirectUri** parâmetros em ADAL
    2.  Defina as informações do inquilino:
        1.  Se a sua aplicação suportar **apenas a minha organização**, substitua este valor com sua **ID do inquilino** ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
        2.  Se a sua aplicação suportar **contas em qualquer diretório organizacional**, substitua este valor com **organizações**
        3.  Se a sua aplicação suportar **utilizadores com contas Microsoft todos os**, substitua este valor com **comuns**
    3.  No seu pedido de token, defina o **resource** para "https://sts.mixedreality.azure.com". Este "recurso" irá indicar ao Azure AD que a aplicação está a solicitar um token para o serviço de âncoras espaciais do Azure.  

Com isso, seu aplicativo deve ser capaz de obter um token do Azure AD; a partir de ADAL Pode definir esse token do Azure AD como o **authenticationToken** em seu objeto de configuração de sessão na cloud. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Autenticação de serviço do Azure AD

A opção recomendada para implementar aplicações tirar partido do âncoras espaciais do Azure para produção é tirar partido de um serviço de back-end que será mediador pedidos de autenticação. O esquema geral deve estar conforme descrito neste diagrama:

![Uma descrição geral da autenticação a âncoras espaciais do Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, é assumido que o seu aplicativo usa seu próprio mecanismo (por exemplo: Conta Microsoft, PlayFab, Facebook, Google ID, nome de utilizador/palavra-passe personalizada, etc.) para autenticar para o seu serviço de back-end. Assim que os utilizadores são autenticados ao seu serviço de back-end, que o serviço pode obter um token do Azure AD, do exchange-lo para um token de acesso para as âncoras espaciais do Azure e retorná-lo para a sua aplicação de cliente.

O token de acesso do Azure AD é recuperado usando a biblioteca ADAL, conforme descrito na documentação do seguinte: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); deve seguir os passos listados na "Quickstarts", que incluem:

1.  Configuração no portal do Azure:
    1.  Registe a sua aplicação no Azure AD:
        1.  No portal do Azure, navegue para **do Azure Active Directory**e selecione **registos das aplicações**
        2.  Selecione **novo registo de aplicação**
        3.  Introduza o nome da sua aplicação, selecione **aplicação Web / API** como o tipo de aplicativo e introduza o URL de autenticação para o seu serviço. Em seguida, pressione **criar**.
        4.  No aplicativo, pressione **configurações**, em seguida, selecione a **chaves** separador. Introduza o nome da sua chave, selecione uma duração e pressione **guardar**. Certifique-se guardar o valor da chave que é apresentado nessa altura, pois precisará incluí-la no código do seu serviço da web.
    2.  Conceda o acesso de aplicação e/ou os utilizadores ao seu recurso:
        1.  Navegue até ao seu recurso de âncoras geográficos no portal do Azure
        2.  Mude para o **controlo de acesso (IAM)** separador
        3.  Pressionar **adicionar atribuição de função**
        1.  [Selecione uma função](#role-based-access-control)
        2.  Na **selecione** campo, introduza o nome de aplicações que criou e para a qual pretende atribuir acesso. Se pretender que os utilizadores da sua aplicação têm funções diferentes em relação à conta de âncoras espaciais, deve registar várias aplicações no Azure AD e atribuir a cada uma função separada. Em seguida, implemente sua lógica de autorização para utilizar a função certa para os seus utilizadores.  
    3.  Pressionar **guardar**.
2.  Em seu código (Nota: pode utilizar o exemplo de serviço incluído no GitHub):
    1.  Certifique-se utilizar o ID da aplicação, o segredo de aplicação e redirecionar o Uri da sua própria aplicação do Azure AD como o ID de cliente, segredo e parâmetros de RedirectUri na ADAL
    2.  Definir o ID de inquilino para o seu ID de inquilino AAAzure adicionar no parâmetro autoridade na ADAL
    3.  No seu pedido de token, defina o **resource** para "https://sts.mixedreality.azure.com" 

Com isso, o seu serviço de back-end pode obter um token do Azure AD. Em seguida, ele pode trocá-lo para um token de MR que irá devolver ao cliente. Utilizar um token do Azure AD para obter um token de MR é feito por meio de uma chamada REST. Eis uma chamada de exemplo:

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

Em que o cabeçalho Authorization é formatado da seguinte forma: `Bearer <accoundId>:<accountKey>`

E a resposta contém o token de MR em texto simples.
 
Esse token MR, em seguida, é devolvido ao cliente. A aplicação de cliente, em seguida, pode configurá-lo como seu token de acesso na configuração de sessão na cloud.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ajudar a controlar o nível de acesso concedido a aplicações, serviços ou utilizadores do Azure AD do seu serviço, foram criadas as seguintes funções para atribuir conforme necessário em suas contas de âncoras espaciais do Azure:

- **Proprietário da conta âncoras geográficos**: aplicações ou utilizadores que tenham esta função são capazes de criar âncoras espaciais, consultá-los e eliminá-los. Quando se autenticar a sua conta com chaves de conta, o **geográficos proprietário da conta âncoras** função é atribuída ao principal de autenticado. 
- **Contribuinte de conta de âncoras geográficos**: aplicações ou utilizadores que tenham esta função podem criar âncoras geográficos, a consulta para os mesmos, mas não é possível eliminá-los. 
- **Leitor de conta de âncoras geográficos**: aplicações ou utilizadores que tenham esta função só são possível consultar as âncoras espaciais, mas não é possível criar novos, excluir os existentes ou atualizar os metadados de âncoras espaciais. Isso é normalmente utilizado para aplicações em que alguns utilizadores organizar o ambiente, enquanto outros podem apenas Lembre-se de âncoras anteriormente efetuadas nesse ambiente.

## <a name="next-steps"></a>Passos Seguintes

Crie seu primeiro aplicativo com âncoras espaciais do Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
