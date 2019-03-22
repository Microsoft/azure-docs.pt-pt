---
title: Incorporar um relatório nas coleções de área de trabalho do Azure Power BI | Documentos da Microsoft
description: Saiba como incorporar um relatório que está nas coleções de área de trabalho do Power BI na sua aplicação.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: ecf73b08755f548c5506888a8113ad53fd8987ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877977"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Incorporar um relatório nas coleções de área de trabalho do Power BI

Saiba como incorporar um relatório que está nas coleções de área de trabalho do Power BI na sua aplicação.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Veremos como, na verdade, incorporar um relatório na sua aplicação. De que já tem um relatório que existe dentro de uma área de trabalho na sua coleção de área de trabalho. Se ainda não tiver feito esse passo, consulte [começar a utilizar coleções de área de trabalho do Power BI](get-started.md).

Pode usar o .NET (c#) ou o SDK de node. js, juntamente com JavaScript, para facilmente criar seu aplicativo com coleções de área de trabalho do Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Utilizar as chaves de acesso para utilizar as APIs REST

Para chamar a API REST, pode passar a chave de acesso que pode ser obtido a partir do portal do Azure para uma coleção de área de trabalho específica. Para obter mais informações, consulte [começar a utilizar coleções de área de trabalho do Power BI](get-started.md).

## <a name="get-a-report-id"></a>Obter um ID de relatório

Cada token de acesso baseia-se um relatório. Terá de obter o id de relatório especificado para o relatório que pretende incorporar. Isso pode ser feito com base em chamadas para o [obter relatórios](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API. Isto irá devolver a id do relatório e o url de incorporação. Isso pode ser feito utilizando o SDK de .NET do Power BI ou chamar a API REST diretamente.

### <a name="using-the-power-bi-net-sdk"></a>Com o SDK de .NET do Power BI

Quando utilizar o SDK de .NET, tem de criar uma credencial de token que baseia-se a chave de acesso a que obter a partir do portal do Azure. Isto requer que instale o [pacote NuGet da API do Power BI](https://www.nuget.org/profiles/powerbi).

**Instalação do pacote de NuGet**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**O código c#**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Chamar a API REST diretamente

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Criar um token de acesso

Tokens, de incorporação de utilização do Power BI coleções de área de trabalho que são HMAC assinado JSON Web Tokens. Os tokens são assinados com a chave de acesso da sua coleção de área de trabalho do Power BI. Incorpore tokens, por predefinição, são utilizados para fornecer acesso só de leitura a um relatório para incorporar numa aplicação. Incorpore tokens emitidos para um relatório específico e devem ser associados um URL de incorporação.

Tokens de acesso devem ser criados no servidor, como as chaves de acesso são utilizadas para início de sessão/encriptar os tokens. Para obter informações sobre como criar um token de acesso, consulte [autenticação e autorização com coleções de área de trabalho do Power BI](app-token-flow.md). Também pode rever o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) método. Eis um exemplo de como isso ficaria utilizando o SDK .NET para o Power BI.

Utilize o ID de relatório que obteve anteriormente. Depois de criar o token de incorporação, em seguida, irá utilizar a chave de acesso para gerar o token que pode utilizar a partir da perspetiva de javascript. O *classe PowerBIToken* necessita que instale o [pacote do Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalação do pacote de NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**O código c#**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Adicionar âmbitos de permissão para tokens de incorporação

Quando utilizar tokens de incorporação, pode querer restringir a utilização dos recursos que proporcionar o acesso. Por esse motivo, pode gerar um token com permissões de âmbito. Para obter mais informações, consulte [âmbitos](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Incorporar com JavaScript

Depois de ter o token de acesso e o ID do relatório, podemos pode incorporar o relatório com JavaScript. Isto requer a instalação do NuGet [pacote de JavaScript do Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). O embedUrl estarão prontos para serem https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Pode utilizar o [exemplo de incorporação de relatório em JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Também apresenta exemplos de código para as diferentes operações que estão disponíveis.

**Instalação do pacote de NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Código de JavaScript**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>Definir o tamanho dos elementos incorporados

O relatório será automaticamente incorporado consoante o tamanho do respetivo contentor. Para substituir o tamanho predefinido do embedded item, basta adicione uma estilos de atributo ou inline classe CSS para width e height.

## <a name="see-also"></a>Consulte também

[Introdução com exemplo](get-started-sample.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pacote de NuGet da API de BI Power](https://www.nuget.org/profiles/powerbi)
[pacote NuGut do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repositório de Git do Power BI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git de nó do Power BI](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
