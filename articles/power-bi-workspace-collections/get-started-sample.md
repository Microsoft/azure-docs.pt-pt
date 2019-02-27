---
title: Introdução com um exemplo
description: Neste artigo, Apresentaremos para o exemplo de introdução de get de coleções de área de trabalho do Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 4a7da4a6e7b4b395063f7e4c9a3e97d610492ef6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882676"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Introdução ao exemplo de coleções de área de trabalho do Power BI

Com o **coleções de área de trabalho do Microsoft Power BI**, pode integrar relatórios do Power BI diretamente nas suas aplicações móveis ou web. Neste artigo, iremos apresentá-lo para o **coleções de área de trabalho do Power BI** exemplo de introdução de get.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Antes de continuarmos, pretende guardar os seguintes recursos: Eles ajudá-lo ao integrar o relatórios do Power BI em suas próprias aplicações e a aplicação de exemplo demasiado.

* [Aplicação de web de área de trabalho de exemplo](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Referência da API de coleções de área de trabalho do BI Power](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI SDK de .NET](https://go.microsoft.com/fwlink/?LinkId=746472) (disponível por meio do NuGet)
* [Exemplo de incorporação de relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Antes de pode configurar e executar as coleções de área de trabalho do Power BI obter exemplo de introdução, tem de criar pelo menos um **coleção de área de trabalho** na sua subscrição do Azure. Para saber como criar uma **coleção de área de trabalho** no portal do Azure, veja [guia de introdução coleções de área de trabalho do Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Configurar a aplicação de exemplo

Vamos guiá-lo ao configurar o ambiente de desenvolvimento do Visual Studio para acessar os componentes necessários para executar a aplicação de exemplo.

1. Transfira e deszipe o [coleções de área de trabalho do Power BI - integrar um relatório numa aplicação web](https://go.microsoft.com/fwlink/?LinkId=761493) exemplo no GitHub.
2. Open **PowerBI-embedded.sln** no Visual Studio. Poderá ter de executar o **pacote de atualização** comando na consola do Gestor de pacotes NuGet para atualizar os pacotes utilizados nesta solução.
3. Compilar a solução.
4. Executar o **ProvisionSample** aplicação de consola. A aplicação de consola de exemplo, provisionar uma área de trabalho e importa um ficheiro PBIX.
5. Para Aprovisionar uma nova **área de trabalho**, selecione a opção 1, **gestão de coleção**e, em seguida, selecione a opção 6, **aprovisionar uma nova área de trabalho**
6. Para importar um novo **relatório**, selecione a opção 2, **relatório de gestão**e, em seguida, selecione a opção 3, **ficheiro de importar o PBIX Desktop numa área de trabalho**.

7. Introduza o seu **coleção de área de trabalho** nome, e **chave de acesso**. Pode obter estas no **portal do Azure**. Para saber mais sobre como obter seu **chave de acesso**, consulte [chaves de acesso de API do modo de exibição Power BI](get-started.md#view-power-bi-api-access-keys) em começar a utilizar com o Microsoft Power BI Embedded.

    ![Chaves de acesso no portal do Azure](media/get-started-sample/azure-portal.png)
8. Copie e guarde o recém-criado **ID da área de trabalho** para utilizar mais adiante neste artigo. Depois do **ID da área de trabalho** é criado, pode encontrá-lo a **portal do Azure**.

    ![ID da área de trabalho no portal do Azure](media/get-started-sample/workspace-id.png)
9. Para importar um ficheiro PBIX para sua **área de trabalho**, selecione a opção **6. Ficheiro de importação de ambiente de trabalho do PBIX numa área de trabalho existente**. Se não tiver um PBIX útil de ficheiros, pode transferir o [PBIX de exemplo de análise de revenda](https://go.microsoft.com/fwlink/?LinkID=780547).
10. Se lhe for pedido, introduza um nome amigável para a sua **conjunto de dados**.

Deverá ver uma resposta como:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Se o ficheiro PBIX contiver quaisquer ligações de consulta direta, execute a opção 7 para atualizar as cadeias de ligação.

Neste ponto, tem um relatório do Power BI PBIX importado para o seu **área de trabalho**. Agora, vamos dar uma olhada em como executar o **coleções de área de trabalho do Power BI** obter a aplicação web de exemplo de introdução.

## <a name="run-the-sample-web-app"></a>Executar a aplicação Web de exemplo

O exemplo de aplicação web é um aplicativo de exemplo que processa os relatórios importados para o seu **área de trabalho**. Eis como configurar o exemplo de aplicação web.

1. Na **PowerBI-embedded** solução do Visual Studio, com o botão direito a **EmbedSample** aplicação web e escolha **definir como projeto de arranque**.
2. Na **Web. config**, na **EmbedSample** aplicação web, edite o **appSettings**: **AccessKey**, **WorkspaceCollection** nome, e **WorkspaceId**.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Executar o **EmbedSample** aplicação web.

Depois de executar o **EmbedSample** aplicativo web, o painel de navegação à esquerda deve conter um **relatórios** menu. Para ver o relatório que importou, expanda **relatórios**e clique num relatório. Se tiver importado os [PBIX de exemplo de análise de revenda](https://go.microsoft.com/fwlink/?LinkID=780547), aplicação web de exemplo teria o seguinte aspeto:

![Barra de navegação à esquerda do exemplo na aplicação de exemplo](media/get-started-sample/sample-left-nav.png)

Depois de clicar num relatório, o **EmbedSample** aplicação web deve ter um aspeto isso:

![Relatório de exemplo exibindo dentro do aplicativo](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Explore o código de exemplo

O **coleções de área de trabalho do Microsoft Power BI** exemplo é uma aplicação de web de exemplo que mostra-lhe como integrar **Power BI** relatórios na sua aplicação. Utiliza um padrão de design Model-View-Controller (MVC) para demonstrar as práticas recomendadas. Esta secção destaca as partes do código de exemplo que pode ser explorado dentro os **PowerBI-embedded** solução de aplicação da web. O padrão Model-View-Controller (MVC) separa a Modelagem de domínio, a apresentação e as ações com base na entrada do usuário em três classes separadas: Modelo, exibe e controla. Para saber mais sobre o MVC, veja [Saiba mais sobre o ASP.NET](http://www.asp.net/mvc).

O **coleções de área de trabalho do Microsoft Power BI** código de exemplo é separado da seguinte forma. Cada secção inclui o nome de ficheiro da solução do Power BI-embedded.sln para que pode encontrar facilmente o código no exemplo.

> [!NOTE]
> Esta secção é um resumo de código de exemplo que mostra como o código foi escrito. Para ver o exemplo completo, carregue a solução do Power BI-embedded.sln no Visual Studio.

### <a name="model"></a>Modelo

O exemplo possui um **ReportsViewModel** e **ReportViewModel**.

**ReportsViewModel.cs**: Representa os relatórios do Power BI.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**: Representa um relatório do Power BI.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>Cadeia de ligação

A cadeia de ligação tem de estar no seguinte formato:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Utilizar o servidor e base de dados comuns atributos falhar. Por exemplo: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Vista

O **View** gerencia a exibição do Power BI **relatórios** e um Power BI **relatório**.

**Reports.cshtml**: Iterar **Model.Reports** para criar um **ActionLink**. O **ActionLink** é composta por da seguinte forma:

| Parte | Descrição |
| --- | --- |
| Título |Nome do relatório. |
| Cadeia de consulta |Uma ligação para o ID de relatório. |
```cshtml
<div id="reports-nav" class="panel-collapse collapse">
    <div class="panel-body">
        <ul class="nav navbar-nav">
            @foreach (var report in Model.Reports)
            {
                var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                <li class="@reportClass">
                    @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                </li>
            }
        </ul>
    </div>
</div>
```
Report.cshtml: Definir o **Model.AccessToken**e a expressão Lambda para **PowerBIReportFor**.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Controlador

**DashboardController.cs**: Cria uma passagem de PowerBIClient uma **token de aplicação**. Um JSON Web Token (JWT) é gerada a partir da **chave de assinatura** para obter o **credenciais**. O **credenciais** são utilizados para criar uma instância de **PowerBIClient**. Assim que tiver uma instância do **PowerBIClient**, pode chamar GetReports() e GetReportsAsync().


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
public ActionResult Reports()
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

        var viewModel = new ReportsViewModel
        {
            Reports = reportsResponse.Value.ToList()
        };

        return PartialView(viewModel);
    }
}
```

Tarefa<ActionResult> relatório (Iddorelatório de cadeia de caracteres)

```csharp
public async Task<ActionResult> Report(string reportId)
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
        var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
        var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

        var viewModel = new ReportViewModel
        {
            Report = report,
            AccessToken = embedToken.Generate(this.accessKey)
        };

        return View(viewModel);
    }
}
```

### <a name="integrate-a-report-into-your-app"></a>Integrar um relatório na sua aplicação

Depois de ter uma **relatório**, que utiliza um **IFrame** para incorporar o Power BI **relatório**. Aqui está um trecho de código do powerbi.js no **coleções de área de trabalho do Microsoft Power BI** exemplo.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Relatórios de filtro incorporados na aplicação

Pode filtrar um relatório incorporado utilizando uma sintaxe de URL. Para tal, adicione uma **$filter** consultar o parâmetro de cadeia de caracteres com um **eq** operador para o url de src do iFrame com o filtro especificado. Esta é a sintaxe de consulta de filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} não pode incluir espaços nem carateres especiais. {fieldValue} aceita um valor único de categoria.  

## <a name="see-also"></a>Consulte também

[Cenários comuns de coleção de área de trabalho do Microsoft Power BI](scenarios.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Incorporar um relatório](embed-report.md)  
[Criar um novo relatório a partir de um conjunto de dados](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)
