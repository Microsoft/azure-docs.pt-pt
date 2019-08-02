---
title: Gerenciamento de sessão-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: mitigações para ameaças expostas no Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: fd7a12dca92a4b84ecd3a2c9644509a1dc705c35
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727858"
---
# <a name="security-frame-session-management"></a>Quadro de segurança: Gerenciamento de sessão
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementar o logoff apropriado usando métodos ADAL ao usar o Azure AD](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Usar tempos de vida finitos para tokens SaS gerados](#finite-tokens)</li></ul> |
| **BD de documentos do Azure** | <ul><li>[Usar tempos de vida mínimos de token para tokens de recurso gerados](#resource-tokens)</li></ul> |
| **AD FS** | <ul><li>[Implementar o logoff apropriado usando métodos WsFederation ao usar o ADFS](#wsfederation-logout)</li></ul> |
| **Servidor de identidade** | <ul><li>[Implementar o logoff apropriado ao usar o servidor de identidade](#proper-logout)</li></ul> |
| **Aplicativo Web** | <ul><li>[Os aplicativos disponíveis em HTTPS devem usar cookies seguros](#https-secure-cookies)</li><li>[Todos os aplicativos baseados em http devem especificar http somente para definição de cookie](#cookie-definition)</li><li>[Mitigar ataques CSRF (solicitação intersite forjada) em páginas da Web do ASP.NET](#csrf-asp)</li><li>[Configurar sessão para tempo de vida de inatividade](#inactivity-lifetime)</li><li>[Implementar o logoff apropriado do aplicativo](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Mitigar ataques CSRF (solicitação intersite forjada) em APIs Web do ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementar o logoff apropriado usando métodos ADAL ao usar o Azure AD

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Se o aplicativo depender do token de acesso emitido pelo Azure AD, o manipulador de eventos de logout deverá chamar |

### <a name="example"></a>Exemplo
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
Ele também deve destruir a sessão do usuário chamando o método Session. Abandon (). O método a seguir mostra a implementação segura do logoff do usuário:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Usar tempos de vida finitos para tokens SaS gerados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Tokens SaS gerados para autenticação no Hub IoT do Azure devem ter um período de expiração finito. Mantenha os tempos de vida do token SaS em um mínimo para limitar a quantidade de tempo que eles podem ser reproduzidos caso os tokens sejam comprometidos.|

## <a id="resource-tokens"></a>Usar tempos de vida mínimos de token para tokens de recurso gerados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | BD de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Reduza o período de token de recurso para um valor mínimo necessário. Tokens de recurso têm um TimeSpan válido padrão de 1 hora.|

## <a id="wsfederation-logout"></a>Implementar o logoff apropriado usando métodos WsFederation ao usar o ADFS

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Se o aplicativo depender do token STS emitido pelo ADFS, o manipulador de eventos de logout deverá chamar o método WSFederationAuthenticationModule. FederatedSignOut () para fazer logoff do usuário. Além disso, a sessão atual deve ser destruída e o valor do token de sessão deve ser redefinido e nullified.|

### <a name="example"></a>Exemplo
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementar o logoff apropriado ao usar o servidor de identidade

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [IdentityServer3-saída federada](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passos** | O IdentityServer dá suporte à capacidade de federar com provedores de identidade externos. Quando um usuário sai de um provedor de identidade upstream, dependendo do protocolo usado, talvez seja possível receber uma notificação quando o usuário sair. Ele permite que o IdentityServer Notifique seus clientes para que eles também possam desconectar o usuário. Verifique a documentação na seção de referências para obter os detalhes da implementação.|

## <a id="https-secure-cookies"></a>Os aplicativos disponíveis em HTTPS devem usar cookies seguros

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambientetype-local |
| **Referências**              | [elemento httpCookies (esquema de configurações ASP.net)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [Propriedade HttpCookie. Secure](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passos** | Os cookies normalmente são acessíveis somente para o domínio para o qual eles foram incluídos no escopo. Infelizmente, a definição de "Domain" não inclui o protocolo para que os cookies criados por HTTPS sejam acessíveis via HTTP. O atributo "Secure" indica ao navegador que o cookie só deve ser disponibilizado por HTTPS. Verifique se todos os cookies definidos por HTTPS usam o atributo **Secure** . O requisito pode ser imposto no arquivo Web. config definindo o atributo requireSSL como true. É a abordagem preferida, pois ela irá impor o atributo **seguro** para todos os cookies atuais e futuros sem a necessidade de fazer quaisquer alterações de código adicionais.|

### <a name="example"></a>Exemplo
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A configuração é imposta mesmo que HTTP seja usado para acessar o aplicativo. Se o HTTP for usado para acessar o aplicativo, a configuração interromperá o aplicativo, pois os cookies são definidos com o atributo Secure e o navegador não os enviará de volta ao aplicativo.

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | Ambientetype-local |
| **Referências**              | N/A  |
| **Passos** | Quando o aplicativo Web é a terceira parte confiável e o IDP é um servidor ADFS, o atributo Secure do token FedAuth pode ser configurado definindo RequireSSL como true na `system.identityModel.services` seção de Web. config:|

### <a name="example"></a>Exemplo
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Todos os aplicativos baseados em http devem especificar http somente para definição de cookie

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Atributo de cookie seguro](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Passos** | Para reduzir o risco de divulgação de informações com um ataque XSS (script entre sites), um novo atributo-httpOnly-foi introduzido aos cookies e tem suporte em todos os principais navegadores. O atributo especifica que um cookie não pode ser acessado por script. Usando cookies do HttpOnly, um aplicativo Web reduz a possibilidade de que as informações confidenciais contidas no cookie possam ser roubadas por meio de script e enviadas para o site de um invasor. |

### <a name="example"></a>Exemplo
Todos os aplicativos baseados em HTTP que usam cookies devem especificar HttpOnly na definição de cookie, implementando a seguinte configuração no Web. config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade FormsAuthentication. RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passos** | O valor da propriedade RequireSSL é definido no arquivo de configuração para um aplicativo ASP.NET usando o atributo requireSSL do elemento de configuração. Você pode especificar no arquivo Web. config para seu aplicativo ASP.NET se o SSL (protocolo SSL) é necessário para retornar o cookie de autenticação de formulários para o servidor, definindo o atributo requireSSL.|

### <a name="example"></a>Exemplo 
O exemplo de código a seguir define o atributo requireSSL no arquivo Web. config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5 |
| **Atributos**              | Ambientetype-local |
| **Referências**              | [Configuração do Windows Identity Foundation (WIF) – parte II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Passos** | Para definir o atributo httpOnly para cookies FedAuth, o valor do atributo hideFromCsript deve ser definido como true. |

### <a name="example"></a>Exemplo
A configuração a seguir mostra a configuração correta:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Mitigar ataques CSRF (solicitação intersite forjada) em páginas da Web do ASP.NET

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | A solicitação entre sites forjada (CSRF ou XSRF) é um tipo de ataque no qual um invasor pode realizar ações no contexto de segurança de uma sessão estabelecida de um usuário diferente em um site da Web. O objetivo é modificar ou excluir o conteúdo, se o site de destino depender exclusivamente de cookies de sessão para autenticar a solicitação recebida. Um invasor pode explorar essa vulnerabilidade, obtendo um navegador de usuário diferente para carregar uma URL com um comando de um site vulnerável no qual o usuário já está conectado. Há várias maneiras para um invasor fazer isso, por exemplo, hospedando um site diferente que carrega um recurso do servidor vulnerável ou fazendo com que o usuário clique em um link. O ataque pode ser evitado se o servidor enviar um token adicional para o cliente, requer que o cliente inclua esse token em todas as solicitações futuras e verifique se todas as solicitações futuras incluem um token que pertença à sessão atual, como usando o ASP.NET AntiForgeryToken ou ViewState. |

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Prevenção de XSRF/CSRF no ASP.NET MVC e páginas da Web](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Passos** | Formulários MVC CSRF e ASP.net – use o `AntiForgeryToken` método auxiliar em exibições; Coloque um `Html.AntiForgeryToken()` no formulário, por exemplo,|

### <a name="example"></a>Exemplo
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exemplo
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, HTML. AntiForgeryToken () dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* A solicitação de entrada tem um cookie chamado __RequestVerificationToken
* A solicitação de entrada tem `Request.Form` uma entrada chamada __RequestVerificationToken
* Esses cookies e `Request.Form` valores correspondem supondo que tudo esteja bem, a solicitação passa normalmente. Mas, caso contrário, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido". 

### <a name="example"></a>Exemplo
Anti-CSRF e AJAX: O token de formulário pode ser um problema para solicitações AJAX, porque uma solicitação AJAX pode enviar dados JSON, não dados de formulário HTML. Uma solução é enviar os tokens em um cabeçalho HTTP personalizado. O código a seguir usa sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens a uma solicitação AJAX. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar a solicitação, extraia os tokens do cabeçalho da solicitação. Em seguida, chame o método antifalsificação. Validate para validar os tokens. O método Validate gera uma exceção se os tokens não forem válidos.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Aproveite os recursos internos do ASP.NET para Evitarr os ataques da Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Passos** | Ataques de CSRF em aplicativos baseados em WebForms podem ser atenuados definindo ViewStateUserKey como uma cadeia de caracteres aleatória que varia para cada ID de usuário ou, melhor ainda, ID de sessão. Por vários motivos técnicos e sociais, a ID de sessão é um ajuste muito melhor, pois uma ID de sessão é imprevisível, atinge o tempo limite e varia de acordo com o usuário.|

### <a name="example"></a>Exemplo
Aqui está o código que você precisa ter em todas as suas páginas:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Configurar sessão para tempo de vida de inatividade

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade HttpSessionState. Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passos** | O tempo limite da sessão representa o evento que ocorre quando um usuário não executa nenhuma ação em um site durante um intervalo (definido pelo servidor Web). O evento, no lado do servidor, altera o status da sessão de usuário para "Invalid" (por exemplo, "não é mais usado") e instrui o servidor Web a destruí-lo (excluindo todos os dados contidos nele). O exemplo de código a seguir define o atributo de sessão de tempo limite como 15 minutos no arquivo Web. config.|

### <a name="example"></a>Exemplo
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Habilitar a detecção de ameaças no SQL do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Elemento Forms para autenticação (esquema de configurações do ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passos** | Definir o tempo limite do cookie de tíquete de autenticação de formulários para 15 minutos|

### <a name="example"></a>Exemplo
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | Ambientetype-local |
| **Referências**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Passos** | Quando o aplicativo Web é de terceira parte confiável e o ADFS é o STS, o tempo de vida dos cookies de autenticação-FedAuth tokens-pode ser definido pela seguinte configuração no Web. config:|

### <a name="example"></a>Exemplo
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Exemplo
Além disso, o tempo de vida do token de declaração SAML emitido pelo ADFS deve ser definido como 15 minutos, executando o seguinte comando do PowerShell no servidor ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementar o logoff apropriado do aplicativo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Execute a saída adequada do aplicativo, quando o usuário pressiona o botão fazer logoff. Após o logout, o aplicativo deve destruir a sessão do usuário e também redefinir e anular o valor do cookie de sessão, juntamente com a redefinição e a anulação do valor do cookie de autenticação. Além disso, quando várias sessões estão vinculadas a uma única identidade de usuário, elas devem ser encerradas coletivamente no lado do servidor no tempo limite ou logout. Por fim, verifique se a funcionalidade de logout está disponível em cada página. |

## <a id="csrf-api"></a>Mitigar ataques CSRF (solicitação intersite forjada) em APIs Web do ASP.NET

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | A solicitação entre sites forjada (CSRF ou XSRF) é um tipo de ataque no qual um invasor pode realizar ações no contexto de segurança de uma sessão estabelecida de um usuário diferente em um site da Web. O objetivo é modificar ou excluir o conteúdo, se o site de destino depender exclusivamente de cookies de sessão para autenticar a solicitação recebida. Um invasor pode explorar essa vulnerabilidade, obtendo um navegador de usuário diferente para carregar uma URL com um comando de um site vulnerável no qual o usuário já está conectado. Há várias maneiras para um invasor fazer isso, por exemplo, hospedando um site diferente que carrega um recurso do servidor vulnerável ou fazendo com que o usuário clique em um link. O ataque pode ser evitado se o servidor enviar um token adicional para o cliente, requer que o cliente inclua esse token em todas as solicitações futuras e verifique se todas as solicitações futuras incluem um token que pertença à sessão atual, como usando o ASP.NET AntiForgeryToken ou ViewState. |

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Impedindo ataques CSRF (solicitação intersite forjada) no ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Passos** | Anti-CSRF e AJAX: O token de formulário pode ser um problema para solicitações AJAX, porque uma solicitação AJAX pode enviar dados JSON, não dados de formulário HTML. Uma solução é enviar os tokens em um cabeçalho HTTP personalizado. O código a seguir usa sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens a uma solicitação AJAX. |

### <a name="example"></a>Exemplo
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar a solicitação, extraia os tokens do cabeçalho da solicitação. Em seguida, chame o método antifalsificação. Validate para validar os tokens. O método Validate gera uma exceção se os tokens não forem válidos.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Exemplo
Formulários MVC CSRF e ASP.NET – use o método auxiliar AntiForgeryToken em exibições; Coloque um HTML. AntiForgeryToken () no formulário, por exemplo,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima produzirá algo semelhante ao seguinte:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, HTML. AntiForgeryToken () dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* A solicitação de entrada tem um cookie chamado __RequestVerificationToken
* A solicitação de entrada tem `Request.Form` uma entrada chamada __RequestVerificationToken
* Esses cookies e `Request.Form` valores correspondem supondo que tudo esteja bem, a solicitação passa normalmente. Mas, caso contrário, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido".

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Provedor de identidade-ADFS, provedor de identidade-Azure AD |
| **Referências**              | [Proteger uma API Web com contas individuais e logon local no ASP.NET Web API 2,2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Passos** | Se a API da Web for protegida usando o OAuth 2,0, ela esperará um token de portador no cabeçalho de solicitação de autorização e concederá acesso à solicitação somente se o token for válido. Diferentemente da autenticação baseada em cookie, os navegadores não anexam os tokens de portador às solicitações. O cliente solicitante precisa anexar explicitamente o token de portador no cabeçalho da solicitação. Portanto, para APIs Web ASP.NET protegidas usando OAuth 2,0, os tokens de portador são considerados uma defesa contra ataques CSRF. Observe que, se a parte MVC do aplicativo usar a autenticação de formulários (ou seja, usar cookies), os tokens antifalsificação precisarão ser usados pelo aplicativo Web MVC. |

### <a name="example"></a>Exemplo
A API da Web deve ser informada para confiar apenas em tokens de portador e não em cookies. Isso pode ser feito pela seguinte configuração no `WebApiConfig.Register` método:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

O método SuppressDefaultHostAuthentication informa à API da Web para ignorar qualquer autenticação que ocorra antes que a solicitação alcance o pipeline da API Web, seja pelo IIS ou pelo middleware OWIN. Dessa forma, podemos restringir a API Web a autenticar somente usando tokens de portador.
