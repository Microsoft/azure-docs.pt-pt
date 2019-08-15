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
ms.date: 08/13/2019
ms.author: dalek
ms.openlocfilehash: abf23eda2474ecbcfcaf0dadb26327225213a9a6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989217"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos de Application Insights

> [!NOTE]
> Este artigo descreve como analisar o uso de dados Application Insights.  Veja os artigos seguintes para obter informações relacionadas.
> - [Monitorizar a utilização e custos estimados](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) descreve como ver a utilização e custos estimados no Azure de várias funcionalidades para diferentes modelos de preços de monitorização. Também descreve como alterar o modelo de preços.

Se você tiver dúvidas sobre como o preço funciona para Application Insights, poderá postar uma pergunta em nosso [Fórum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Modelo preços

O preço do [aplicativo Azure][start] insights é baseado no volume de dados ingerido. Cada recurso de Application Insights é cobrado como um serviço separado e contribui para a fatura de sua assinatura do Azure.

### <a name="data-volume-details"></a>Detalhes do volume de dados

* Volume de dados é o número de bytes de telemetria recebidos por Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado que é recebido por Application Insights do seu aplicativo. Para [dados tabulares importados para a análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descompactado dos arquivos que são enviados para Application insights.
* Os encargos do volume de dados de seu aplicativo agora são relatados em um novo medidor de cobrança chamado ingestão de **dados** a partir de abril de 2018. Esse novo medidor é compartilhado entre tecnologias de monitoramento, como o Application insights e o Log Analytics e, no momento, está sob o nome do serviço **log Analytics**. 
* [Live Metrics Stream](../../azure-monitor/app/live-stream.md) dados não são contados para fins de preços.

> [!NOTE]
> Todos os preços exibidos nas capturas de tela neste artigo são apenas para fins de exemplo. Para obter os preços atuais em sua moeda e região, consulte [preços de Application insights][pricing].

### <a name="multi-step-web-tests"></a>Testes Web de vários passos

[Testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md) incorrem em um encargo adicional. Testes na Web de várias etapas são testes da Web que executam uma sequência de ações.

Não há nenhum encargo separado para *testes de ping* de uma única página. A telemetria de testes de ping e testes de várias etapas é cobrada da mesma forma que outra telemetria do seu aplicativo.

## <a name="review-usage-and-estimate-costs"></a>Examine os custos de uso e estimativa

Application Insights facilita a compreensão do que os custos provavelmente serão baseados em padrões de uso recentes. Para começar, na portal do Azure, para o recurso de Application Insights, vá para a página **uso e custos estimados** :

![Escolha os preços](./media/pricing/pricing-001.png)

R. Examine o volume de dados do mês. Isso inclui todos os dados que são recebidos e retidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) do servidor e dos aplicativos cliente e dos testes de disponibilidade.  
B. Um encargo separado é feito para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que estão incluídos no encargo de volume de dados.)  
C. Exibir tendências de volume de dados para o mês passado.  
D. Habilitar [amostragem](../../azure-monitor/app/sampling.md)de ingestão de dados.   
E. Defina o limite de volume de dados diário.  

Para investigar seu uso de Application Insights mais profundamente, abra a página métricas, adicione a métrica denominada "volume de ponto de dados" e, em seguida, selecione a opção *aplicar divisão* para dividir os dados por "tipo de item de telemetria". 

