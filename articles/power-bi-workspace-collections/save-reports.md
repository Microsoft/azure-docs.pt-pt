---
title: Guardar relatórios nas coleções de área de trabalho do Power BI | Documentos da Microsoft
description: Saiba como pode guardar relatórios em coleções de área de trabalho do Power BI. Isto requer as permissões adequadas para funcionar com êxito.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 8de31479e55aca64a5412bb89765c9b6062c0358
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864886"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Guardar relatórios nas coleções de área de trabalho do Power BI

Saiba como pode guardar relatórios em coleções de área de trabalho do Power BI. Como salvar relatórios exige permissões adequadas para funcionar com êxito.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Dentro de coleções de área de trabalho do Power BI, pode editar os relatórios existentes e salvá-los. Também pode criar um novo relatório e guardar como um novo relatório para criar um.

Para guardar um relatório, tem primeiro de criar um token para o relatório específico com os âmbitos certos:

* Para permitir guardar Report.ReadWrite é necessário o âmbito
* Para permitir guardar como, os âmbitos de Report.Read e Workspace.Report.Copy são obrigatórios
* Para permitir guardar e guardar como, Report.ReadWrite e Workspace.Report.Copy são necessários

Respectivamente, para permitir que o direito save/guardar como botões no menu ficheiro tem de fornecer a permissão adequada na configuração de incorporação quando incorporar o relatório:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> O token de acesso também precisa dos escopos apropriados. Para obter mais informações, consulte [âmbitos](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Incorporar relatório no modo de edição

Digamos que quer incorporar um relatório no modo de edição dentro da sua aplicação, por isso, basta passar as propriedades corretas na configuração de incorporação e chamar powerbi.embed(). Fornece permissões e uma viewMode para ver o salvamento e guardar como botões quando no modo de edição. Para obter mais informações, consulte [detalhes de configuração de incorporação](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Agora, um relatório é incorporado na sua aplicação no modo de edição.

## <a name="save-report"></a>Guardar relatório

Depois de incorporar o relatório no modo de edição com o token correto e as permissões, pode guardar o relatório no menu de ficheiro ou a partir de javascript:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Guardar como

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
> Só depois *guardar como* é um novo relatório criado. Depois do guardar, a tela ainda está a mostrar o relatório antigo no modo de edição e não o novo relatório. Incorpore o relatório novo que foi criado. Incorporar o novo relatório requer um novo token de acesso à medida que são criados por cada relatório.

Em seguida, terá de carregar o novo relatório depois de um *guardar como*. A carregar o novo relatório é semelhante ao incorporar qualquer relatório.

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

## <a name="see-also"></a>Consulte também

[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Criar um novo relatório a partir de um conjunto de dados](create-report-from-dataset.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)

