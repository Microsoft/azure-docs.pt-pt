---
title: Ligações do Microsoft Graph para funções azure
description: Compreenda como utilizar os gatilhos e encadernações do Microsoft Graph nas Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76715026"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Ligações do Microsoft Graph para funções azure

Este artigo explica como configurar e trabalhar com os gatilhos e encadernações do Microsoft Graph em Funções Azure. Com isto, pode utilizar funções Azure para trabalhar com dados, insights e eventos a partir do [Microsoft Graph](https://developer.microsoft.com/graph).

A extensão do Microsoft Graph fornece as seguintes encadernações:
- Uma ligação de [entrada de token auth](#token-input) permite interagir com qualquer API do Microsoft Graph.
- Uma [ligação](#excel-input) de entrada de tabela Excel permite-lhe ler dados do Excel.
- Uma [ligação](#excel-output) de saída de tabela Excel permite modificar os dados do Excel.
- Uma ligação de entrada de [ficheiro OneDrive](#onedrive-input) permite-lhe ler ficheiros a partir do OneDrive.
- Uma ligação de [saída de ficheiro OneDrive](#onedrive-output) permite-lhe escrever para ficheiros no OneDrive.
- Uma [ligação](#outlook-output) de saída de mensagem Outlook permite-lhe enviar e-mail através do Outlook.
- Uma coleção de [gatilhos e encadernações](#webhooks) do Microsoft Graph permite-lhe reagir a eventos a partir do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> As encadernações do Microsoft Graph estão atualmente em pré-visualização para a versão 2.x e superior das Funções Azure. Não são suportados na versão 1.x das Funções.

## <a name="packages"></a>Pacotes

A ligação de entrada auth token é fornecida no pacote [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. As outras encadernações do Microsoft Graph são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) O código fonte para as embalagens está no repositório GitHub de extensão de [funções azure-microsoftgraph.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Configuração das extensões

As encadernações do Microsoft Graph estão disponíveis através de _extensões de ligação_. As extensões de ligação são componentes opcionais para o tempo de funcionamento das Funções Azure. Esta secção mostra como configurar as extensões do Microsoft Graph e do auth token.

### <a name="enabling-functions-20-preview"></a>Visualização das Funções de Habilitação 2.0

As extensões de encadernação estão disponíveis apenas para pré-visualização das Funções Azure 2.0. 

Para obter informações sobre como definir uma aplicação de funções para utilizar a versão pré-visualização 2.0 do tempo de execução das Funções, consulte [como direcionar as versões](set-runtime-version.md)de execução das Funções Azure .

### <a name="installing-the-extension"></a>Instalação da extensão

Para instalar uma extensão do portal Azure, navegue para um modelo ou encadernação que o refira. Crie uma nova função e, enquanto no ecrã de seleção do modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos deste cenário. Em alternativa, pode navegar para o separador "Integrar" de uma função existente e selecionar uma das encadernações abrangidas por este artigo.

Em ambos os casos, aparecerá um aviso que especifica a extensão a instalar. Clique em **Instalar** para obter a extensão. Cada extensão só precisa de ser instalada uma vez por aplicação de função. 

> [!Note] 
> O processo de instalação no portal pode demorar até 10 minutos num plano de consumo.

Se estiver a utilizar o Visual Studio, pode obter as extensões instalando [os pacotes NuGet que estão listados anteriormente neste artigo](#packages).

### <a name="configuring-authentication--authorization"></a>Configuração da autenticação / autorização

As encadernações descritas neste artigo exigem a sua identidade. Isto permite que o Microsoft Graph aplique permissões e interações de auditoria. A identidade pode ser um utilizador que acede à sua aplicação ou à própria aplicação. Para configurar esta identidade, configure a [autenticação/autorização](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) do Serviço app com o Diretório Ativo Azure. Também terá de solicitar quaisquer permissões de recursos que as suas funções exijam.

> [!Note] 
> A extensão do Microsoft Graph apenas suporta a autenticação Azure AD. Os utilizadores precisam de fazer login com uma conta de trabalho ou escola.

Se estiver a utilizar o portal Azure, verá um aviso abaixo do pedido para instalar a extensão. O aviso leva-o a configurar a autenticação/autorização do serviço de aplicações e a solicitar quaisquer permissões que o modelo ou encadernação exija. Clique em **Configurar O D.C. Agora** ou **adicione permissões agora,** conforme apropriado.



<a name="token-input"></a>
## <a name="auth-token"></a>Símbolo de Auth

A ligação de entrada de token auth recebe um token Azure AD para um determinado recurso e fornece-o ao seu código como uma cadeia. O recurso pode ser qualquer um para o qual o pedido tem permissões. 

Esta secção contém as seguintes subsecções:

* [Exemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuração](#auth-token---configuration)
* [Utilização](#auth-token---usage)

### <a name="auth-token---example"></a>Auth token - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth token - C# exemplo de script

O exemplo seguinte obtém informações sobre o perfil do utilizador.

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

O código de script C# usa o símbolo para fazer uma chamada HTTP para o Microsoft Graph e devolve o resultado:

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

#### <a name="auth-token---javascript-example"></a>Auth token - Exemplo JavaScript

O exemplo seguinte obtém informações sobre o perfil do utilizador.

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

O código JavaScript utiliza o símbolo para fazer uma chamada HTTP para o Microsoft Graph e devolve o resultado.

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

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Auth token - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Token` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Necessário - o nome variável utilizado no código de função para o símbolo do auth. Consulte [A utilização de uma incadernação](#token-input-code)de token a uth a partir do código .|
|**tipo**| n/d |Obrigatório - deve ser `token`definido para .|
|**direção**| n/d |Obrigatório - deve ser `in`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**Recurso**|**recurso**|Necessário - Um URL de recurso Azure AD para o qual o símbolo está a ser solicitado.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth token - uso

A ligação em si não requer permissões ads Azure, mas dependendo da forma como o token é usado, você pode precisar solicitar permissões adicionais. Verifique os requisitos do recurso a que pretende aceder com o símbolo.

O símbolo é sempre apresentado ao código como uma corda.

> [!Note]
> Ao desenvolver-se localmente `userFromToken` `userFromRequest` com uma das `userFromId`opções ou opções necessárias, o token necessário pode ser [obtido manualmente](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado no `X-MS-TOKEN-AAD-ID-TOKEN` cabeçalho de pedido a partir de uma aplicação de cliente de chamada.


<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada excel

A ligação de entrada da tabela Excel lê o conteúdo de uma tabela Excel armazenada no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuração](#excel-input---configuration)
* [Utilização](#excel-input---usage)

### <a name="excel-input---example"></a>Excel entrada - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel entrada - C# exemplo de script

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

#### <a name="excel-input---javascript-example"></a>Excel entrada - Exemplo JavaScript

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

O seguinte código JavaScript lê o conteúdo da tabela especificada e devolve-os ao utilizador.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel entrada - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Entrada Excel - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Excel` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para a tabela Excel. Consulte A utilização de [uma encadernação de entrada de mesa Excel a partir do código](#excel-input-code).|
|**tipo**| n/d |Obrigatório - deve ser `excel`definido para .|
|**direção**| n/d |Obrigatório - deve ser `in`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**caminho**|**Caminho**|Necessário - o caminho no OneDrive para o livro Excel.|
|**folha de cálculoNome**|**Folha de cálculoNome**|A folha de cálculo na qual a mesa é encontrada.|
|**tableName**|**Nome de mesa**|O nome da mesa. Se não especificado, o conteúdo da folha de cálculo será utilizado.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel entrada - utilização

Esta ligação requer as seguintes permissões da AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- corda[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando a ligação do modelo estrutural)










<a name="excel-output"></a>
## <a name="excel-output"></a>Saída excel

A ligação de saída excel modifica o conteúdo de uma tabela Excel armazenada no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuração](#excel-output---configuration)
* [Utilização](#excel-output---usage)

### <a name="excel-output---example"></a>Saída excel - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel output - C# script exemplo

O exemplo seguinte adiciona linhas a uma tabela Excel.

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

O código de script C# adiciona uma nova linha à tabela (assumidamente uma coluna única) com base na entrada da cadeia de consulta:

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

#### <a name="excel-output---javascript-example"></a>Saída excel - Exemplo JavaScript

O exemplo seguinte adiciona linhas a uma tabela Excel.

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

O seguinte código JavaScript adiciona uma nova linha à tabela (assumidamente uma coluna única) com base na entrada da cadeia de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Saída excel - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Saída excel - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Excel` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Necessário - o nome variável utilizado no código de função para o símbolo do auth. Consulte A utilização de [uma ligação de saída de tabela Excel a partir do código](#excel-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `excel`definido para .|
|**direção**| n/d |Obrigatório - deve ser `out`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**Userid** |**userId** |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**caminho**|**Caminho**|Necessário - o caminho no OneDrive para o livro Excel.|
|**folha de cálculoNome**|**Folha de cálculoNome**|A folha de cálculo na qual a mesa é encontrada.|
|**tableName**|**Nome de mesa**|O nome da mesa. Se não especificado, o conteúdo da folha de cálculo será utilizado.|
|**atualizaçãoTipo**|**Tipo de atualização**|Necessário - O tipo de alteração a fazer à mesa. Pode ser um dos seguintes valores:<ul><li><code>update</code>- Substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code>- Adiciona a carga útil à extremidade da tabela no OneDrive criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Saída excel - utilização

Esta ligação requer as seguintes permissões da AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso total aos ficheiros dos utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- corda[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando a ligação do modelo estrutural)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada de ficheiros

A ligação de entrada oneDrive File lê o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuração](#file-input---configuration)
* [Utilização](#file-input---usage)

### <a name="file-input---example"></a>Entrada de ficheiros - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada de ficheiros - C# exemplo de script

O exemplo que se segue lê um ficheiro que é armazenado no OneDrive.

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

O código de script C# lê o ficheiro especificado na corda de consulta e regista o seu comprimento:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrada de ficheiros - Exemplo JavaScript

O exemplo que se segue lê um ficheiro que é armazenado no OneDrive.

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

O seguinte código JavaScript lê o ficheiro especificado na corda de consulta e devolve o seu comprimento.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrada de ficheiros - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Entrada de ficheiros - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `OneDrive` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para o ficheiro. Consulte A utilização de [uma inserção](#onedrive-input-code)de entrada de ficheiro OneDrive a partir do código .|
|**tipo**| n/d |Obrigatório - deve ser `onedrive`definido para .|
|**direção**| n/d |Obrigatório - deve ser `in`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**caminho**|**Caminho**|Necessário - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada de ficheiros - utilização

Esta ligação requer as seguintes permissões da AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- byte[]
- Transmitir em fluxo
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Saída de ficheiros

A ligação de saída do ficheiro OneDrive modifica o conteúdo de um ficheiro armazenado no OneDrive.

Esta secção contém as seguintes subsecções:

* [Exemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuração](#file-output---configuration)
* [Utilização](#file-output---usage)

### <a name="file-output---example"></a>Saída de ficheiros - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Saída de ficheiros - C# script exemplo

O exemplo seguinte escreve para um ficheiro que é armazenado no OneDrive.

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

O código de script C# obtém texto da cadeia de consulta e escreve-o para um ficheiro de texto (FunctionsTest.txt, tal como definido no exemplo anterior) na raiz do OneDrive do chamador:

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

#### <a name="file-output---javascript-example"></a>Saída de ficheiros - Exemplo JavaScript

O exemplo seguinte escreve para um ficheiro que é armazenado no OneDrive.

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

O código JavaScript obtém texto da cadeia de consulta e escreve-o para um ficheiro de texto (FunctionsTest.txt, tal como definido no config acima) na raiz do OneDrive do chamador.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Saída de ficheiros - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Saída de ficheiros - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `OneDrive` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para o ficheiro. Ver [Utilizar uma ligação de saída de ficheiro OneDrive a partir do código](#onedrive-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `onedrive`definido para .|
|**direção**| n/d |Obrigatório - deve ser `out`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**Userid** |**userId** |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**caminho**|**Caminho**|Necessário - o caminho no OneDrive para o ficheiro.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Saída de ficheiros - utilização

Esta ligação requer as seguintes permissões da AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso total aos ficheiros dos utilizadores|

A ligação expõe os seguintes tipos às funções .NET:
- byte[]
- Transmitir em fluxo
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Saída de outlook

A ligação de saída de mensagem Outlook envia uma mensagem de correio através do Outlook.

Esta secção contém as seguintes subsecções:

* [Exemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuração](#outlook-output---configuration)
* [Utilização](#outlook-output---usage)

### <a name="outlook-output---example"></a>Saída de outlook - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Saída de outlook - C# script exemplo

O exemplo seguinte envia um e-mail através do Outlook.

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

O código de script C# envia um correio do chamador para um destinatário especificado na cadeia de consulta:

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

#### <a name="outlook-output---javascript-example"></a>Saída do outlook - Exemplo JavaScript

O exemplo seguinte envia um e-mail através do Outlook.

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

### <a name="outlook-output---attributes"></a>Saída de outlook - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Saída do outlook - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Outlook` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para a mensagem de correio. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `outlook`definido para .|
|**direção**| n/d |Obrigatório - deve ser `out`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Saída de perspetivas - utilização

Esta ligação requer as seguintes permissões da AD Azure:

|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar correio como utilizador|

A ligação expõe os seguintes tipos às funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipos de objetos personalizados (utilizando a ligação do modelo estrutural)






## <a name="webhooks"></a>Webhooks

Os webhooks permitem-lhe reagir a eventos no Microsoft Graph. Para suportar webhooks, são necessárias funções para criar, refrescar e reagir a _subscrições de webhook_. Uma solução completa de webhook requer uma combinação das seguintes encadernações:
- Um [gatilho webhook do Microsoft Graph](#webhook-trigger) permite-lhe reagir a um webhook que está a chegar.
- Uma ligação de entrada de [subscrição webhook do Microsoft Graph](#webhook-input) permite-lhe listar as subscrições existentes e reatual-las opcionalmente.
- Uma ligação de saída de [subscrição webhook do Microsoft Graph](#webhook-output) permite-lhe criar ou eliminar subscrições de webhook.

As próprias encadernações não requerem permissões ads azure, mas você precisa solicitar permissões relevantes para o tipo de recurso a que deseja reagir. Para obter uma lista das permissões necessárias para cada tipo de recurso, consulte [as permissões](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)de subscrição .

Para obter mais informações sobre webhooks, consulte [Trabalhar com webhooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Gatilho webhook

O gatilho webhook do Microsoft Graph permite que uma função reaja a um webhook que chega a partir do Microsoft Graph. Todas as instâncias deste acionador podem reagir a um tipo de recurso do Microsoft Graph.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Utilização](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook trigger - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook trigger - C# script exemplo

O exemplo que se segue trata dos webhooks para as mensagens de entrada do Outlook. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para evitar que expire.

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

O código de script C# reage às mensagens de correio e regista o corpo das enviadas pelo destinatário e que contém "Funções Azure" no assunto:

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

O exemplo que se segue trata dos webhooks para as mensagens de entrada do Outlook. Para utilizar um gatilho webhook [cria uma subscrição](#webhook-output---example), e pode [atualizar a subscrição](#webhook-subscription-refresh) para evitar que expire.

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

O código JavaScript reage às mensagens de correio e regista o corpo das enviadas pelo destinatário e contém "Funções Azure" no assunto:

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

### <a name="webhook-trigger---attributes"></a>Webhook trigger - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Gatilho webhook - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `GraphWebhookTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para a mensagem de correio. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `graphWebhook`definido para .|
|**direção**| n/d |Obrigatório - deve ser `trigger`definido para .|
|**recursosType**|**Tipo de Recursos**|Necessário - o recurso gráfico para o qual esta função deve responder aos ganchos web. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code>- alterações feitas nas mensagens Do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>- alterações feitas em itens de raiz OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>- alterações nos contactos pessoais no Outlook.</li><li><code>#Microsoft.Graph.Event</code>- alterações feitas em itens de calendário do Outlook.</li></ul>|

> [!Note]
> Uma aplicação de função só pode `resourceType` ter uma função que está registada com um determinado valor.

### <a name="webhook-trigger---usage"></a>Webhook trigger - utilização

A ligação expõe os seguintes tipos às funções .NET:
- Tipos de SDK do Microsoft Graph `Microsoft.Graph.Message` relevantes para o tipo de recurso, tais como ou `Microsoft.Graph.DriveItem`.
- Tipos de objetos personalizados (utilizando a ligação do modelo estrutural)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada webhook

A ligação de entrada web hook do Microsoft Graph permite-lhe recuperar a lista de subscrições geridas por esta aplicação de função. A encadernação lê-se no armazenamento de aplicações de funções, pelo que não reflete outras subscrições criadas a partir de fora da app.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuração](#webhook-input---configuration)
* [Utilização](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada webhook - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada webhook - C# exemplo de script

O exemplo seguinte recebe todas as subscrições para o utilizador de chamadas e elimina-as.

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

O exemplo seguinte recebe todas as subscrições para o utilizador de chamadas e elimina-as.

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

O código JavaScript recebe as subscrições e elimina-as:

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

### <a name="webhook-input---attributes"></a>Entrada webhook - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [graphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Entrada Webhook - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `GraphWebhookSubscription` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para a mensagem de correio. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `graphWebhookSubscription`definido para .|
|**direção**| n/d |Obrigatório - deve ser `in`definido para .|
|**filtro**|**Filtro**| Se definido `userFromRequest`para , então a ligação só recuperará subscrições pertencentes ao utilizador de chamada (válido apenas com [gatilho HTTP]).| 

### <a name="webhook-input---usage"></a>Entrada Webhook - utilização

A ligação expõe os seguintes tipos às funções .NET:
- cadeia[]
- Matrizes personalizadas do tipo de objeto
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscrição[]





## <a name="webhook-output"></a>Saída webhook

A ligação de saída de subscrição webhook permite-lhe criar, eliminar e atualizar subscrições de webhook no Microsoft Graph.

Esta secção contém as seguintes subsecções:

* [Exemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuração](#webhook-output---configuration)
* [Utilização](#webhook-output---usage)

### <a name="webhook-output---example"></a>Saída webhook - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Saída webhook - C# script exemplo

O exemplo seguinte cria uma subscrição. Pode [renovar a subscrição](#webhook-subscription-refresh) para evitar que expire.

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

O código de script C# regista um webhook que notificará esta aplicação de função quando o utilizador de chamadas receber uma mensagem Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Saída webhook - Exemplo JavaScript

O exemplo seguinte cria uma subscrição. Pode [renovar a subscrição](#webhook-subscription-refresh) para evitar que expire.

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

O código JavaScript regista um webhook que notificará esta aplicação de função quando o utilizador de chamadas receber uma mensagem Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Saída webhook - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [graphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Saída webhook - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `GraphWebhookSubscription` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**nome**| n/d |Obrigatório - o nome variável utilizado no código de função para a mensagem de correio. Ver Utilizar uma saída de [saída de mensagem Outlook a partir do código](#outlook-output-code).|
|**tipo**| n/d |Obrigatório - deve ser `graphWebhookSubscription`definido para .|
|**direção**| n/d |Obrigatório - deve ser `out`definido para .|
|**identidade**|**Identidade**|Obrigatório - A identidade que será usada para realizar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>- Apenas válido com [gatilho HTTP]. Usa a identidade do utilizador que chama.</li><li><code>userFromId</code>- Utiliza a identidade de um utilizador previamente registado com o ID especificado. Veja <code>userId</code> a propriedade.</li><li><code>userFromToken</code>- Utiliza a identidade representada pelo símbolo especificado. Veja <code>userToken</code> a propriedade.</li><li><code>clientCredentials</code>- Utiliza a identidade da aplicação de funções.</li></ul>|
|**userId**|**Userid**  |Necessário se e apenas `userFromId`se a _identidade_ for definida para . Um ID principal do utilizador associado a um utilizador previamente iniciado.|
|**userToken**|**UserToken**|Necessário se e apenas `userFromToken`se a _identidade_ for definida para . Um símbolo válido para a aplicação de funções. |
|**ação**|**Ação**|Obrigatório - especifica a ação que a encadernação deve realizar. Pode ser um dos seguintes valores:<ul><li><code>create</code>- Regista uma nova subscrição.</li><li><code>delete</code>- Elimina uma subscrição especificada.</li><li><code>refresh</code>- Atualiza uma subscrição especificada para evitar que expire.</li></ul>|
|**subscriçãoRecurso**|**Recurso de Subscrição**|Necessário se e _action_ apenas se `create`a ação estiver definida para . Especifica o recurso Microsoft Graph que será monitorizado para alterações. Ver [Trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|**Tipo de mudança**|Necessário se e _action_ apenas se `create`a ação estiver definida para . Indica o tipo de alteração no recurso subscrito que irá levantar uma notificação. Os valores suportados `updated` `deleted`são: `created`, . Vários valores podem ser combinados usando uma lista separada da vírvia.|

### <a name="webhook-output---usage"></a>Saída webhook - utilização

A ligação expõe os seguintes tipos às funções .NET:
- string
- Microsoft.Graph.Subscrição




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de subscrição webhook

Existem duas abordagens para subscrições refrescantes:

- Utilize a identidade da aplicação para lidar com todas as subscrições. Isto requer o consentimento de um administrador do Azure Ative Directory. Isto pode ser usado por todas as línguas suportadas pelas Funções Azure.
- Utilize a identidade associada a cada subscrição ligando manualmente cada ID do utilizador. Isto requer algum código personalizado para executar a ligação. Isto só pode ser utilizado por funções .NET.

Esta secção contém um exemplo para cada uma destas abordagens:

* [Exemplo de identidade de aplicativo](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade do utilizador](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Atualização de subscrição webhook - exemplo de identidade de aplicativo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicativo - C# script exemplo

O exemplo seguinte utiliza a identidade da aplicação para renovar uma subscrição.

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

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicativo - C# script exemplo

O exemplo seguinte utiliza a identidade da aplicação para renovar uma subscrição.

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Atualização de subscrição webhook - exemplo de identidade do utilizador

O exemplo que se segue utiliza a identidade do utilizador para renovar uma subscrição.

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
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

[Acionador HTTP]: functions-bindings-http-webhook.md
[Trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