Application Insights encargos são adicionados à sua fatura do Azure. Você pode ver os detalhes da sua fatura do Azure na seção de **cobrança** do portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu à esquerda, selecione cobrança](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
O volume de dados que você envia é limitado de três maneiras:

* **Amostragem**: Você pode usar a amostragem para reduzir a quantidade de telemetria que é enviada do seu servidor e de aplicativos cliente, com o mínimo de distorção de métricas. A amostragem é a principal ferramenta que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre os [recursos de amostragem](../../azure-monitor/app/sampling.md). 
* **Limite diário**: Quando você cria um recurso de Application Insights no portal do Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso de Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. É pretendido que o usuário gere o limite diário antes de implantar o aplicativo em produção. 

    O limite máximo é de 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de tráfego intenso. 

    Tome cuidado ao definir o limite diário. Seu objetivo deve ser *nunca atingir o limite diário*. Se você atingir o limite diário, perderá dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção de **limite de volume diário** . Você pode acessar essa opção no painel **uso e custos estimados** (isso é descrito em mais detalhes posteriormente no artigo).
    Removemos a restrição em alguns tipos de assinatura que têm crédito que não pôde ser usado para Application Insights. Anteriormente, se a assinatura tiver um limite de gastos, a caixa de diálogo de limite diário terá instruções para remover o limite de gastos e permitir que o limite diário seja aumentado além de 32,3 MB/dia.
* **Limitação**: A limitação limita a taxa de dados a 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação.

*O que acontece se meu aplicativo exceder a taxa de limitação?*

* O volume de dados Enviado por seu aplicativo é avaliado a cada minuto. Se ele exceder a média de taxa por segundo ao longo do minuto, o servidor recusará algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta reenviá-los. Ele espalha um surto em vários minutos. Se seu aplicativo envia dados consistentemente em mais do que a taxa de limitação, alguns dados serão descartados. (Os SDKs de ASP.NET, Java e JavaScript tentam reenviar os dados dessa maneira; outros SDKs podem simplesmente descartar dados restritos.) Se a limitação ocorrer, um aviso de notificação alertará que isso ocorreu.

*Como fazer saber a quantidade de dados que meu aplicativo está enviando?*

Você pode usar uma das seguintes opções para ver a quantidade de dados que seu aplicativo está enviando:

* Vá para o painel **uso e custo estimado** para ver o gráfico de volume de dados diário. 
* Em Metrics Explorer, adicione um novo gráfico. Para a métrica do gráfico, selecione **volume de ponto de dados**. Ative o **agrupamento**e, em seguida, agrupe por **tipo de dados**.

## <a name="reduce-your-data-rate"></a>Reduzir sua taxa de dados
Aqui estão algumas coisas que você pode fazer para reduzir o volume de dados:

* Use a [amostragem](../../azure-monitor/app/sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer suas métricas. Você não perde a capacidade de navegar entre os itens relacionados na pesquisa. Em aplicativos de servidor, a amostragem funciona automaticamente.
* [Limite o número de chamadas AJAX que podem ser](../../azure-monitor/app/javascript.md#detailed-configuration) relatadas em cada exibição de página ou desative os relatórios Ajax.
* [Edite ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar os módulos de coleta que você não precisa. Por exemplo, você pode decidir que os contadores de desempenho ou os dados de dependência são inessenciais.
* Divida sua telemetria entre chaves de instrumentação separadas. 
* Métricas de agregação prévia. Se você colocar chamadas para TrackMetric em seu aplicativo, poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-autenticação](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gerir o volume de dados máximo diário

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo durante o restante do dia. Não é *aconselhável* que seu aplicativo atinja o limite diário. Você não pode acompanhar a integridade e o desempenho do seu aplicativo depois que ele atinge o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", caso seu aplicativo comece a enviar volumes de telemetria muito mais altos.

Para alterar o limite diário, na seção **Configurar** do recurso de Application insights, no painel **uso e custos estimados** , selecione **limite diário**.

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Amostragem
A [amostragem](../../azure-monitor/app/sampling.md) é um método para reduzir a taxa na qual a telemetria é enviada ao seu aplicativo, ao mesmo tempo em que retém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Você também mantém as contagens de eventos corretas.

A amostragem é uma maneira eficaz de reduzir os encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem retém itens relacionados de telemetria, portanto, por exemplo, ao usar a pesquisa, você pode encontrar a solicitação relacionada a uma exceção específica. O algoritmo também mantém as contagens corretas para que você veja os valores corretos no Gerenciador de métricas para taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* A [amostragem adaptável](../../azure-monitor/app/sampling.md) é o padrão para o SDK do ASP.net. A amostragem adaptável ajusta-se automaticamente ao volume de telemetria que seu aplicativo envia. Ele funciona automaticamente no SDK em seu aplicativo Web para que o tráfego de telemetria na rede seja reduzido. 
* A *amostragem* de ingestão é uma alternativa que opera no ponto em que a telemetria do seu aplicativo entra no serviço Application insights. A amostragem de ingestão não afeta o volume de telemetria enviado do seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a cota que é usada pela telemetria de navegadores e outros SDKs.

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

Agora, Application Insights está integrando um número limitado de clientes Application Insights à nossa visualização de retenção de variável. Informações sobre como participar deste programa de visualização estão disponíveis [aqui](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031).

A retenção padrão para recursos de Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso de Application Insights. O conjunto completo de períodos de retenção disponíveis é 30, 60, 120, 180, 270, 365, 550 ou 730 dias. 

Quando a cobrança está habilitada para maior retenção, os dados mantidos por mais de 90 dias serão cobrados como a mesma taxa que é cobrada no momento para a retenção de dados do Azure Log Analytics. Saiba mais na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).  Mantenha-se atualizado sobre o progresso da retenção de variáveis [votando essa sugestão](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar emails de limite diário

Para desabilitar os emails de limite de volume diário, na seção **Configurar** do recurso de Application insights, no painel **uso e custos estimados** , selecione **limite diário**. Há configurações para enviar email quando o limite é atingido, bem como quando um nível de aviso ajustável foi atingido. Se você quiser desabilitar todos os emails relacionados a volume de limite diário desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Tipo de preço corporativo herdado (por nó)

Para os pioneiros do Aplicativo Azure insights, ainda existem dois tipos de preço possíveis: Básico e empresarial. O tipo de preço básico é o mesmo descrito acima e é a camada padrão. Ele inclui todos os recursos da camada Enterprise, sem custo adicional. A camada básica cobra principalmente do volume de dados que é ingerido. 

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
| 4 aplicativos cujos recursos do Application insights estão na mesma assinatura; cada aplicativo que executa duas instâncias durante 16 horas de pico e 4 instâncias durante 8 horas de pico | 13.33 | 
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