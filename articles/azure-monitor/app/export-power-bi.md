---
title: Exportar para Power BI do Aplicativo Azure insights | Microsoft Docs
description: As consultas do Analytics podem ser exibidas no Power BI.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/10/2018
ms.openlocfilehash: 3ec20f82bc12b60e5b88a02bb92a4a0ada4dcae7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668015"
---
# <a name="feed-power-bi-from-application-insights"></a>Power BI de feed de Application Insights
[Power bi](https://www.powerbi.com/) é um conjunto de ferramentas de negócios que ajuda você a analisar dados e compartilhar informações. Os painéis avançados estão disponíveis em todos os dispositivos. Você pode combinar dados de várias fontes, incluindo consultas de análise de [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md).

Há três métodos de exportar dados de Application Insights para Power BI:

* [**Exportar consultas do Analytics**](#export-analytics-queries). Esse é o método preferencial. Escreva qualquer consulta que desejar e exporte-a para Power BI. Você pode inserir essa consulta em um painel, juntamente com outros dados.
* [**Exportação e Azure Stream Analytics contínuas**](../../azure-monitor/app/export-stream-analytics.md). Esse método será útil se você quiser armazenar seus dados por longos períodos de tempo. Se você não tiver um requisito de retenção de dados estendidos, use o método de consulta Export Analytics. A exportação e a Stream Analytics contínuas envolvem mais trabalho para configurar e a sobrecarga de armazenamento adicional.
* **Adaptador de Power bi**. O conjunto de gráficos é predefinido, mas você pode adicionar suas próprias consultas de qualquer outra fonte.

> [!NOTE]
> O adaptador de Power BI agora está **preterido**. Os gráficos predefinidos para essa solução são preenchidos por consultas não editáveis estáticas. Você não tem a capacidade de editar essas consultas e, dependendo de determinadas propriedades de seus dados, é possível que a conexão com o Power BI seja bem-sucedida, mas nenhum dado é preenchido. Isso ocorre devido a critérios de exclusão que são definidos dentro da consulta codificada. Embora essa solução ainda possa funcionar para alguns clientes, devido à falta de flexibilidade do adaptador, a solução recomendada é usar a funcionalidade de [**consulta do Export Analytics**](#export-analytics-queries) .

## <a name="export-analytics-queries"></a>Exportar consultas do Analytics
Essa rota permite que você escreva qualquer consulta de análise desejada ou exporte os Funils de uso e, em seguida, exporte-os para um painel de Power BI. (Você pode adicionar ao painel criado pelo adaptador.)

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: Instale o Power BI Desktop
Para importar sua consulta de Application Insights, use a versão de área de trabalho do Power BI. Em seguida, você pode publicá-lo na Web ou em seu espaço de trabalho Power BI nuvem. 

Instale o [Power bi desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta do Analytics
1. [Abra o Analytics e escreva sua consulta](../../azure-monitor/log-query/get-started-portal.md).
2. Teste e refine a consulta até ficar satisfeito com os resultados. Certifique-se de que a consulta seja executada corretamente na análise antes de exportá-la.
3. No menu **Exportar** , escolha **Power bi (M)** . Salve o arquivo de texto.
   
    ![Captura de tela da análise, com o menu exportar realçado](./media/export-power-bi/analytics-export-power-bi.png)
4. Em Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor avançado**.

    Cole o script de linguagem M exportado no Editor Avançado.

    ![Captura de tela de Power BI Desktop, com Editor Avançado realçado](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Para permitir que Power BI acesse o Azure, talvez seja necessário fornecer credenciais. Use a **conta institucional** para entrar com seu conta Microsoft.
   
    ![Captura de tela da caixa de diálogo Power BI configurações de consulta](./media/export-power-bi/power-bi-import-sign-in.png)

    Se você precisar verificar as credenciais, use o comando de menu **configurações de fonte de dados** no editor de consultas. Certifique-se de especificar as credenciais que você usa para o Azure, que podem ser diferentes das suas credenciais para Power BI.
6. Escolha uma visualização para a consulta e selecione os campos para o eixo x, eixo y e dimensão de segmentação.
   
    ![Captura de tela de Power BI Desktop opções de visualização](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publique seu relatório em seu Power BI espaço de trabalho de nuvem. A partir daí, você pode inserir uma versão sincronizada em outras páginas da Web.
   
    ![Captura de tela de Power BI Desktop, com o botão publicar realçado](./media/export-power-bi/publish-power-bi.png)
8. Atualize o relatório manualmente em intervalos ou configure uma atualização agendada na página opções.

### <a name="export-a-funnel"></a>Exportar um funil
1. [Faça seu funil](../../azure-monitor/app/usage-funnels.md).
2. Selecione **Power bi**.

   ![Captura de tela do botão de Power BI](./media/export-power-bi/button.png)

3. Em Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor avançado**.

   ![Captura de tela de Power BI Desktop, com o botão de consulta em branco realçado](./media/export-power-bi/blankquery.png)

   Cole o script de linguagem M exportado no Editor Avançado. 

   ![Captura de tela de Power BI Desktop, com Editor Avançado realçado](./media/export-power-bi/advancedquery.png)

4. Selecione os itens da consulta e escolha uma visualização de funil.

   ![Captura de tela de Power BI Desktop opções de visualização](./media/export-power-bi/selectsequence.png)

5. Altere o título para torná-lo significativo e publique o relatório em seu espaço de trabalho Power BI nuvem. 

   ![Captura de tela de Power BI Desktop, com título alterar realçado](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

Você pode encontrar erros pertencentes às credenciais ou ao tamanho do conjunto de um. Aqui estão algumas informações sobre o que fazer sobre esses erros.

### <a name="unauthorized-401-or-403"></a>Não autorizado (401 ou 403)
Isso pode acontecer se o token de atualização não tiver sido atualizado. Tente estas etapas para garantir que você ainda tenha acesso:

1. Entre no portal do Azure e verifique se você pode acessar o recurso.
2. Tente atualizar as credenciais para o painel.
3. Tente limpar o cache de sua área de trabalho do PowerBI.


   Se você tiver acesso e a atualização das credenciais não funcionar, abra um tíquete de suporte.

### <a name="bad-gateway-502"></a>Gateway inadequado (502)
Isso geralmente é causado por uma consulta de análise que retorna muitos dados. Tente usar um intervalo de tempo menor para a consulta. 

Se a redução do conjunto de DataSet proveniente da consulta de análise não atender aos seus requisitos, considere o uso da [API](https://dev.applicationinsights.io/documentation/overview) para extrair um conjunto de um DataSet maior. Veja como converter a exportação de consulta M para usar a API.

1. Crie uma [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script de Power BI M que você exportou do Analytics, substituindo a URL de Azure Resource Manager pela API do Application Insights.
   * Substituir **https:\//Management.Azure.com/subscriptions/...**
   * com, **https:\//API.applicationinsights.Io/beta/apps/...**
3. Por fim, atualize as credenciais para básico e use sua chave de API.

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre amostragem
Dependendo da quantidade de dados enviados pelo seu aplicativo, talvez você queira usar o recurso de amostragem adaptável, que envia apenas uma porcentagem da telemetria. O mesmo será verdadeiro se você definir manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adaptador de Power BI (preterido)
Esse método cria um painel completo de telemetria para você. O DataSet inicial é predefinido, mas você pode adicionar mais dados a ele.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Entre no [Power bi](https://app.powerbi.com/).
2. Abra **obter dados** ![captura de tela do ícone GetData no canto inferior esquerdo](./media/export-power-bi/001.png), **Serviços**.

    ![Capturas de tela de obter da fonte de dados de Application Insights](./media/export-power-bi/002.png)

3. Selecione **obter agora** em Application insights.

   ![Capturas de tela de obter da fonte de dados de Application Insights](./media/export-power-bi/003.png)
4. Forneça os detalhes do recurso de Application Insights e, em seguida, **entre**.

    ![Captura de tela de obter da fonte de dados de Application Insights](./media/export-power-bi/005.png)

     Essas informações podem ser encontradas no painel de visão geral do Application Insights:

     ![Captura de tela de obter da fonte de dados de Application Insights](./media/export-power-bi/004.png)

5. Abra o aplicativo de Power BI de Application Insights recém-criado.

6. Aguarde um ou dois minutos para que os dados sejam importados.

    ![Captura de tela do adaptador de Power BI](./media/export-power-bi/010.png)

Você pode editar o painel, combinando os gráficos de Application Insights com os de outras fontes e com consultas de análise. Você pode obter mais gráficos na Galeria de visualização e cada gráfico tem parâmetros que podem ser definidos.

Após a importação inicial, o painel e os relatórios continuam a ser atualizados diariamente. Você pode controlar o agendamento de atualização no conjunto de os.

## <a name="next-steps"></a>Passos seguintes
* [Power BI-aprender](https://www.powerbi.com/learning/)
* [Tutorial de análise](../../azure-monitor/log-query/get-started-portal.md)

