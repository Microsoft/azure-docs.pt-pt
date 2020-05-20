---
title: Gestão de configuração para a ferramenta de modelação de ameaças da Microsoft
titleSuffix: Azure
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
ms.openlocfilehash: 811feb26e492efeb505f43202bee484d3edfb8a5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658600"
---
# <a name="security-frame-configuration-management--mitigations"></a>Quadro de segurança: Gestão de Configuração / Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Implementar a Política de Segurança do Conteúdo (CSP) e desativar o javascript inline](#csp-js)</li><li>[Ativar o filtro XSS do navegador](#xss-filter)</li><li>[ASP.NET aplicações devem desativar o rastreio e a depuração antes da implantação](#trace-deploy)</li><li>[Aceda a javascripts de terceiros apenas a partir de fontes fidedignas](#js-trusted)</li><li>[Certifique-se de que as páginas ASP.NET autenticadas incorporam defesas ui redressing ou click-jacking](#ui-defenses)</li><li>[Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado em aplicações Web ASP.NET](#cors-aspnet)</li><li>[Ativar atributo ValidadoSolicitar em páginas ASP.NET](#validate-aspnet)</li><li>[Utilize versões mais recentes de bibliotecas JavaScript hospedadas localmente](#local-js)</li><li>[Desativar o cheiro automático de MIME](#mime-sniff)</li><li>[Remova os cabeçalhos padrão do servidor nos Web Sites do Windows Azure para evitar a recolha de impressões digitais](#standard-finger)</li></ul> |
| **Base de dados** | <ul><li>[Configure uma firewall do Windows para acesso a motor de base de dados](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado na ASP.NET Web API](#cors-api)</li><li>[Criptografe secções dos ficheiros de configuração da Web API que contenham dados sensíveis](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Certifique-se de que todas as interfaces de administração estão seguras com credenciais fortes](#admin-strong)</li><li>[Certifique-se de que o código desconhecido não pode ser executado em dispositivos](#unknown-exe)</li><li>[Criptografe OS e divisórias adicionais do Dispositivo IoT com bit-locker](#partition-iot)</li><li>[Certifique-se de que apenas os serviços/funcionalidades mínimos estão ativados nos dispositivos](#min-enable)</li></ul> |
| **Gateway de campo iot** | <ul><li>[Criptografe OS e divisórias adicionais de IoT Field Gateway com bit-locker](#field-bit-locker)</li><li>[Certifique-se de que as credenciais de login predefinidas do gateway de campo são alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway da nuvem iot** | <ul><li>[Certifique-se de que o Cloud Gateway implementa um processo para manter os firmware de dispositivos conectados atualizados](#cloud-firmware)</li></ul> |
| **Limite de confiança de máquina** | <ul><li>[Certifique-se de que os dispositivos têm controlos de segurança de ponta configurados de acordo com as políticas organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir a gestão segura das chaves de acesso ao armazenamento Azure](#secure-keys)</li><li>[Certifique-se de que só são permitidas origens fidedignas se o CORS estiver habilitado no armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Ativar a funcionalidade de estrangulamento de serviço da WCF](#throttling)</li><li>[Divulgação de informação sobre o WCF através de metadados](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementar a Política de Segurança do Conteúdo (CSP) e desativar o javascript inline

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | Uma Introdução à Política de [Segurança de Conteúdos,](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)Referência de Política de Segurança de [Conteúdo,](https://content-security-policy.com/) [Funcionalidades de Segurança,](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/) [Introdução à política de segurança de conteúdos,](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) [posso usar cSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Passos** | <p>A Política de Segurança de Conteúdo (CSP) é um mecanismo de segurança de defesa em profundidade, uma norma W3C, que permite aos proprietários de aplicações web ter controlo sobre o conteúdo incorporado no seu site. O CSP é adicionado como um cabeçalho de resposta HTTP no servidor web e é aplicado do lado do cliente pelos navegadores. É uma política baseada em whitelist - um site pode declarar um conjunto de domínios fidedignos a partir dos quais conteúdos ativos como javaScript podem ser carregados.</p><p>A CSP proporciona os seguintes benefícios de segurança:</p><ul><li>**Proteção contra xSS:** Se uma página for vulnerável ao XSS, um intruso pode explorá-la de duas maneiras:<ul><li>`<script>malicious code</script>`Injetar. Esta exploração não funcionará devido à Restrição Base-1 da CSP</li><li>`<script src="http://attacker.com/maliciousCode.js"/>`Injetar. Esta exploração não funcionará, uma vez que o domínio controlado pelo atacante não estará na lista branca de domínios da CSP.</li></ul></li><li>**Controlo sobre a exfiltração** de dados: Se algum conteúdo malicioso numa página web tentar ligar-se a um website externo e roubar dados, a ligação será abortada por CSP. Isto porque o domínio alvo não estará na lista branca do CSP</li><li>**Defesa contra click-jacking:** click-jacking é uma técnica de ataque que usa a qual um adversário pode enquadrar um site genuíno e forçar os utilizadores a clicar em elementos UI. Atualmente, a defesa contra o jacking de cliques é conseguida configurando um cabeçalho de resposta- X-Frame-Options. Nem todos os navegadores respeitam este cabeçalho e o CSP será uma forma padrão de se defender contra o click-jacking</li><li>**Relatório de ataque em tempo real:** Se houver um ataque de injeção num website ativado por CSP, os navegadores ativarão automaticamente uma notificação para um ponto final configurado no servidor web. Desta forma, a CSP serve como um sistema de alerta em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Política de exemplo: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Esta política permite que os scripts carreguem apenas a partir do servidor da aplicação web e do servidor de análise do Google. Os scripts carregados de qualquer outro site serão rejeitados. Quando o CSP está ativado num website, as seguintes funcionalidades são automaticamente desativadas para mitigar os ataques xSS. 

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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Filtro de proteção XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Passos** | <p>A configuração do cabeçalho de resposta x-XSS-Protection controla o filtro de script do site transversal do navegador. Este cabeçalho de resposta pode ter valores seguintes:</p><ul><li>`0:`Isto irá desativar o filtro</li><li>`1: Filter enabled`Se for detetado um ataque de scripts cross-site, para parar o ataque, o navegador irá sanitizar a página</li><li>`1: mode=block : Filter enabled`. Em vez de sanitizar a página, quando um ataque XSS é detetado, o navegador impedirá a renderização da página</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. O navegador vai sanitizar a página e denunciar a violação.</li></ul><p>Esta é uma função de Crómio que utiliza relatórios de violação de CSP para enviar detalhes para um URI à sua escolha. As últimas 2 opções são consideradas valores seguros.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET aplicações devem desativar o rastreio e a depuração antes da implantação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ASP.NET Visão Geral de Debugging](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.NET visão geral](https://msdn.microsoft.com/library/bb386420.aspx)do rastreio , Como: Ativar o rastreio para uma [aplicação ASP.NET](https://msdn.microsoft.com/library/0x5wc973.aspx), [Como: Ativar a Depuração para aplicações ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passos** | Quando o rastreio está ativado para a página, cada navegador que o solicite também obtém a informação de vestígios que contém dados sobre o estado interno do servidor e fluxo de trabalho. Essa informação pode ser sensível à segurança. Quando a depuração está ativada para a página, erros que ocorram no servidor resultam num conjunto completo de dados de vestígios de pilha apresentados ao navegador. Esses dados podem expor informações sensíveis à segurança sobre o fluxo de trabalho do servidor. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Aceda a javascripts de terceiros apenas a partir de fontes fidedignas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | os JavaScripts de terceiros devem ser referenciados apenas a partir de fontes fidedignas. Os pontos finais de referência devem estar sempre no TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Certifique-se de que as páginas ASP.NET autenticadas incorporam defesas ui redressing ou click-jacking

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [OWASP click-jacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Combate ao jacking de cliques com opções x-frame-options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Passos** | <p>click-jacking, também conhecido como um "ui reparação ataque", é quando um intruso usa várias camadas transparentes ou opacas para enganar um utilizador a clicar num botão ou link em outra página quando pretendia clicar na página de nível superior.</p><p>Esta camada é conseguida através da criação de uma página maliciosa com um iframe, que carrega a página da vítima. Assim, o intruso está a "sequestrar" cliques destinados à sua página e a encaminhar-os para outra página, provavelmente propriedade de outra aplicação, domínio ou ambos. Para evitar ataques de jacking de cliques, detete os cabeçalhos de resposta HTTP de X-Frame-Options adequados que instruam o navegador a não permitir o enquadramento de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-FRAME-OPTIONS pode ser definido através do IIS web.config. Código Web.config snippet para sites que nunca devem ser emoldurados: 
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
Código Web.config para sites que só devem ser emoldurados por páginas no mesmo domínio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado em aplicações Web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição é chamada de política de origem mesma, e impede um site malicioso de ler dados sensíveis de outro site. No entanto, por vezes, pode ser necessário expor as APIs de forma segura que outros sites podem consumir. Cross Origin Resource Sharing (CORS) é um padrão W3C que permite a um servidor relaxar a política de origem mesma. Utilizando o CORS, um servidor pode permitir explicitamente alguns pedidos de origem cruzada enquanto rejeita outros.</p><p>O CORS é mais seguro e flexível do que técnicas anteriores, como o JSONP. No seu cerne, permitir que o CORS se traduza na adição de alguns cabeçalhos de resposta HTTP (Access-Control-*) à aplicação web e isso pode ser feito de várias maneiras.</p>|

### <a name="example"></a>Exemplo
Se o acesso ao Web.config estiver disponível, então o CORS pode ser adicionado através do seguinte código: 
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
Se o acesso ao web.config não estiver disponível, então o CORS pode ser configurado adicionando o seguinte código CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Por favor, note que é fundamental garantir que a lista de origens no atributo "Access-Control-Allow-Origin" é definida para um conjunto finito e de origem fidedigna. Se não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada para a aplicação web >sem quaisquer restrições, tornando assim a aplicação vulnerável aos ataques do CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Ativar atributo ValidadoSolicitar em páginas ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de Pedidos - Prevenção de Ataques de Script](https://www.asp.net/whitepapers/request-validation) |
| **Passos** | <p>A validação do pedido, uma funcionalidade de ASP.NET desde a versão 1.1, impede o servidor de aceitar conteúdo que contenha HTML não codificado. Esta funcionalidade foi concebida para ajudar a prevenir alguns ataques de injeção de scripts, pelo que o código de script ou HTML do cliente pode ser submetido inconscientemente a um servidor, armazenado e depois apresentado a outros utilizadores. Recomendamos ainda que valide todos os dados de entrada e o HTML os consiga quando apropriado.</p><p>A validação do pedido é realizada comparando todos os dados de entrada com uma lista de valores potencialmente perigosos. Se ocorrer uma correspondência, ASP.NET levanta um `HttpRequestValidationException` . Por defeito, a funcionalidade de Validação de Pedidos está ativada.</p>|

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
Por favor, note que a funcionalidade de Validação de Pedidos não é suportada e não faz parte do gasoduto MVC6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Utilize versões mais recentes de bibliotecas JavaScript hospedadas localmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os desenvolvedores que usam bibliotecas javaScript padrão como jQuery devem usar versões aprovadas de bibliotecas javaScript comuns que não contenham falhas de segurança conhecidas. Uma boa prática é usar a versão mais recente das bibliotecas, uma vez que contêm correções de segurança para vulnerabilidades conhecidas nas suas versões mais antigas.</p><p>Se a versão mais recente não puder ser utilizada por razões de compatibilidade, devem ser utilizadas as versões mínimas abaixo.</p><p>Versões mínimas aceitáveis:</p><ul><li>**Rio JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validar 1.9</li><li>JQuery Mobile 1.0.1</li><li>Ciclo JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Kit de ferramentas de controlo do Ajax**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Formas Web e Ajax**<ul><li>ASP.NET Formulários Web e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregue qualquer biblioteca JavaScript de sites externos, tais como CDNs públicos</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Desativar o cheiro automático de MIME

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 Parte de Segurança V: Proteção Abrangente,](https://docs.microsoft.com/archive/blogs/ie/ie8-security-part-v-comprehensive-protection) [Tipo MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Passos** | O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que o seu conteúdo não deve ser farejado mime. Este cabeçalho foi concebido para mitigar os ataques mime-sniffing. Para cada página que possa conter conteúdo controlável do utilizador, deve utilizar o cabeçalho HTTP X-Content-Type-Options:nosniff. Para ativar o cabeçalho necessário globalmente para todas as páginas da aplicação, pode fazer uma das seguintes páginas|

### <a name="example"></a>Exemplo
Adicione o cabeçalho no ficheiro web.config se a aplicação for hospedada pelos Serviços de Informação da Internet (IIS) 7 em diante. 
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
Adicione o cabeçalho através do Pedido de Início de Aplicação Global \_ 
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
Só é possível ativar o cabeçalho necessário para páginas específicas, adicionando-o a respostas individuais: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Remova os cabeçalhos padrão do servidor nos Web Sites do Windows Azure para evitar a recolha de impressões digitais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | AmbienteType - Azure |
| **Referências**              | [Remover os cabeçalhos padrão do servidor nos Web Sites do Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Passos** | Cabeçalhos como Server, X-Powered-By, X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. Recomenda-se suprimir estes cabeçalhos, evitando assim a recolha de impressões digitais na aplicação |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Configure uma firewall do Windows para acesso a motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/A, Versão SQL - V12 |
| **Referências**              | Como configurar uma firewall de base de [dados Azure SQL,](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/) [configure uma firewall do Windows para acesso](https://msdn.microsoft.com/library/ms175043) a motor de base de dados |
| **Passos** | Os sistemas de firewall ajudam a prevenir o acesso não autorizado a recursos do computador. Para aceder a uma instância do Motor de Base de Dados do Servidor SQL através de uma firewall, tem de configurar a firewall no computador que executa o Servidor SQL para permitir o acesso |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Certifique-se de que só são permitidas origens fidedignas se o CORS estiver ativado na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC 5 |
| **Atributos**              | N/D  |
| **Referências**              | [Habilitar pedidos de origem cruzada em ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - Suporte CORS em ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Passos** | <p>A segurança do browser impede que uma página Web realize pedidos de AJAX para outro domínio. Esta restrição é chamada de política de origem mesma, e impede um site malicioso de ler dados sensíveis de outro site. No entanto, por vezes, pode ser necessário expor as APIs de forma segura que outros sites podem consumir. Cross Origin Resource Sharing (CORS) é um padrão W3C que permite a um servidor relaxar a política de origem mesma.</p><p>Utilizando o CORS, um servidor pode permitir explicitamente alguns pedidos de origem cruzada enquanto rejeita outros. O CORS é mais seguro e flexível do que técnicas anteriores, como o JSONP.</p>|

### <a name="example"></a>Exemplo
No método App_Start/WebApiConfig.cs, adicione o seguinte código ao método WebApiConfig.Register 
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
O atributo enableCors pode ser aplicado a métodos de ação num controlador da seguinte forma: 

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

Por favor, note que é fundamental garantir que a lista de origens no atributo EnableCors é definida para um conjunto finito e de origem fidedigna. Se não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada para a API sem quaisquer restrições, >tornando assim a API vulnerável aos ataques do CSRF. Os EnableCors podem ser decorados ao nível do controlador. 

### <a name="example"></a>Exemplo
Para desativar o CORS num determinado método numa classe, o atributo de Deficientes pode ser utilizado como mostrado abaixo: 
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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Habilitar pedidos de origem cruzada (CORS) em ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Passos** | <p>Em ASP.NET Core 1.0, o CORS pode ser ativado utilizando middleware ou utilizando MVC. Ao utilizar o MVC para ativar o CORS são utilizados os mesmos serviços CORS, mas o meio-termo CORS não.</p>|

**Abordagem-1** Habilitar o CORS com o middleware: Para permitir o CORS para toda a aplicação, adicione o middleware CORS ao gasoduto de pedido utilizando o método de extensão UseCors. Uma política de cross-origin pode ser especificada ao adicionar o middleware CORS utilizando a classe CorsPolicyBuilder. Há duas maneiras de fazer isto:

### <a name="example"></a>Exemplo
A primeira é chamar UseCors com uma lambda. A lambda leva um objeto CorsPolicyBuilder: 
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
A segunda é definir uma ou mais políticas cors nomeadas e, em seguida, selecionar a política pelo nome no tempo de execução. 
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

**Abordagem-2** Habilitar o CORS em MVC: Os desenvolvedores podem, em alternativa, utilizar o MVC para aplicar cors específicos por ação, por controlador ou globalmente para todos os controladores.

### <a name="example"></a>Exemplo
Por ação: Para especificar uma política cors para uma ação específica, adicione o atributo [EnableCors] à ação. Especifique o nome da apólice. 
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
Por favor, note que é fundamental garantir que a lista de origens no atributo EnableCors é definida para um conjunto finito e de origem fidedigna. Se não configurar isto de forma inadequada (por exemplo, definir o valor como '*') permitirá que sites maliciosos desencadeiem pedidos de origem cruzada para a API sem quaisquer restrições, >tornando assim a API vulnerável aos ataques do CSRF. 

### <a name="example"></a>Exemplo
Para desativar o CORS para um controlador ou ação, utilize o atributo [Desativação]. 
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
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Encriptar secções de configuração em ASP.NET 2.0 Utilizando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), especificar um fornecedor de [configuração protegido,](https://msdn.microsoft.com/library/68ze1hb2.aspx)utilizando o cofre de [chaves Azure para proteger os segredos](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) da aplicação |
| **Passos** | Ficheiros de configuração como o Web.config, appsettings.json são frequentemente usados para conter informações sensíveis, incluindo nomes de utilizadores, palavras-passe, cadeias de ligação de base de dados e chaves de encriptação. Caso não proteja esta informação, a sua aplicação é vulnerável a atacantes ou utilizadores maliciosos que obtenham informações confidenciais, tais como nomes de utilizadores de contas e palavras-passe, nomes de bases de dados e nomes de servidores. Com base no tipo de implementação (azure/on-prem), criptografe as secções sensíveis dos ficheiros config utilizando DPAPI ou serviços como o Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Certifique-se de que todas as interfaces de administração estão seguras com credenciais fortes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Quaisquer interfaces administrativas que o dispositivo ou porta de entrada de campo exponha devem ser protegidas com credenciais fortes. Além disso, quaisquer outras interfaces expostas como WiFi, SSH, partilhas de ficheiros, FTP devem ser protegidas com credenciais fortes. Não devem ser utilizadas senhas fracas por defeito. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Certifique-se de que o código desconhecido não pode ser executado em dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Ativar encriptação segura de boot e dispositivo de bit-locker no Núcleo IoT do Windows 10](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Passos** | A UEFI Secure Boot limita o sistema apenas para permitir a execução de binários assinados por uma autoridade especificada. Esta funcionalidade impede que o código desconhecido seja executado na plataforma e potencialmente enfraquecendo a postura de segurança da mesmas. Ative a UeFI Secure Boot e restringe a lista de autoridades de certificados que são confiáveis para assinar código. Assine todo o código que seja implantado no dispositivo utilizando uma das autoridades de confiança. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Criptografe OS e divisórias adicionais do Dispositivo IoT com bit-locker

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da encriptação do dispositivo bit-locker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo préos necessário na UEFI que realiza as medidas necessárias. Estas medições pré-OS garantem que o SO mais tarde tem um registo definitivo de como o Sistema operativo foi lançado. Criptografe as divisórias de OS utilizando o bit-locker e quaisquer divisórias adicionais também no caso de armazenarem quaisquer dados sensíveis. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Certifique-se de que apenas os serviços/funcionalidades mínimos estão ativados nos dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Não ative nem desligue quaisquer funcionalidades ou serviços no Sistema Operativo que não sejam necessários para o funcionamento da solução. Para, por exemplo, se o dispositivo não necessitar de um UI para ser implantado, instale o Windows IoT Core no modo sem cabeça. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Criptografe OS e divisórias adicionais de IoT Field Gateway com bit-locker

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Windows 10 IoT Core implementa uma versão leve da encriptação do dispositivo bit-locker, que tem uma forte dependência da presença de um TPM na plataforma, incluindo o protocolo préos necessário na UEFI que realiza as medidas necessárias. Estas medições pré-OS garantem que o SO mais tarde tem um registo definitivo de como o Sistema operativo foi lançado. Criptografe as divisórias de OS utilizando o bit-locker e quaisquer divisórias adicionais também no caso de armazenarem quaisquer dados sensíveis. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Certifique-se de que as credenciais de login predefinidas do gateway de campo são alteradas durante a instalação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que as credenciais de login predefinidas do gateway de campo são alteradas durante a instalação |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Certifique-se de que o Cloud Gateway implementa um processo para manter os firmware de dispositivos conectados atualizados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Visão geral de gestão de dispositivos Do IoT Hub,](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/) [Como atualizar firmware de dispositivos](../../iot-hub/tutorial-firmware-update.md) |
| **Passos** | LWM2M é um protocolo da Open Mobile Alliance for IoT Device Management. A gestão do dispositivo Azure IoT permite interagir com dispositivos físicos utilizando trabalhos de dispositivos. Certifique-se de que o Cloud Gateway implementa um processo para manter rotineiramente o dispositivo e outros dados de configuração atualizados utilizando a Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Certifique-se de que os dispositivos têm controlos de segurança de ponta configurados de acordo com as políticas organizacionais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os dispositivos têm controlos de segurança de ponto final, tais como bit-locker para encriptação de nível de disco, antivírus com assinaturas atualizadas, firewall baseado em hospedeiros, upgrades de OS, políticas de grupo, etc. estão configurados de acordo com as políticas de segurança organizacional. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Garantir a gestão segura das chaves de acesso ao armazenamento Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Guia de segurança de armazenamento azure - gestão das chaves da sua conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Passos** | <p>Armazenamento de chaves: É aconselhável armazenar as chaves de acesso ao Armazenamento Azure no Cofre de Chaves Azure como um segredo e fazer com que as aplicações recuperem a chave do cofre chave. Isto é recomendado devido às seguintes razões:</p><ul><li>A aplicação nunca terá a chave de armazenamento codificada num ficheiro de configuração, que remove aquela avenida de alguém que tem acesso às teclas sem permissão específica.</li><li>O acesso às teclas pode ser controlado através do Diretório Ativo Azure. Isto significa que um proprietário de conta pode dar acesso a um punhado de aplicações que precisam de recuperar as chaves do Cofre chave Azure. Outras aplicações não poderão aceder às chaves sem lhes conceder permissão especificamente</li><li>Regeneração chave: Recomenda-se que tenha em curso um processo para regenerar as chaves de acesso ao armazenamento Azure por razões de segurança. Os detalhes sobre o porquê e como planear a regeneração da chave estão documentados no artigo de referência do Guia de Segurança de Armazenamento do Azure</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Certifique-se de que só são permitidas origens fidedignas se o CORS estiver habilitado no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte CORS para os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Passos** | O Armazenamento Azure permite-lhe ativar a partilha de recursos cors – Cross Origin. Para cada conta de armazenamento, pode especificar domínios que podem aceder aos recursos dessa conta de armazenamento. Por padrão, o CORS é desativado em todos os serviços. Pode ativar o CORS utilizando a API REST ou a biblioteca de clientes de armazenamento para chamar um dos métodos para definir as políticas de serviço. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Ativar a funcionalidade de estrangulamento de serviço da WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Passos** | <p>Não impor um limite à utilização dos recursos do sistema poderia resultar na exaustão dos recursos e, em última análise, na negação do serviço.</p><ul><li>**EXPLICAÇÃO:** A Windows Communication Foundation (WCF) oferece a capacidade de acelerar os pedidos de serviço. Permitir demasiados pedidos de clientes pode inundar um sistema e esgotar os seus recursos. Por outro lado, permitir apenas um pequeno número de pedidos a um serviço pode impedir que utilizadores legítimos utilizem o serviço. Cada serviço deve ser individualmente sintonizado e configurado para permitir a quantidade adequada de recursos.</li><li>**RECOMENDAÇÕES** Ative a funcionalidade de estrangulamento de serviço da WCF e estabeleça limites adequados à sua aplicação.</li></ul>|

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

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>Divulgação de informação sobre o WCF através de metadados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Passos** | Os metadados podem ajudar os atacantes a aprender sobre o sistema e planear uma forma de ataque. Os serviços wCF podem ser configurados para expor metadados. Os metadados dão informações detalhadas sobre a descrição do serviço e não devem ser transmitidos em ambientes de produção. As `HttpGetEnabled`  /  `HttpsGetEnabled` propriedades da classe ServiceMetaData definem se um serviço irá expor os metadados | 

### <a name="example"></a>Exemplo
O código abaixo instrui a WCF a transmitir metadados de um serviço
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não difunda metadados de serviço num ambiente de produção. Desative as propriedades httpGetEnabled / HttpsGetEnabled da classe ServiceMetaData para falsas. 

### <a name="example"></a>Exemplo
O código abaixo instrui a WCF a não transmitir os metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
