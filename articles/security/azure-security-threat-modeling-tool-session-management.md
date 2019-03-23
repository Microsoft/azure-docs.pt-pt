---
title: Sessão gestão - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: atenuações para ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: e8f3cf3889b3f79e930630ff0e768a0c4875eec6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361732"
---
# <a name="security-frame-session-management"></a>Quadro de segurança: Gerenciamento de sessões
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementar a fim de sessão adequado usando métodos da ADAL, quando utilizar o Azure AD](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Utilizar os tempos de vida finitos para tokens de SaS gerados](#finite-tokens)</li></ul> |
| **DB de documentos do Azure** | <ul><li>[Utilize o mínimo durações de token para tokens de recurso gerados](#resource-tokens)</li></ul> |
| **AD FS** | <ul><li>[Implementar a fim de sessão adequada através de métodos de WsFederation ao utilizar o ADFS](#wsfederation-logout)</li></ul> |
| **Servidor de identidades** | <ul><li>[Implementar a fim de sessão adequada ao utilizar o servidor de identidades](#proper-logout)</li></ul> |
| **Aplicação Web** | <ul><li>[Aplicações disponíveis através de HTTPS tem de utilizar cookies seguros](#https-secure-cookies)</li><li>[Todas as aplicações de http com base devem especificar http apenas para a definição de cookie](#cookie-definition)</li><li>[Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) em páginas da web ASP.NET](#csrf-asp)</li><li>[Configurar a sessão para a duração de inatividade](#inactivity-lifetime)</li><li>[Implementar a fim de sessão correto da aplicação](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) com as APIs de Web do ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementar a fim de sessão adequado usando métodos da ADAL, quando utilizar o Azure AD

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Se o aplicativo conta com o token de acesso emitido pelo Azure AD, o manipulador de eventos de fim de sessão deve chamar |

### <a name="example"></a>Exemplo
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
Ele também deve destruir o sessão do utilizador chamando o método Session.Abandon(). Método a seguir mostra a implementação segura de fim de sessão do utilizador:
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

## <a id="finite-tokens"></a>Utilizar os tempos de vida finitos para tokens de SaS gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Tokens de SaS gerados para autenticar no IoT Hub do Azure devem ter um período de expiração finita. Mantenha as durações de token de SaS para um mínimo para limitar a quantidade de tempo que pode ser reproduzidos no caso dos tokens sejam comprometidos.|

## <a id="resource-tokens"></a>Utilize o mínimo durações de token para tokens de recurso gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | DB de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Reduza o período de tempo do token de recursos para um valor mínimo necessário. Tokens de recursos tem um intervalo de tempo válido do padrão de 1 hora.|

## <a id="wsfederation-logout"></a>Implementar a fim de sessão adequada através de métodos de WsFederation ao utilizar o ADFS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Se a aplicação depende de token de STS emitido pelo ADFS, o manipulador de eventos de fim de sessão deve chamar o método de WSFederationAuthenticationModule.FederatedSignOut() para terminar o utilizador. Também deve ser destruída a sessão atual e o valor de token de sessão deve ser reposto e nullified.|

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

## <a id="proper-logout"></a>Implementar a fim de sessão adequada ao utilizar o servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Fim de sessão Federado de IdentityServer3](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passos** | IdentityServer suporta a capacidade para federar com fornecedores de identidade externo. Quando um utilizador inicia sessão fora de um fornecedor de identidade a montante, consoante o protocolo utilizado, ele poderá ser possível receber uma notificação quando o utilizador termina. Ele permite que IdentityServer notificar os clientes para que eles podem também terminar sessão do utilizador. Verifique a documentação na seção referências para os detalhes da implementação.|

## <a id="https-secure-cookies"></a>Aplicações disponíveis através de HTTPS tem de utilizar cookies seguros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [o elemento (Nastavení Technologie ASP.NET) de httpCookies](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure propriedade](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passos** | Os cookies são normalmente apenas acessíveis para o domínio para as quais eles foram o escopo. Infelizmente, a definição de "domínio" não inclui o protocolo para que os cookies que são criados através de HTTPS são acessíveis através de HTTP. O atributo "seguro", indica ao navegador, que o cookie deve apenas ser disponibilizado através de HTTPS. Certifique-se de que todos os cookies definida sobre a utilização HTTPS a **seguro** atributo. O requisito pode ser imposto no arquivo Web. config, definindo o atributo requireSSL como true. É a melhor abordagem porque ele aplicará os **seguro** atributo para todos os cookies atuais e futuros sem a necessidade de fazer quaisquer alterações de código adicionais.|

### <a name="example"></a>Exemplo
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A definição é imposta, mesmo que o HTTP é utilizado para aceder à aplicação. Se o HTTP é utilizado para aceder à aplicação, a definição divide o aplicativo, porque os cookies são definidos com o atributo seguro e o navegador não envia-os para a aplicação.

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | N/A  |
| **Passos** | Quando a aplicação web é a terceira parte confiável e o IdP é o servidor do ADFS, atributo de seguro do token FedAuth pode ser configurado através da definição requireSSL como True no `system.identityModel.services` secção da Web. config:|

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

## <a id="cookie-definition"></a>Todas as aplicações de http com base devem especificar http apenas para a definição de cookie

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Atributos de Cookie seguro](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Passos** | Para mitigar o risco de divulgação de informações com um ataque de criação de scripts entre sites (XSS), um novo atributo - httpOnly - foi introduzido ao cookies e é suportado por todos os principais navegadores. O atributo Especifica que um cookie não é acessível por meio de script. Ao utilizar os cookies HttpOnly, um aplicativo web reduz a possibilidade de informações confidenciais contidas no cookie podem ser roubadas por meio de script e enviadas para o site de um atacante. |

### <a name="example"></a>Exemplo
Todos os aplicativos baseados em HTTP que utilize cookies devem especificar HttpOnly na definição do cookie, com a implementação após a configuração no Web. config:
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
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade de FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passos** | O valor da propriedade RequireSSL está definido no ficheiro de configuração para uma aplicação ASP.NET, utilizando o atributo requireSSL do elemento de configuração. Pode especificar no arquivo Web. config para a sua aplicação ASP.NET se o SSL (Secure Sockets Layer) é necessária para retornar o cookie de autenticação de formulários para o servidor ao definir o atributo requireSSL.|

### <a name="example"></a>Exemplo 
O exemplo de código seguinte define o atributo de requireSSL no arquivo Web. config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [Configuração do Windows Identity Foundation (WIF) – parte II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Passos** | Para definir o atributo de httpOnly para FedAuth cookies, o valor do atributo hideFromCsript deve ser definido como True. |

### <a name="example"></a>Exemplo
Configuração seguinte mostra a configuração correta:
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

## <a id="csrf-asp"></a>Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) em páginas da web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Falsificação de solicitação (CSRF ou XSRF) é um tipo de ataque no qual um invasor pode executar ações no contexto de segurança de sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou eliminar o conteúdo, se o web site de destino depende exclusivamente de cookies de sessão para autenticar recebeu o pedido. Um invasor poderia explorar essa vulnerabilidade, obtendo o navegador de um utilizador diferente ao carregar um URL com um comando a partir de um site vulnerável em que o usuário já está conectado no. Existem várias formas de um atacante fazer isso, como, por que aloja um web site diferentes, que carrega um recurso do servidor vulnerável ou obter o utilizador clica numa hiperligação. O ataque pode ser impedido de se o servidor envia um token adicional para o cliente, exige que o cliente incluir esse token em todas as solicitações futuras e verifica que todas as solicitações futuras incluem um token que diz respeito à sessão atual, tal como através da utilização do ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Prevenção de XSRF/CSRF no ASP.NET MVC e páginas da Web](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Passos** | Anti-CSRF e formulários do ASP.NET MVC - utilizar o `AntiForgeryToken` método auxiliar nos modos de exibição; coloque um `Html.AntiForgeryToken()` no formulário, por exemplo,|

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
Ao mesmo tempo, Html.AntiForgeryToken() dá o visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro de [ValidateAntiForgeryToken] para o método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* O pedido recebido tem um cookie chamado __RequestVerificationToken
* O pedido recebido tem um `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` hodnoty se shodují supondo que todas as é bem, a solicitação passa por normalmente. Mas, se não for, em seguida, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido". 

### <a name="example"></a>Exemplo
Anti-CSRF e AJAX: O token de formulário pode ser um problema para pedidos de AJAX, uma vez que uma solicitação AJAX pode enviar dados JSON, não dados de formulário HTML. Uma solução é enviar os tokens num cabeçalho HTTP personalizado. O código a seguir usa a sintaxe do Razor para gerar os tokens e, em seguida, adiciona os tokens para uma solicitação AJAX. 
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
Ao processar o pedido, extrair os tokens do cabeçalho do pedido. Em seguida, chame o método de AntiForgery.Validate para validar os tokens. O método Validate gerará uma exceção se os tokens não são válidos.
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
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Tirar partido das funcionalidades incorporadas de ASP.NET para evitar ataques Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Passos** | Ataques CSRF em aplicativos de WebForm com base podem ser mitigados ao definir ViewStateUserKey como uma cadeia de caracteres aleatória que varia para cada utilizador - ID de utilizador ou, melhor ainda, ID de sessão. Por diversos motivos técnicos e sociais, o ID de sessão é uma opção bem melhor porque uma sessão do ID é imprevisível, exceder o tempo limite e varia numa base por utilizador.|

### <a name="example"></a>Exemplo
Eis o código que tem de ter em todas as páginas:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Configurar a sessão para a duração de inatividade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade de HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passos** | Tempo limite da sessão representa os eventos que ocorrem quando um utilizador não executa qualquer ação num site durante um intervalo (definido pelo servidor web). O evento, no lado do servidor, altere o estado da sessão do usuário para "inválido" (por exemplo "não utilizado mais") e instruir o servidor web para destruí-lo (a eliminar todos os dados contidos nele). O exemplo de código seguinte define o atributo de sessão de tempo limite para 15 minutos no arquivo Web. config.|

### <a name="example"></a>Exemplo
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Ativar a deteção de ameaças em SQL do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms |
| **Atributos**              | N/A  |
| **Referências**              | [Elemento de formulários para a autenticação (Nastavení Technologie ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passos** | Definir o tempo de limite do cookie de Tíquete de autenticação de formulários para 15 minutos|

### <a name="example"></a>Exemplo
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Passos** | Quando a aplicação web é a terceira parte confiável e AD FS é o STS, o tempo de vida dos cookies de autenticação - FedAuth tokens - pode ser definido em Web. config, a seguinte configuração:|

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
Também de afirmações do ADFS emitidos SAML duração do token deve ser definida como 15 minutos, executando o seguinte comando do powershell no servidor do ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementar a fim de sessão correto da aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Execute adequada terminar sessão da aplicação, quando o usuário pressiona termine de botão. Após a fim de sessão, aplicativo deve destruir a sessão do utilizador e também repor e anula o valor do cookie de sessão, juntamente com a reposição e nullifying o valor do cookie de autenticação. Além disso, quando várias sessões estão associadas a uma identidade de utilizador único, eles tem de ser coletivamente terminados no lado do servidor no tempo limite ou de fim de sessão. Por último, certifique-se de que a funcionalidade de fim de sessão está disponível em cada página. |

## <a id="csrf-api"></a>Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) com as APIs de Web do ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Falsificação de solicitação (CSRF ou XSRF) é um tipo de ataque no qual um invasor pode executar ações no contexto de segurança de sessão estabelecida de um utilizador diferente num site. O objetivo é modificar ou eliminar o conteúdo, se o web site de destino depende exclusivamente de cookies de sessão para autenticar recebeu o pedido. Um invasor poderia explorar essa vulnerabilidade, obtendo o navegador de um utilizador diferente ao carregar um URL com um comando a partir de um site vulnerável em que o usuário já está conectado no. Existem várias formas de um atacante fazer isso, como, por que aloja um web site diferentes, que carrega um recurso do servidor vulnerável ou obter o utilizador clica numa hiperligação. O ataque pode ser impedido de se o servidor envia um token adicional para o cliente, exige que o cliente incluir esse token em todas as solicitações futuras e verifica que todas as solicitações futuras incluem um token que diz respeito à sessão atual, tal como através da utilização do ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Impedir a solicitação intersite Forjada (CSRF) ataques na ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Passos** | Anti-CSRF e AJAX: O token de formulário pode ser um problema para pedidos de AJAX, uma vez que uma solicitação AJAX pode enviar dados JSON, não dados de formulário HTML. Uma solução é enviar os tokens num cabeçalho HTTP personalizado. O código a seguir usa a sintaxe do Razor para gerar os tokens e, em seguida, adiciona os tokens para uma solicitação AJAX. |

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
Ao processar o pedido, extrair os tokens do cabeçalho do pedido. Em seguida, chame o método de AntiForgery.Validate para validar os tokens. O método Validate gerará uma exceção se os tokens não são válidos.
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
Anti-CSRF e formulários do ASP.NET MVC - usar o método de programa auxiliar de AntiForgeryToken nos modos de exibição; colocar um Html.AntiForgeryToken() no formulário, por exemplo,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima serão de saída semelhante ao seguinte:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, Html.AntiForgeryToken() dá o visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro de [ValidateAntiForgeryToken] para o método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* O pedido recebido tem um cookie chamado __RequestVerificationToken
* O pedido recebido tem um `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` hodnoty se shodují supondo que todas as é bem, a solicitação passa por normalmente. Mas, se não for, em seguida, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido".

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Fornecedor de identidade de fornecedor - ADFS, de identidade - Azure AD |
| **Referências**              | [Proteger uma API Web com contas individuais e início de sessão Local no ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Passos** | Se a API Web é protegida com o OAuth 2.0, em seguida, ele espera um token de portador no cabeçalho do pedido de autorização e concede acesso a pedido, apenas se o token é válido. Ao contrário da autenticação baseada em cookies, navegadores não anexe os tokens de portador a pedidos. O cliente solicitador tem de anexar explicitamente o token de portador no cabeçalho do pedido. Por conseguinte, para APIs da Web ASP.NET protegida com o OAuth 2.0, os tokens de portador são considerados como uma defesa contra ataques CSRF. Tenha em atenção que, se a parte MVC da aplicação utiliza (ou seja, utiliza cookies) de autenticação de formulários, tokens antifalsificação precisa de ser utilizado pela aplicação web MVC. |

### <a name="example"></a>Exemplo
A API Web tem de ser informados sobre a confiar apenas em tokens de portador e não em cookies. Ele pode ser feito a seguinte configuração no `WebApiConfig.Register` método:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

O método SuppressDefaultHostAuthentication informa à API de Web para ignorar qualquer autenticação, que ocorre antes do pedido atinge o pipeline da Web API, tanto pelo IIS por middleware da OWIN. Dessa forma, a Microsoft pode limitar API Web para autenticar apenas com os tokens de portador.
