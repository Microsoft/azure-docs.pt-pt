---
title: Segurança ao nível de linha com as Coleções de Área de Trabalho do Power BI
description: Detalhes sobre a segurança ao nível da linha com as coleções de área de trabalho do Power BI
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 1f9d012754cdfa5b0f4c4e7a102d826080166484
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864631"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Segurança ao nível de linha com as Coleções de Área de Trabalho do Power BI

Segurança ao nível da linha (RLS) pode ser utilizada para restringir o acesso de utilizador para dados em particular dentro de um relatório ou conjunto de dados, permitindo que vários utilizadores diferentes utilizar o mesmo relatório enquanto todos os dados de diferentes ver. As coleções de área de trabalho do Power BI suporta conjuntos de dados configurados com RLS.

![Fluxo de segurança ao nível da linha nas coleções de área de trabalho do Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Para tirar partido da RLS, é importante que compreender três conceitos principais: Os utilizadores, funções e regras. Vamos ver mais detalhadamente cada:

**Os utilizadores** – estes são os utilizadores finais reais visualizar relatórios. Nas coleções de área de trabalho do Power BI, os utilizadores são identificados pela propriedade de nome de utilizador num Token de aplicação.

**Funções** – os utilizadores pertencem a funções. Uma função é um contentor para regras e pode ter um nome semelhante, como o "Gestor de vendas" ou "Representante de vendas". Nas coleções de área de trabalho do Power BI, os utilizadores são identificados pela propriedade funções num Token de aplicação.

**Regras** – funções têm regras e essas regras são os filtros reais que vão ser aplicadas aos dados. Isto pode ser tão simples como "país = EUA" ou algo muito mais dinâmica.

### <a name="example"></a>Exemplo

No restante deste artigo, fornecemos um exemplo de RLS de criação e, em seguida, consumimos o mesmo dentro de uma aplicação incorporada. Nosso exemplo utiliza a [exemplo de análise de revenda](https://go.microsoft.com/fwlink/?LinkID=780547) ficheiro PBIX.

![Relatório de vendas de exemplo](media/row-level-security/scenario-2.png)

O nosso exemplo de análise de revenda mostra as vendas de todas as lojas numa cadeia de varejo específico. Sem RLS, independentemente da regional que manager inicia sessão e visualiza o relatório, verão os mesmos dados. Gerenciamento sênior determinou que cada gestor regional deverá ver apenas as vendas das lojas que gerem e fazer isso, podemos utilizar a RLS.

A RLS foi criada no Power BI Desktop. Quando o conjunto de dados e o relatório estão abertos, podemos mudar para a vista de diagrama para ver o esquema:

![Diagrama do modelo no Power BI Desktop](media/row-level-security/diagram-view-3.png)

Seguem-se alguns aspetos a observar neste esquema:

* Todas as medidas, como **Total de vendas**, são armazenados no **vendas** tabela de fatos.
* Existem quatro tabelas de dimensão relacionada adicionais: **Item**, **tempo**, **Store**, e **Distrito**.
* As setas nas linhas de relação indicam de que forma filtros podem circular de uma tabela para outra. Por exemplo, se um filtro está colocado em **tempo [data]**, no esquema atual, iria filtrar apenas valores a **vendas** tabela. Nenhuma outra tabela é afetada por este filtro, uma vez que todas as setas nas linhas de relação apontam para a tabela de vendas e não para longe.
* O **Distrito** tabela indica quem é o Gestor de cada escola:
  
  ![Linhas de tabela distrito](media/row-level-security/district-table-4.png)

Com base neste esquema, se aplicar um filtro para o **Gestor distrital** coluna na tabela distrito, e se esse filtro corresponder ao utilizador visualizando o relatório, que filtrar também filtros a **Store** e  **Vendas** tabelas apenas para mostram dados para a escola específica manager.

Eis como:

1. No separador modelação, clique em **gerir funções**.  
   ![Gerir o botão de funções no Friso de modelagem](media/row-level-security/modeling-tab-5.png)
2. Criar uma nova função chamada **Manager**.  
   ![Criação de funções no Power BI Desktop](media/row-level-security/manager-role-6.png)
3. Na **Distrito** tabela introduza a seguinte expressão DAX: **[District Manager] = username)**  
   ![Expressão de filtro DAX para a tabela na função](media/row-level-security/manager-role-7.png)
4. Para se certificar de que as regras estão a funcionar, no **modelagem** separador, clique em **ver como funções**e, em seguida, introduza o seguinte:  
   ![Ver como funções](media/row-level-security/view-as-roles-8.png)

   Os relatórios irão agora mostrar dados como se estiver conectado como **Andrew Ma**.

Aplicar o filtro, conforme fizemos aqui, filtra para baixo de todos os registos na **Distrito**, **Store**, e **vendas** tabelas. No entanto, devido a direção do filtro nas relações entre **vendas** e **tempo**, **vendas** e **Item**e o **Item** e **tempo** tabelas não serão filtradas para baixo.

![Vista de diagrama com relações realçado](media/row-level-security/diagram-view-9.png)

Isso pode ser ok para este requisito, no entanto, se não Queremos que os gerentes de ver itens para os quais não têm quaisquer vendas, poderíamos Ativar filtragem cruzada bidirecional para a relação e o filtro de segurança em ambas as direções do fluxo. Isso pode ser feito editando a relação entre **vendas** e **Item**, assim:

![Relação na direção do filtro cruzado](media/row-level-security/edit-relationship-10.png)

Agora, os filtros podem circular também da tabela de vendas para o **Item** tabela:

![Ícone de direção do filtro de relação na vista de diagrama](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Se estiver a utilizar o modo DirectQuery para os seus dados, tem de ativar a filtragem bidirecional-cruzada selecionando estas duas opções:

1. **Arquivo** -> **opções e definições** -> **funcionalidades de pré-visualização** -> **Permitir filtragem cruzada em ambas as direções para o DirectQuery** .
2. **Arquivo** -> **opções e definições** -> **DirectQuery** -> **permitir medidas não restritas no modo de DirectQuery**.

Para saber mais sobre a filtragem cruzada bidirecional, transfira o [filtragem cruzada bidirecional no Power BI Desktop e no SQL Server Analysis Services 2016](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) White Paper.

Isso conclui todo o trabalho que precisa ser feito no Power BI Desktop, mas há um mais trabalho que precisa ser feito para tornar a RLS regras que definimos o trabalho no Power BI Embedded. Os utilizadores são autenticados e autorizados pela sua aplicação e tokens de aplicação são utilizados para conceder acesso a esse utilizador a um relatório do Power BI Embedded específico. Power BI Embedded não tem quaisquer informações específicas sobre quem é seu usuário. Para a RLS funcionar, terá de passar algum contexto adicional como parte do seu token de aplicação:

* **nome de utilizador** (opcional) – utilizado com RLS trata de uma cadeia de caracteres que pode ser utilizada para ajudar a identificar o utilizador ao aplicar regras RLS. Ver a utilizar a linha de segurança ao nível com o Power BI Embedded
* **funções** – uma cadeia que contém as funções para selecionar ao aplicar regras de segurança de nível de linha. Se passar mais de uma função, devem ser passadas como uma matriz de cadeia de caracteres.

Criar o token ao utilizar o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) método. Se a propriedade de nome de utilizador estiver presente, também tem de passar pelo menos um valor em funções.

Por exemplo, pode alterar o EmbedSample. DashboardController linha 55 pode ser atualizada de

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

para

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

O token de aplicação completo é parecido com este:

![Exemplo do JSON web token](media/row-level-security/app-token-string-12.png)

Agora, com todas as peças em conjunto, quando alguém inicia sessão no nosso aplicativo para ver este relatório, verão os dados que estão autorizados a ver, conforme definido pela nossa segurança ao nível da linha.

![Relatório apresentado na aplicação](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Consulte também

[Segurança ao nível da linha (RLS) com o Power](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
