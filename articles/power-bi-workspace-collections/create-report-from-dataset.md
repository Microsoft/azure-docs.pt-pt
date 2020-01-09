---
title: Criar relatório de um conjunto de Power BI de coleções de espaço de trabalho
description: Os relatórios de coleta de espaço de trabalho Power BI agora podem ser criados a partir de um conjunto de um DataSet em seu próprio aplicativo.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427065"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Criar um novo relatório de um conjunto de Power BI de coleções de espaços de trabalho

Os relatórios de coleta de espaço de trabalho Power BI agora podem ser criados a partir de um conjunto de um DataSet em seu próprio aplicativo.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

O método de autenticação é semelhante ao da inserção de um relatório. Ele se baseia em tokens de acesso que são específicos de um conjunto de informações. Os tokens usados para PowerBI.com são emitidos por Azure Active Directory (AAD). Power BI tokens de coleção de espaço de trabalho são emitidos por seu próprio aplicativo.

Ao criar um relatório inserido, os tokens emitidos são para um conjunto de informações específico. Os tokens devem ser associados à URL de inserção no mesmo elemento para garantir que cada um tenha um token exclusivo. Para criar um relatório inserido, os escopos *DataSet. Read e Workspace. Report. Create* devem ser fornecidos no token de acesso.

## <a name="create-access-token-needed-to-create-new-report"></a>Criar um token de acesso necessário para criar um novo relatório

Power BI coleções de espaço de trabalho usam um token de inserção, que é tokens Web JSON assinados por HMAC. Os tokens são assinados com a chave de acesso da sua coleção de espaço de trabalho Power BI. Os tokens de inserção, por padrão, são usados para fornecer acesso somente leitura a um relatório a ser inserido em um aplicativo. Os tokens de inserção são emitidos para um relatório específico e devem ser associados a uma URL de inserção.

Tokens de acesso devem ser criados no servidor, pois as chaves de acesso são usadas para assinar/criptografar os tokens. Para obter informações sobre como criar um token de acesso, consulte [Autenticando e autorizando com Power bi coleções de espaço de trabalho](app-token-flow.md). Você também pode examinar o método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) . Aqui está um exemplo de como seria o uso do SDK do .NET para Power BI.

Neste exemplo, temos nossa ID de conjunto de código para a qual desejamos criar o novo relatório. Também precisamos adicionar os escopos para *DataSet. Read e Workspace. Report. Create*.

A *classe PowerBIToken* requer que você instale o [pacote Power bi Core NuGet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalação do pacote NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#auto-completar**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Criar um novo relatório em branco

Para criar um novo relatório, a configuração de criação deve ser fornecida. Isso deve incluir o token de acesso, o embedURL e o DatasetId para o qual desejamos criar o relatório. Isso requer que você instale o pacote do NuGet [Power bi JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). O embedUrl será apenas https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Você pode usar o [exemplo de inserção de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Ele também fornece exemplos de código para as diferentes operações que estão disponíveis.

**Instalação do pacote NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

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

Chamar *powerbi. CreateReport ()* faz uma tela em branco no modo de edição aparecer dentro do elemento *div* .

![Novo relatório em branco](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Salvar novos relatórios

O relatório não será criado até que você chame a operação **salvar como** . Isso pode ser feito no menu arquivo ou no JavaScript.

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
> Um novo relatório será criado somente depois **que o salvar como** for chamado. Depois de salvar, a tela ainda mostrará o conjunto de relatórios no modo de edição e não no relatório. Você precisa recarregar o novo relatório como faria com qualquer outro relatório.

![Menu arquivo – salvar como](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Carregar o novo relatório

Para interagir com o novo relatório, você precisa inseri-lo da mesma maneira que o aplicativo incorpora um relatório regular, ou seja, um novo token deve ser emitido especificamente para o novo relatório e, em seguida, chamar o método embed.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizar o salvamento e o carregamento de um novo relatório usando o evento "Saved"

Para automatizar o processo de "salvar como" e, em seguida, carregar o novo relatório, você pode fazer uso do evento "Saved". Esse evento é acionado quando a operação de salvamento é concluída e retorna um objeto JSON que contém o novo ReportID, o nome do relatório, o antigo ReportID (se houver) e se a operação foi saveal ou Save.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Para automatizar o processo que você pode escutar no evento "Saved", pegue a nova ReportID, crie o novo token e insira o novo relatório com ele.

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

## <a name="see-also"></a>Ver também

[Introdução com exemplo](get-started-sample.md)  
[Guardar relatórios](save-reports.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote NuGet Core de Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI pacote JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
