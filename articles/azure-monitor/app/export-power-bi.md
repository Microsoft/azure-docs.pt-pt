---
title: Exportar para o Power BI do Application Insights do Azure | Documentos da Microsoft
description: Consultas de análise podem ser apresentadas no Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: 13156b3419d961850c8e4e408a9cf565a5f957ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086399"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed do Power BI do Application Insights
[Power BI](https://www.powerbi.com/) é um conjunto de ferramentas de negócio que o ajuda a analisar dados e partilhar informações. Dashboards avançados estão disponíveis em todos os dispositivos. Pode combinar dados de várias origens, incluindo consultas de análise de [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Existem três métodos de exportação de dados do Application Insights para o Power BI:

* [**Exportar consultas de análise**](#export-analytics-queries). Este é o método preferencial. Escrever qualquer consulta que pretende e exportá-lo para o Power BI. Pode colocar esta consulta num dashboard, juntamente com quaisquer outros dados.
* [**A exportação contínua e o Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Esse método é útil se desejar armazenar os dados por longos períodos de tempo. Se não tiver um requisito de retenção de dados expandidos, use o método de consulta de análise de exportação. A exportação contínua e o Stream Analytics envolve mais trabalho para configurar e a sobrecarga de armazenamento adicional.
* **Adaptador do Power BI**. O conjunto de gráficos está predefinido, mas pode adicionar suas próprias consultas de quaisquer outras fontes.

> [!NOTE]
> O Power BI está agora **preterido**. Os gráficos predefinidos para esta solução são preenchidos por consultas uneditable estáticas. Não tem a capacidade de editar estas consultas e dependendo determinadas propriedades dos seus dados é possível que a ligação ao Power BI para ter êxito, mas nenhum dado é preenchido. Isso se deve critérios de exclusão definidos na consulta embutido em código. Embora esta solução ainda pode funcionar para alguns clientes, devido à falta de flexibilidade do adaptador a solução recomendada consiste em utilizar o [ **exportar a consulta do Analytics** ](#export-analytics-queries) funcionalidade.

## <a name="export-analytics-queries"></a>Consultas de análise de exportação
Esta rota permite-lhe escrever qualquer consulta de análise como ou, exportar a partir de Funis de utilização e, em seguida, exportar que a um dashboard do Power BI. (Pode adicionar ao dashboard criado pelo adaptador.)

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar a sua consulta do Application Insights, utilize a versão de área de trabalho do Power BI. Em seguida, pode publicá-lo para a web ou para sua área de trabalho do Power BI na cloud. 

Instale [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta do Analytics
1. [Abrir o Analytics e escrever a sua consulta](../../azure-monitor/log-query/get-started-portal.md).
2. Testar e refinar a consulta até estar satisfeito com os resultados. Certifique-se de que a consulta é executada corretamente no Analytics antes de exportá-lo.
3. Sobre o **exportar** menu, escolha **Power BI (M)**. Guarde o ficheiro de texto.
   
    ![Captura de ecrã de análise, com o menu de exportação realçado](./media/export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas, sob **View**, selecione **Editor avançado**.

    Cole o script de idioma do M exportado no Advanced Editor.

    ![Captura de ecrã do Power BI Desktop, com o Editor avançado realçado](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Para permitir que o Power BI para aceder ao Azure, poderá ter de fornecer credenciais. Uso **conta institucional** para iniciar sessão com a sua conta Microsoft.
   
    ![Caixa de diálogo de captura de ecrã do Power BI definições da consulta](./media/export-power-bi/power-bi-import-sign-in.png)

    Se precisar de verificar as credenciais, utilize o **definições da origem de dados** comando de menu no editor de consultas. Certifique-se de que especifique as credenciais que utiliza para o Azure, que pode ser diferente das credenciais da sua para o Power BI.
6. Escolha uma visualização para sua consulta e selecione os campos para o eixo x, y e segmentar dimensão.
   
    ![Opções de visualização de captura de ecrã do Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publicar o relatório para a área de trabalho do Power BI na cloud. A partir daí, pode incorporar uma versão sincronizada nos outros páginas da web.
   
    ![Captura de ecrã do Power BI Desktop, com o botão Publish realçado](./media/export-power-bi/publish-power-bi.png)
8. Atualizar o relatório manualmente em intervalos ou configurar uma atualização agendada na página de opções.

### <a name="export-a-funnel"></a>Exportar um funil
1. [Tornar seu funil](../../azure-monitor/app/usage-funnels.md).
2. Selecione **do Power BI**.

   ![Botão de captura de ecrã do Power BI](./media/export-power-bi/button.png)

3. No Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas, sob **View**, selecione **Editor avançado**.

   ![Captura de ecrã do Power BI Desktop, com o botão de consulta em branco realçado](./media/export-power-bi/blankquery.png)

   Cole o script de idioma do M exportado no Advanced Editor. 

   ![Captura de ecrã do Power BI Desktop, com o Editor avançado realçado](./media/export-power-bi/advancedquery.png)

4. Selecionar itens da consulta e escolha uma visualização de funil.

   ![Opções de visualização de captura de ecrã do Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Alterar o título para torná-lo com significado e publicar o relatório para a área de trabalho do Power BI na cloud. 

   ![Captura de ecrã do Power BI Desktop, com alteração de título realçada](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

Poderá encontrar erros relacionados a credenciais ou o tamanho do conjunto de dados. Eis algumas informações sobre o que fazer sobre estes erros.

### <a name="unauthorized-401-or-403"></a>Não autorizada (401 ou 403)
Isto pode acontecer se não tiver sido atualizado o seu token de atualização. Experimente estes passos para garantir que ainda terá acesso:

1. Inicie sessão no portal do Azure e certifique-se de que pode aceder ao recurso.
2. Tente atualizar as credenciais para o dashboard.

   Se tem acesso e atualizar as credenciais não funciona, abra um pedido de suporte.

### <a name="bad-gateway-502"></a>Gateway inválido (502)
Normalmente, isto é provocado por uma consulta do Analytics que devolve muitos dados. Tente utilizar um intervalo de tempo menor para a consulta. 

Se reduzir o conjunto de dados provenientes da consulta de análise não cumpre os requisitos, considere utilizar o [API](https://dev.applicationinsights.io/documentation/overview) para obter um maior conjunto de dados. Eis como converter a exportação de consulta M para utilizar a API.

1. Criar uma [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script de Power BI M que exportou do Analytics ao substituir o URL do Gestor de recursos do Azure com a API do Application Insights.
   * Substitua  **https://management.azure.com/subscriptions/...**
   * com,  **https://api.applicationinsights.io/beta/apps/...**
3. Por fim, Atualize as credenciais para básico e utilizar a sua chave de API.

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre a amostragem
Dependendo da quantidade de dados enviados pela sua aplicação, pode querer utilizar a funcionalidade de amostragem adaptável, que envia apenas uma percentagem da sua telemetria. O mesmo se aplica se tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adaptador do Power BI (preterido)
Este método cria um dashboard completo de telemetria para. O conjunto de dados inicial está predefinido, mas pode adicionar mais dados a ele.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Inicie sessão no [do Power BI](https://app.powerbi.com/).
2. Open **obter dados** ![captura de ecrã de GetData ícone no canto inferior esquerdo](./media/export-power-bi/001.png), **serviços**.

    ![Capturas de ecrã de introdução da origem de dados do Application Insights](./media/export-power-bi/002.png)

3. Selecione **obter agora** em Application Insights.

   ![Capturas de ecrã de introdução da origem de dados do Application Insights](./media/export-power-bi/003.png)
4. Forneça os detalhes do recurso do Application Insights e, em seguida **início de sessão**.

    ![Captura de ecrã de introdução da origem de dados do Application Insights](./media/export-power-bi/005.png)

     Estas informações podem ser encontradas no painel de descrição geral do Application Insights:

     ![Captura de ecrã de introdução da origem de dados do Application Insights](./media/export-power-bi/004.png)

5. Abra o recém-criado Application Insights aplicação do Power BI.

6. Aguarde um minuto ou dois para que os dados a serem importados.

    ![Adaptador de captura de ecrã do Power BI](./media/export-power-bi/010.png)

Pode editar o dashboard, combinando os gráficos do Application Insights com as outras origens e com consultas de análise. Pode obter mais gráficos na Galeria de visualização, e cada gráfico tem parâmetros, que pode definir.

Após a importação inicial, o dashboard e os relatórios continuam a ser atualizados diariamente. Pode controlar a agenda de atualização no conjunto de dados.

## <a name="next-steps"></a>Passos Seguintes
* [Power BI – Saiba mais](https://www.powerbi.com/learning/)
* [Tutorial de análise](../../azure-monitor/log-query/get-started-portal.md)

