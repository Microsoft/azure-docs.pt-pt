---
title: Gestão de Sessões - Microsoft Threat Modeling Tool - Azure [ Microsoft Docs
description: atenuações para ameaças expostas na Ferramenta de Modelação de Ameaças
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
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161503"
---
# <a name="security-frame-session-management"></a>Quadro de segurança: Gestão de sessões
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementar logout adequado utilizando métodos ADAL ao utilizar a AD Azure](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Use vidas finitas para fichas SaS geradas](#finite-tokens)</li></ul> |
| **Documento Azure DB** | <ul><li>[Use as vidas mínimas para tokens gerados](#resource-tokens)</li></ul> |
| **AD FS** | <ul><li>[Implementar logout adequado utilizando métodos WsFederation ao utilizar ADFS](#wsfederation-logout)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Implementar logout adequado ao utilizar o Servidor de Identidade](#proper-logout)</li></ul> |
| **Aplicação Web** | <ul><li>[As aplicações disponíveis sobre HTTPS devem usar cookies seguros](#https-secure-cookies)</li><li>[Todas as aplicações baseadas em http devem especificar http apenas para definição de cookies](#cookie-definition)</li><li>[Atenuar contra ataques de falsificação de pedidos de sítios cruzados (CSRF) em páginas web ASP.NET](#csrf-asp)</li><li>[Preparar sessão para a vida útil da inatividade](#inactivity-lifetime)</li><li>[Implementar o logout adequado a partir da aplicação](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Atenuar contra ataques de falsificação de pedidos de sítios cruzados (CSRF) em ASP.NET APIs web](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementar logout adequado utilizando métodos ADAL ao utilizar a AD Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depender do token de acesso emitido pela Azure AD, o manipulador de eventos de logout deve ligar |

### <a name="example"></a>Exemplo
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
Também deve destruir a sessão do utilizador, ligando para o método Session.Abandon() O método seguinte mostra a implementação segura do logout do utilizador:
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

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Use vidas finitas para fichas SaS geradas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | As fichas SaS geradas para autenticar o Hub Azure IoT devem ter um prazo de validade finito. Mantenha as vidas token SaS ao mínimo para limitar o tempo que podem ser repetidos no caso de os tokens estarem comprometidos.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Use as vidas mínimas para tokens gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Reduza a hora do token do recurso para um valor mínimo exigido. As fichas de recurso têm uma altura de tempo válida padrão de 1 hora.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementar logout adequado utilizando métodos WsFederation ao utilizar ADFS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depender do token STS emitido pela ADFS, o manipulador de eventos de logout deve ligar para o método WSFederationAuthenticationModule.FederatedSignOut() para iniciar o registo do utilizador. Também a sessão atual deve ser destruída e o valor simbólico da sessão deve ser reiniciado e anulado.|

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

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementar logout adequado ao utilizar o Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Signo de IdentityServer3-Federado](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passos** | O IdentityServer suporta a capacidade de federar com fornecedores de identidade externos. Quando um utilizador assina um fornecedor de identidade a montante, dependendo do protocolo utilizado, pode ser possível receber uma notificação quando o utilizador assinar. Permite que o IdentityServer notifique os seus clientes para que também possam assinar o utilizador. Consulte a documentação na secção de referências para obter os detalhes da implementação.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>As aplicações disponíveis sobre HTTPS devem usar cookies seguros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | AmbienteTipo - OnPrem |
| **Referências**              | [elemento httpCookies (ASP.NET Definições Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure Property](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passos** | Normalmente, os cookies só são acessíveis ao domínio para o qual foram amplamente acessíveis. Infelizmente, a definição de "domínio" não inclui o protocolo para que os cookies que são criados em HTTPS sejam acessíveis em HTTP. O atributo "seguro" indica ao navegador que o cookie só deve ser disponibilizado através de HTTPS. Certifique-se de que todos os cookies definidos sobre HTTPS utilizam o atributo **seguro.** O requisito pode ser aplicado no ficheiro web.config, definindo o atributo necessário para o verdadeiro. É a abordagem preferida porque irá impor o atributo **seguro** para todos os cookies atuais e futuros sem a necessidade de fazer quaisquer alterações adicionais de código.|

### <a name="example"></a>Exemplo
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A definição é executada mesmo que http seja utilizado para aceder à aplicação. Se o HTTP for utilizado para aceder à aplicação, a definição quebra a aplicação porque os cookies são definidos com o atributo seguro e o navegador não os enviará de volta para a aplicação.

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | AmbienteTipo - OnPrem |
| **Referências**              | N/D  |
| **Passos** | Quando a aplicação web é a Parte De Fiação, e o IDP é servidor ADFS, o atributo seguro do `system.identityModel.services` token FedAuth pode ser configurado através da definição requerida SSL to True na secção web.config:|

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

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Todas as aplicações baseadas em http devem especificar http apenas para definição de cookies

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Atributo de Cookie Seguro](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Passos** | Para mitigar o risco de divulgação de informação com um ataque de scripts inter-site (XSS), um novo atributo - httpOnly - foi introduzido nos cookies e é suportado por todos os principais navegadores. O atributo especifica que um cookie não é acessível através do script. Ao utilizar cookies HttpOnly, uma aplicação web reduz a possibilidade de que informações sensíveis contidas no cookie possam ser roubadas através de script e enviadas para o site de um intruso. |

### <a name="example"></a>Exemplo
Todas as aplicações baseadas em HTTP que utilizam cookies devem especificar httpOnly na definição de cookies, implementando a seguinte configuração em web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [FormasAutenticação.Requer propriedade SSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passos** | O valor de propriedade RequireSSL é definido no ficheiro de configuração para uma aplicação ASP.NET utilizando o atributo necessário do elemento de configuração. Pode especificar no ficheiro Web.config para a sua aplicação ASP.NET se o SSL (Secure Sockets Layer) é necessário para devolver o cookie de autenticação de formulários ao servidor, definindo o atributo necessário.|

### <a name="example"></a>Exemplo 
O exemplo de código que se segue define o atributo necessário no ficheiro Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5 |
| **Atributos**              | AmbienteTipo - OnPrem |
| **Referências**              | [Configuração da Fundação Windows Identity (WIF) – Parte II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Passos** | Para definir httpOnly atributo para cookies FedAuth, o valor do atributo do FromCsript deve ser definido para True. |

### <a name="example"></a>Exemplo
A configuração seguinte mostra a configuração correta:
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

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Atenuar contra ataques de falsificação de pedidos de sítios cruzados (CSRF) em páginas web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedido transversal (CSRF ou XSRF) é um tipo de ataque no qual um intruso pode realizar ações no contexto de segurança de uma sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou excluir conteúdo, se o site direcionado depender exclusivamente de cookies de sessão para autenticar o pedido recebido. Um intruso poderia explorar esta vulnerabilidade obtendo um navegador de um utilizador diferente para carregar um URL com um comando de um site vulnerável no qual o utilizador já está conectado. Existem muitas formas de um intruso fazer isso, como por exemplo hospedando um site diferente que carrega um recurso do servidor vulnerável, ou fazendo com que o utilizador clique num link. O ataque pode ser evitado se o servidor enviar um token adicional ao cliente, requer que o cliente inclua esse símbolo em todos os pedidos futuros, e verifica que todos os pedidos futuros incluem um símbolo que diz respeito à sessão atual, como por exemplo, utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção xSRF/CSRF em ASP.NET MVC e páginas Web](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Passos** | Formulários Anti-CSRF e ASP.NET `AntiForgeryToken` MVC - Utilize o método de ajuda em pontos de vista; colocar `Html.AntiForgeryToken()` um na forma, por exemplo,|

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
Ao mesmo tempo, html.AntiForgeryToken() dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar um post de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação alvo. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica que:
* O pedido de entrada tem um cookie chamado __RequestVerificationToken
* O pedido de `Request.Form` entrada tem uma entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` valores coincidem Assumindo que está tudo bem, o pedido passa normalmente. Mas se não, então uma falha de autorização com a mensagem "Um token anti-falsificação exigido não foi fornecido ou foi inválido". 

### <a name="example"></a>Exemplo
Anti-CSRF e AJAX: O formulário pode ser um problema para os pedidos do AJAX, porque um pedido de AJAX pode enviar dados JSON, e não dados de formulário HTML. Uma solução é enviar as fichas num cabeçalho HTTP personalizado. O código seguinte usa a sintaxe Razor para gerar os tokens, e depois adiciona as fichas a um pedido do AJAX. 
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
Quando processar o pedido, extraias as fichas do cabeçalho do pedido. Em seguida, ligue para o método AntiForgery.Valide o método para validar as fichas. O método validar abre uma exceção se as fichas não forem válidas.
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

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Aproveite ASP.NET funcionalidades incorporadas para evitar ataques web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Passos** | Os ataques cSRF em aplicações baseadas no WebForm podem ser atenuados definindo ViewStateUserKey para uma cadeia aleatória que varia para cada utilizador - ID do utilizador ou, melhor ainda, ID da sessão. Por uma série de razões técnicas e sociais, o ID da sessão é muito melhor adequado porque uma identificação de sessão é imprevisível, vezes fora, e varia por utilizador.|

### <a name="example"></a>Exemplo
Aqui está o código que precisa de ter em todas as suas páginas:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Preparar sessão para a vida útil da inatividade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passos** | O tempo de saída da sessão representa o evento que ocorre quando um utilizador não realiza qualquer ação num web site durante um intervalo (definido pelo servidor web). O evento, do lado do servidor, altere o estado da sessão do utilizador para 'inválido' (por exemplo, "já não utilizado") e instrua o servidor web a destruí-lo (apagando todos os dados contidos no mesmo). O exemplo de código que se segue define o atributo da sessão de tempo para 15 minutos no ficheiro Web.config.|

### <a name="example"></a>Exemplo
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Ativar a deteção de ameaças no Azure SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Forma elemento de autenticação (ASP.NET Definições Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passos** | Defina o tempo de tempo de cookie de autenticação de formulários para 15 minutos|

### <a name="example"></a>Exemplo
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | AmbienteTipo - OnPrem |
| **Referências**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Passos** | Quando a aplicação web é Relying Party e ADFS é o STS, a vida útil dos cookies de autenticação - tokens FedAuth - pode ser definida pela seguinte configuração em web.config:|

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
Também a ADFS emitida a alegação saml token token deve ser definida para 15 minutos, executando o seguinte comando powershell no servidor ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementar o logout adequado a partir da aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Execute o 'SignOut' adequado da aplicação, quando o utilizador premir o botão de arranque. No início do início, a aplicação deve destruir a sessão do utilizador e também repor e anular o valor dos cookies da sessão, juntamente com a reposição e anulação do valor dos cookies de autenticação. Além disso, quando várias sessões estão ligadas a uma única identidade de utilizador, devem ser terminadas coletivamente no lado do servidor no tempo de desconto ou no início do início. Por último, certifique-se de que a funcionalidade Logout está disponível em todas as páginas. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Atenuar contra ataques de falsificação de pedidos de sítios cruzados (CSRF) em ASP.NET APIs web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedido transversal (CSRF ou XSRF) é um tipo de ataque no qual um intruso pode realizar ações no contexto de segurança de uma sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou excluir conteúdo, se o site direcionado depender exclusivamente de cookies de sessão para autenticar o pedido recebido. Um intruso poderia explorar esta vulnerabilidade obtendo um navegador de um utilizador diferente para carregar um URL com um comando de um site vulnerável no qual o utilizador já está conectado. Existem muitas formas de um intruso fazer isso, como por exemplo hospedando um site diferente que carrega um recurso do servidor vulnerável, ou fazendo com que o utilizador clique num link. O ataque pode ser evitado se o servidor enviar um token adicional ao cliente, requer que o cliente inclua esse símbolo em todos os pedidos futuros, e verifica que todos os pedidos futuros incluem um símbolo que diz respeito à sessão atual, como por exemplo, utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção de ataques de falsificação de pedidos de sítios cruzados (CSRF) em ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Passos** | Anti-CSRF e AJAX: O formulário pode ser um problema para os pedidos do AJAX, porque um pedido de AJAX pode enviar dados JSON, e não dados de formulário HTML. Uma solução é enviar as fichas num cabeçalho HTTP personalizado. O código seguinte usa a sintaxe Razor para gerar os tokens, e depois adiciona as fichas a um pedido do AJAX. |

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
Quando processar o pedido, extraias as fichas do cabeçalho do pedido. Em seguida, ligue para o método AntiForgery.Valide o método para validar as fichas. O método validar abre uma exceção se as fichas não forem válidas.
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
Formulários Anti-CSRF e ASP.NET MVC - Utilize o método de ajuda antiForgeryToken em pontos de vista; colocar um Html.AntiForgeryToken() na forma, por exemplo,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima irá dar algo como o seguinte:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, html.AntiForgeryToken() dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar um post de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação alvo. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica que:
* O pedido de entrada tem um cookie chamado __RequestVerificationToken
* O pedido de `Request.Form` entrada tem uma entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` valores coincidem Assumindo que está tudo bem, o pedido passa normalmente. Mas se não, então uma falha de autorização com a mensagem "Um token anti-falsificação exigido não foi fornecido ou foi inválido".

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Fornecedor de Identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | [Proteja um API web com contas individuais e login local em ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Passos** | Se a Web API for protegida utilizando o OAuth 2.0, então espera que um portador token no cabeçalho de pedido de autorização e conceda acesso ao pedido apenas se o token for válido. Ao contrário da autenticação baseada em cookies, os navegadores não ligam os tokens do portador aos pedidos. O cliente que solicita deve anexar explicitamente o token do portador no cabeçalho do pedido. Portanto, para ASP.NET APIs web protegidos usando OAuth 2.0, os tokens do portador são considerados como uma defesa contra ataques CSRF. Por favor, note que se a parte mVC da aplicação usar formulários de autenticação (ou seja, usar cookies), os tokens anti-falsificação têm de ser utilizados pela aplicação web do MVC. |

### <a name="example"></a>Exemplo
A Web API tem de ser informada para confiar apenas em tokens ao portador e não em cookies. Pode ser feito pela seguinte `WebApiConfig.Register` configuração no método:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

O método SupressãoDoHostAuthentication diz à Web API para ignorar qualquer autenticação que ocorra antes de o pedido chegar ao pipeline Web API, quer pelo IIS quer pelo middleware OWIN. Dessa forma, podemos restringir a Web API a autenticar apenas usando tokens ao portador.
