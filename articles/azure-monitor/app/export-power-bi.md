---
title: Exportação para Power BI da Azure Application Insights | Microsoft Docs
description: As consultas analíticas podem ser exibidas no Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 4bdae09d193d5ae9c59fe9352e07b8219a76841d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584312"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed Power BI a partir de Insights de Aplicações
[Power BI](https://www.powerbi.com/) é um conjunto de ferramentas de negócio que o ajudam a analisar dados e partilhar insights. Os painéis de instrumentos ricos estão disponíveis em todos os dispositivos. Pode combinar dados de muitas fontes, incluindo consultas de Analytics da [Azure Application Insights](./app-insights-overview.md).

Existem três métodos de exportação de dados de Insights de Aplicação para o Power BI:

* [**Consultas de Exportação analíticas.**](#export-analytics-queries) Este é o método preferido. Escreva qualquer consulta que quiser e exporte-a para o Power BI. Pode colocar esta consulta num dashboard, juntamente com quaisquer outros dados.
* [**Exportação contínua e Azure Stream Analytics**](./export-stream-analytics.md). Este método é útil se pretender armazenar os seus dados por longos períodos de tempo. Se não tiver um requisito alargado de retenção de dados, utilize o método de consulta de análise de exportação. A exportação contínua e a Stream Analytics envolve mais trabalho para configurar e despesas adicionais de armazenamento.
* **Adaptador de BI de potência**. O conjunto de gráficos é predefinido, mas pode adicionar as suas próprias consultas de qualquer outra fonte.

> [!NOTE]
> O adaptador Power BI está agora **depretado.** Os gráficos predefinidos para esta solução são povoados por consultas estáticas e nãoeditáveis. Não tem a capacidade de editar estas consultas e, dependendo de certas propriedades dos seus dados, é possível que a ligação ao Power BI seja bem sucedida, mas nenhum dado é preenchido. Isto deve-se a critérios de exclusão definidos dentro da consulta codificada. Embora esta solução ainda possa funcionar para alguns clientes, devido à falta de flexibilidade do adaptador, a solução recomendada é utilizar a funcionalidade [**de consulta de exportação analytics.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Consultas de Exportação Analytics
Esta rota permite-lhe escrever qualquer consulta de Analytics que você gosta, ou exportar de Funis de Uso, e depois exportá-lo para um dashboard Power BI. (Pode adicionar ao painel criado pelo adaptador.)

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar a sua consulta De Insights de Aplicação, utilize a versão para desktop do Power BI. Em seguida, pode publicá-lo na web ou no seu espaço de trabalho em nuvem Power BI. 

Instale [o Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta analítica
1. [Abra a Análise e escreva a sua consulta.](../logs/log-analytics-tutorial.md)
2. Teste e refine a consulta até ficar satisfeito com os resultados. Certifique-se de que a consulta funciona corretamente em Analytics antes de exportá-la.
3. No menu **Exportação,** escolha **Power BI (M)**. Guarde o ficheiro de texto.
   
    ![Screenshot de Analytics, com menu Export em destaque](./media/export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, **selecione Obter** Consulta em Branco de  >  **Dados**. Em seguida, no editor de consulta, em **View,** selecione **Advanced Editor**.

    Cole o guião M Language exportado para o Editor Avançado.

    ![Screenshot do Power BI Desktop, com Editor Avançado em destaque](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Para permitir que o Power BI aceda ao Azure, poderá ter de fornecer credenciais. Utilize a **conta Organizacional** para iniciar sôms na sua conta Microsoft.
   
    ![Screenshot da caixa de diálogo de definições de consulta Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Se precisar de verificar as credenciais, utilize o comando do menu **Definições de Fonte de Dados** no editor de consulta. Certifique-se de especificar as credenciais que utiliza para o Azure, que pode ser diferente das suas credenciais para Power BI.
6. Escolha uma visualização para a sua consulta e selecione os campos para a dimensão do eixo x, eixo y e segmentação.
   
    ![Screenshot das opções de visualização do Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publique o seu relatório no seu espaço de trabalho em nuvem Power BI. A partir daí, pode incorporar uma versão sincronizada noutras páginas web.
   
    ![Screenshot do Power BI Desktop, com botão de publicação em destaque](./media/export-power-bi/publish-power-bi.png)
8. Refresque o relatório manualmente em intervalos ou crie uma atualização programada na página de opções.

### <a name="export-a-funnel"></a>Exportar um funil
1. [Faça o seu funil.](./usage-funnels.md)
2. Selecione **Power BI**.

   ![Screenshot do botão Power BI](./media/export-power-bi/button.png)

3. No Power BI Desktop, **selecione Obter** Consulta em Branco de  >  **Dados**. Em seguida, no editor de consulta, em **View,** selecione **Advanced Editor**.

   ![Screenshot do Power BI Desktop, com botão de consulta em branco em destaque](./media/export-power-bi/blankquery.png)

   Cole o guião M Language exportado para o Editor Avançado. 

   ![Screenshot mostra o Power BI Desktop, com Editor Avançado em destaque](./media/export-power-bi/advancedquery.png)

4. Selecione itens da consulta e escolha uma visualização do funil.

   ![A screenshot mostra as opções de visualização do funil de desktop Power BI](./media/export-power-bi/selectsequence.png)

5. Altere o título para torná-lo significativo e publique o seu relatório no seu espaço de trabalho em nuvem Power BI. 

   ![Screenshot do Power BI Desktop, com mudança de título em destaque](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

Pode encontrar erros relativos a credenciais ou ao tamanho do conjunto de dados. Aqui está algumas informações sobre o que fazer sobre estes erros.

### <a name="unauthorized-401-or-403"></a>Não autorizado (401 ou 403)
Isto pode acontecer se o seu token de atualização não tiver sido atualizado. Experimente estes passos para garantir que ainda tem acesso:

1. Inscreva-se no portal Azure e certifique-se de que pode aceder ao recurso.
2. Tente refrescar as credenciais para o painel.
3. Tente limpar a cache do seu Ambiente de Trabalho Power BI.


   Se tiver acesso e refrescar as credenciais não funciona, por favor abra um bilhete de apoio.

### <a name="bad-gateway-502"></a>Bad Gateway (502)
Isto é geralmente causado por uma consulta de Analytics que devolve muitos dados. Tente utilizar um intervalo de tempo mais pequeno para a consulta. 

Se a redução do conjunto de dados proveniente da consulta Analytics não satisfaz os seus requisitos, considere usar a [API](https://dev.applicationinsights.io/documentation/overview) para extrair um conjunto de dados maior. Aqui está como converter a exportação M-Consulta para usar a API.

1. Criar uma [chave API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script Power BI M que exportou da Analytics substituindo o URL do Gestor de Recursos Azure pela API de Insights de Aplicação.
   * Substitua **https: \/ /management.azure.com/subscriptions/...**
   * com, **https: \/ /api.applicationinsights.io/beta/apps/...**
3. Por fim, atualize as credenciais para o básico e use a sua chave API.

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre a amostragem
Dependendo da quantidade de dados enviados pela sua aplicação, é melhor utilizar a função de amostragem adaptativa, que envia apenas uma percentagem da sua telemetria. O mesmo acontece se tiver uma amostragem manualmente definida no SDK ou na ingestão. [Saiba mais sobre a amostragem.](./sampling.md)

## <a name="power-bi-adapter-deprecated"></a>Adaptador de BI de potência (precotado)
Este método cria um dashboard completo de telemetria para si. O conjunto de dados inicial é predefinido, mas pode adicionar mais dados ao mesmo.

### <a name="get-the-adapter"></a>Pegue o adaptador
1. Inicie sessão no [Power BI](https://app.powerbi.com/).
2. Abra o Screenshot de **dados** ![ do Ícone GetData no canto inferior esquerdo , ](./media/export-power-bi/001.png) **Serviços**.

    ![As imagens mostram Obter o botão na janela serviços.](./media/export-power-bi/002.png)

3. Selecione **Obtenha-o agora** em Insights de Aplicação.

   ![Screenshots de Get from Application Insights data source](./media/export-power-bi/003.png)
4. Forneça os detalhes do seu recurso Application Insights e, em seguida, **iniciar sôm-in**.

    ![A screenshot mostra a janela Connect to Application Insights.](./media/export-power-bi/005.png)

     Estas informações podem ser encontradas no painel de visão geral da aplicação:

     ![Screenshot de Obter a partir de Fonte de dados de Insights de Aplicação](./media/export-power-bi/004.png)

5. Abra a recém-criada App Insights Power BI App.

6. Espere um minuto ou dois para que os dados sejam importados.

    ![Screenshot do adaptador Power BI](./media/export-power-bi/010.png)

Pode editar o dashboard, combinando os gráficos de Insights de Aplicação com os de outras fontes, e com as consultas de Analytics. Você pode obter mais gráficos na galeria de visualização, e cada gráfico tem parâmetros que você pode definir.

Após a importação inicial, o dashboard e os relatórios continuam a atualizar-se diariamente. Pode controlar o calendário de atualização no conjunto de dados.

## <a name="next-steps"></a>Passos seguintes
* [Power BI - Aprender](https://www.powerbi.com/learning/)
* [Tutorial de analítica](../logs/log-analytics-tutorial.md)