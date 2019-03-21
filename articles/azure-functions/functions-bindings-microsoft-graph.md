---
title: Enlaces do Microsoft Graph para as funções do Azure
description: Compreenda como utilizar acionadores do Microsoft Graph e enlaces nas funções do Azure.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: f112bdf9eacf51852659ab49a5673b0c8bfb0e46
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997547"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Enlaces do Microsoft Graph para as funções do Azure

Este artigo explica como configurar e trabalhar com acionadores do Microsoft Graph e enlaces nas funções do Azure. Com eles, pode utilizar as funções do Azure para trabalhar com dados, informações e eventos a partir da [Microsoft Graph](https://developer.microsoft.com/graph).

A extensão do Microsoft Graph fornece as seguintes ligações:
- Uma [enlace de entrada do token de autenticação](#token-input) permite-lhe interagir com qualquer API do Microsoft Graph.
- Uma [enlace de entrada de tabela do Excel](#excel-input) permite-lhe ler dados a partir do Excel.
- Uma [enlace de saída de tabela do Excel](#excel-output) permite-lhe modificar os dados do Excel.
- R [enlace de entrada do ficheiro no OneDrive](#onedrive-input) permite-lhe ler ficheiros do OneDrive.
- R [enlace de saída do ficheiro no OneDrive](#onedrive-output) permite-lhe escrever a ficheiros no OneDrive.
- Uma [enlace de saída de mensagem do Outlook](#outlook-output) permite-lhe enviar um e-mail através do Outlook.
- Uma coleção de [acionadores de webhook do Microsoft Graph e enlaces](#webhooks) permite-lhe reagir a eventos do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Enlaces do Microsoft Graph estão atualmente em pré-visualização para a versão das funções do Azure 2.x. Não são suportadas na versão de funções 1.x.

## <a name="packages"></a>Pacotes

O enlace de entrada de token de autenticação é fornecido no [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) pacote NuGet. Os outros enlaces do Microsoft Graph são fornecidos na [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) pacote. Os pacotes de código-fonte está no [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Como configurar as extensões

Enlaces do Graph da Microsoft estão disponíveis por meio _extensões de enlace_. As extensões de enlace são componentes opcionais para o runtime das funções do Azure. Esta secção mostra como configurar o Microsoft Graph e extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Ativar pré-visualização do Functions 2.0

Extensões de enlace estão disponíveis apenas para pré-visualização do Azure 2.0 de funções. 

Para obter informações sobre como definir uma aplicação de funções para utilizar a versão de pré-visualização 2.0 do runtime das funções, consulte [como versões de tempo de execução de funções do Azure de destino](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalação da extensão

Para instalar uma extensão do portal do Azure, navegue para um modelo ou de ligação que faz referência a ele. Criar uma nova função e, no ecrã de seleção de modelo, escolha o cenário de "Microsoft Graph". Selecione um dos modelos este cenário. Em alternativa, pode navegar para o separador "Integrar" de uma função já existente e selecione uma das ligações abordadas neste artigo.

Em ambos os casos, será apresentado um aviso que especifica a extensão para ser instalado. Clique em **instalar** para obter a extensão. Cada extensão só precisa de ser instalado uma vez por aplicação de funções. 

> [!Note] 
> O processo de instalação no portal pode demorar até 10 minutos num plano de consumo.

Se estiver a utilizar o Visual Studio, pode obter as extensões, instalando [os pacotes de NuGet que são apresentados anteriormente neste artigo](#packages).

### <a name="configuring-authentication--authorization"></a>Configurar a autenticação / autorização

As associações descritas neste artigo requerem uma identidade a ser utilizado. Isso permite que o Microsoft Graph aplicar permissões e as interações de auditoria. A identidade pode ser um utilizador aceder ao seu aplicativo ou a própria aplicação. Para configurar esta identidade, configure [aplicação serviço de autenticação / autorização](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) com o Azure Active Directory. Também terá de solicitar quaisquer permissões de recursos que exigem as suas funções.

> [!Note] 
> A extensão do Microsoft Graph só suporta a autenticação do Azure AD. Os utilizadores têm de iniciar sessão com uma conta escolar ou profissional.

Se estiver a utilizar o portal do Azure, verá um aviso abaixo da linha de comandos para instalar a extensão. O aviso pede-lhe para configurar a aplicação serviço de autenticação / autorização e a pedido requer que todas as permissões no modelo ou a ligação. Clique em **configurar agora para o Azure AD** ou **adicionar permissões agora** conforme adequado.



<a name="token-input"></a>
## <a name="auth-token"></a>Token de autenticação

O enlace de entrada de token de autenticação obtém um Azure AD token para um determinado recurso e fornece-o para seu código como uma cadeia de caracteres. O recurso pode ser qualquer um para o qual o aplicativo tem permissões. 

Esta secção contém as seguintes subsecções:

* [Exemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuração](#auth-token---configuration)
* [Utilização](#auth-token---usage)

### <a name="auth-token---example"></a>Token de autenticação - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token de autenticação - exemplo de script do c#

O exemplo seguinte obtém as informações de perfil do usuário.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# utiliza o token para fazer uma chamada HTTP para o Microsoft Graph e devolve o resultado:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token de autenticação - exemplo de JavaScript

O exemplo seguinte obtém as informações de perfil do usuário.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código JavaScript utiliza o token para fazer uma chamada HTTP para o Microsoft Graph e devolve o resultado.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Token de autenticação - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) atributo.

### <a name="auth-token---configuration"></a>Token de autenticação - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Token` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para o token de autenticação. Ver [com um token de autenticação de entrada vinculação do código](#token-input-code).|
|**tipo**||Necessário - tem de ser definido como `token`.|
|**direção**||Necessário - tem de ser definido como `in`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**Recurso**|**resource**|Necessário - um URL de recurso do Azure AD para o qual o token está a ser requerido.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token de autenticação - utilização

O enlace em si não requer quaisquer permissões do Azure AD, mas dependendo de como o token é utilizado, poderá ter de solicitar permissões adicionais. Verifique os requisitos do recurso que pretende aceder com o token.

O token é sempre apresentado ao código como uma cadeia de caracteres.

> [!Note]
> Ao desenvolver localmente com qualquer uma das `userFromId`, `userFromToken` ou `userFromRequest` opções, o token solicitado pode ser [manualmente de obteve](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado no `X-MS-TOKEN-AAD-ID-TOKEN` cabeçalho do pedido de um aplicativo de cliente chamada.


<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada de Excel

O enlace de entrada de tabela de Excel lê o conteúdo de uma tabela do Excel armazenados no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuração](#excel-input---configuration)
* [Utilização](#excel-input---usage)

### <a name="excel-input---example"></a>Introdução - do Excel exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel de entrada - exemplo de script do c#

O seguinte procedimento *Function* arquivo define um acionador HTTP com um enlace de entrada do Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O seguinte código de script do c# lê o conteúdo da tabela especificada e devolve-os para o utilizador:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel de entrada - exemplo de JavaScript

O seguinte procedimento *Function* arquivo define um acionador HTTP com um enlace de entrada do Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O seguinte código JavaScript lê o conteúdo da tabela especificada e devolve-os para o usuário.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel de entrada - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atributo.

### <a name="excel-input---configuration"></a>Excel introdução - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Excel` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para a tabela de Excel. Ver [usar uma tabela do Excel a vinculação do código de entrada](#excel-input-code).|
|**tipo**||Necessário - tem de ser definido como `excel`.|
|**direção**||Necessário - tem de ser definido como `in`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**path**|**Caminho**|Necessário - o caminho no OneDrive para o livro do Excel.|
|**worksheetName**|**WorksheetName**|A folha de cálculo na qual é encontrada a tabela.|
|**tableName**|**TableName**|Nome da tabela. Se não for especificado, será utilizado o conteúdo da folha de cálculo.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel de entrada - utilização

Este enlace requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros do utilizador|

O enlace expõe os seguintes tipos de funções de .NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (usando a associação de modelo estruturais)










<a name="excel-output"></a>
## <a name="excel-output"></a>Saída do Excel

O Excel enlace de saída modifica o conteúdo de uma tabela do Excel armazenados no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuração](#excel-output---configuration)
* [Utilização](#excel-output---usage)

### <a name="excel-output---example"></a>Saída - do Excel exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel de saída - exemplo de script do c#

O exemplo seguinte adiciona linhas a uma tabela do Excel.

O *Function* arquivo define um acionador HTTP com um Excel enlace de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# adiciona uma nova linha à tabela (assumindo que seja de coluna única) com base na entrada da cadeia de consulta:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel de saída - exemplo de JavaScript

O exemplo seguinte adiciona linhas a uma tabela do Excel.

O *Function* arquivo define um acionador HTTP com um Excel enlace de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O seguinte código JavaScript adiciona uma nova linha à tabela (assumindo que seja de coluna única) com base na entrada da cadeia de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel de saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atributo.

### <a name="excel-output---configuration"></a>Excel saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Excel` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para o token de autenticação. Ver [utilizar uma tabela do Excel a vinculação do código de saída](#excel-output-code).|
|**tipo**||Necessário - tem de ser definido como `excel`.|
|**direção**||Necessário - tem de ser definido como `out`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**UserId** |**userId** |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**path**|**Caminho**|Necessário - o caminho no OneDrive para o livro do Excel.|
|**worksheetName**|**WorksheetName**|A folha de cálculo na qual é encontrada a tabela.|
|**tableName**|**TableName**|Nome da tabela. Se não for especificado, será utilizado o conteúdo da folha de cálculo.|
|**updateType**|**UpdateType**|Necessário - o tipo de alteração a fazer à tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code> -Substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code> -Adiciona o payload ao final da tabela no OneDrive com a criação de novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel de saída - utilização

Este enlace requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Tem acesso total aos ficheiros de utilizador|

O enlace expõe os seguintes tipos de funções de .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (usando a associação de modelo estruturais)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada de arquivo

O enlace de entrada do ficheiro no OneDrive lê o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuração](#file-input---configuration)
* [Utilização](#file-input---usage)

### <a name="file-input---example"></a>Ficheiro de entrada - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Ficheiro de entrada - exemplo de script do c#

O exemplo seguinte lê um ficheiro que é armazenado no OneDrive.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de ficheiros de OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# lê o arquivo especificado na cadeia de consulta e registra seu comprimento:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Ficheiro de entrada - exemplo de JavaScript

O exemplo seguinte lê um ficheiro que é armazenado no OneDrive.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de ficheiros de OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O seguinte código JavaScript lê o arquivo especificado na cadeia de consulta e retorna seu comprimento.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Ficheiro de entrada - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atributo.

### <a name="file-input---configuration"></a>Ficheiro de entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `OneDrive` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para o ficheiro. Ver [através de um ficheiro do OneDrive enlace no código de entrada](#onedrive-input-code).|
|**tipo**||Necessário - tem de ser definido como `onedrive`.|
|**direção**||Necessário - tem de ser definido como `in`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**path**|**Caminho**|Necessário - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Ficheiro de entrada - utilização

Este enlace requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros do utilizador|

O enlace expõe os seguintes tipos de funções de .NET:
- byte[]
- Transmitir em fluxo
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Ficheiro de saída

O ficheiro do OneDrive enlace de saída modifica o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuração](#file-output---configuration)
* [Utilização](#file-output---usage)

### <a name="file-output---example"></a>Ficheiro de saída - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Ficheiro de saída - exemplo de script do c#

O exemplo seguinte escreve um ficheiro que é armazenado no OneDrive.

O *Function* arquivo define um acionador HTTP com um OneDrive enlace de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# obtém o texto da cadeia de consulta e escreve-os num arquivo de texto (FunctionsTest.txt conforme definido no exemplo anterior) na raiz do OneDrive do chamador:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Ficheiro de saída - exemplo de JavaScript

O exemplo seguinte escreve um ficheiro que é armazenado no OneDrive.

O *Function* arquivo define um acionador HTTP com um OneDrive enlace de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código JavaScript obtém o texto da cadeia de consulta e escreve-a num arquivo de texto (FunctionsTest.txt como definido na configuração acima) na raiz do OneDrive do chamador.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Ficheiro de saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atributo.

### <a name="file-output---configuration"></a>Ficheiro de saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `OneDrive` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para o ficheiro. Ver [através de um ficheiro do OneDrive enlace no código de saída](#onedrive-output-code).|
|**tipo**||Necessário - tem de ser definido como `onedrive`.|
|**direção**||Necessário - tem de ser definido como `out`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**UserId** |**userId** |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**path**|**Caminho**|Necessário - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Ficheiro de saída - utilização

Este enlace requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Tem acesso total aos ficheiros de utilizador|

O enlace expõe os seguintes tipos de funções de .NET:
- byte[]
- Transmitir em fluxo
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Saída do Outlook

A mensagem do Outlook de saída enlace envia uma mensagem de correio através do Outlook.

Esta secção contém as seguintes subsecções:

* [Exemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuração](#outlook-output---configuration)
* [Utilização](#outlook-output---usage)

### <a name="outlook-output---example"></a>Saída do Outlook - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook de saída - exemplo de script do c#

O exemplo seguinte envia um e-mail através do Outlook.

O *Function* arquivo define um acionador HTTP com um Outlook enlace de saída de mensagem:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

O código de script do c# envia um email do chamador para um destinatário especificado na cadeia de consulta:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook de saída - exemplo de JavaScript

O exemplo seguinte envia um e-mail através do Outlook.

O *Function* arquivo define um acionador HTTP com um Outlook enlace de saída de mensagem:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

O código JavaScript envia um email do chamador para um destinatário especificado na cadeia de consulta:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook de saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) atributo.

### <a name="outlook-output---configuration"></a>Saída do Outlook - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Outlook` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para a mensagem de correio. Ver [usar uma mensagem do Outlook enlace no código de saída](#outlook-output-code).|
|**tipo**||Necessário - tem de ser definido como `outlook`.|
|**direção**||Necessário - tem de ser definido como `out`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook de saída - utilização

Este enlace requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar correio como utilizador|

O enlace expõe os seguintes tipos de funções de .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipos de objeto personalizado (usando a associação de modelo estruturais)






## <a name="webhooks"></a>Webhooks

Os Webhooks permitem-lhe reagir a eventos no Microsoft Graph. Para suportar webhooks, as funções são necessárias para criar, atualizar e reagir a _subscrições de webhook_. Uma solução completa de webhook requer uma combinação das seguintes ligações:
- R [acionador de webhook do Microsoft Graph](#webhook-trigger) permite-lhe reagir a um webhook da entrada.
- R [enlace de entrada de subscrição de webhook do Microsoft Graph](#webhook-input) permite-lhe listar as assinaturas existentes e, opcionalmente, atualizá-los.
- R [enlace de saída de subscrição de webhook do Microsoft Graph](#webhook-output) permite-lhe criar ou eliminar subscrições de webhook.

As associações propriamente ditas não exigem qualquer permissões do Azure AD, mas precisa solicitar permissões relevantes para o tipo de recurso que pretende reagir a. Para obter uma lista dos quais são necessárias permissões para cada tipo de recurso, consulte [permissões de subscrição](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Para obter mais informações sobre os webhooks, consulte [Trabalhar com webhooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Webhook trigger

O acionador de webhook do Microsoft Graph permite que uma função para reagir a um webhook da entrada do Microsoft Graph. Cada instância deste acionador pode reagir a um tipo de recurso do Microsoft Graph.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Utilização](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Acionador de Webhook - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Acionador de Webhook - exemplo de script do c#

O exemplo seguinte processa webhooks para mensagens de entrada do Outlook. Para utilizar um webhook acionar [criar uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para impedi-lo de expirar.

O *Function* arquivo define um acionador de webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

O código de script do c# reage a mensagens de correio recebidas e regista o corpo daquelas enviadas pelo destinatário e que contém "As funções do Azure" no assunto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Acionador de Webhook - JavaScript de exemplo

O exemplo seguinte processa webhooks para mensagens de entrada do Outlook. Para utilizar um webhook acionar [criar uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para impedi-lo de expirar.

O *Function* arquivo define um acionador de webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

O código JavaScript reage a mensagens de correio recebidas e regista o corpo daquelas enviadas pelo destinatário e que contém "As funções do Azure" no assunto:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Acionador de Webhook - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) atributo.

### <a name="webhook-trigger---configuration"></a>Acionador de Webhook - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebHookTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para a mensagem de correio. Ver [usar uma mensagem do Outlook enlace no código de saída](#outlook-output-code).|
|**tipo**||Necessário - tem de ser definido como `graphWebhook`.|
|**direção**||Necessário - tem de ser definido como `trigger`.|
|**resourceType**|**ResourceType**|Necessário - o recurso de gráfico para os quais esta função deve responder a webhooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code> -as alterações efetuadas às mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> -alterações efetuadas aos itens de raiz do OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> -as alterações feitas ao pessoas contactos no Outlook.</li><li><code>#Microsoft.Graph.Event</code> -alterações efetuadas aos itens de calendário do Outlook.</li></ul>|

> [!Note]
> Uma aplicação de funções só pode ter uma função que está registrada em relação a um determinado `resourceType` valor.

### <a name="webhook-trigger---usage"></a>Acionador de Webhook - utilização

O enlace expõe os seguintes tipos de funções de .NET:
- SDK do Microsoft Graph tipos relevantes para o tipo de recurso, tal como `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem`.
- Tipos de objeto personalizado (usando a associação de modelo estruturais)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada de Webhook

O enlace de entrada do Microsoft Graph webhook permite-lhe obter a lista de subscrições geridas por esta aplicação de função. O enlace lê do armazenamento de aplicação de função, para que ele não reflete outras subscrições criadas a partir de fora da aplicação.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuração](#webhook-input---configuration)
* [Utilização](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada de Webhook - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook de entrada - exemplo de script do c#

O exemplo seguinte obtém todas as subscrições para o utilizador chamador e elimina-los.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de subscrição e uma saída de subscrição de ligação que utiliza a ação de eliminação:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# obtém as subscrições e elimina-los:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook de entrada - exemplo de JavaScript

O exemplo seguinte obtém todas as subscrições para o utilizador chamador e elimina-los.

O *Function* arquivo define um acionador HTTP com um enlace de entrada de subscrição e uma saída de subscrição de ligação que utiliza a ação de eliminação:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código JavaScript obtém as subscrições e elimina-los:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhook de entrada - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atributo.

### <a name="webhook-input---configuration"></a>Entrada de Webhook - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebHookSubscription` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para a mensagem de correio. Ver [usar uma mensagem do Outlook enlace no código de saída](#outlook-output-code).|
|**tipo**||Necessário - tem de ser definido como `graphWebhookSubscription`.|
|**direção**||Necessário - tem de ser definido como `in`.|
|**filter**|**Filtro**| Se definido como `userFromRequest`, em seguida, o enlace só obtém as subscrições pertencentes ao utilizador que chama (apenas válidas com [acionador HTTP]).| 

### <a name="webhook-input---usage"></a>Webhook de entrada - utilização

O enlace expõe os seguintes tipos de funções de .NET:
- string[]
- Matrizes de tipo de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Saída de Webhook

A subscrição de webhook enlace de saída, pode criar, eliminar e atualizar subscrições de webhook no Microsoft Graph.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuração](#webhook-output---configuration)
* [Utilização](#webhook-output---usage)

### <a name="webhook-output---example"></a>Saída de Webhook - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook de saída - exemplo de script do c#

O exemplo seguinte cria uma subscrição. Pode [atualizar a subscrição](#webhook-subscription-refresh) para impedi-lo de expirar.

O *Function* arquivo define um acionador HTTP com um enlace utilizando a ação de criação de saída de subscrição:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código de script do c# registra um webhook que o irão notificar esta aplicação de função quando o utilizador chamador recebe uma mensagem do Outlook:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook de saída - exemplo de JavaScript

O exemplo seguinte cria uma subscrição. Pode [atualizar a subscrição](#webhook-subscription-refresh) para impedi-lo de expirar.

O *Function* arquivo define um acionador HTTP com um enlace utilizando a ação de criação de saída de subscrição:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código JavaScript registra um webhook que o irão notificar esta aplicação de função quando o utilizador chamador recebe uma mensagem do Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook de saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atributo.

### <a name="webhook-output---configuration"></a>Saída de Webhook - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebHookSubscription` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**||Necessário - o nome da variável no código de função para a mensagem de correio. Ver [usar uma mensagem do Outlook enlace no código de saída](#outlook-output-code).|
|**tipo**||Necessário - tem de ser definido como `graphWebhookSubscription`.|
|**direção**||Necessário - tem de ser definido como `out`.|
|**identity**|**Identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> -Só é válida com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code> -Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code> -Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> -Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Se necessário e apenas se for _identidade_ está definida como `userFromId`. ID principal de utilizador associado um utilizador com sessão iniciada no anteriormente.|
|**userToken**|**UserToken**|Se necessário e apenas se for _identidade_ está definida como `userFromToken`. Um token válido para a aplicação de funções. |
|**action**|**Ação**|É necessário - Especifica a ação a vinculação deve executar. Pode ser um dos seguintes valores:<ul><li><code>create</code> -Registra uma nova subscrição.</li><li><code>delete</code> -Elimina uma subscrição especificada.</li><li><code>refresh</code> -Atualiza uma subscrição especificada para mantê-la de expirar.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Se necessário e apenas se o _ação_ está definida como `create`. Especifica o recurso do Microsoft Graph que será monitorizado para que as alterações. Ver [Trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|**ChangeType**|Se necessário e apenas se o _ação_ está definida como `create`. Indica o tipo de alteração no recurso subscrito que vai criar uma notificação. Os valores suportados são: `created`, `updated`, `deleted`. É possível combinar vários valores utilizando uma lista separada por vírgulas.|

### <a name="webhook-output---usage"></a>Webhook de saída - utilização

O enlace expõe os seguintes tipos de funções de .NET:
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de subscrição de Webhook

Existem duas abordagens para atualizar as assinaturas:

- Utilize a identidade da aplicação para lidar com todas as subscrições. Isso exigirá o consentimento de administrador do Azure Active Directory. Isto pode ser utilizado por todos os idiomas suportados pelas funções do Azure.
- Utilizar a identidade associada a cada subscrição a ligação manualmente cada ID de utilizador. Isso exigirá um código personalizado para efetuar o enlace. Só pode ser utilizado por funções de .NET.

Esta secção contém um exemplo para cada uma dessas abordagens:

* [Exemplo de identidade da aplicação](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade do utilizador](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Atualização de subscrição de Webhook - exemplo de identidade da aplicação

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicação - exemplo de script do c#

O exemplo seguinte utiliza a identidade da aplicação para atualizar uma subscrição.

O *Function* define um acionador de temporizador com uma assinatura do enlace de entrada e de uma subscrição de enlace de saída:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

O código de script do c# atualiza as subscrições:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicação - exemplo de script do c#

O exemplo seguinte utiliza a identidade da aplicação para atualizar uma subscrição.

O *Function* define um acionador de temporizador com uma assinatura do enlace de entrada e de uma subscrição de enlace de saída:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

O código JavaScript atualiza as subscrições:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Atualização de subscrição de Webhook - exemplo de identidade do utilizador

O exemplo seguinte utiliza a identidade do utilizador para atualizar uma subscrição.

O *Function* ficheiro define um acionador de temporizador e difere o enlace de entrada de subscrição para o código de função:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código de script do c# atualiza as subscrições e cria o enlace de saída no código, usando a identidade de cada utilizador:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

[Acionador HTTP]: functions-bindings-http-webhook.md
[Trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
