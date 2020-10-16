---
title: Gestão de Sessão - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a mitigação da gestão da sessão para ameaças expostas na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: c26acb1460516781b34a5dcc861164e9ef87a37a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331629"
---
# <a name="security-frame-session-management"></a>Quadro de Segurança: Gestão de Sessão
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implemente o logout adequado utilizando métodos ADAL ao utilizar o Azure AD](#logout-adal)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Use vidas finitas para tokens SaS gerados](#finite-tokens)</li></ul> |
| **Documento Azure DB** | <ul><li>[Utilize as vidas mínimas simbólicas para tokens de recursos gerados](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementar o logout adequado utilizando métodos de WsFederation ao utilizar o ADFS](#wsfederation-logout)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Implemente o logout adequado ao utilizar o Servidor de Identidade](#proper-logout)</li></ul> |
| **Aplicação Web** | <ul><li>[As aplicações disponíveis em HTTPS devem utilizar cookies seguros](#https-secure-cookies)</li><li>[Toda a aplicação baseada em http deve especificar http apenas para definição de cookies](#cookie-definition)</li><li>[Mitigar contra ataques de falsificação de pedidos de cross-site (CSRF) em páginas web ASP.NET](#csrf-asp)</li><li>[Preparar sessão para a vida útil da inatividade](#inactivity-lifetime)</li><li>[Implementar o logout adequado a partir da aplicação](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Mitigar contra ataques de falsificação de pedidos de cross-site (CSRF) em APIs web ASP.NET](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implemente o logout adequado utilizando métodos ADAL ao utilizar o Azure AD

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depender do token de acesso emitido pela Azure AD, o manipulador de eventos logout deve ligar |

### <a name="example"></a>Exemplo
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
Também deve destruir a sessão do utilizador chamando o método Session.Abandon(). O método seguinte mostra uma implementação segura do logout do utilizador:
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

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Use vidas finitas para tokens SaS gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os tokens SaS gerados para autenticação no Azure IoT Hub devem ter um período de validade finito. Mantenha as vidas de símbolo sas ao mínimo para limitar o tempo que podem ser reproduzidos no caso de as fichas estarem comprometidas.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Utilize as vidas mínimas simbólicas para tokens de recursos gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Reduza o tempo de token de recurso para um valor mínimo necessário. As fichas de recursos têm um tempo de tempo válido de 1 hora.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementar o logout adequado utilizando métodos de WsFederation ao utilizar o ADFS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depender do token STS emitido pela ADFS, o manipulador de eventos logout deve ligar para o método WSFederationAuthenticationModule.FederatedSignOut() para registar o utilizador. Também a sessão atual deve ser destruída, e o valor simbólico da sessão deve ser reposto e anulado.|

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

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implemente o logout adequado ao utilizar o Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Assinatura IdentitáriaServer3-Federada](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passos** | O IdentityServer apoia a capacidade de federação com fornecedores de identidade externos. Quando um utilizador assina fora de um fornecedor de identidade a montante, dependendo do protocolo utilizado, pode ser possível receber uma notificação quando o utilizador assinar. Permite que o IdentityServer notifique os seus clientes para que também possam assinar o utilizador. Consulte a documentação na secção de referências para obter os detalhes da implementação.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>As aplicações disponíveis em HTTPS devem utilizar cookies seguros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [httpCookies Element (ASP.NET Definições Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure Property](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passos** | Normalmente, os cookies só são acessíveis ao domínio para o qual foram examinados. Infelizmente, a definição de "domínio" não inclui o protocolo para que os cookies que são criados em HTTPS estejam acessíveis em HTTPS. O atributo "seguro" indica ao navegador que o cookie só deve ser disponibilizado através do HTTPS. Certifique-se de que todos os cookies definidos em HTTPS utilizem o atributo **secure.** A exigência pode ser aplicada no ficheiro web.config, definindo o atributo requereSSL à verdadeira. É a abordagem preferida porque irá impor o atributo **seguro** para todos os cookies atuais e futuros sem a necessidade de fazer quaisquer alterações adicionais de código.|

### <a name="example"></a>Exemplo
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A definição é aplicada mesmo que HTTP seja utilizado para aceder à aplicação. Se HTTP for utilizado para aceder à aplicação, a configuração quebra a aplicação porque os cookies são definidos com o atributo seguro e o navegador não os enviará de volta para a aplicação.

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | N/D  |
| **Passos** | Quando a aplicação web é a Parte De Gestão, e o IdP é servidor ADFS, o atributo seguro do token da FedAuth pode ser configurado por definição requerSSL a True na `system.identityModel.services` secção de web.config:|

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

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Toda a aplicação baseada em http deve especificar http apenas para definição de cookies

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Atributo de cookies seguros](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Passos** | Para mitigar o risco de divulgação de informação com um ataque de scripts cross-site (XSS), um novo atributo - httpOnly - foi introduzido nos cookies e é suportado por todos os principais navegadores. O atributo especifica que um cookie não é acessível através do script. Ao utilizar cookies HttpOnly, uma aplicação web reduz a possibilidade de que informações sensíveis contidas no cookie possam ser roubadas através do script e enviadas para o site de um intruso. |

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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [FormuláriosAufercation.requer propriedade de Sl](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passos** | O valor da propriedade RequireSSL é definido no ficheiro de configuração para uma aplicação ASP.NET utilizando o atributo requereSSL do elemento de configuração. Pode especificar no ficheiro Web.config para a sua aplicação ASP.NET se a Segurança da Camada de Transporte (TLS), anteriormente conhecida como SSL (Camada de Tomadas Seguras), é necessária para devolver o cookie de autenticação de formulários ao servidor, definindo o atributo requerendoSSL.|

### <a name="example"></a>Exemplo 
O exemplo de código a seguir define o atributo requereSSL no ficheiro Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [Configuração da Fundação da Identidade do Windows (WIF) – Parte II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Passos** | Para definir httpOn atribua para cookies FedAuth, o valor de atributo HideFromCsript deve ser definido para True. |

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

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Mitigar contra ataques de falsificação de pedidos de cross-site (CSRF) em páginas web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedidos de sites (CSRF ou XSRF) é um tipo de ataque no qual um intruso pode realizar ações no contexto de segurança de uma sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou eliminar conteúdo, se o site direcionado se basear exclusivamente em cookies de sessão para autenticar o pedido recebido. Um intruso poderia explorar esta vulnerabilidade obtendo um navegador de um utilizador diferente para carregar um URL com um comando a partir de um site vulnerável no qual o utilizador já está iniciado. Existem muitas formas de um intruso fazer isso, como por exemplo hospedar um site diferente que carrega um recurso a partir do servidor vulnerável, ou fazer com que o utilizador clique num link. O ataque pode ser evitado se o servidor enviar um sinal adicional ao cliente, requer que o cliente inclua esse token em todos os pedidos futuros, e verifique que todos os pedidos futuros incluem um token que se refere à sessão atual, como por exemplo, utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção XSRF/CSRF em ASP.NET MVC e Páginas Web](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Passos** | Formulários anti-CSRF e ASP.NET MVC - Utilize o `AntiForgeryToken` método de ajuda em Vistas; coloque um no `Html.AntiForgeryToken()` formulário, por exemplo,|

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
Ao mesmo tempo, html.AntiForgeryToken() dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor escondido aleatório acima indicado. Em seguida, para validar um post de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação alvo. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica:
* O pedido de entrada tem um cookie chamado __RequestVerificationToken
* O pedido de entrada tem uma `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` valores correspondem Assumindo que está tudo bem, o pedido passa normalmente. Mas se não, então uma falha de autorização com mensagem "Um sinal anti-falsificação exigido não foi fornecido ou foi inválido". 

### <a name="example"></a>Exemplo
Anti-CSRF e AJAX: O token do formulário pode ser um problema para os pedidos do AJAX, porque um pedido do AJAX pode enviar dados de JSON, não dados de formulário HTML. Uma solução é enviar os tokens num cabeçalho HTTP personalizado. O código seguinte utiliza a sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens a um pedido do AJAX. 
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
Quando processar o pedido, extraia as fichas do cabeçalho do pedido. Em seguida, ligue para o método AntiForgery.Validate para validar os tokens. O método Valide abre uma exceção se as fichas não forem válidas.
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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Aproveite ASP.NET funcionalidades incorporadas para evitar ataques web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Passos** | Os ataques CSRF em aplicações baseadas no WebForm podem ser atenuados definindo o ViewStateUserKey para uma cadeia aleatória que varia para cada utilizador - ID do utilizador ou, melhor ainda, ID de sessão. Por uma série de razões técnicas e sociais, o ID da sessão é muito melhor porque um ID de sessão é imprevisível, horários e varia por utilizador.|

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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passos** | O tempo limite de sessão representa o evento que ocorre quando um utilizador não realiza qualquer ação num web site durante um intervalo (definido pelo servidor web). O evento, do lado do servidor, altera o estado da sessão de utilizador para 'inválido' (por exemplo, "já não é utilizado") e instrui o servidor web a destruí-lo (eliminando todos os dados nele contidos). O exemplo de código a seguir define o atributo de sessão de tempo limite para 15 minutos no ficheiro Web.config.|

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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Formulários elemento para autenticação (ASP.NET Esquema de Definições)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passos** | Desa quando definir o tempo limite de cookies de autenticação de formulários para 15 minutos|

### <a name="example"></a>Exemplo
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Passos** | Quando a aplicação web é Relying Party e ADFS é o STS, o tempo de vida útil dos cookies de autenticação - fichas FedAuth - pode ser definido pela seguinte configuração em web.config:|

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
Também o ADFS emitiu a alegação de que o token deve ser definido para 15 minutos, executando o seguinte comando de powershell no servidor ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementar o logout adequado a partir da aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Execute a assinatura adequada da aplicação, quando o utilizador premir o botão 'iniciar sessão'. Após o logout, a aplicação deve destruir a sessão do utilizador e também redefinir e anular o valor dos cookies da sessão, juntamente com a reposição e anulação do valor dos cookies de autenticação. Além disso, quando várias sessões estão ligadas a uma única identidade de utilizador, devem ser encerradas coletivamente no lado do servidor no tempo limite ou logout. Por último, certifique-se de que a funcionalidade Logout está disponível em todas as páginas. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Mitigar contra ataques de falsificação de pedidos de cross-site (CSRF) em APIs web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedidos de sites (CSRF ou XSRF) é um tipo de ataque no qual um intruso pode realizar ações no contexto de segurança de uma sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou eliminar conteúdo, se o site direcionado se basear exclusivamente em cookies de sessão para autenticar o pedido recebido. Um intruso poderia explorar esta vulnerabilidade obtendo um navegador de um utilizador diferente para carregar um URL com um comando a partir de um site vulnerável no qual o utilizador já está iniciado. Existem muitas formas de um intruso fazer isso, como por exemplo hospedar um site diferente que carrega um recurso a partir do servidor vulnerável, ou fazer com que o utilizador clique num link. O ataque pode ser evitado se o servidor enviar um sinal adicional ao cliente, requer que o cliente inclua esse token em todos os pedidos futuros, e verifique que todos os pedidos futuros incluem um token que se refere à sessão atual, como por exemplo, utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção de ataques de falsificação de pedidos de transcomerções (CSRF) em ASP.NET API web](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Passos** | Anti-CSRF e AJAX: O token do formulário pode ser um problema para os pedidos do AJAX, porque um pedido do AJAX pode enviar dados de JSON, não dados de formulário HTML. Uma solução é enviar os tokens num cabeçalho HTTP personalizado. O código seguinte utiliza a sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens a um pedido do AJAX. |

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
Quando processar o pedido, extraia as fichas do cabeçalho do pedido. Em seguida, ligue para o método AntiForgery.Validate para validar os tokens. O método Valide abre uma exceção se as fichas não forem válidas.
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
Formulários anti-CSRF e MVC ASP.NET - Utilize o método de ajuda AntiForgeryToken em Vistas; colocar um Html.AntiForgeryToken() na forma, por exemplo,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima irá dar produção como o seguinte:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, html.AntiForgeryToken() dá ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor escondido aleatório acima indicado. Em seguida, para validar um post de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação alvo. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica:
* O pedido de entrada tem um cookie chamado __RequestVerificationToken
* O pedido de entrada tem uma `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` valores correspondem Assumindo que está tudo bem, o pedido passa normalmente. Mas se não, então uma falha de autorização com mensagem "Um sinal anti-falsificação exigido não foi fornecido ou foi inválido".

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Fornecedor de identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | [Garantir uma API web com contas individuais e login local em ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Passos** | Se a API Web for protegida usando o OAuth 2.0, então espera um sinal ao portador no cabeçalho do pedido de autorização e concede acesso ao pedido apenas se o token for válido. Ao contrário da autenticação baseada em cookies, os navegadores não anexam os tokens ao portador de pedidos. O cliente que solicita precisa de anexar explicitamente o sinal do portador no cabeçalho do pedido. Portanto, para ASP.NET APIs web protegidos usando OAuth 2.0, os tokens portadores são considerados como uma defesa contra os ataques de CSRF. Por favor, note que se a parte MVC da aplicação utiliza a autenticação de formulários (isto é, utiliza cookies), as fichas anti-falsificação têm de ser utilizadas pela aplicação web MVC. |

### <a name="example"></a>Exemplo
A API Web tem de ser informada para se basear apenas em fichas ao portador e não em cookies. Pode ser feito pela seguinte configuração em `WebApiConfig.Register` método:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

O método SupressEfaultHostAuthentication diz à Web API para ignorar qualquer autenticação que oace antes do pedido chegar ao pipeline Web API, seja por IIS ou por middleware OWIN. Dessa forma, podemos restringir a API web a autenticar apenas usando fichas portadoras.
