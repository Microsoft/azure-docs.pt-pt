---
title: Exportação para Power BI a partir de Insights de Aplicação Azure [ Microsoft Docs
description: Consultas de análise podem ser exibidas no Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664002"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed Power BI a partir de Insights de Aplicação
[O Power BI](https://www.powerbi.com/) é um conjunto de ferramentas de negócio que o ajuda a analisar dados e a partilhar insights. Os painéis ricos estão disponíveis em todos os dispositivos. Pode combinar dados de muitas fontes, incluindo consultas de Analytics a partir de Insights de [Aplicação Azure](../../azure-monitor/app/app-insights-overview.md).

Existem três métodos de exportação de dados de insights de aplicação para o Power BI:

* [**Consultas de Análise de Exportação.**](#export-analytics-queries) Este é o método preferido. Escreva qualquer consulta que quiser e exporte-a para o Power BI. Pode colocar esta consulta num painel de instrumentos, juntamente com quaisquer outros dados.
* [**Exportação contínua e Azure Stream Analytics.**](../../azure-monitor/app/export-stream-analytics.md) Este método é útil se quiser armazenar os seus dados por longos períodos de tempo. Se não tiver um requisito alargado de retenção de dados, utilize o método de consulta de análise de exportação. A exportação contínua e o Stream Analytics envolvem mais trabalho para configurar e sobrecargas adicionais de armazenamento.
* **Adaptador POWER BI**. O conjunto de gráficos está predefinido, mas pode adicionar as suas próprias consultas a partir de quaisquer outras fontes.

> [!NOTE]
> O adaptador Power BI está agora **depreciado.** Os gráficos predefinidos para esta solução são povoados por consultas estáticas não editáveis. Não tem a capacidade de editar estas consultas e, dependendo de determinadas propriedades dos seus dados, é possível que a ligação ao Power BI seja bem sucedida, mas nenhum dado é povoado. Isto deve-se a critérios de exclusão que são definidos dentro da consulta codificada. Embora esta solução ainda possa funcionar para alguns clientes, devido à falta de flexibilidade do adaptador a solução recomendada é utilizar a funcionalidade de consulta de [**exportação Analytics.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Consultas de Análise de Exportação
Esta rota permite-lhe escrever qualquer consulta de Analytics que você goste, ou exportar de Funis de Utilização, e depois exportá-lo para um dashboard Power BI. (Pode adicionar ao painel de instrumentos criado pelo adaptador.)

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar Power BI Desktop
Para importar a sua consulta de Insights de Aplicação, utiliza a versão desktop do Power BI. Em seguida, pode publicá-lo na web ou no seu espaço de trabalho em nuvem Power BI. 

Instale o Ambiente de [Trabalho power BI](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta de Analytics
1. [Abra o Analytics e escreva a sua consulta.](../../azure-monitor/log-query/get-started-portal.md)
2. Teste e refine a consulta até ficar satisfeito com os resultados. Certifique-se de que a consulta corre corretamente no Analytics antes de exportá-la.
3. No menu **Export,** escolha **Power BI (M)**. Guarde o ficheiro de texto.
   
    ![Screenshot de Analytics, com menu exportado em destaque](./media/export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione Obter**Consulta em branco**de **dados** > . Em seguida, no editor de consulta, em **View,** **selecione Advanced Editor**.

    Colar o guião de Língua M exportado para o Editor Avançado.

    ![Screenshot do Power BI Desktop, com Editor Avançado em destaque](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Para permitir o acesso do Power BI ao Azure, poderá ter de fornecer credenciais. Utilize a **conta Organizacional** para iniciar sessão com a sua conta Microsoft.
   
    ![Screenshot da caixa de diálogo de definições de consulta de power bi](./media/export-power-bi/power-bi-import-sign-in.png)

    Se precisar de verificar as credenciais, utilize o comando do menu **Definições** de Origem de Dados no editor de consulta. Certifique-se de especificar as credenciais que utiliza para o Azure, que pode ser diferente das suas credenciais para O Power BI.
6. Escolha uma visualização para a sua consulta e selecione os campos para eixo x, eixo y e dimensão segmentante.
   
    ![Screenshot das opções de visualização do Ambiente de Trabalho Power BI](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publique o seu relatório no seu espaço de trabalho em nuvem Power BI. A partir daí, pode incorporar uma versão sincronizada noutras páginas web.
   
    ![Screenshot do Power BI Desktop, com botão Publicar em destaque](./media/export-power-bi/publish-power-bi.png)
8. Refresque o relatório manualmente em intervalos ou instale uma atualização programada na página de opções.

### <a name="export-a-funnel"></a>Exportar um Funil
1. [Faça o seu Funil.](../../azure-monitor/app/usage-funnels.md)
2. Selecione **Power BI**.

   ![Screenshot do botão Power BI](./media/export-power-bi/button.png)

3. No Power BI Desktop, selecione Obter**Consulta em branco**de **dados** > . Em seguida, no editor de consulta, em **View,** **selecione Advanced Editor**.

   ![Screenshot do Power BI Desktop, com botão De Consulta Em Branco em destaque](./media/export-power-bi/blankquery.png)

   Colar o guião de Língua M exportado para o Editor Avançado. 

   ![Screenshot do Power BI Desktop, com Editor Avançado em destaque](./media/export-power-bi/advancedquery.png)

4. Selecione itens da consulta e escolha uma visualização funnel.

   ![Screenshot das opções de visualização do Ambiente de Trabalho Power BI](./media/export-power-bi/selectsequence.png)

5. Mude o título para o tornar significativo e publique o seu relatório no seu espaço de trabalho em nuvem Power BI. 

   ![Screenshot do Power BI Desktop, com mudança de título em destaque](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

Pode encontrar erros relacionados com credenciais ou o tamanho do conjunto de dados. Aqui está algumas informações sobre o que fazer sobre estes erros.

### <a name="unauthorized-401-or-403"></a>Não autorizado (401 ou 403)
Isto pode acontecer se o seu token de atualização não tiver sido atualizado. Experimente estes passos para garantir que ainda tem acesso:

1. Inscreva-se no portal Azure e certifique-se de que pode aceder ao recurso.
2. Tente refrescar as credenciais para o painel de instrumentos.
3. Tente limpar a cache do seu Ambiente de Trabalho PowerBI.


   Se você tem acesso e refrescante as credenciais não funcionam, por favor abra um bilhete de apoio.

### <a name="bad-gateway-502"></a>Bad Gateway (502)
Isto é geralmente causado por uma consulta do Analytics que devolve demasiados dados. Tente usar um intervalo de tempo menor para a consulta. 

Se a redução do conjunto de dados proveniente da consulta do Analytics não cumprir os seus requisitos, considere utilizar a [API](https://dev.applicationinsights.io/documentation/overview) para puxar um conjunto de dados maior. Aqui está como converter a exportação M-Query para usar a API.

1. Criar uma [tecla API.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. Atualize o script Power BI M que exportou do Analytics substituindo o URL do Gestor de Recursos Azure pela API de Insights de Aplicação.
   * Substitua **https:\//management.azure.com/subscriptions/...**
   * com, **https:\//api.applicationinsights.io/beta/apps/...**
3. Por fim, atualize as credenciais para o básico e utilize a sua chave API.

**Roteiro existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre a amostragem
Dependendo da quantidade de dados enviados pela sua aplicação, é melhor utilizar a função de amostragem adaptativa, que envia apenas uma percentagem da sua telemetria. O mesmo acontece se tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

## <a name="power-bi-adapter-deprecated"></a>Adaptador Power BI (depreciado)
Este método cria um painel completo de telemetria para si. O conjunto de dados inicial está predefinido, mas pode adicionar-lhe mais dados.

### <a name="get-the-adapter"></a>Pegue o adaptador
1. Inicie sessão no [Power BI](https://app.powerbi.com/).
2. Abra **obter screenshot** ![de dados do](./media/export-power-bi/001.png)ícone GetData no canto inferior esquerdo, **Serviços**.

    ![Screenshots de Get from Application Insights fonte de dados](./media/export-power-bi/002.png)

3. Selecione agora em Insights **de** Aplicação.

   ![Screenshots de Get from Application Insights fonte de dados](./media/export-power-bi/003.png)
4. Forneça os detalhes do seu recurso Application Insights e, em seguida, **Inscreva-se**.

    ![Screenshot de Get from Application Insights fonte de dados](./media/export-power-bi/005.png)

     Estas informações podem ser encontradas no painel de visão geral da Aplicação Insights:

     ![Screenshot de Get from Application Insights fonte de dados](./media/export-power-bi/004.png)

5. Abra a recém-criada App Application Insights Power BI.

6. Espere um minuto ou dois para que os dados sejam importados.

    ![Screenshot do adaptador Power BI](./media/export-power-bi/010.png)

Pode editar o dashboard, combinando os gráficos de Insights de Aplicação com os de outras fontes, e com consultas de Analytics. Você pode obter mais gráficos na galeria de visualização, e cada gráfico tem parâmetros que você pode definir.

Após a importação inicial, o painel de instrumentos e os relatórios continuam a atualizar-se diariamente. Pode controlar o calendário de atualização no conjunto de dados.

## <a name="next-steps"></a>Passos seguintes
* [Power BI - Aprenda](https://www.powerbi.com/learning/)
* [Tutorial de análise](../../azure-monitor/log-query/get-started-portal.md)

