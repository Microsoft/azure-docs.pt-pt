---
title: Gerenciamento de exceções-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728080"
---
# <a name="security-frame-exception-management--mitigations"></a>Quadro de segurança: Gerenciamento de exceções | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF-não incluir o nó de userdebug no arquivo de configuração](#servicedebug)</li><li>[WCF-não inclui o nó de metadados no arquivo de configuração](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que o tratamento de exceção adequado seja feito em ASP.NET Web API](#exception)</li></ul> |
| **Aplicativo Web** | <ul><li>[Não expor detalhes de segurança em mensagens de erro](#messages)</li><li>[Implementar página de tratamento de erro padrão](#default)</li><li>[Definir o método de implantação para varejo no IIS](#deployment)</li><li>[As exceções devem falhar com segurança](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF-não incluir o nó de userdebug no arquivo de configuração

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Passos** | Os serviços do Windows Communication Framework (WCF) podem ser configurados para expor informações de depuração. As informações de depuração não devem ser usadas em ambientes de produção. A `<serviceDebug>` marca define se o recurso de informações de depuração está habilitado para um serviço WCF. Se o atributo includeExceptionDetailInFaults for definido como true, as informações de exceção do aplicativo serão retornadas aos clientes. Os invasores podem aproveitar as informações adicionais que eles adquirem da saída de depuração para ataques de montagem direcionados à estrutura, ao banco de dados ou a outros recursos usados pelo aplicativo. |

### <a name="example"></a>Exemplo
O arquivo de configuração a seguir `<serviceDebug>` inclui a marca: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desabilite as informações de depuração no serviço. Isso pode ser feito removendo a `<serviceDebug>` marca do arquivo de configuração do aplicativo. 

## <a id="servicemetadata"></a>WCF-não inclui o nó de metadados no arquivo de configuração

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Genérico, NET Framework 3 |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Passos** | Expor publicamente informações sobre um serviço pode fornecer aos invasores um valioso insight sobre como eles podem explorar o serviço. A `<serviceMetadata>` marca habilita o recurso de publicação de metadados. Os metadados de serviço podem conter informações confidenciais que não devem ser acessíveis publicamente. No mínimo, somente permita que usuários confiáveis acessem os metadados e verifique se as informações desnecessárias não estão expostas. Melhor ainda, desabilite totalmente a capacidade de publicar metadados. Uma configuração segura do WCF não conterá `<serviceMetadata>` a marca. |

## <a id="exception"></a>Certifique-se de que o tratamento de exceção adequado seja feito em ASP.NET Web API

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | N/A  |
| **Referências**              | [Tratamento de exceção no ASP.NET Web API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [validação de modelo no ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Por padrão, a maioria das exceções não capturadas no ASP.NET Web API são convertidas em uma resposta HTTP com código de status`500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de status retornado pela API, `HttpResponseException` pode ser usado como mostrado abaixo: 
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
Para obter mais controle sobre a resposta de exceção `HttpResponseMessage` , a classe pode ser usada como mostrado abaixo: 
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
Para capturar exceções sem tratamento que não sejam do tipo `HttpResponseException`, os filtros de exceção podem ser usados. Os filtros de exceção `System.Web.Http.Filters.IExceptionFilter` implementam a interface. A maneira mais simples de escrever um filtro de exceção é derivar da `System.Web.Http.Filters.ExceptionFilterAttribute` classe e substituir o método OnException. 

### <a name="example"></a>Exemplo
Aqui está um filtro que converte `NotImplementedException` as exceções no código `501, Not Implemented`de status http: 
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

Há várias maneiras de registrar um filtro de exceção da API Web:
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
Para aplicar o filtro a todas as ações em um `controller`, adicione o filtro como um atributo `controller` à classe: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro globalmente a todos os controladores de API da Web, adicione uma instância do filtro `GlobalConfiguration.Configuration.Filters` à coleção. Os filtros de exceção nesta coleção se aplicam a qualquer ação do controlador da API Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para a validação do modelo, o estado do modelo pode ser passado para o método CreateErrorResponse, conforme mostrado abaixo: 
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

Verifique os links na seção de referências para obter detalhes adicionais sobre a manipulação de exceção e a validação de modelo no ASP.NET Web API 

## <a id="messages"></a>Não expor detalhes de segurança em mensagens de erro

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>As mensagens de erro genéricas são fornecidas diretamente para o usuário sem incluir dados confidenciais do aplicativo. Exemplos de dados confidenciais incluem:</p><ul><li>Nomes de servidor</li><li>Cadeias de ligação</li><li>Nomes</li><li>Palavras-passe</li><li>Procedimentos SQL</li><li>Detalhes de falhas de SQL dinâmicas</li><li>Rastreamento de pilha e linhas de código</li><li>Variáveis armazenadas na memória</li><li>Locais de unidade e pasta</li><li>Pontos de instalação do aplicativo</li><li>Definições de configuração do host</li><li>Outros detalhes do aplicativo interno</li></ul><p>O trapping de todos os erros em um aplicativo e o fornecimento de mensagens de erro genéricas, bem como a habilitação de erros personalizados no IIS, ajudará a evitar a divulgação de informações. SQL Server o tratamento de exceção do banco de dados e do .NET, entre outras arquiteturas de tratamento de erros, são especialmente detalhados e extremamente úteis para um usuário mal-intencionado que realiza a criação de perfil do seu aplicativo. Não exiba diretamente o conteúdo de uma classe derivada da classe de exceção do .NET e verifique se você tem uma manipulação de exceção adequada para que uma exceção inesperada não seja gerada inadvertidamente diretamente para o usuário.</p><ul><li>Fornecer mensagens de erro genéricas diretamente ao usuário que abstrai detalhes específicos encontrados diretamente na mensagem de erro/exceção</li><li>Não exibir o conteúdo de uma classe de exceção do .NET diretamente para o usuário</li><li>Interceptar todas as mensagens de erro e, se apropriado, informar ao usuário por meio de uma mensagem de erro genérica enviada ao cliente do aplicativo</li><li>Não exponha o conteúdo da classe de exceção diretamente ao usuário, especialmente o valor de retorno de `.ToString()`, ou os valores das propriedades Message ou StackTrace. Registrar essas informações com segurança e exibir uma mensagem mais inofensiva para o usuário</li></ul>|

## <a id="default"></a>Implementar página de tratamento de erro padrão

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Caixa de diálogo Editar configurações de páginas de erro do ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Passos** | <p>Quando um aplicativo ASP.NET falha e causa um erro de servidor interno HTTP/1. x 500 ou uma configuração de recurso (como filtragem de solicitação) impede que uma página seja exibida, uma mensagem de erro será gerada. Os administradores podem escolher se o aplicativo deve ou não exibir uma mensagem amigável para o cliente, uma mensagem de erro detalhada para o cliente ou uma mensagem de erro detalhada somente para localhost. A `<customErrors>` marca no Web. config tem três modos:</p><ul><li>**No** Especifica que os erros personalizados estão habilitados. Se nenhum atributo DefaultRedirect for especificado, os usuários verão um erro genérico. Os erros personalizados são mostrados para os clientes remotos e para o host local</li><li>**Desconto** Especifica que os erros personalizados estão desabilitados. Os erros de ASP.NET detalhados são mostrados para os clientes remotos e para o host local</li><li>**RemoteOnly:** Especifica que os erros personalizados são mostrados apenas para os clientes remotos e que os erros de ASP.NET são mostrados para o host local. Esse é o valor padrão</li></ul><p>Abra o `web.config` arquivo para o aplicativo/site e verifique se a marca `<customErrors mode="RemoteOnly" />` tem ou `<customErrors mode="On" />` está definida.</p>|

## <a id="deployment"></a>Definir o método de implantação para varejo no IIS

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Elemento Deployment (esquema de configurações de ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Passos** | <p>A `<deployment retail>` opção é destinada ao uso por servidores IIS de produção. Essa opção é usada para ajudar os aplicativos a serem executados com o melhor desempenho possível e menos possíveis vazamentos de informações de segurança, desabilitando a capacidade do aplicativo de gerar a saída de rastreamento em uma página, desabilitando a capacidade de exibir mensagens de erro detalhadas para os usuários finais e a desabilitação da opção de depuração.</p><p>Muitas vezes, opções e opções que são focadas no desenvolvedor, como rastreamento e depuração de solicitação com falha, são habilitadas durante o desenvolvimento ativo. É recomendável que o método de implantação em qualquer servidor de produção seja definido como varejo. Abra o arquivo Machine. config e certifique- `<deployment retail="true" />` se de que permaneça definido como true.</p>|

## <a id="fail"></a>As exceções devem falhar com segurança

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Falhar com segurança](https://www.owasp.org/index.php/Fail_securely) |
| **Passos** | O aplicativo deve falhar com segurança. Qualquer método que retorna um valor booliano, com base no qual determinada decisão é feita, deve ter um bloco de exceção criado com cuidado. Há muitos erros lógicos devido a quais problemas de segurança surgiram, quando o bloco de exceção é escrito com cuidado.|

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
O método acima sempre retornará true, se ocorrer alguma exceção. Se o usuário final fornecer uma URL malformada, que o navegador respeita, `Uri()` mas o construtor não, isso gerará uma exceção, e a vítima será direcionada para a URL válida, mas malformada. 
