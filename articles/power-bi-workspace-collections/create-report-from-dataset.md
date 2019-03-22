---
title: Criar um novo relatório a partir de um conjunto de dados nas coleções de área de trabalho do Power BI | Documentos da Microsoft
description: Os relatórios do Power BI coleção de área de trabalho agora podem ser criados a partir de um conjunto de dados em seu próprio aplicativo.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 9b3a91399ddcae687a982527c02d23d31eec4289
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891489"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Criar um novo relatório a partir de um conjunto de dados nas coleções de área de trabalho do Power BI

Os relatórios do Power BI coleção de área de trabalho agora podem ser criados a partir de um conjunto de dados em seu próprio aplicativo.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

O método de autenticação é semelhante da incorporação de relatórios. Ele é baseado em tokens de acesso que são específicos para um conjunto de dados. Tokens utilizados para o PowerBI.com são emitidos pelo Azure Active Directory (AAD). Tokens de coleção de área de trabalho do BI Power são emitidos pela sua própria aplicação.

Ao criar um relatório do Embedded, são os tokens emitidos para um conjunto de dados específico. Tokens devem ser associados com o URL de incorporação no mesmo elemento para garantir que cada um tem um token exclusivo. Para criar um relatório do Embedded *Dataset.Read e Workspace.Report.Create* âmbitos tem de ser fornecidos no token de acesso.

## <a name="create-access-token-needed-to-create-new-report"></a>Criar token de acesso necessário para criar novo relatório

As coleções de área de trabalho do Power BI utilizar uma incorporação token, que é HMAC assinado os JSON Web Tokens. Os tokens são assinados com a chave de acesso da sua coleção de área de trabalho do Power BI. Incorpore tokens, por predefinição, são utilizados para fornecer acesso só de leitura a um relatório para incorporar numa aplicação. Incorpore tokens emitidos para um relatório específico e devem ser associados um URL de incorporação.

Tokens de acesso devem ser criados no servidor, como as chaves de acesso são utilizadas para início de sessão/encriptar os tokens. Para obter informações sobre como criar um token de acesso, consulte [autenticação e autorização com coleções de área de trabalho do Power BI](app-token-flow.md). Também pode rever o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) método. Eis um exemplo de como isso ficaria utilizando o SDK .NET para o Power BI.

Neste exemplo, temos nossa ID de conjunto de dados que queremos criar um novo relatório no. Também precisamos de adicionar os âmbitos para *Dataset.Read e Workspace.Report.Create*.

O *classe PowerBIToken* necessita que instale o [pacote do Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalação do pacote de NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**O código c#**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Criar um novo relatório em branco

Para criar um novo relatório, deve ser fornecida a configuração de criar. Isto deve incluir o token de acesso, o embedURL e o datasetID que quer criar o relatório. Isto requer a instalação do nuget [pacote de JavaScript do Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). O embedUrl estarão prontos para serem https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Pode utilizar o [exemplo de incorporação de relatório em JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Também apresenta exemplos de código para as diferentes operações que estão disponíveis.

**Instalação do pacote de NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Código de JavaScript**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Chamar *powerbi.createReport()* faz uma tela em branco no modo de edição aparecer dentro do *div* elemento.

![Novo relatório em branco](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Guardar novos relatórios

O relatório não é criado até que chamar o **guardar como** operação. Isso pode ser feito no menu de ficheiro ou a partir de JavaScript.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> É criado um novo relatório apenas após **guardar como** é chamado. Depois de guardar, a tela mostrará ainda o conjunto de dados no modo de edição e não o relatório. É necessário recarregar o novo relatório, como faria com qualquer outro relatório.

![Ficheiro Menu - Guardar como](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Carregar o novo relatório

Para interagir com o novo relatório, terá de incorporá-lo da mesma forma que o aplicativo incorpora um relatório regular, ou seja, um novo token tem de ser emitido especificamente para o novo relatório e, em seguida, chamar o método de incorporação.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizar salvar e carregar de um novo relatório utilizando o evento de "guardado"

Para automatizar o processo de "Guardar como" e, em seguida, carregar o novo relatório, pode fazer uso do evento "guardado". Este evento é desencadeado quando o salvamento operação estiver concluída, e ele retorna um objeto Json que contém o novo Iddorelatório, nome do relatório, o Iddorelatório antigo (se houvesse um) e se a operação foi saveAs ou guardar.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Para automatizar o processo que pode escutar o evento de "guardado", faça o o Iddorelatório nova, criar o token novo e incorporar o novo relatório com o mesmo.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Consulte também

[Introdução com exemplo](get-started-sample.md)  
[Guardar relatórios](save-reports.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote de NuGut principal do Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Pacote do Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
