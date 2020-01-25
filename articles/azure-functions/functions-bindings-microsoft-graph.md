---
title: Microsoft Graph associações para Azure Functions
description: Entenda como usar Microsoft Graph gatilhos e associações no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715026"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph associações para Azure Functions

Este artigo explica como configurar e trabalhar com Microsoft Graph gatilhos e associações no Azure Functions. Com eles, você pode usar Azure Functions para trabalhar com dados, informações e eventos do [Microsoft Graph](https://developer.microsoft.com/graph).

A extensão Microsoft Graph fornece as seguintes associações:
- Uma [Associação de entrada de token de autenticação](#token-input) permite que você interaja com qualquer API de Microsoft Graph.
- Uma [Associação de entrada de tabela do Excel](#excel-input) permite que você leia dados do Excel.
- Uma [Associação de saída de tabela do Excel](#excel-output) permite que você modifique os dados do Excel.
- Uma [Associação de entrada de arquivo do onedrive](#onedrive-input) permite que você leia arquivos do onedrive.
- Uma [Associação de saída de arquivo do onedrive](#onedrive-output) permite que você grave em arquivos no onedrive.
- Uma [Associação de saída de mensagem do Outlook](#outlook-output) permite que você envie emails por meio do Outlook.
- Uma coleção de [Microsoft Graph gatilhos e associações de webhook](#webhooks) permite reagir a eventos do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> No momento, as associações de Microsoft Graph estão em visualização para Azure Functions versão 2. x e superior. Eles não têm suporte no functions versão 1. x.

## <a name="packages"></a>Pacotes

A associação de entrada do token de autenticação é fornecida no pacote NuGet [Microsoft. Azure. webjobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) . As outras associações de Microsoft Graph são fornecidas no pacote [Microsoft. Azure. webjobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) . O código-fonte para os pacotes está no repositório GitHub [Azure-Functions-microsoftgraph-Extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Configurando as extensões

As associações de Microsoft Graph estão disponíveis por meio de _extensões de associação_. As extensões de associação são componentes opcionais para o tempo de execução de Azure Functions. Esta seção mostra como configurar o Microsoft Graph e as extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Habilitando o Functions 2,0 versão prévia

As extensões de associação estão disponíveis apenas para a versão prévia do Azure Functions 2,0. 

Para obter informações sobre como definir um aplicativo de funções para usar a versão prévia 2,0 do tempo de execução do functions, consulte [como direcionar Azure Functions versões de tempo de execução](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalando a extensão

Para instalar uma extensão do portal do Azure, navegue até um modelo ou associação que o referencie. Crie uma nova função e, na tela seleção de modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos deste cenário. Como alternativa, você pode navegar para a guia "integrar" de uma função existente e selecionar uma das associações abordadas neste artigo.

Em ambos os casos, será exibido um aviso que especifica a extensão a ser instalada. Clique em **instalar** para obter a extensão. Cada extensão precisa ser instalada apenas uma vez por aplicativo de funções. 

> [!Note] 
> O processo de instalação no portal pode levar até 10 minutos em um plano de consumo.

Se você estiver usando o Visual Studio, poderá obter as extensões instalando [os pacotes NuGet listados anteriormente neste artigo](#packages).

### <a name="configuring-authentication--authorization"></a>Configurando autenticação/autorização

As associações descritas neste artigo exigem uma identidade a ser usada. Isso permite que o Microsoft Graph imponha permissões e interações de auditoria. A identidade pode ser um usuário acessando seu aplicativo ou o próprio aplicativo. Para configurar essa identidade, configure a [autenticação/autorização do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) com o Azure Active Directory. Você também precisará solicitar permissões de recurso que suas funções exigem.

> [!Note] 
> A extensão de Microsoft Graph dá suporte apenas à autenticação do Azure AD. Os usuários precisam fazer logon com uma conta corporativa ou de estudante.

Se estiver usando o portal do Azure, você verá um aviso abaixo do prompt para instalar a extensão. O aviso solicita que você configure a autenticação/autorização do serviço de aplicativo e solicite quaisquer permissões exigidas pelo modelo ou associação. Clique em **Configurar o Azure ad agora** ou **adicionar permissões agora** , conforme apropriado.



<a name="token-input"></a>
## <a name="auth-token"></a>Símbolo de Auth

A associação de entrada do token de autenticação Obtém um token do Azure AD para um determinado recurso e o fornece ao seu código como uma cadeia de caracteres. O recurso pode ser qualquer para o qual o aplicativo tenha permissões. 

Esta seção contém as seguintes subseções:

* [Exemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuração](#auth-token---configuration)
* [Utilização](#auth-token---usage)

### <a name="auth-token---example"></a>Token de autenticação-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token de autenticação C# -exemplo de script

O exemplo a seguir obtém as informações de perfil do usuário.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de token:

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

O C# código de script usa o token para fazer uma chamada http para o Microsoft Graph e retorna o resultado:

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

#### <a name="auth-token---javascript-example"></a>Token de autenticação-exemplo de JavaScript

O exemplo a seguir obtém as informações de perfil do usuário.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de token:

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

O código JavaScript usa o token para fazer uma chamada HTTP para o Microsoft Graph e retorna o resultado.

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

### <a name="auth-token---attributes"></a>Token de autenticação-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo de [token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) .

### <a name="auth-token---configuration"></a>Token de autenticação-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Token` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório-o nome da variável usada no código de função para o token de autenticação. Consulte [usando uma associação de entrada de token de autenticação do código](#token-input-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `token`.|
|**direção**| n/d |Obrigatório-deve ser definido como `in`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**Recurso**|**resource**|Obrigatório-uma URL de recurso do Azure AD para a qual o token está sendo solicitado.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token de autenticação-uso

A associação em si não requer permissões do Azure AD, mas, dependendo de como o token é usado, talvez seja necessário solicitar permissões adicionais. Verifique os requisitos do recurso que você pretende acessar com o token.

O token sempre é apresentado ao código como uma cadeia de caracteres.

> [!Note]
> Ao desenvolver localmente com uma das opções `userFromId`, `userFromToken` ou `userFromRequest`, o token necessário pode ser [obtido manualmente](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado no cabeçalho de solicitação `X-MS-TOKEN-AAD-ID-TOKEN` de um aplicativo cliente de chamada.


<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada do Excel

A associação de entrada de tabela do Excel lê o conteúdo de uma tabela do Excel armazenada no OneDrive.

Esta seção contém as seguintes subseções:

* [Exemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuração](#excel-input---configuration)
* [Utilização](#excel-input---usage)

### <a name="excel-input---example"></a>Entrada do Excel-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Exemplo de script C# de entrada do Excel

O arquivo *Function. JSON* a seguir define um gatilho http com uma associação de entrada do Excel:

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

O código C# de script a seguir lê o conteúdo da tabela especificada e retorna-os para o usuário:

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

#### <a name="excel-input---javascript-example"></a>Entrada do Excel-exemplo de JavaScript

O arquivo *Function. JSON* a seguir define um gatilho http com uma associação de entrada do Excel:

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

O código JavaScript a seguir lê o conteúdo da tabela especificada e retorna-os para o usuário.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Entrada do Excel-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-input---configuration"></a>Entrada do Excel-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Excel` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório-o nome da variável usada no código de função para a tabela do Excel. Consulte [usando uma associação de entrada de tabela do Excel do código](#excel-input-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `excel`.|
|**direção**| n/d |Obrigatório-deve ser definido como `in`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**path**|**Caminho**|Obrigatório-o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|**WorksheetName**|A planilha na qual a tabela é encontrada.|
|**tableName**|**TableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Entrada do Excel-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando a associação de modelo estrutural)










<a name="excel-output"></a>
## <a name="excel-output"></a>Saída do Excel

A associação de saída do Excel modifica o conteúdo de uma tabela do Excel armazenada no OneDrive.

Esta seção contém as seguintes subseções:

* [Exemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuração](#excel-output---configuration)
* [Utilização](#excel-output---usage)

### <a name="excel-output---example"></a>Saída do Excel-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Saída do Excel C# -exemplo de script

O exemplo a seguir adiciona linhas a uma tabela do Excel.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída do Excel:

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

O C# código de script adiciona uma nova linha à tabela (assumida como uma coluna única) com base na entrada da cadeia de caracteres de consulta:

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

#### <a name="excel-output---javascript-example"></a>Saída do Excel-exemplo de JavaScript

O exemplo a seguir adiciona linhas a uma tabela do Excel.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída do Excel:

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

O código JavaScript a seguir adiciona uma nova linha à tabela (assumida como uma coluna única) com base na entrada da cadeia de caracteres de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Saída do Excel-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-output---configuration"></a>Saída do Excel-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Excel` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório-o nome da variável usada no código de função para o token de autenticação. Consulte [usando uma associação de saída de tabela do Excel do código](#excel-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `excel`.|
|**direção**| n/d |Obrigatório-deve ser definido como `out`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**UserId** |**userId** |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**path**|**Caminho**|Obrigatório-o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|**WorksheetName**|A planilha na qual a tabela é encontrada.|
|**tableName**|**TableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|
|**updateType**|**UpdateType**|Obrigatório-o tipo de alteração a ser feita na tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code> – substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code>-adiciona a carga ao final da tabela no OneDrive criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Saída do Excel-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando a associação de modelo estrutural)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada de arquivo

A associação de entrada de arquivo do OneDrive lê o conteúdo de um arquivo armazenado no OneDrive.

Esta seção contém as seguintes subseções:

* [Exemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuração](#file-input---configuration)
* [Utilização](#file-input---usage)

### <a name="file-input---example"></a>Entrada de arquivo-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada de arquivo C# -exemplo de script

O exemplo a seguir lê um arquivo armazenado no OneDrive.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de arquivo do onedrive:

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

O C# código de script lê o arquivo especificado na cadeia de caracteres de consulta e registra seu comprimento:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrada de arquivo-exemplo de JavaScript

O exemplo a seguir lê um arquivo armazenado no OneDrive.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de arquivo do onedrive:

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

O código JavaScript a seguir lê o arquivo especificado na cadeia de caracteres de consulta e retorna seu comprimento.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrada de arquivo-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [onedrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-input---configuration"></a>Entrada de arquivo-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `OneDrive` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório-o nome da variável usada no código de função para o arquivo. Consulte [usando uma associação de entrada de arquivo do onedrive do código](#onedrive-input-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `onedrive`.|
|**direção**| n/d |Obrigatório-deve ser definido como `in`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**path**|**Caminho**|Obrigatório-o caminho no OneDrive para o arquivo.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada de arquivo-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- byte[]
- Transmissão
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Saída do arquivo

A associação de saída de arquivo do OneDrive modifica o conteúdo de um arquivo armazenado no OneDrive.

Esta seção contém as seguintes subseções:

* [Exemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuração](#file-output---configuration)
* [Utilização](#file-output---usage)

### <a name="file-output---example"></a>Saída do arquivo-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Arquivo de saída C# -exemplo de script

O exemplo a seguir grava em um arquivo armazenado no OneDrive.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída do onedrive:

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

O C# código de script Obtém o texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest. txt, conforme definido no exemplo anterior) na raiz do onedrive do chamador:

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

#### <a name="file-output---javascript-example"></a>Saída de arquivo-exemplo de JavaScript

O exemplo a seguir grava em um arquivo armazenado no OneDrive.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída do onedrive:

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

O código JavaScript Obtém texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest. txt conforme definido na configuração acima) na raiz do OneDrive do chamador.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Saída de arquivo-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [onedrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-output---configuration"></a>Saída de arquivo-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `OneDrive` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório-o nome da variável usada no código de função para o arquivo. Consulte [usando uma associação de saída de arquivo do onedrive do código](#onedrive-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `onedrive`.|
|**direção**| n/d |Obrigatório-deve ser definido como `out`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**UserId** |**userId** |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**path**|**Caminho**|Obrigatório-o caminho no OneDrive para o arquivo.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Saída de arquivo-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- byte[]
- Transmissão
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Saída do Outlook

A associação de saída de mensagem do Outlook envia uma mensagem de email por meio do Outlook.

Esta seção contém as seguintes subseções:

* [Exemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuração](#outlook-output---configuration)
* [Utilização](#outlook-output---usage)

### <a name="outlook-output---example"></a>Saída do Outlook-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Saída do Outlook C# -exemplo de script

O exemplo a seguir envia um email por meio do Outlook.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída de mensagem do Outlook:

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

O C# código de script envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

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

#### <a name="outlook-output---javascript-example"></a>Saída do Outlook-exemplo de JavaScript

O exemplo a seguir envia um email por meio do Outlook.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída de mensagem do Outlook:

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

O código JavaScript envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

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

### <a name="outlook-output---attributes"></a>Saída do Outlook-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo do [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) .

### <a name="outlook-output---configuration"></a>Saída do Outlook-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Outlook` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Consulte [usando uma associação de saída de mensagem do Outlook do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `outlook`.|
|**direção**| n/d |Obrigatório-deve ser definido como `out`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Saída do Outlook-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar email como usuário|

A associação expõe os seguintes tipos para funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipos de objetos personalizados (usando a associação de modelo estrutural)






## <a name="webhooks"></a>Webhooks

WebHooks permitem reagir a eventos no Microsoft Graph. Para dar suporte a WebHooks, as funções são necessárias para criar, atualizar e reagir a _assinaturas de webhook_. Uma solução de webhook completa requer uma combinação das seguintes associações:
- Um [gatilho Microsoft Graph webhook](#webhook-trigger) permite reagir a um webhook de entrada.
- Uma [Microsoft Graph Associação de entrada de assinatura de webhook](#webhook-input) permite listar assinaturas existentes e, opcionalmente, atualizá-las.
- Uma [Microsoft Graph Associação de saída de assinatura de webhook](#webhook-output) permite criar ou excluir assinaturas de webhook.

As associações em si não exigem permissões do Azure AD, mas você precisa solicitar permissões relevantes para o tipo de recurso que deseja reagir. Para obter uma lista de quais permissões são necessárias para cada tipo de recurso, consulte [permissões de assinatura](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Para obter mais informações sobre WebHooks, consulte [trabalhando com WebHooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Gatilho de webhook

O gatilho Microsoft Graph webhook permite que uma função reaja a um webhook de entrada do Microsoft Graph. Cada instância deste gatilho pode reagir a um tipo de recurso do Microsoft Graph.

Esta seção contém as seguintes subseções:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Utilização](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Gatilho de webhook-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Gatilho de webhook C# -exemplo de script

O exemplo a seguir manipula WebHooks para mensagens de entrada do Outlook. Para usar um gatilho de webhook, você [cria uma assinatura](#webhook-output---example)e pode [atualizar a assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *Function. JSON* define um gatilho de webhook:

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

O C# código de script reage às mensagens de email de entrada e registra o corpo daqueles enviados pelo destinatário e contendo "Azure Functions" no assunto:

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

#### <a name="webhook-trigger---javascript-example"></a>Gatilho de webhook-exemplo de JavaScript

O exemplo a seguir manipula WebHooks para mensagens de entrada do Outlook. Para usar um gatilho de webhook, você [cria uma assinatura](#webhook-output---example)e pode [atualizar a assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *Function. JSON* define um gatilho de webhook:

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

O código JavaScript reage a mensagens de email de entrada e registra em log o corpo daqueles enviados pelo destinatário e contendo "Azure Functions" no assunto:

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

### <a name="webhook-trigger---attributes"></a>Gatilho de webhook-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) .

### <a name="webhook-trigger---configuration"></a>Gatilho de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebhookTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Consulte [usando uma associação de saída de mensagem do Outlook do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `graphWebhook`.|
|**direção**| n/d |Obrigatório-deve ser definido como `trigger`.|
|**resourceType**|**ResourceType**|Obrigatório-o recurso de grafo para o qual essa função deve responder a WebHooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code>-alterações feitas nas mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>-alterações feitas em itens raiz do OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>-alterações feitas em contatos pessoais no Outlook.</li><li><code>#Microsoft.Graph.Event</code>-alterações feitas nos itens do calendário do Outlook.</li></ul>|

> [!Note]
> Um aplicativo de funções só pode ter uma função registrada em um determinado valor de `resourceType`.

### <a name="webhook-trigger---usage"></a>Gatilho de webhook-uso

A associação expõe os seguintes tipos para funções .NET:
- Microsoft Graph tipos de SDK relevantes para o tipo de recurso, como `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem`.
- Tipos de objetos personalizados (usando a associação de modelo estrutural)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada de webhook

A associação de entrada de webhook Microsoft Graph permite que você recupere a lista de assinaturas gerenciadas por este aplicativo de funções. A associação lê do armazenamento do aplicativo de funções e, portanto, não reflete outras assinaturas criadas de fora do aplicativo.

Esta seção contém as seguintes subseções:

* [Exemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuração](#webhook-input---configuration)
* [Utilização](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada de webhook-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada de webhook C# -exemplo de script

O exemplo a seguir obtém todas as assinaturas para o usuário que está chamando e as exclui.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de assinatura e uma associação de saída de assinatura que usa a ação Excluir:

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

O C# código de script Obtém as assinaturas e as exclui:

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

#### <a name="webhook-input---javascript-example"></a>Entrada de webhook-exemplo de JavaScript

O exemplo a seguir obtém todas as assinaturas para o usuário que está chamando e as exclui.

O arquivo *Function. JSON* define um gatilho http com uma associação de entrada de assinatura e uma associação de saída de assinatura que usa a ação Excluir:

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

O código JavaScript Obtém as assinaturas e as exclui:

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

### <a name="webhook-input---attributes"></a>Entrada de webhook-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-input---configuration"></a>Entrada de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebhookSubscription` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Consulte [usando uma associação de saída de mensagem do Outlook do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `graphWebhookSubscription`.|
|**direção**| n/d |Obrigatório-deve ser definido como `in`.|
|**filter**|**Sem**| Se definido como `userFromRequest`, a associação só recuperará as assinaturas pertencentes ao usuário que está chamando (válido somente com o [gatilho http]).| 

### <a name="webhook-input---usage"></a>Entrada de webhook-uso

A associação expõe os seguintes tipos para funções .NET:
- string[]
- Matrizes de tipo de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Saída de webhook

A associação de saída de assinatura de webhook permite que você crie, exclua e atualize assinaturas de webhook no Microsoft Graph.

Esta seção contém as seguintes subseções:

* [Exemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuração](#webhook-output---configuration)
* [Utilização](#webhook-output---usage)

### <a name="webhook-output---example"></a>Saída de webhook-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Saída de webhook C# -exemplo de script

O exemplo a seguir cria uma assinatura. Você pode [atualizar a assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída de assinatura usando a ação criar:

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

O C# código de script registra um webhook que notificará esse aplicativo de funções quando o usuário que estiver chamando receber uma mensagem do Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Saída de webhook-exemplo de JavaScript

O exemplo a seguir cria uma assinatura. Você pode [atualizar a assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *Function. JSON* define um gatilho http com uma associação de saída de assinatura usando a ação criar:

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

O código JavaScript registra um webhook que notificará esse aplicativo de funções quando o usuário que estiver chamando receber uma mensagem do Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Saída de webhook-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-output---configuration"></a>Saída de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `GraphWebhookSubscription` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**name**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Consulte [usando uma associação de saída de mensagem do Outlook do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório-deve ser definido como `graphWebhookSubscription`.|
|**direção**| n/d |Obrigatório-deve ser definido como `out`.|
|**identity**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-somente válido com [gatilho http]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code>-usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code>-usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code>-usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de funções. |
|**Action**|**Ação**|Obrigatório-especifica a ação que a associação deve executar. Pode ser um dos seguintes valores:<ul><li><code>create</code>-registra uma nova assinatura.</li><li><code>delete</code>-exclui uma assinatura especificada.</li><li><code>refresh</code>-atualiza uma assinatura especificada para impedir que ela expire.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Necessário se e somente se a _ação_ for definida como `create`. Especifica o recurso de Microsoft Graph que será monitorado quanto a alterações. Consulte [trabalhando com WebHooks no Microsoft Graph]. |
|**changeType**|**ChangeType**|Necessário se e somente se a _ação_ for definida como `create`. Indica o tipo de alteração no recurso assinado que irá gerar uma notificação. Os valores com suporte são: `created`, `updated`, `deleted`. Vários valores podem ser combinados usando uma lista separada por vírgulas.|

### <a name="webhook-output---usage"></a>Saída de webhook-uso

A associação expõe os seguintes tipos para funções .NET:
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de assinatura de webhook

Há duas abordagens para atualizar assinaturas:

- Use a identidade do aplicativo para lidar com todas as assinaturas. Isso exigirá o consentimento de um administrador Azure Active Directory. Isso pode ser usado por todos os idiomas com suporte pelo Azure Functions.
- Use a identidade associada a cada assinatura ligando manualmente cada ID de usuário. Isso exigirá um código personalizado para executar a associação. Isso só pode ser usado por funções do .NET.

Esta seção contém um exemplo para cada uma dessas abordagens:

* [Exemplo de identidade do aplicativo](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade do usuário](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Atualização de assinatura de webhook-exemplo de identidade de aplicativo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#app-identity-refresh---c-script-example)
* Javascript

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade do C# aplicativo – exemplo de script

O exemplo a seguir usa a identidade do aplicativo para atualizar uma assinatura.

O *Function. JSON* define um gatilho de temporizador com uma associação de entrada de assinatura e uma associação de saída de assinatura:

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

O C# código de script atualiza as assinaturas:

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

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade do C# aplicativo – exemplo de script

O exemplo a seguir usa a identidade do aplicativo para atualizar uma assinatura.

O *Function. JSON* define um gatilho de temporizador com uma associação de entrada de assinatura e uma associação de saída de assinatura:

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

O código JavaScript atualiza as assinaturas:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Atualização de assinatura de webhook-exemplo de identidade do usuário

O exemplo a seguir usa a identidade do usuário para atualizar uma assinatura.

O arquivo *Function. JSON* define um gatilho de temporizador e adia a associação de entrada de assinatura para o código de função:

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

O C# código de script atualiza as assinaturas e cria a associação de saída no código, usando a identidade de cada usuário:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

[Gatilho HTTP]: functions-bindings-http-webhook.md
[Trabalhando com WebHooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
