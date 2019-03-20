---
title: Alternar entre ver e editar o modo para relatórios nas coleções de área de trabalho do Power BI | Documentos da Microsoft
description: Saiba como alternar entre ver e editar o modo para os relatórios de coleções de área de trabalho do Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 689ab38f2fc2d7643fb61c7137a4d88c297615db
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834188"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Alternar entre ver e editar o modo para relatórios nas coleções de área de trabalho do Power BI

Saiba como alternar entre ver e editar o modo para os relatórios de coleções de área de trabalho do Power BI.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Criar um token de acesso

Tem de criar um token de acesso que lhe dá a capacidade de ver e editar um relatório. Para editar e guardar um relatório, terá do **Report.ReadWrite** permissão do token. Para obter mais informações, consulte [autenticação e autorização nas coleções de área de trabalho do Power BI](app-token-flow.md).

> [!NOTE]
> Isto permite-lhe editar e guardar as alterações a um relatório existente. Se gostaria de ter também a função de suportar **guardar como**, precisa fornecer permissões adicionais. Para obter mais informações, consulte [âmbitos](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuração de incorporação

Tem de fornecer um viewMode e permissões para ver a botão quando no modo de edição. Para obter mais informações, consulte [detalhes de configuração de incorporação](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, no JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Isto indica ao incorporar o relatório no modo de exibição com base nas **viewMode** sendo definido como **modelos. ViewMode.View**.

## <a name="view-mode"></a>Modo de exibição

Pode utilizar o seguinte JavaScript para alternar para modo de exibição, se estiver no modo de edição.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Modo de edição

Pode utilizar o seguinte JavaScript alternar para modo de edição, se estiver na vista de modo.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Consulte também

[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de Git do Power BI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git de nó do Power BI](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
