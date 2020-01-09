---
title: Alternar entre modo de exibição e modo de edição para relatórios
description: Saiba como alternar entre a exibição e o modo de edição para seus relatórios em Power BI coleções de espaço de trabalho.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357669"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Alternar entre modo de exibição e modo de edição para relatórios em Power BI coleções de espaço de trabalho

Saiba como alternar entre a exibição e o modo de edição para seus relatórios em Power BI coleções de espaço de trabalho.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Criando um token de acesso

Você precisa criar um token de acesso que ofereça a capacidade de exibir e editar um relatório. Para editar e salvar um relatório, você precisa da permissão de token **Report. ReadWrite** . Para obter mais informações, consulte [Autenticando e autorizando em coleções de espaço de trabalho Power bi](app-token-flow.md).

> [!NOTE]
> Isso permite que você edite e salve as alterações em um relatório existente. Se você também gostar da função de suporte para **salvar como**, precisará fornecer permissões adicionais. Para obter mais informações, consulte [escopos](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuração de inserção

Você precisa fornecer permissões e um ViewMode para ver o botão salvar no modo de edição. Para obter mais informações, consulte [Inserir detalhes de configuração](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, em JavaScript:

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

Isso indica que o relatório deve ser inserido no modo de exibição com base em **ViewMode** definido como **modelos. ViewMode. View**.

## <a name="view-mode"></a>Modo de exibição

Você pode usar o JavaScript a seguir para alternar para o modo de exibição, se você estiver no modo de edição.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Modo de edição

Você pode usar o JavaScript a seguir para alternar para o modo de edição, se você estiver no modo de exibição.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Ver também

[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de git do PowerBI-node](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
