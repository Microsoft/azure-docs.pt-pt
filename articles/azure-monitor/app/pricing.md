---
title: Gerenciar o uso e os custos do Aplicativo Azure insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos em Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 10/03/2019
ms.author: dalek
ms.openlocfilehash: 4674dede5912dc1dc64bd0e092e28461f30bebcd
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554221"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos de Application Insights

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para Application Insights.  Um artigo relacionado, [monitoramento de uso e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços.

O Application Insights foi projetado para obter tudo o que você precisa para monitorar a disponibilidade, o desempenho e o uso de seus aplicativos Web, estejam eles hospedados no Azure ou no local. O Application Insights dá suporte a linguagens e estruturas populares, como .NET, Java e node. js, e integra-se com processos e ferramentas DevOps como o Azure DevOps, JIRA e PagerDuty. É importante entender o que determina os custos de monitoramento de seus aplicativos. Neste artigo, examinaremos o que impulsiona os custos de monitoramento de seu aplicativo e como você pode monitorar e controlá-los proativamente.

Se você tiver dúvidas sobre como o preço funciona para Application Insights, poderá postar uma pergunta em nosso [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Modelo de determinação de preço

O preço do [aplicativo Azure insights][start] é um modelo **pago conforme o uso** com base no volume de dados ingerido e, opcionalmente, para a retenção de dados mais longa. Cada recurso de Application Insights é cobrado como um serviço separado e contribui para a fatura de sua assinatura do Azure. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado que é recebido por Application Insights do seu aplicativo. Não há nenhum encargo de volume de dados para usar o [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

[Testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md) incorrem em um encargo adicional. Testes na Web de várias etapas são testes da Web que executam uma sequência de ações. Não há nenhum encargo separado para *testes de ping* de uma única página. A telemetria de testes de ping e testes de várias etapas é cobrada da mesma forma que outra telemetria do seu aplicativo.

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerenciar seu aplicativo 

Se você ainda não estiver usando Application Insights, poderá usar a [calculadora de preços de Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso de Application insights. Comece inserindo "Azure Monitor" na caixa de pesquisa e clicando no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione Application Insights na lista suspensa tipo.  Aqui você pode inserir o número de GB de dados que você espera coletar por mês, de modo que eles perguntam a quantidade de dados Application Insights coletam o monitoramento do aplicativo. 

Há duas abordagens para resolver isso: uso de monitoramento padrão e amostragem adaptável, que está disponível no SDK do ASP.NET ou Estime a possível ingestão de dados com base no que outros clientes semelhantes viram. 

### <a name="data-collection-when-using-sampling"></a>Coleta de dados ao usar amostragem

Com a [amostragem adaptável](https://docs.microsoft.com/azure/azure-monitor/app/sampling#adaptive-sampling-in-your-aspnetaspnet-core-web-applications)do SDK do ASP.net, o volume de dados é ajustado automaticamente para manter-se dentro de uma taxa máxima de tráfego especificada para o monitoramento de Application insights padrão. Se o aplicativo produzir uma quantidade menor de telemetria, como durante a depuração ou devido ao baixo uso, os itens não serão descartados pelo processador de amostragem, desde que o volume esteja abaixo do nível de eventos configurados por segundo. Para um aplicativo de alto volume, com o limite padrão de 5 eventos por segundo, a amostragem adaptável limitará o número de eventos diários a 432.000. Usando um tamanho de evento médio típico de 1 KB, isso corresponde a 13,4 GB de telemetria por mês de 31 dias por nó que hospeda seu aplicativo (já que a amostragem é feita de local para cada nó). 

Para SDKs que não dão suporte à amostragem adaptável, você pode empregar a [amostragem de ingestão](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling) , que é amostrada quando os dados são recebidos por Application insights com base em uma porcentagem de dados a serem retidos ou [amostragem de taxa fixa para ASP.net, ASP.NET Core e Java sites](https://docs.microsoft.com/azure/azure-monitor/app/sampling#fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites) para reduzir o tráfego enviado do seu servidor Web e navegadores da Web

### <a name="learn-from-what-similar-customers-collect"></a>Aprenda com o que os clientes semelhantes coletam

Na calculadora de preços do monitoramento do Azure para Application Insights, se você habilitar a funcionalidade "estimar volume de dados com base na atividade do aplicativo", poderá fornecer entradas sobre seu aplicativo (solicitações por mês e exibições de página por mês, caso você possa coletar telemetria do lado do cliente) e, em seguida, a calculadora informará a quantidade mediana e 90 º percentil de dados coletados por aplicativos semelhantes. É claro que esses aplicativos abrangem o intervalo de configuração de Application Insights (por exemplo, alguns têm [amostragem](../../azure-monitor/app/sampling.md)padrão, alguns não têm amostragem, etc.), portanto, você ainda tem o controle para reduzir o volume de dados que está ingerindo muito abaixo do nível mediano usando a amostragem. Mas esse é um ponto de partida para entender o que outros clientes semelhantes estão vendo. 

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e calcule os custos

Application Insights facilita a compreensão do que os custos provavelmente serão baseados em padrões de uso recentes. Para começar, na portal do Azure, para o recurso de Application Insights, vá para a página **uso e custos estimados** : 

![Escolha os preços](./media/pricing/pricing-001.png)

R. Examine o volume de dados do mês. Isso inclui todos os dados que são recebidos e retidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) do servidor e dos aplicativos cliente e dos testes de disponibilidade.  
B. Um encargo separado é feito para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que estão incluídos no encargo de volume de dados.)  
C. Exibir tendências de volume de dados para o mês passado.  
D. Habilitar [amostragem](../../azure-monitor/app/sampling.md)de ingestão de dados.   
E. Defina o limite de volume de dados diário.  

(Observe que todos os preços exibidos nas capturas de tela neste artigo são apenas para fins de exemplo. Para obter os preços atuais em sua moeda e região, consulte [preços de Application insights][pricing].)

Para investigar seu uso de Application Insights mais profundamente, abra a página **métricas** , adicione a métrica denominada "volume de ponto de dados" e, em seguida, selecione a opção *aplicar divisão* para dividir os dados por "tipo de item de telemetria". 

Application Insights encargos são adicionados à sua fatura do Azure. Você pode ver os detalhes da sua fatura do Azure na seção de **cobrança** do portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu à esquerda, selecione cobrança](./media/pricing/02-billing.png)

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Exibindo o uso de Application Insights em sua fatura do Azure 

O Azure fornece uma grande funcionalidade útil no gerenciamento de [custos do Azure +](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub de cobrança. Por exemplo, a funcionalidade de "análise de custo" permite que você exiba seus gastos para os recursos do Azure. A adição de um filtro por tipo de recurso (para Microsoft. insights/componentes para Application Insights) permitirá que você acompanhe seus gastos.

Mais informações sobre seu uso podem ser obtidas [baixando seu uso do portal do Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure por dia. Nesta planilha do Excel, o uso de seus recursos de Application Insights pode ser encontrado primeiro filtrando a coluna "categoria do medidor" para mostrar "Application Insights" e "Log Analytics" e, em seguida, adicionar um filtro na coluna "ID da instância" que é "contém Microsoft. insights/Components ".  A maior parte Application Insights uso é relatada em metros com a categoria de medidor de Log Analytics, já que há um único back-end de logs para todos os componentes de Azure Monitor.  Somente Application Insights recursos em tipos de preço herdados e testes na Web de várias etapas são relatados com uma categoria de medidor de Application Insights.  O uso é mostrado na coluna "quantidade consumida" e a unidade de cada entrada é mostrada na coluna "unidade de medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua fatura de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="managing-your-data-volume"></a>Gerenciando o volume de dados 

Para entender a quantidade de dados que seu aplicativo está enviando, você pode:

* Vá para o painel **uso e custo estimado** para ver o gráfico de volume de dados diário. 
* Em Metrics Explorer, adicione um novo gráfico. Para a métrica do gráfico, selecione **volume de ponto de dados**. Ative o **agrupamento**e, em seguida, agrupe por **tipo de dados**.
* Use o tipo de dados `systemEvents`. Por exemplo, para ver o volume de dados ingerido no último dia, a consulta seria:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Essa consulta pode ser usada em um [alerta de log do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) para configurar alertas em volumes de dados. 

O volume de dados que você envia pode ser gerenciado usando as seguintes técnicas:

* **Amostragem**: você pode usar a amostragem para reduzir a quantidade de telemetria que é enviada do seu servidor e de aplicativos cliente, com o mínimo de distorção de métricas. A amostragem é a principal ferramenta que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre os [recursos de amostragem](../../azure-monitor/app/sampling.md).

* **Limitar chamadas AJAX**: você pode [limitar o número de chamadas AJAX que podem ser relatadas](../../azure-monitor/app/javascript.md#configuration) em cada exibição de página ou desativar relatórios Ajax.

* **Desabilitar módulos desnecessários**: [edite ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar os módulos de coleta que você não precisa. Por exemplo, você pode decidir que os contadores de desempenho ou os dados de dependência são inessenciais.

* **Métricas de agregação prévia**: se você colocar chamadas para TrackMetric em seu aplicativo, poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-autenticação](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Limite diário**: quando você cria um recurso de Application Insights no portal do Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso de Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. É pretendido que o usuário gere o limite diário antes de implantar o aplicativo em produção. 

    O limite máximo é de 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de tráfego intenso. 
    
    Emails de aviso sobre o limite diário são enviados para a conta que são membros dessas funções para seu Application Insights recurso: "administrador", "AccountAdmin", "coadministrador", "proprietário".

    Tome cuidado ao definir o limite diário. Seu objetivo deve ser *nunca atingir o limite diário*. Se você atingir o limite diário, perderá dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção de **limite de volume diário** . Você pode acessar essa opção no painel **uso e custos estimados** (isso é descrito em mais detalhes posteriormente no artigo).
    
    Removemos a restrição em alguns tipos de assinatura que têm crédito que não pôde ser usado para Application Insights. Anteriormente, se a assinatura tiver um limite de gastos, a caixa de diálogo de limite diário terá instruções para remover o limite de gastos e permitir que o limite diário seja aumentado além de 32,3 MB/dia.
    
* **Limitação**: a limitação limita a taxa de dados a 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação. O volume de dados Enviado por seu aplicativo é avaliado a cada minuto. Se ele exceder a média de taxa por segundo ao longo do minuto, o servidor recusará algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta reenviá-los. Ele espalha um surto em vários minutos. Se seu aplicativo envia dados consistentemente em mais do que a taxa de limitação, alguns dados serão descartados. (Os SDKs de ASP.NET, Java e JavaScript tentam reenviar os dados dessa maneira; outros SDKs podem simplesmente descartar dados restritos.) Se a limitação ocorrer, um aviso de notificação alertará que isso ocorreu.

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar seu volume máximo de dados diário

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo durante o restante do dia. Não é *aconselhável* que seu aplicativo atinja o limite diário. Você não pode acompanhar a integridade e o desempenho do seu aplicativo depois que ele atinge o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", caso seu aplicativo comece a enviar volumes de telemetria muito mais altos.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite de dados diário a ser definido

Examine Application Insights uso e custos estimados para entender a tendência de ingestão de dados e qual é o limite de volume diário a ser definido. Isso deve ser considerado com cuidado, pois você não poderá monitorar seus recursos depois que o limite for atingido. 

### <a name="set-the-daily-cap"></a>Definir o limite diário

Para alterar o limite diário, na seção **Configurar** do recurso de Application insights, na página **uso e custos estimados** , selecione **limite diário**.

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

Para [alterar o limite diário por meio de Azure Resource Manager](../../azure-monitor/app/powershell.md), a propriedade a ser alterada é a `dailyQuota`.  Por meio de Azure Resource Manager você também pode definir o `dailyQuotaResetTime` e a `warningThreshold` do limite diário. 

## <a name="sampling"></a>Amostragem
A [amostragem](../../azure-monitor/app/sampling.md) é um método para reduzir a taxa na qual a telemetria é enviada ao seu aplicativo, ao mesmo tempo em que retém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Você também mantém as contagens de eventos corretas.

A amostragem é uma maneira eficaz de reduzir os encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem retém itens relacionados de telemetria, portanto, por exemplo, ao usar a pesquisa, você pode encontrar a solicitação relacionada a uma exceção específica. O algoritmo também mantém as contagens corretas para que você veja os valores corretos no Gerenciador de métricas para taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* A [amostragem adaptável](../../azure-monitor/app/sampling.md) é o padrão para o SDK do ASP.net. A amostragem adaptável ajusta-se automaticamente ao volume de telemetria que seu aplicativo envia. Ele funciona automaticamente no SDK em seu aplicativo Web para que o tráfego de telemetria na rede seja reduzido. 
* A *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria do seu aplicativo entra no serviço Application insights. A amostragem de ingestão não afeta o volume de telemetria enviado do seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a cota que é usada pela telemetria de navegadores e outros SDKs.

Para definir a amostragem de ingestão, vá para o painel de **preços** :

![No painel cota e preço, selecione o bloco amostras e, em seguida, selecione uma fração de amostragem](./media/pricing/pricing-004.png)

> [!WARNING]
> O painel **amostragem de dados** controla apenas o valor de amostragem de ingestão. Ele não reflete a taxa de amostragem aplicada pelo SDK do Application Insights em seu aplicativo. Se a telemetria de entrada já tiver sido amostrada no SDK, a amostragem de ingestão não será aplicada.
>

Para descobrir a taxa de amostragem real, independentemente de onde ela foi aplicada, use uma [consulta de análise](analytics.md). A consulta é parecida com esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registro retido, `itemCount` indica o número de registros originais que ele representa. É igual a 1 + o número de registros descartados anteriores. 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

A retenção padrão para recursos de Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso de Application Insights. O conjunto completo de períodos de retenção disponíveis é 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. 

Para alterar a retenção, de seu Application Insights recurso, vá para a página **uso e custos estimados** e selecione a opção de **retenção de dados** :

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-005.png)

A retenção também pode ser [definida programaticamente usando o PowerShell](powershell.md#set-the-data-retention) usando o parâmetro `retentionInDays`. Além disso, se você definir a retenção de dados para 30 dias, poderá disparar uma limpeza imediata de dados mais antigos usando o parâmetro `immediatePurgeDataOn30Days`, que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade de limpeza só é exposta por meio de Azure Resource Manager e deve ser usada com extrema atenção. 

Quando a cobrança começa para uma retenção mais longa no início de dezembro de 2019, os dados mantidos por mais de 90 dias serão cobrados como a mesma taxa que é cobrada atualmente para a retenção de dados do Azure Log Analytics. Saiba mais na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Mantenha-se atualizado sobre o progresso da retenção de variáveis [votando essa sugestão](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="data-transfer-charges-using-application-insights"></a>Cobranças de transferência de dados usando Application Insights

Enviar dados para Application Insights pode incorrer em encargos de largura de banda de dados. Conforme descrito na [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/), a transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída na taxa normal. A transferência de dados de entrada é gratuita. No entanto, esse encargo é muito pequeno (alguns%) em comparação com os custos de ingestão de dados de log Application Insights. Consequentemente, controlar os custos de Log Analytics precisa se concentrar no volume de dados ingerido e temos diretrizes para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).   

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar emails de limite diário

Para desabilitar os emails de limite de volume diário, na seção **Configurar** do recurso de Application insights, no painel **uso e custos estimados** , selecione **limite diário**. Há configurações para enviar email quando o limite é atingido, bem como quando um nível de aviso ajustável foi atingido. Se você quiser desabilitar todos os emails relacionados a volume de limite diário desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Tipo de preço corporativo herdado (por nó)

Para os pioneiros do Aplicativo Azure insights, ainda existem dois tipos de preço possíveis: básico e corporativo. O tipo de preço básico é o mesmo descrito acima e é a camada padrão. Ele inclui todos os recursos da camada Enterprise, sem custo adicional. A camada básica cobra principalmente do volume de dados que é ingerido. 

> [!NOTE]
> Esses tipos de preço herdados foram renomeados. O tipo de preço Enterprise agora é chamado **por nó** e o tipo de preço básico agora é chamado **por GB**. Esses novos nomes são usados abaixo e na portal do Azure.  

A camada por nó (anteriormente Enterprise) tem um encargo por nó e cada nó recebe uma concessão de dados diária. No tipo de preço por nó, você será cobrado pelos dados ingeridos acima da bonificação incluída. Se você estiver usando o Operations Management Suite, deverá escolher a camada por nó. 

Para obter os preços atuais em sua moeda e região, consulte [preços de Application insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [apresentamos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para o monitoramento do Azure. Esse modelo adota um modelo "pré-pago" simples entre o portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como [avaliar o impacto da mudança para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) com base em seus padrões de uso e [como optar pelo novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Direitos por camada de nó e de assinatura do Operations Management Suite

Os clientes que comprarem o Operations Management Suite E1 e E2 poderão obter Application Insights por nó como um componente adicional sem custo adicional como [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade do Operations Management Suite E1 e E2 inclui um direito a um nó do Application Insights por camada de nó. Cada nó de Application Insights inclui até 200 MB de dados ingeridos por dia (separados da ingestão de dados Log Analytics), com retenção de dados de 90 dias sem custo adicional. A camada é descrita mais detalhadamente mais adiante neste artigo. 

Como essa camada é aplicável somente a clientes com uma assinatura do Operations Management Suite, os clientes que não têm uma assinatura do Operations Management Suite não veem uma opção para selecionar essa camada.

> [!NOTE]
> Para garantir que você obtenha esse direito, seus recursos de Application Insights devem estar no tipo de preço por nó. Esse direito aplica-se somente a nós. Application Insights recursos na camada por GB não percebem nenhum benefício. Esse direito não é visível nos custos estimados mostrados no painel **uso e custo estimado** . Além disso, se você mover uma assinatura para o novo modelo de preços de monitoramento do Azure em abril de 2018, a camada por GB será a única camada disponível. Mover uma assinatura para o novo modelo de preços de monitoramento do Azure não será aconselhável se você tiver uma assinatura do Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Como a camada por nó funciona

* Você paga por cada nó que envia telemetria para todos os aplicativos na camada por nó.
  * Um *nó* é um computador de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda seu aplicativo.
  * Os computadores de desenvolvimento, os navegadores de cliente e os dispositivos móveis não contam como nós.
  * Se seu aplicativo tiver vários componentes que enviam telemetria, como um serviço Web e um trabalho de back-end, os componentes serão contados separadamente.
  * [Live Metrics Stream](../../azure-monitor/app/live-stream.md) dados não são contados para fins de preços. Em uma assinatura, seus encargos são por nó, não por aplicativo. Se você tiver cinco nós que enviam telemetria para 12 aplicativos, a cobrança será para cinco nós.
* Embora as cobranças sejam tocadas por mês, você será cobrado apenas por qualquer hora em que um nó envie telemetria de um aplicativo. A cobrança por hora é a cobrança mensal entre aspas dividida por 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizada não é transferida de um dia para o outro.
  * Se você escolher o tipo de preço por nó, cada assinatura receberá uma concessão diária de dados com base no número de nós que enviam telemetria para os recursos de Application Insights nessa assinatura. Portanto, se você tiver cinco nós que enviam dados todos os dias, você terá uma concessão em pool de 1 GB aplicada a todos os Application Insights recursos nessa assinatura. Não importa se determinados nós enviam mais dados do que outros nós porque os dados incluídos são compartilhados entre todos os nós. Se estiver em um determinado dia, os Application Insights recursos receberão mais dados do que o que está incluído na alocação de dados diária para essa assinatura, os encargos excedentes por GB de dados serão aplicados. 
  * A concessão de dados diária é calculada como o número de horas no dia (usando UTC) que cada nó envia telemetria dividida por 24 multiplicado por 200 MB. Portanto, se você tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia seriam ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Ao preço de 2,30 USD por GB para dados excedentes, a cobrança seria de 1,15 $ se os nós enviarem 1 GB de dados nesse dia.
  * A bonificação diária por nível de nó não é compartilhada com aplicativos para os quais você escolheu a camada por GB. A bonificação não utilizada não é transferida do dia a dia. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nós distintos

| Cenário                               | Contagem total de nós diários |
|:---------------------------------------|:----------------:|
| 1 aplicativo usando 3 Azure App instâncias de serviço e um servidor virtual | 4 |
| 3 aplicativos em execução em 2 VMs; os recursos de Application Insights para esses aplicativos estão na mesma assinatura e na camada por nó | 2 | 
| 4 aplicativos cujos recursos do Application insights estão na mesma assinatura; cada aplicativo que executa duas instâncias durante 16 horas de pico e 4 instâncias durante 8 horas de pico | 13,33 | 
| Serviços de nuvem com uma função de trabalho e uma função Web, cada uma executando 2 instâncias | 4 | 
| Um cluster de Service Fabric do Azure de 5 nós executando microserviços 50; cada microserviço executando 3 instâncias | 5|

* A contagem exata de nós depende de qual Application Insights SDK seu aplicativo está usando. 
  * No SDK versões 2,2 e posteriores, o [SDK do Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) Application insights e o [SDK da Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatam cada host de aplicativo como um nó. Exemplos são o nome do computador para servidores físicos e hosts de VM ou o nome da instância para serviços de nuvem.  A única exceção é um aplicativo que usa somente o SDK do [.NET Core](https://dotnet.github.io/) e do Application insights Core. Nesse caso, apenas um nó é relatado para todos os hosts porque o nome do host não está disponível. 
  * Para versões anteriores do SDK, o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta como as versões mais recentes do SDK, mas o [SDK principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) relata apenas um nó, independentemente do número de hosts de aplicativo. 
  * Se seu aplicativo usar o SDK para definir **roleInstance** como um valor personalizado, por padrão, esse mesmo valor será usado para determinar a contagem de nós. 
  * Se você estiver usando uma nova versão do SDK com um aplicativo que é executado de computadores cliente ou dispositivos móveis, a contagem de nós pode retornar um número muito grande (devido ao grande número de computadores cliente ou dispositivos móveis). 

## <a name="automation"></a>Automatização

Você pode escrever um script para definir o tipo de preço usando o gerenciamento de recursos do Azure. [Saiba como](powershell.md#price).


## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/