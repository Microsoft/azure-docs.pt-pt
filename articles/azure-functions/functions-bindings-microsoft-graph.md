---
title: Ligações do Microsoft Graph para Funções Azure
description: Compreenda como utilizar os gatilhos e ligações do Microsoft Graph em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 9dd4067d066362f5842b504971afbc59fd0717a3
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212202"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Ligações do Microsoft Graph para Funções Azure

Este artigo explica como configurar e trabalhar com os gatilhos e encadernações do Microsoft Graph em Funções Azure. Com estes, pode utilizar as Funções Azure para trabalhar com dados, insights e eventos a partir do Gráfico da [Microsoft.](https://developer.microsoft.com/graph)

A extensão do Microsoft Graph fornece as seguintes ligações:
- [Uma ligação de entrada simbólica](#token-input) permite interagir com qualquer API do Microsoft Graph.
- Uma [ligação de entrada de tabela Excel](#excel-input) permite-lhe ler dados do Excel.
- Uma [ligação de saída da tabela Excel](#excel-output) permite modificar os dados do Excel.
- Uma [ligação de entrada de ficheiro OneDrive](#onedrive-input) permite-lhe ler ficheiros a partir do OneDrive.
- Uma [ligação de saída de ficheiro OneDrive](#onedrive-output) permite-lhe escrever para ficheiros no OneDrive.
- Uma [ligação de saída de mensagem do Outlook](#outlook-output) permite-lhe enviar e-mail através do Outlook.
- Uma coleção de [webhook webhook](#webhooks) do Microsoft permite-lhe reagir a eventos a partir do Gráfico da Microsoft.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> As ligações do Microsoft Graph estão atualmente em pré-visualização para a versão 2.x e superior das funções do Azure Functions. Não são suportados na versão 1.x das Funções.

## <a name="packages"></a>Pacote

A ligação de entrada auth token é fornecida no pacote [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGets. As outras ligações microsoft graph são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) O código fonte para as embalagens [encontra-se no repositório GitHub, de extensão de gráfico-microsoftgraph-microsoftgraph.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Criação das extensões

As ligações do Microsoft Graph estão disponíveis através _de extensões de ligação_. As extensões de encadernação são componentes opcionais para o tempo de funcionamento das Funções Azure. Esta secção mostra como configurar as extensões do Microsoft Graph e auth token.

### <a name="enabling-functions-20"></a>Habilitação de Funções 2.0

As extensões de encadernação só estão disponíveis para as Funções Azure 2.0. 

Para obter informações sobre como definir uma aplicação de função para utilizar a versão 2.0 do tempo de execução das Funções, consulte [como direcionar as versões de tempo de execução do Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalação da extensão

Para instalar uma extensão a partir do portal Azure, navegue para um modelo ou ligação que o refira. Crie uma nova função e, enquanto no ecrã de seleção do modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos deste cenário. Em alternativa, pode navegar para o separador "Integrar" de uma função existente e selecionar uma das encadernações abrangidas por este artigo.

Em ambos os casos, aparecerá um aviso que especifica a extensão a instalar. Clique **em Instalar** para obter a extensão. Cada extensão só precisa de ser instalada uma vez por aplicação de função. 

> [!Note] 
> O processo de instalação no portal pode demorar até 10 minutos num plano de consumo.

Se estiver a utilizar o Visual Studio, poderá obter as extensões instalando [os pacotes NuGet que estão listados anteriormente neste artigo.](#packages)

### <a name="configuring-authentication--authorization"></a>Configuração de Autenticação/ Autorização

As encadernações descritas neste artigo exigem a utilização de uma identidade. Isto permite ao Microsoft Graph impor permissões e interações de auditoria. A identidade pode ser um utilizador a aceder à sua aplicação ou à própria aplicação. Para configurar esta identidade, crie [a Autenticação/Autorização do Serviço de Aplicações](../app-service/overview-authentication-authorization.md) com o Diretório Ativo Azure. Também terá de solicitar quaisquer permissões de recursos que as suas funções exijam.

> [!Note] 
> A extensão do Microsoft Graph suporta apenas a autenticação Azure AD. Os utilizadores precisam de fazer login com uma conta de trabalho ou escola.

Se estiver a utilizar o portal Azure, verá um aviso abaixo da indicação para instalar a extensão. O aviso leva-o a configurar a Autenticação/Autorização do Serviço de Aplicações e a solicitar quaisquer permissões que o modelo ou encadernação exija. Clique **em Configurar Azure AD agora** ou Adicione **permissões agora,** conforme apropriado.



<a name="token-input"></a>
## <a name="auth-token"></a>Ficha de Auth

A ligação de entrada simbólica auth recebe um token AD Azure para um determinado recurso e fornece-o ao seu código como uma cadeia. O recurso pode ser qualquer um para o qual a aplicação tenha permissões. 

Esta secção contém as seguintes subsecções:

* [Exemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuração](#auth-token---configuration)
* [Utilização](#auth-token---usage)

### <a name="auth-token---example"></a>Auth token - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth token - Exemplo de script C#

O exemplo a seguir obtém informações sobre o perfil do utilizador.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de entrada simbólica:

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

O código de script C# utiliza o token para fazer uma chamada HTTP para o Microsoft Graph e devolve o resultado:

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

#### <a name="auth-token---javascript-example"></a>Auth token - JavaScript exemplo

O exemplo a seguir obtém informações sobre o perfil do utilizador.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de entrada simbólica:

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

### <a name="auth-token---attributes"></a>Auth token - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Auth token - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Token` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para o token auth. Ver [Utilizando uma ligação de entrada simbólica auth a partir do código](#token-input-code).|
|**tipo**| n/a |Necessário - deve ser definido para `token` .|
|**direção**| n/a |Necessário - deve ser definido para `in` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**Recurso**|**recurso**|Obrigatório - Um URL de recurso AZure AD para o qual o token está sendo solicitado.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth token - uso

A ligação em si não requer permissões AD Azure, mas dependendo da forma como o token é usado, você pode precisar solicitar permissões adicionais. Verifique os requisitos do recurso a que pretende aceder com o token.

O símbolo é sempre apresentado ao código como uma corda.

> [!Note]
> Ao desenvolver-se localmente com qualquer um dos `userFromId` , `userFromToken` ou `userFromRequest` opções, o token necessário pode ser [obtido manualmente](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado no `X-MS-TOKEN-AAD-ID-TOKEN` cabeçalho de pedido a partir de uma aplicação de cliente chamador.


<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada Excel

A ligação de entrada da tabela Excel lê o conteúdo de uma tabela Excel armazenada no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuração](#excel-input---configuration)
* [Utilização](#excel-input---usage)

### <a name="excel-input---example"></a>Entrada Excel - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Entrada Excel - Exemplo de script C#

O seguinte *function.jsno* ficheiro define um gatilho HTTP com uma ligação de entrada do Excel:

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

O seguinte código de script C# lê o conteúdo da tabela especificada e devolve-os ao utilizador:

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

#### <a name="excel-input---javascript-example"></a>Entrada Excel - Exemplo JavaScript

O seguinte *function.jsno* ficheiro define um gatilho HTTP com uma ligação de entrada do Excel:

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

O código JavaScript seguinte lê o conteúdo da tabela especificada e devolve-os ao utilizador.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Entrada Excel - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Entrada Excel - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Excel` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para a tabela Excel. Ver [Utilização de uma ligação de entrada de tabela Excel a partir do código](#excel-input-code).|
|**tipo**| n/a |Necessário - deve ser definido para `excel` .|
|**direção**| n/a |Necessário - deve ser definido para `in` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**caminho**|**Caminho**|Obrigatório - o caminho no OneDrive para o livro excel.|
|**folha de cálculoName**|**Folha de cálculoName**|A folha de cálculo na qual a mesa é encontrada.|
|**tableName**|**Nome de mesa**|O nome da mesa. Se não for especificado, o conteúdo da folha de cálculo será utilizado.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Entrada Excel - utilização

Esta ligação requer as seguintes permissões AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizador|

A ligação expõe os seguintes tipos às funções .NET:
- corda[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando a ligação de modelos estruturais)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel saída

A ligação de saída do Excel modifica o conteúdo de uma tabela Excel armazenada no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuração](#excel-output---configuration)
* [Utilização](#excel-output---usage)

### <a name="excel-output---example"></a>Excel output - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel saída - Exemplo de script C#

O exemplo a seguir adiciona linhas a uma tabela Excel.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de saída do Excel:

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

O código de script C# adiciona uma nova linha à tabela (assumida como uma coluna única) com base na entrada da cadeia de consulta:

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

#### <a name="excel-output---javascript-example"></a>Saída Excel - Exemplo JavaScript

O exemplo a seguir adiciona linhas a uma tabela Excel.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de saída do Excel:

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

O seguinte código JavaScript adiciona uma nova linha à tabela (assumida como uma coluna única) com base na entrada da cadeia de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Saída Excel - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Saída Excel - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Excel` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para o token auth. Ver [Utilização de uma ligação de saída de tabela Excel a partir do código](#excel-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `excel` .|
|**direção**| n/a |Necessário - deve ser definido para `out` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**UserId** |**userId** |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**caminho**|**Caminho**|Obrigatório - o caminho no OneDrive para o livro excel.|
|**folha de cálculoName**|**Folha de cálculoName**|A folha de cálculo na qual a mesa é encontrada.|
|**tableName**|**Nome de mesa**|O nome da mesa. Se não for especificado, o conteúdo da folha de cálculo será utilizado.|
|**atualizaçãoType**|**Atualização DeType**|Obrigatório - O tipo de alteração a fazer à mesa. Pode ser um dos seguintes valores:<ul><li><code>update</code> - Substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code> - Adiciona a carga útil ao fim da tabela no OneDrive criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel output - utilização

Esta ligação requer as seguintes permissões AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso total aos ficheiros dos utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- corda[][]
- Newtonsoft.Js. Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando a ligação de modelos estruturais)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada de ficheiros

A ligação de entrada do Ficheiro OneDrive lê o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuração](#file-input---configuration)
* [Utilização](#file-input---usage)

### <a name="file-input---example"></a>Entrada de ficheiros - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada de ficheiro - Exemplo de script C#

O exemplo a seguir lê-se num ficheiro que é armazenado no OneDrive.

O *function.jsno* ficheiro define um gatilho HTTP com uma ligação de entrada de ficheiro OneDrive:

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

O código de script C# lê o ficheiro especificado na cadeia de consulta e regista o seu comprimento:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrada de ficheiros - Exemplo JavaScript

O exemplo a seguir lê-se num ficheiro que é armazenado no OneDrive.

O *function.jsno* ficheiro define um gatilho HTTP com uma ligação de entrada de ficheiro OneDrive:

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

O código JavaScript seguinte lê o ficheiro especificado na cadeia de consulta e devolve o seu comprimento.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrada de ficheiros - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Entrada de ficheiro - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `OneDrive` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para o ficheiro. Ver [Utilizar uma ligação de entrada de ficheiro OneDrive a partir do código](#onedrive-input-code).|
|**tipo**| n/a |Necessário - deve ser definido para `onedrive` .|
|**direção**| n/a |Necessário - deve ser definido para `in` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**caminho**|**Caminho**|Obrigatório - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada de ficheiro - utilização

Esta ligação requer as seguintes permissões AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizador|

A ligação expõe os seguintes tipos às funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Saída de ficheiro

A ligação de saída de ficheiro OneDrive modifica o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuração](#file-output---configuration)
* [Utilização](#file-output---usage)

### <a name="file-output---example"></a>Saída de ficheiro - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Saída de ficheiro - Exemplo de script C#

O exemplo a seguir escreve para um ficheiro que é armazenado no OneDrive.

O *function.jsno* ficheiro define um gatilho HTTP com uma ligação OneDrive:

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

O código de script C# obtém texto da cadeia de consulta e escreve-o para um ficheiro de texto (FunctionsTest.txt conforme definido no exemplo anterior) na raiz do OneDrive do autor do visitante:

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

#### <a name="file-output---javascript-example"></a>Saída de ficheiro - Exemplo JavaScript

O exemplo a seguir escreve para um ficheiro que é armazenado no OneDrive.

O *function.jsno* ficheiro define um gatilho HTTP com uma ligação OneDrive:

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

O código JavaScript obtém texto a partir da cadeia de consulta e escreve-o para um ficheiro de texto (FunctionsTest.txt tal como definido no config acima) na raiz do OneDrive do autor da chamada.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Saída de ficheiro - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Saída de ficheiro - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `OneDrive` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para ficheiro. Ver [Utilização de uma saída de ficheiro OneDrive a partir do código](#onedrive-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `onedrive` .|
|**direção**| n/a |Necessário - deve ser definido para `out` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**UserId** |**userId** |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**caminho**|**Caminho**|Obrigatório - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Saída de ficheiro - utilização

Esta ligação requer as seguintes permissões AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso total aos ficheiros dos utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Saída outlook

A vinculação da saída da mensagem do Outlook envia uma mensagem de correio através do Outlook.

Esta secção contém as seguintes subsecções:

* [Exemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuração](#outlook-output---configuration)
* [Utilização](#outlook-output---usage)

### <a name="outlook-output---example"></a>Saída outlook - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Saída outlook - exemplo de script C#

O exemplo a seguir envia um e-mail através do Outlook.

O *function.jsno* ficheiro define um gatilho HTTP com uma saída de mensagem outlook:

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

O código de script C# envia um correio do autor da chamada para um destinatário especificado na cadeia de consulta:

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

#### <a name="outlook-output---javascript-example"></a>Saída outlook - Exemplo JavaScript

O exemplo a seguir envia um e-mail através do Outlook.

O *function.jsno* ficheiro define um gatilho HTTP com uma saída de mensagem outlook:

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

O código JavaScript envia um correio do chamador para um destinatário especificado na cadeia de consulta:

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

### <a name="outlook-output---attributes"></a>Saída outlook - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Saída outlook - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Outlook` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para a mensagem de correio. Ver [Utilizar uma saída de saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `outlook` .|
|**direção**| n/a |Necessário - deve ser definido para `out` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook output - utilização

Esta ligação requer as seguintes permissões AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar correio como utilizador|

A ligação expõe os seguintes tipos às funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Js. Linq.JObject
- string
- Tipos de objetos personalizados (utilizando a ligação de modelos estruturais)






## <a name="webhooks"></a>Webhooks

Os webhooks permitem-lhe reagir a eventos no Gráfico microsoft. Para suportar webhooks, são necessárias funções para criar, refrescar e reagir a _subscrições webhook_. Uma solução webhook completa requer uma combinação das seguintes ligações:
- Um [gatilho webhook microsoft Graph](#webhook-trigger) permite-lhe reagir a um webhook que está a chegar.
- Uma [ligação de entrada de subscrição webhook do Microsoft Graph](#webhook-input) permite-lhe listar as subscrições existentes e a atualizá-las opcionalmente.
- Uma [ligação de saída de subscrição webhook do Microsoft Graph](#webhook-output) permite-lhe criar ou eliminar subscrições webhook.

As próprias ligações não requerem permissões AD Azure, mas precisa de solicitar permissões relevantes para o tipo de recurso a que pretende reagir. Para obter uma lista das permissões necessárias para cada tipo de recurso, consulte [permissões de subscrição](/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Para obter mais informações sobre webhooks, consulte [Working with webhooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Gatilho webhook

O gatilho webhook microsoft Graph permite que uma função reaja a um webhook de entrada a partir do Gráfico microsoft. Todas as instâncias deste acionador podem reagir a um tipo de recurso do Microsoft Graph.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Utilização](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook trigger - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook trigger - Exemplo de script C#

O exemplo a seguir trata de webhooks para mensagens Outlook recebidas. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [refrescar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O *function.jsno* ficheiro define um gatilho webhook:

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

O código de script C# reage a mensagens de correio recebidas e regista o corpo dos enviados pelo destinatário e contém "Funções Azure" no assunto:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook trigger - Exemplo JavaScript

O exemplo a seguir trata de webhooks para mensagens Outlook recebidas. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [refrescar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O *function.jsno* ficheiro define um gatilho webhook:

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

O código JavaScript reage a mensagens de correio recebidas e regista o corpo dos enviados pelo destinatário e contém "Funções Azure" no assunto:

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

### <a name="webhook-trigger---attributes"></a>Gatilho Webhook - atributos

Nas [bibliotecas de classe C#,](functions-dotnet-class-library.md)utilize o atributo [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Webhook trigger - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `GraphWebhookTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para a mensagem de correio. Ver [Utilizar uma saída de saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `graphWebhook` .|
|**direção**| n/a |Necessário - deve ser definido para `trigger` .|
|**RecursosType**|**ResourceType**|Requerido - o recurso gráfico para o qual esta função deve responder a webhooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code> - alterações efetuadas às mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> - alterações efetuadas nos itens de raiz OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> - alterações efetuadas aos contactos pessoais no Outlook.</li><li><code>#Microsoft.Graph.Event</code> - alterações efetuadas aos itens do calendário do Outlook.</li></ul>|

> [!Note]
> Uma aplicação de função só pode ter uma função que está registada contra um determinado `resourceType` valor.

### <a name="webhook-trigger---usage"></a>Webhook trigger - utilização

A ligação expõe os seguintes tipos às funções .NET:
- Tipos SDK do Microsoft Graph relevantes para o tipo de recurso, tais como `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem` .
- Tipos de objetos personalizados (utilizando a ligação de modelos estruturais)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada Webhook

A ligação de entrada webhook do Microsoft Graph permite-lhe recuperar a lista de subscrições geridas por esta aplicação de função. A ligação lê-se a partir do armazenamento de aplicações de funções, pelo que não reflete outras subscrições criadas de fora da app.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuração](#webhook-input---configuration)
* [Utilização](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada Webhook - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada Webhook - Exemplo de script C#

O exemplo a seguir obtém todas as subscrições para o utilizador de chamadas e elimina-as.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de entrada de subscrição e uma ligação de saída de subscrição que utiliza a ação de eliminação:

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

O código de script C# recebe as subscrições e elimina-as:

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

#### <a name="webhook-input---javascript-example"></a>Entrada Webhook - Exemplo JavaScript

O exemplo a seguir obtém todas as subscrições para o utilizador de chamadas e elimina-as.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de entrada de subscrição e uma ligação de saída de subscrição que utiliza a ação de eliminação:

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

O código JavaScript obtém as subscrições e elimina-as:

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

### <a name="webhook-input---attributes"></a>Entrada Webhook - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Entrada Webhook - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `GraphWebhookSubscription` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para a mensagem de correio. Ver [Utilizar uma saída de saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `graphWebhookSubscription` .|
|**direção**| n/a |Necessário - deve ser definido para `in` .|
|**filtro**|**Filtro**| Se for definido para `userFromRequest` , então a ligação só irá recuperar as subscrições detidas pelo utilizador chamador (válidas apenas com [o gatilho HTTP]).| 

### <a name="webhook-input---usage"></a>Entrada Webhook - utilização

A ligação expõe os seguintes tipos às funções .NET:
- corda[]
- Matrizes de tipo de objeto personalizado
- Newtonsoft.Js. Linq.JObject[]
- Microsoft.Graph.Subscrição[]





## <a name="webhook-output"></a>Saída webhook

A ligação de saída de subscrição webhook permite-lhe criar, eliminar e atualizar subscrições webhook no Gráfico do Microsoft.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuração](#webhook-output---configuration)
* [Utilização](#webhook-output---usage)

### <a name="webhook-output---example"></a>Saída Webhook - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Saída Webhook - Exemplo de script C#

O exemplo a seguir cria uma subscrição. Pode [refrescar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de saída de subscrição utilizando a ação de criação:

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

O código de script C# regista um webhook que notificará esta aplicação de função quando o utilizador chamador receber uma mensagem Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Saída Webhook - Exemplo JavaScript

O exemplo a seguir cria uma subscrição. Pode [refrescar a subscrição](#webhook-subscription-refresh) para evitar que expire.

O *function.jsem* ficheiro define um gatilho HTTP com uma ligação de saída de subscrição utilizando a ação de criação:

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

O código JavaScript regista um webhook que notificará esta aplicação de função quando o utilizador de chamada receber uma mensagem Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Saída Webhook - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Saída Webhook - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `GraphWebhookSubscription` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/a |Requerido - o nome variável utilizado no código de função para a mensagem de correio. Ver [Utilizar uma saída de saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/a |Necessário - deve ser definido para `graphWebhookSubscription` .|
|**direção**| n/a |Necessário - deve ser definido para `out` .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> - Válido apenas com [gatilho HTTP]. Utiliza a identidade do utilizador chamador.</li><li><code>userFromId</code> - Utiliza a identidade de um utilizador previamente iniciado com o ID especificado. Veja a <code>userId</code> propriedade.</li><li><code>userFromToken</code> - Utiliza a identidade representada pelo símbolo especificado. Veja a <code>userToken</code> propriedade.</li><li><code>clientCredentials</code> - Utiliza a identidade da aplicação de função.</li></ul>|
|**userId**|**UserId**  |Necessário se e somente se a _identidade_ estiver definida para `userFromId` . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e somente se a _identidade_ estiver definida para `userFromToken` . Um símbolo válido para a aplicação de função. |
|**ação**|**Ação**|Requerido - especifica a ação que a encadernação deve realizar. Pode ser um dos seguintes valores:<ul><li><code>create</code> - Regista uma nova subscrição.</li><li><code>delete</code> - Elimina uma subscrição especificada.</li><li><code>refresh</code> - Refreshes uma subscrição especificada para evitar que expire.</li></ul>|
|**subscriçãoResource**|**SubscriçãoResource**|Necessário se e apenas se a _ação_ estiver definida para `create` . Especifica o recurso Microsoft Graph que será monitorizado para alterações. Ver [Trabalhar com webhooks no Microsoft Graph]. |
|**alterarType**|**AlterarType**|Necessário se e apenas se a _ação_ estiver definida para `create` . Indica o tipo de alteração no recurso subscrito que irá levantar uma notificação. Os valores suportados são: `created` `updated` . . . `deleted` . Vários valores podem ser combinados usando uma lista separada por vírgula.|

### <a name="webhook-output---usage"></a>Saída Webhook - utilização

A ligação expõe os seguintes tipos às funções .NET:
- string
- Microsoft.Graph.Subscrição




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de subscrição webhook

Existem duas abordagens para subscrições refrescantes:

- Use a identidade da aplicação para lidar com todas as subscrições. Isto requer o consentimento de um administrador do Azure Ative Directory. Isto pode ser usado por todos os idiomas suportados por Funções Azure.
- Utilize a identidade associada a cada subscrição ligando manualmente cada ID do utilizador. Isto requer algum código personalizado para executar a encadernação. Isto só pode ser utilizado por funções .NET.

Esta secção contém um exemplo para cada uma destas abordagens:

* [Exemplo de identidade de aplicativo](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade do utilizador](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook Subscription refresh - exemplo de identidade de aplicativo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>App identity refresh - C# script exemplo

O exemplo a seguir utiliza a identidade da aplicação para refrescar uma subscrição.

O *function.jsdefine* um gatilho do temporizador com uma ligação de entrada de subscrição e uma ligação de saída de subscrição:

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

O código de script C# atualiza as subscrições:

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

### <a name="app-identity-refresh---c-script-example"></a>App identity refresh - C# script exemplo

O exemplo a seguir utiliza a identidade da aplicação para refrescar uma subscrição.

O *function.jsdefine* um gatilho do temporizador com uma ligação de entrada de subscrição e uma ligação de saída de subscrição:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook Atualização de Subscrição - exemplo de identidade do utilizador

O exemplo a seguir utiliza a identidade do utilizador para atualizar uma subscrição.

O *function.jsem* ficheiro define um gatilho do temporizador e adia a ligação da entrada de subscrição ao código de função:

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

O código de script C# atualiza as subscrições e cria a ligação de saída em código, utilizando a identidade de cada utilizador:

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
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)

[Acionador HTTP]: functions-bindings-http-webhook.md
[Trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
