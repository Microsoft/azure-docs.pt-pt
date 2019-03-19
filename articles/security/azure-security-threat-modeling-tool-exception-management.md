---
title: Exceção gestão - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
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
ms.openlocfilehash: 5c768497cc21b3fae82a9db290531af33841a2cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877305"
---
# <a name="security-frame-exception-management--mitigations"></a>Quadro de segurança: Gestão de exceções | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - não inclua serviceDebug nó no ficheiro de configuração](#servicedebug)</li><li>[WCF - não inclua serviceMetadata nó no ficheiro de configuração](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a manipulação de exceção adequada é feita na ASP.NET Web API](#exception)</li></ul> |
| **Aplicação Web** | <ul><li>[Não expõem detalhes de segurança em mensagens de erro](#messages)</li><li>[Implementar a página de processamento de erros de predefinição](#default)</li><li>[Definir o método de implementação de revenda no IIS](#deployment)</li><li>[Exceções devem falhar com segurança](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - não inclua serviceDebug nó no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Passos** | Serviços Windows Communication Framework (WCF) podem ser configurados para expor as informações de depuração. Depurar informações não devem ser utilizadas em ambientes de produção. O `<serviceDebug>` marca define se a funcionalidade de informações de depuração está ativada para um serviço WCF. Se o atributo includeExceptionDetailInFaults está definido como true, informações de exceção da aplicação será retornado para os clientes. Os atacantes podem aproveitar as informações adicionais que obtêm da depuração de saída para montar ataques direcionados no framework, da base de dados ou outros recursos usados pelo aplicativo. |

### <a name="example"></a>Exemplo
O seguinte ficheiro de configuração inclui o `<serviceDebug>` etiqueta: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desative as informações de depuração no serviço. Pode fazê-lo ao remover o `<serviceDebug>` marca do ficheiro de configuração do seu aplicativo. 

## <a id="servicemetadata"></a>WCF - não inclua serviceMetadata nó no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Genérico, o .NET Framework 3 |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Passos** | Publicamente expor informações sobre um serviço pode fornecer aos atacantes ideias valiosas de como eles podem explorar o serviço. O `<serviceMetadata>` marca ativa a funcionalidade de publicação de metadados. Metadados de serviço podem conter informações confidenciais que não devem estar acessíveis publicamente. No mínimo, permitir apenas utilizadores fidedignos acessar os metadados e certifique-se de que não estão expostas informações desnecessárias. Melhor ainda, desabilite totalmente a capacidade de publicá-los. Uma configuração segura do WCF não irá conter o `<serviceMetadata>` marca. |

## <a id="exception"></a>Certifique-se de que a manipulação de exceção adequada é feita na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | N/A  |
| **Referências**              | [Manipulação na ASP.NET Web API de exceção](https://www.asp.net/web-api/overview/error-handling/exception-handling), [modelar validação na ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Por predefinição, o mais exceções não identificadas na ASP.NET Web API são convertidas numa resposta HTTP com o código de estado `500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de estado devolvido pela API, `HttpResponseException` pode ser utilizada, conforme mostrado abaixo: 
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
Para controlar ainda mais a resposta de exceção, o `HttpResponseMessage` classe pode ser usada, conforme mostrado abaixo: 
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
Para capturar exceções não tratadas que não são do tipo `HttpResponseException`, podem ser utilizados filtros de exceção. Filtros de exceção implementam o `System.Web.Http.Filters.IExceptionFilter` interface. A maneira mais simples de criar um filtro de exceção é derivar o `System.Web.Http.Filters.ExceptionFilterAttribute` de classe e substituir o método OnException. 

### <a name="example"></a>Exemplo
Aqui está um filtro que converte `NotImplementedException` exceções em código de estado HTTP `501, Not Implemented`: 
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

Existem várias formas de registar um filtro de exceção de Web API:
- Por ação
- Pelo controlador
- globalmente

### <a name="example"></a>Exemplo
Para aplicar o filtro a uma ação específica, adicione o filtro como um atributo para a ação: 
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
Para aplicar o filtro para todas as ações num `controller`, adicione o filtro como um atributo para o `controller` classe: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro globalmente a todos os controladores da Web API, adicione uma instância do filtro para o `GlobalConfiguration.Configuration.Filters` coleção. Filtros de exceção nesta coleção se aplicam a qualquer ação do controlador de Web API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para a validação de modelo, o estado de modelo pode ser transmitido ao método CreateErrorResponse, conforme mostrado abaixo: 
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

Verifique os links na seção referências para obter mais detalhes sobre a manipulação de exceção e a validação de modelo na ASP.NET Web API 

## <a id="messages"></a>Não expõem detalhes de segurança em mensagens de erro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Mensagens de erro genéricas são fornecidas diretamente para o utilizador sem incluir dados confidenciais de aplicações. Exemplos de dados confidenciais:</p><ul><li>Nomes de servidor</li><li>Cadeias de ligação</li><li>Nomes de utilizador</li><li>Palavras-passe</li><li>Procedimentos SQL</li><li>Detalhes de falhas SQL dinâmicas</li><li>Rastreio de pilha e linhas de código</li><li>Variáveis armazenadas na memória</li><li>Localizações de unidades e pastas</li><li>Pontos de instalação de aplicações</li><li>Definições de configuração de anfitrião</li><li>Outros detalhes da aplicação interna</li></ul><p>Interceptando erros todos os dentro de um aplicativo e exibindo mensagens de erro genérica, bem como habilitar erros personalizados dentro do IIS ajudará a evitar a divulgação de informações. Base de dados do SQL Server e .NET manipulação de exceção, entre outros arquiteturas, de tratamento de erros são especialmente verboso e extremamente útil para um utilizador mal intencionado que seu aplicativo de criação de perfis. Não diretamente exibir o conteúdo de uma classe derivada da classe de exceção .NET e certifique-se de que tem o tratamento de exceção adequada para que uma exceção inesperada inadvertidamente não é apresentada diretamente para o utilizador.</p><ul><li>Fornecer mensagens de erro genéricas diretamente para o utilizador que abstraem ausente detalhes específicos encontrados diretamente na mensagem de exceção/erro</li><li>Não apresentar o conteúdo de uma classe de exceção .NET diretamente para o utilizador</li><li>Interceptar todas as mensagens de erro e se tal for apropriado informar ao usuário por meio de uma mensagem de erro genérico enviada para o cliente do aplicativo</li><li>Não expõem o conteúdo da classe de exceção diretamente para o usuário, especialmente o valor de retorno `.ToString()`, ou os valores das propriedades da mensagem ou o rastreio da pilha. Estas informações de registo e exibir uma mensagem mais inócua para o usuário de forma segura</li></ul>|

## <a id="default"></a>Implementar a página de processamento de erros de predefinição

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Editar caixa de diálogo de definições de páginas de erro do ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Passos** | <p>Quando ocorre uma falha de um aplicativo ASP.NET e faz com que um erro de servidor interno do HTTP/1.x 500, ou uma configuração de funcionalidades (como a filtragem de pedidos) impede que uma página que está a ser exibido, será gerada uma mensagem de erro. Os administradores podem escolher se é ou não o aplicativo exibirá uma mensagem amigável para o cliente, a mensagem de erro detalhada para o cliente ou a mensagem de erro detalhada para localhost apenas. O <customErrors> etiqueta na Web. config tem três modos:</p><ul><li>**On:** Especifica que os erros personalizados estão ativados. Se não for especificado nenhum atributo defaultRedirect, os utilizadores veem um erro genérico. Os erros personalizados são exibidos para os clientes remotos e para o anfitrião local</li><li>**Off:** Especifica que os erros personalizados estão desativados. Os erros detalhados do ASP.NET são mostrados para os clientes remotos e para o anfitrião local</li><li>**RemoteOnly:** Especifica que erros personalizados são apresentados apenas para os clientes remotos e que os erros do ASP.NET são apresentados ao anfitrião local. Este é o valor predefinido</li></ul><p>Abra o `web.config` de ficheiros para a aplicação/site e certifique-se de que a etiqueta tem `<customErrors mode="RemoteOnly" />` ou `<customErrors mode="On" />` definido.</p>|

## <a id="deployment"></a>Definir o método de implementação de revenda no IIS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [implementação elemento (Nastavení Technologie ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Passos** | <p>O `<deployment retail>` comutador é destinado ao uso pelos servidores IIS de produção. Este parâmetro é utilizado para ajudar a executar com o menor número possíveis leakages de informações de segurança e melhor desempenho possível ao desativar a capacidade da aplicação para gerar a saída de rastreamento numa página, desativar a capacidade de exibir mensagens de erro detalhadas para as aplicações os utilizadores finais e desativar o comutador de depuração.</p><p>Muitas vezes, comutadores e as opções que estão voltada para desenvolvedores, como falha pedir rastreamento e depuração, são ativadas durante o desenvolvimento de Active Directory. Recomenda-se que o método de implementação em qualquer servidor de produção ser definido de revenda. Abra o arquivo Machine config e certifique-se de que `<deployment retail="true" />` permanece definido como true.</p>|

## <a id="fail"></a>Exceções devem falhar com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Falhar de forma segura](https://www.owasp.org/index.php/Fail_securely) |
| **Passos** | Aplicação deve falhar com segurança. Qualquer método que retorna um valor booleano, com base no que determinados decisão, deverá ter criado cuidadosamente de blocos de exceção. Existem muitos erros lógicos que problemas de segurança com a ampliação no, quando o bloco de exceção é escrito de forma impensada.|

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
O método acima sempre retornará True, se ocorrer alguma exceção. Se o utilizador final fornece um URL com formato incorreto, o que respeita o navegador, mas o `Uri()` construtor não, isso lançará uma exceção e a vítima será direcionada para o URL válido, mas tem um formato incorreto. 
