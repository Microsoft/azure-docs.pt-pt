---
title: Gestão de Exceções - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a gestão de exceções na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517432"
---
# <a name="security-frame-exception-management--mitigations"></a>Quadro de Segurança: Gestão de Exceções / Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Não inclua o nó de dedepuração de serviço no ficheiro de configuração](#servicedebug)</li><li>[WCF- Não inclua o nó de serviçoMetadata no ficheiro de configuração](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que o manuseamento adequado da exceção é feito em ASP.NET Web API](#exception)</li></ul> |
| **Aplicação Web** | <ul><li>[Não exponha detalhes de segurança em mensagens de erro](#messages)</li><li>[Implementar página de tratamento de erros padrão](#default)</li><li>[Definir método de implantação para retalhista no IIS](#deployment)</li><li>[Exceções devem falhar com segurança](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Não inclua o nó de dedepuração de serviço no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Passos** | Os serviços do Windows Communication Framework (WCF) podem ser configurados para expor informações de depuragem. A informação de depurg não deve ser utilizada em ambientes de produção. A `<serviceDebug>` etiqueta define se a função de informação de depurador está ativada para um serviço WCF. Se o atributo incluirExceptionDetailInFaults é definido como verdadeiro, as informações de exceção da aplicação serão devolvidas aos clientes. Os atacantes podem aproveitar as informações adicionais que obtêm da depuragem da produção para montar ataques direcionados para a estrutura, base de dados ou outros recursos utilizados pela aplicação. |

### <a name="example"></a>Exemplo
O seguinte ficheiro de configuração inclui a `<serviceDebug>` etiqueta: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desativar a informação de depuração no serviço. Isto pode ser conseguido removendo a `<serviceDebug>` etiqueta do ficheiro de configuração da sua aplicação. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Não inclua o nó de serviçoMetadata no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Quadro genérico e LÍQUIDO 3 |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Passos** | Expor publicamente informações sobre um serviço pode fornecer aos atacantes informações valiosas sobre como podem explorar o serviço. A `<serviceMetadata>` etiqueta permite a funcionalidade de publicação de metadados. Os metadados de serviço podem conter informações sensíveis que não devem ser acessíveis ao público. No mínimo, apenas permitir que utilizadores de confiança acedam aos metadados e garantam que informações desnecessárias não são expostas. Melhor ainda, desative totalmente a capacidade de publicar metadados. Uma configuração segura do WCF não contém a `<serviceMetadata>` etiqueta. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Certifique-se de que o manuseamento adequado da exceção é feito em ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Exceção Manuseamento em API web ASP.NET,](https://www.asp.net/web-api/overview/error-handling/exception-handling) [validação de modelos em ASP.NET API Web](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Por padrão, a maioria das exceções não conseguidas na ASP.NET Web API são traduzidas numa resposta HTTP com código de estado `500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de estado devolvido pela API, `HttpResponseException` pode ser utilizado como indicado abaixo: 
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
Para um maior controlo sobre a resposta de exceção, a `HttpResponseMessage` classe pode ser utilizada como indicado abaixo: 
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
Para apanhar exceções não manuseadas que não sejam do `HttpResponseException` tipo, podem ser utilizados filtros de exceção. Filtros de exceção implementam a `System.Web.Http.Filters.IExceptionFilter` interface. A forma mais simples de escrever um filtro de exceção é derivar da `System.Web.Http.Filters.ExceptionFilterAttribute` classe e anular o método OnException. 

### <a name="example"></a>Exemplo
Aqui está um filtro que converte `NotImplementedException` exceções em código de estado HTTP `501, Not Implemented` : 
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

Existem várias formas de registar um filtro de exceção da API Web:
- Por ação
- Por controlador
- Globalmente

### <a name="example"></a>Exemplo
Para aplicar o filtro a uma ação específica, adicione o filtro como um atributo à ação: 
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
Para aplicar o filtro a todas as ações num `controller` , adicione o filtro como um atributo à `controller` classe: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro globalmente a todos os controladores web da API, adicione uma instância do filtro à `GlobalConfiguration.Configuration.Filters` coleção. Os filtros de exceção nesta coleção aplicam-se a qualquer ação do controlador Web API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para validação do modelo, o estado do modelo pode ser passado para o método CreateErrorResponse, como mostrado abaixo: 
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

Consulte os links na secção de referências para obter mais detalhes sobre o manuseamento excecional e validação do modelo em ASP.NET Web API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Não exponha detalhes de segurança em mensagens de erro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>As mensagens de erro genéricas são fornecidas diretamente ao utilizador sem incluir dados de aplicação sensíveis. Exemplos de dados sensíveis incluem:</p><ul><li>Nomes do servidor</li><li>Cadeias de ligação</li><li>Nomes de utilizador</li><li>Palavras-passe</li><li>Procedimentos SQL</li><li>Detalhes de falhas dinâmicas do SQL</li><li>Traços de pilha e linhas de código</li><li>Variáveis armazenadas na memória</li><li>Locais de unidade e pasta</li><li>Pontos de instalação de aplicações</li><li>Definições de configuração do anfitrião</li><li>Outros detalhes da aplicação interna</li></ul><p>Capturar todos os erros dentro de uma aplicação e fornecer mensagens de erro genéricas, bem como permitir erros personalizados dentro do IIS ajudará a prevenir a divulgação de informações. A base de dados SQL Server e o tratamento de exceções .NET, entre outras arquiteturas de manuseamento de erros, são especialmente verbosos e extremamente úteis para um utilizador malicioso que faz o perfil da sua aplicação. Não exiba diretamente o conteúdo de uma classe derivada da classe .NET Exception, e certifique-se de que tem o manuseamento de exceções adequado para que uma exceção inesperada não seja inadvertidamente elevada diretamente ao utilizador.</p><ul><li>Fornecer mensagens de erro genéricas diretamente ao utilizador que abstratos detalhes específicos encontrados diretamente na mensagem de exceção/erro</li><li>Não apresente o conteúdo de uma classe de exceção .NET diretamente ao utilizador</li><li>Aprisionar todas as mensagens de erro e, se for caso disso, informar o utilizador através de uma mensagem de erro genérica enviada ao cliente da aplicação</li><li>Não exponha diretamente o conteúdo da classe Exception ao utilizador, especialmente ao valor de `.ToString()` devolução, nem aos valores das propriedades Message ou StackTrace. Registando esta informação de forma segura e exibindo uma mensagem mais inócua ao utilizador</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementar página de tratamento de erros padrão

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Caixa de Diálogo Editar Definições de Páginas de Erro .NET](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Passos** | <p>Quando uma aplicação ASP.NET falha e causa um Erro interno do Servidor HTTP/1.x 500, ou uma configuração de funcionalidade (tal como filtragem de pedidos) impede que uma página seja exibida, uma mensagem de erro será gerada. Os administradores podem escolher se a aplicação deve ou não apresentar uma mensagem amigável ao cliente, uma mensagem de erro detalhada ao cliente ou uma mensagem de erro detalhada apenas para o local. A `<customErrors>` etiqueta no web.config tem três modos:</p><ul><li>**Em:** Especifica que os erros personalizados estão ativados. Se não for especificado nenhum atributo predefinidoRedirect, os utilizadores vêem um erro genérico. Os erros personalizados são mostrados aos clientes remotos e ao anfitrião local</li><li>**Fora:** Especifica que os erros personalizados são desativados. Os erros de ASP.NET detalhados são mostrados aos clientes remotos e ao anfitrião local</li><li>**Remotamente Apenas:** Especifica que os erros personalizados são mostrados apenas aos clientes remotos, e que ASP.NET erros são mostrados ao anfitrião local. Este é o valor padrão</li></ul><p>Abra o `web.config` ficheiro para a aplicação/site e certifique-se de que a etiqueta tem `<customErrors mode="RemoteOnly" />` ou `<customErrors mode="On" />` definido.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Definir método de implantação para retalhista no IIS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Elemento de implantação (ASP.NET Esquema de Definições)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Passos** | <p>O `<deployment retail>` interruptor destina-se a ser utilizado por servidores IIS de produção. Este switch é utilizado para ajudar as aplicações a executar o melhor desempenho possível e o menor número possível de fugas de informação de segurança, desativando a capacidade da aplicação de gerar saída de vestígios numa página, desativando a capacidade de exibir mensagens de erro detalhadas para os utilizadores finais e desativando o interruptor de depuração.</p><p>Muitas vezes, os interruptores e opções que estão focados no desenvolvedor, tais como rastreio de pedidos falhados e depuração, são ativados durante o desenvolvimento ativo. Recomenda-se que o método de implantação de qualquer servidor de produção seja definido para retalhista. abrir o ficheiro machine.config e garantir que `<deployment retail="true" />` se mantém definido como verdadeiro.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Exceções devem falhar com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Falhar de forma segura](https://owasp.org/www-community/Fail_securely) |
| **Passos** | A aplicação deve falhar com segurança. Qualquer método que devolva um valor Boolean, com base no qual determinada decisão é tomada, deve ter um bloco de exceções cuidadosamente criado. Há muitos erros lógicos devido aos quais problemas de segurança se infiltram, quando o bloco de exceções é escrito descuidadamente.|

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
O método acima vai sempre devolver True, se alguma exceção acontecer. Se o utilizador final fornecer um URL mal formado, que o navegador respeita, mas o `Uri()` construtor não, isso irá lançar uma exceção, e a vítima será levada para o URL válido mas mal formado.