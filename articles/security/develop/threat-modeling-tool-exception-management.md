---
title: Gestão de Exceções - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68728080"
---
# <a name="security-frame-exception-management--mitigations"></a>Quadro de Segurança: Gestão de Exceções [ Gestão de Exceções] Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Não inclua o serviçoNódebug no ficheiro de configuração](#servicedebug)</li><li>[WCF- Não inclua o nó de metadados de serviço no ficheiro de configuração](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que o manuseamento adequado da exceção é feito em ASP.NET Web API](#exception)</li></ul> |
| **Aplicação Web** | <ul><li>[Não exponha detalhes de segurança em mensagens de erro](#messages)</li><li>[Implementar página de manipulação de erros predefinido](#default)</li><li>[Definir método de implantação para o retalho no IIS](#deployment)</li><li>[Exceções devem falhar com segurança](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Não inclua o serviçoNódebug no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Passos** | Os serviços do Windows Communication Framework (WCF) podem ser configurados para expor informações de depuração. As informações sobre depuração não devem ser utilizadas em ambientes de produção. A `<serviceDebug>` etiqueta define se a funcionalidade de informação de depuração está ativada para um serviço WCF. Se o atributo incluir ExceptionDetailInFaults está definido como verdadeiro, as informações de exceção da aplicação serão devolvidas aos clientes. Os atacantes podem aproveitar as informações adicionais que obtêm através da depuração da produção para montar ataques direcionados para o quadro, base de dados ou outros recursos utilizados pela aplicação. |

### <a name="example"></a>Exemplo
O seguinte ficheiro `<serviceDebug>` de configuração inclui a etiqueta: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desative a informação de depuração no serviço. Isto pode ser realizado `<serviceDebug>` removendo a etiqueta do ficheiro de configuração da sua aplicação. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Não inclua o nó de metadados de serviço no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Quadro 3 genérico e LÍQUIDO |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Passos** | Expor publicamente informações sobre um serviço pode fornecer aos atacantes uma visão valiosa de como podem explorar o serviço. A `<serviceMetadata>` etiqueta permite a funcionalidade de publicação de metadados. Os metadados de serviço podem conter informações sensíveis que não devem ser acessíveis ao público. No mínimo, apenas permite que os utilizadores de confiança acedam aos metadados e garantam que informações desnecessárias não são expostas. Melhor ainda, desativar totalmente a capacidade de publicar metadados. Uma configuração WCF segura `<serviceMetadata>` não conterá a etiqueta. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Certifique-se de que o manuseamento adequado da exceção é feito em ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Tratamento de exceções em ASP.NET Web API,](https://www.asp.net/web-api/overview/error-handling/exception-handling)Validação de [Modelos em ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Por padrão, a maioria das exceções não apanhadas em ASP.NET Web API são traduzidas numa resposta HTTP com código de estado`500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de estado `HttpResponseException` devolvido pela API, pode ser utilizado como indicado a seguir: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Exemplo
Para um maior controlo sobre `HttpResponseMessage` a resposta à exceção, a classe pode ser utilizada como indicado a seguir: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Para capturar exceções não tratadas que `HttpResponseException`não sejam do tipo, podem ser utilizados filtros de exceção. Os filtros `System.Web.Http.Filters.IExceptionFilter` de exceção implementam a interface. A forma mais simples de escrever um `System.Web.Http.Filters.ExceptionFilterAttribute` filtro de exceção é derivar da classe e anular o método OnException. 

### <a name="example"></a>Exemplo
Aqui está um filtro `NotImplementedException` que converte `501, Not Implemented`exceções em código de estado HTTP: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Existem várias formas de registar um filtro de exceção Web API:
- Por ação
- Por controlador
- Globalmente

### <a name="example"></a>Exemplo
Para aplicar o filtro a uma ação específica, adicione o filtro como atributo à ação: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Exemplo
Para aplicar o filtro a todas `controller`as ações em a, `controller` adicione o filtro como um atributo à classe: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro globalmente a todos os controladores Web `GlobalConfiguration.Configuration.Filters` API, adicione uma instância do filtro à recolha. Os filtros de exceção nesta coleção aplicam-se a qualquer ação do controlador Web API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para validação de modelos, o estado do modelo pode ser passado para o método CreateErrorResponse, como mostrado abaixo: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Consulte os links na secção de referências para obter detalhes adicionais sobre manipulação excecional e validação de modelos em ASP.NET API Web 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Não exponha detalhes de segurança em mensagens de erro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>As mensagens de erro genéricas são fornecidas diretamente ao utilizador sem incluir dados sensíveis da aplicação. Exemplos de dados sensíveis incluem:</p><ul><li>Nomes do servidor</li><li>Cadeias de ligação</li><li>Nomes de utilizador</li><li>Palavras-passe</li><li>Procedimentos SQL</li><li>Detalhes das falhas dinâmicas do SQL</li><li>Empilhar vestígios e linhas de código</li><li>Variáveis armazenadas na memória</li><li>Localizações de unidade e pasta</li><li>Pontos de instalação de aplicação</li><li>Configurações de configuração do anfitrião</li><li>Outros detalhes internos da aplicação</li></ul><p>Capturar todos os erros dentro de uma aplicação e fornecer mensagens genéricas de erro, bem como permitir erros personalizados dentro do IIS ajudará a impedir a divulgação de informação. A base de dados do SQL Server e o manuseamento de .NET Exception, entre outras arquiteturas de manipulação de erros, são especialmente verbosos e extremamente úteis para um utilizador malicioso que perfis da sua aplicação. Não exiba diretamente o conteúdo de uma classe derivada da classe .NET Exception e certifique-se de que tem um manuseamento de exceção adequado para que uma exceção inesperada não seja inadvertidamente levantada diretamente ao utilizador.</p><ul><li>Fornecer mensagens genéricas de erro diretamente ao utilizador que abstratem detalhes específicos encontrados diretamente na mensagem de exceção/erro</li><li>Não exiba o conteúdo de uma classe de exceção .NET diretamente ao utilizador</li><li>Prenda todas as mensagens de erro e, se for caso disso, informe o utilizador através de uma mensagem de erro genérica enviada ao cliente da aplicação</li><li>Não exponha o conteúdo da classe Exception diretamente ao `.ToString()`utilizador, especialmente o valor de devolução das propriedades de Message ou StackTrace. Inicie sessão segura desta informação e exiba uma mensagem mais inócua ao utilizador</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementar página de manipulação de erros predefinido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Caixa de Diálogo Editar Definições de Páginas de Erro .NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Passos** | <p>Quando uma aplicação ASP.NET falha e causa um Erro de Servidor Interno HTTP/1.x 500, ou uma configuração de funcionalidade (como a Filtragem de Pedidos) impede que uma página seja exibida, gera-se uma mensagem de erro. Os administradores podem escolher se a aplicação deve ou não apresentar uma mensagem amigável ao cliente, uma mensagem de erro detalhada ao cliente ou uma mensagem de erro detalhada apenas para o local. A `<customErrors>` etiqueta no web.config tem três modos:</p><ul><li>**Em:** Especifica que erros personalizados estão ativados. Se não for especificado o atributo predefinidoRedirect, os utilizadores vêem um erro genérico. Os erros personalizados são mostrados aos clientes remotos e ao anfitrião local</li><li>**Fora:** Especifica que erros personalizados são desativados. Os erros detalhados ASP.NET são mostrados aos clientes remotos e ao anfitrião local</li><li>**RemoteOnly:** Especifica que erros personalizados são mostrados apenas aos clientes remotos, e que ASP.NET erros são mostrados ao anfitrião local. Este é o valor padrão</li></ul><p>Abra `web.config` o ficheiro para a aplicação/site `<customErrors mode="RemoteOnly" />` e `<customErrors mode="On" />` certifique-se de que a etiqueta está definida ou definida.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Definir método de implantação para o retalho no IIS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Elemento de implementação (ASP.NET Definições Schema)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Passos** | <p>O `<deployment retail>` interruptor destina-se a ser utilizado por servidores IIS de produção. Este interruptor é utilizado para ajudar as aplicações a executar com o melhor desempenho possível e com o mínimo possível de fugas de informação de segurança, desativando a capacidade da aplicação de gerar saída de vestígios numa página, desativando a capacidade de exibir mensagens de erro detalhadas para os utilizadores finais e desativando o interruptor de depuração.</p><p>Muitas vezes, os interruptores e opções que estão focados no desenvolvimento, tais como rastreios de pedidos falhados e depuração, são ativados durante o desenvolvimento ativo. Recomenda-se que o método de implantação em qualquer servidor de produção seja definido para o retalho. abra o ficheiro machine.config `<deployment retail="true" />` e certifique-se de que permanece definido como verdadeiro.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Exceções devem falhar com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Falhar de forma segura](https://www.owasp.org/index.php/Fail_securely) |
| **Passos** | A aplicação deve falhar com segurança. Qualquer método que derreta um valor booleano, com base no qual determinada decisão é tomada, deve ter um bloco de exceção cuidadosamente criado. Há muitos erros lógicos devido aos quais as questões de segurança se infiltram, quando o bloco de exceção é escrito descuidadamente.|

### <a name="example"></a>Exemplo
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
O método acima sempre devolverá True, se alguma exceção acontecer. Se o utilizador final fornecer um URL mal formado, `Uri()` que o navegador respeite, mas o construtor não, isso lançará uma exceção, e a vítima será levada para o URL válido, mas mal formado. 
