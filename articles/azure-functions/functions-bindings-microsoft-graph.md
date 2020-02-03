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

Este artigo explica como configurar e trabalhar com Microsoft Graph gatilhos e associações no Azure Functions. Com isto, pode utilizar funções Azure para trabalhar com dados, insights e eventos a partir do [Microsoft Graph](https://developer.microsoft.com/graph).

A extensão Microsoft Graph fornece as seguintes associações:
- Uma ligação de [entrada de token auth](#token-input) permite interagir com qualquer API do Microsoft Graph.
- Uma [ligação](#excel-input) de entrada de tabela Excel permite-lhe ler dados do Excel.
- Uma [ligação](#excel-output) de saída de tabela Excel permite modificar os dados do Excel.
- Uma ligação de entrada de [ficheiro OneDrive](#onedrive-input) permite-lhe ler ficheiros a partir do OneDrive.
- Uma ligação de [saída de ficheiro OneDrive](#onedrive-output) permite-lhe escrever para ficheiros no OneDrive.
- Uma [ligação](#outlook-output) de saída de mensagem Outlook permite-lhe enviar e-mail através do Outlook.
- Uma coleção de [gatilhos e encadernações](#webhooks) do Microsoft Graph permite-lhe reagir a eventos a partir do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> No momento, as associações de Microsoft Graph estão em visualização para Azure Functions versão 2. x e superior. Eles não têm suporte no functions versão 1. x.

## <a name="packages"></a>Pacotes

A ligação de entrada auth token é fornecida no pacote [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. As outras encadernações do Microsoft Graph são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) O código fonte para as embalagens está no repositório GitHub de extensão de [funções azure-microsoftgraph.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Configurando as extensões

As encadernações do Microsoft Graph estão disponíveis através de _extensões de ligação_. As extensões de associação são componentes opcionais para o tempo de execução de Azure Functions. Esta seção mostra como configurar o Microsoft Graph e as extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Habilitando o Functions 2,0 versão prévia

As extensões de associação estão disponíveis apenas para a versão prévia do Azure Functions 2,0. 

Para obter informações sobre como definir uma aplicação de funções para utilizar a versão pré-visualização 2.0 do tempo de execução das Funções, consulte [como direcionar as versões](set-runtime-version.md)de execução das Funções Azure .

### <a name="installing-the-extension"></a>Instalando a extensão

Para instalar uma extensão do portal do Azure, navegue até um modelo ou associação que o referencie. Crie uma nova função e, na tela seleção de modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos deste cenário. Como alternativa, você pode navegar para a guia "integrar" de uma função existente e selecionar uma das associações abordadas neste artigo.

Em ambos os casos, será exibido um aviso que especifica a extensão a ser instalada. Clique em **Instalar** para obter a extensão. Cada extensão precisa ser instalada apenas uma vez por aplicativo de funções. 

> [!Note] 
> O processo de instalação no portal pode levar até 10 minutos em um plano de consumo.

Se estiver a utilizar o Visual Studio, pode obter as extensões instalando [os pacotes NuGet que estão listados anteriormente neste artigo](#packages).

### <a name="configuring-authentication--authorization"></a>Configurando autenticação/autorização

As associações descritas neste artigo exigem uma identidade a ser usada. Isso permite que o Microsoft Graph imponha permissões e interações de auditoria. A identidade pode ser um usuário acessando seu aplicativo ou o próprio aplicativo. Para configurar esta identidade, configure a [autenticação/autorização](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) do Serviço app com o Diretório Ativo Azure. Você também precisará solicitar permissões de recurso que suas funções exigem.

> [!Note] 
> A extensão de Microsoft Graph dá suporte apenas à autenticação do Azure AD. Os usuários precisam fazer logon com uma conta corporativa ou de estudante.

Se estiver usando o portal do Azure, você verá um aviso abaixo do prompt para instalar a extensão. O aviso solicita que você configure a autenticação/autorização do serviço de aplicativo e solicite quaisquer permissões exigidas pelo modelo ou associação. Clique em **Configurar O D.C. Agora** ou **adicione permissões agora,** conforme apropriado.



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

* [C#script (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token de autenticação C# -exemplo de script

O exemplo a seguir obtém as informações de perfil do usuário.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada simbólica:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada simbólica:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Token de autenticação-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Token`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório-o nome da variável usada no código de função para o token de autenticação. Consulte [A utilização de uma incadernação](#token-input-code)de token a uth a partir do código .|
|**tipo**| n/d |Obrigatório - deve ser definido para `token`.|
|**direção**| n/d |Obrigatório - deve ser definido para `in`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**Recurso**|**recurso**|Obrigatório-uma URL de recurso do Azure AD para a qual o token está sendo solicitado.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token de autenticação-uso

A associação em si não requer permissões do Azure AD, mas, dependendo de como o token é usado, talvez seja necessário solicitar permissões adicionais. Verifique os requisitos do recurso que você pretende acessar com o token.

O token sempre é apresentado ao código como uma cadeia de caracteres.

> [!Note]
> Ao desenvolver-se localmente com uma das opções `userFromId`, `userFromToken` ou `userFromRequest`, o token necessário pode ser [obtido manualmente](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado em `X-MS-TOKEN-AAD-ID-TOKEN` cabeçalho de pedido a partir de uma aplicação de cliente de chamada.


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

* [C#script (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Exemplo de script C# de entrada do Excel

O ficheiro *função.json* define um gatilho HTTP com uma ligação de entrada Excel:

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

O ficheiro *função.json* define um gatilho HTTP com uma ligação de entrada Excel:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Entrada do Excel-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Excel`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório-o nome da variável usada no código de função para a tabela do Excel. Consulte A utilização de [uma encadernação de entrada de mesa Excel a partir do código](#excel-input-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `excel`.|
|**direção**| n/d |Obrigatório - deve ser definido para `in`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**caminho**|**Caminho**|Obrigatório-o caminho no OneDrive para a pasta de trabalho do Excel.|
|**folha de cálculoNome**|**Folha de cálculoNome**|A planilha na qual a tabela é encontrada.|
|**nome da tabela**|**Nome de mesa**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|

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

* [C#script (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Saída do Excel C# -exemplo de script

O exemplo a seguir adiciona linhas a uma tabela do Excel.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída excel:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída excel:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Saída do Excel-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Excel`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório-o nome da variável usada no código de função para o token de autenticação. Consulte A utilização de [uma ligação de saída de tabela Excel a partir do código](#excel-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `excel`.|
|**direção**| n/d |Obrigatório - deve ser definido para `out`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**Userid** |**userId** |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**caminho**|**Caminho**|Obrigatório-o caminho no OneDrive para a pasta de trabalho do Excel.|
|**folha de cálculoNome**|**Folha de cálculoNome**|A planilha na qual a tabela é encontrada.|
|**nome da tabela**|**Nome de mesa**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|
|**atualizaçãoTipo**|**Tipo de atualização**|Obrigatório-o tipo de alteração a ser feita na tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code> - Substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code> - Adiciona a carga útil à extremidade da tabela no OneDrive criando novas linhas.</li></ul>|

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

* [C#script (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada de arquivo C# -exemplo de script

O exemplo a seguir lê um arquivo armazenado no OneDrive.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada de ficheiro OneDrive:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada de ficheiro OneDrive:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Entrada de arquivo-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `OneDrive`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório-o nome da variável usada no código de função para o arquivo. Consulte A utilização de [uma inserção](#onedrive-input-code)de entrada de ficheiro OneDrive a partir do código .|
|**tipo**| n/d |Obrigatório - deve ser definido para `onedrive`.|
|**direção**| n/d |Obrigatório - deve ser definido para `in`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**caminho**|**Caminho**|Obrigatório-o caminho no OneDrive para o arquivo.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada de arquivo-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- byte[]
- Stream
- Cadeia de caracteres
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

* [C#script (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Arquivo de saída C# -exemplo de script

O exemplo a seguir grava em um arquivo armazenado no OneDrive.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída OneDrive:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída OneDrive:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Saída de arquivo-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `OneDrive`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório-o nome da variável usada no código de função para o arquivo. Ver [Utilizar uma ligação de saída de ficheiro OneDrive a partir do código](#onedrive-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `onedrive`.|
|**direção**| n/d |Obrigatório - deve ser definido para `out`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**Userid** |**userId** |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**caminho**|**Caminho**|Obrigatório-o caminho no OneDrive para o arquivo.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Saída de arquivo-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação expõe os seguintes tipos para funções .NET:
- byte[]
- Stream
- Cadeia de caracteres
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

* [C#script (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Saída do Outlook C# -exemplo de script

O exemplo a seguir envia um email por meio do Outlook.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída de mensagem Outlook:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída de mensagem Outlook:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Saída do Outlook-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Outlook`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `outlook`.|
|**direção**| n/d |Obrigatório - deve ser definido para `out`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Saída do Outlook-uso

Essa associação requer as seguintes permissões do Azure AD:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar email como usuário|

A associação expõe os seguintes tipos para funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- Cadeia de caracteres
- Tipos de objetos personalizados (usando a associação de modelo estrutural)






## <a name="webhooks"></a>Webhooks

WebHooks permitem reagir a eventos no Microsoft Graph. Para suportar webhooks, são necessárias funções para criar, refrescar e reagir a _subscrições de webhook_. Uma solução de webhook completa requer uma combinação das seguintes associações:
- Um [gatilho webhook do Microsoft Graph](#webhook-trigger) permite-lhe reagir a um webhook que está a chegar.
- Uma ligação de entrada de [subscrição webhook do Microsoft Graph](#webhook-input) permite-lhe listar as subscrições existentes e reatual-las opcionalmente.
- Uma ligação de saída de [subscrição webhook do Microsoft Graph](#webhook-output) permite-lhe criar ou eliminar subscrições de webhook.

As associações em si não exigem permissões do Azure AD, mas você precisa solicitar permissões relevantes para o tipo de recurso que deseja reagir. Para obter uma lista das permissões necessárias para cada tipo de recurso, consulte [as permissões](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)de subscrição .

Para obter mais informações sobre webhooks, consulte [Trabalhar com webhooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Gatilho de webhook

O gatilho Microsoft Graph webhook permite que uma função reaja a um webhook de entrada do Microsoft Graph. Cada instância deste gatilho pode reagir a um tipo de recurso do Microsoft Graph.

Esta seção contém as seguintes subseções:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Utilização](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Gatilho de webhook-exemplo

Veja o exemplo de idioma específico:

* [C#script (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Gatilho de webhook C# -exemplo de script

O exemplo a seguir manipula WebHooks para mensagens de entrada do Outlook. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O ficheiro *função.json* define um gatilho webhook:

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

O exemplo a seguir manipula WebHooks para mensagens de entrada do Outlook. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O ficheiro *função.json* define um gatilho webhook:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Gatilho de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `GraphWebhookTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `graphWebhook`.|
|**direção**| n/d |Obrigatório - deve ser definido para `trigger`.|
|**recursosType**|**Tipo de Recursos**|Obrigatório-o recurso de grafo para o qual essa função deve responder a WebHooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code> - alterações feitas nas mensagens Do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> - alterações feitas em itens de raiz OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> - alterações nos contactos pessoais no Outlook.</li><li><code>#Microsoft.Graph.Event</code> - alterações feitas em itens de calendário do Outlook.</li></ul>|

> [!Note]
> Uma aplicação de função só pode ter uma função que está registada contra um dado `resourceType` valor.

### <a name="webhook-trigger---usage"></a>Gatilho de webhook-uso

A associação expõe os seguintes tipos para funções .NET:
- Tipos de SDK do Microsoft Graph relevantes para o tipo de recurso, tais como `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem`.
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

* [C#script (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada de webhook C# -exemplo de script

O exemplo a seguir obtém todas as assinaturas para o usuário que está chamando e as exclui.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada de subscrição e uma ligação de saída de subscrição que utiliza a ação de exclusão:

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

O ficheiro *function.json* define um gatilho HTTP com uma ligação de entrada de subscrição e uma ligação de saída de subscrição que utiliza a ação de exclusão:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [graphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Entrada de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `GraphWebhookSubscription`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `graphWebhookSubscription`.|
|**direção**| n/d |Obrigatório - deve ser definido para `in`.|
|**filtro**|**Filtro**| Se for definido para `userFromRequest`, então a ligação só recuperará as subscrições de propriedade do utilizador de chamada (válida apenas com [Gatilho HTTP]| 

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

* [C#script (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Saída de webhook C# -exemplo de script

O exemplo a seguir cria uma assinatura. Pode [renovar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída de subscrição utilizando a ação de criação:

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

O exemplo a seguir cria uma assinatura. Pode [renovar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O ficheiro *function.json* define um gatilho HTTP com uma ligação de saída de subscrição utilizando a ação de criação:

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [graphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Saída de webhook-configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `GraphWebhookSubscription`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório – o nome da variável usada no código de função para a mensagem de email. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser definido para `graphWebhookSubscription`.|
|**direção**| n/d |Obrigatório - deve ser definido para `out`.|
|**identidade**|**Identidade**|Obrigatório-a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Apenas válido com [gatilho HTTP]. Usa a identidade do usuário que está chamando.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja a propriedade <code>userId</code>.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas se _a identidade_ estiver definida para `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se e apenas se _a identidade_ estiver definida para `userFromToken`. Um token válido para o aplicativo de funções. |
|**ação**|**Ação**|Obrigatório-especifica a ação que a associação deve executar. Pode ser um dos seguintes valores:<ul><li><code>create</code> - Regista uma nova subscrição.</li><li><code>delete</code> - Elimina uma subscrição especificada.</li><li><code>refresh</code> - Refresca uma subscrição especificada para evitar que expire.</li></ul>|
|**subscriçãoRecurso**|**Recurso de Subscrição**|Necessário se e apenas se a _ação_ estiver definida para `create`. Especifica o recurso de Microsoft Graph que será monitorado quanto a alterações. Ver [Trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|**Tipo de mudança**|Necessário se e apenas se a _ação_ estiver definida para `create`. Indica o tipo de alteração no recurso assinado que irá gerar uma notificação. Os valores suportados são: `created`, `updated`, `deleted`. Vários valores podem ser combinados usando uma lista separada por vírgulas.|

### <a name="webhook-output---usage"></a>Saída de webhook-uso

A associação expõe os seguintes tipos para funções .NET:
- Cadeia de caracteres
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de assinatura de webhook

Há duas abordagens para atualizar assinaturas:

- Use a identidade do aplicativo para lidar com todas as assinaturas. Isso exigirá o consentimento de um administrador Azure Active Directory. Isso pode ser usado por todos os idiomas com suporte pelo Azure Functions.
- Use a identidade associada a cada assinatura ligando manualmente cada ID de usuário. Isso exigirá um código personalizado para executar a associação. Isso só pode ser usado por funções do .NET.

Esta seção contém um exemplo para cada uma dessas abordagens:

* [Exemplo de identidade de aplicativo](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade do utilizador](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Atualização de assinatura de webhook-exemplo de identidade de aplicativo

Veja o exemplo de idioma específico:

* [C#script (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade do C# aplicativo – exemplo de script

O exemplo a seguir usa a identidade do aplicativo para atualizar uma assinatura.

A *função.json* define um gatilho temporizador com uma ligação de entrada de subscrição e uma ligação de saída de subscrição:

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

A *função.json* define um gatilho temporizador com uma ligação de entrada de subscrição e uma ligação de saída de subscrição:

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

O ficheiro *função.json* define um gatilho temporizador e adia a ligação de entrada de subscrição ao código de função:

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

[Gatilho HTTP]: functions-bindings-http-webhook.md
[Trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
