---
title: Métricas de autocarro da Azure Service em Azure Monitor| Microsoft Docs
description: Este artigo explica como usar o Azure Monitor para monitorizar as entidades de Service Bus (filas, tópicos e subscrições).
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 1e57def2ee7409ae62cdbe065c36e8a53a140e18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570579"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Métricas de ônibus de serviço Azure em Azure Monitor

As métricas do Service Bus dão-lhe o estado dos recursos na sua subscrição do Azure. Com um rico conjunto de dados de métricas, você pode avaliar a saúde geral dos seus recursos de Service Bus, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes, uma vez que o ajudam a monitorizar o estado do Service Bus. As métricas também podem ajudar a resolver problemas de causa de raiz sem precisar de contactar o suporte do Azure.

O Azure Monitor fornece interfaces de utilizador unificadas para monitorização em vários serviços Azure. Para obter mais informações, consulte [monitorar no Microsoft Azure](../azure-monitor/overview.md) e nas [métricas do Monitor De Recuperação Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Quando não houver qualquer interação com uma entidade durante 2 horas, as métricas começarão a mostrar "0" como valor até que a entidade não fique inativa.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias formas de aceder às métricas. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e analisar soluções como registos do Azure Monitor e Centros de Eventos. Para obter mais informações, consulte [métricas no Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).

As métricas são ativadas por padrão, e pode aceder aos últimos 30 dias de dados. Se precisar de manter os dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Este valor está configurado em [definições de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar métricas ao longo do tempo no [portal Azure](https://portal.azure.com). O exemplo a seguir mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![Screenshot da página Monitor - Métricas (pré-visualização) no portal Azure.][1]

Também pode aceder às métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nome e, em seguida, selecione **Métricas**. Para apresentar métricas filtradas no âmbito da entidade, selecione a entidade e, em seguida, selecione **Métricas**.

![Screenshot da página Monitor - Métricas (pré-visualização) filtrada no âmbito da entidade.][2]

Para as métricas que suportam dimensões, deve filtrar com o valor de dimensão pretendido.

## <a name="billing"></a>Faturação

As métricas e alertas no Monitor Azure são carregados por alerta. Estas cargas devem estar disponíveis no portal quando o alerta for configurado e antes de ser guardado. 

Soluções adicionais que ingerem dados de métricas são faturadas diretamente por essas soluções. Por exemplo, é faturado pelo Azure Storage se arquivar dados de métricas numa conta de Armazenamento Azure. também é faturado pelo Log Analytics se transmitir dados de métricas para Registar Analytics para análise avançada.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "precedida" não serão suportadas para a frente.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O intervalo de tempo suportado para todas as métricas do Service Bus é de 1 minuto.

## <a name="request-metrics"></a>Solicitar métricas

Conta o número de pedidos de dados e operações de gestão.

| Nome da Métrica | Description |
| ------------------- | ----------------- |
| Pedidos Recebidos| O número de pedidos feitos ao serviço service bus durante um período especificado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Pedidos Com Êxito|O número de pedidos bem sucedidos feitos ao serviço service bus durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Erros do servidor|O número de pedidos não processados devido a um erro no serviço service bus durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Erros do utilizador (ver a seguinte subsecção)|O número de pedidos não processados devido a erros do utilizador durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Pedidos de Aceleração|O número de pedidos que foram estrangulados porque o uso foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|

### <a name="user-errors"></a>Erros do utilizador

Os dois tipos seguintes de erros são classificados como erros do utilizador:

1. Erros do lado do cliente (em HTTP seriam 400 erros).
2. Erros que ocorrem durante o processamento de mensagens, tais como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Description |
| ------------------- | ----------------- |
|Mensagens Recebidas|O número de eventos ou mensagens enviadas para a Service Bus durante um período especificado. Esta métrica não inclui mensagens que são reencaminhadas automaticamente.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Mensagens Enviadas|O número de eventos ou mensagens recebidas da Service Bus durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
| Mensagens| Contagem de mensagens em uma fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome da entidade |
| Mensagens Ativas| Contagem de mensagens ativas em uma fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome da entidade |
| Mensagens com letras mortas| Contagem de mensagens com letras mortas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/>Dimensão: Nome da entidade |
| Mensagens agendadas| Contagem de mensagens programadas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média  <br/> Dimensão: Nome da entidade |
| Mensagens Completas| Contagem de mensagens completas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome da entidade |
| Mensagens Abandonadas| Contagem de mensagens abandonadas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome da entidade |
| Tamanho | Tamanho de uma entidade (fila ou tópico) em bytes. <br/><br/>Unidade: Contagem <br/>Tipo de agregação: Média <br/>Dimensão: Nome da entidade | 

> [!NOTE]
> Os valores das mensagens, ativas, com letras mortas, programadas, completadas e abandonadas são valores pontuais. As mensagens recebidas que foram consumidas imediatamente após esse ponto-a-tempo podem não ser refletidas nestas métricas. 

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Description |
| ------------------- | ----------------- |
|Ligações Ativas|O número de ligações ativas num espaço de nome e numa entidade no espaço de nomes. O valor desta métrica é um valor pontual. As ligações que estavam ativas imediatamente após esse ponto-a-tempo podem não ser refletidas na métrica.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Ligações Abertas |O número de ligações abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|
|Ligações Fechadas |O número de ligações fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da entidade|

## <a name="resource-usage-metrics"></a>Métricas de utilização de recursos

> [!NOTE] 
> As seguintes métricas só estão disponíveis com o nível **premium.** 
> 
> As métricas importantes para monitorizar eventuais interrupções para um espaço de nome de nível premium são: **utilização de CPU por espaço de nome** e tamanho de memória por espaço de **nome**. [Confiúde alertas](../azure-monitor/alerts/alerts-metric.md) para estas métricas utilizando o Monitor Azure.
> 
> A outra métrica que pode monitorizar é: **pedidos acelerados.** Não deve ser um problema, desde que o espaço de nomes permaneça dentro da sua memória, CPU, e limites de conexões intermediadas. Para mais informações, consulte [Throttling no nível Premium do Bus Da Azure Service](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Nome da Métrica | Description |
| ------------------- | ----------------- |
|Utilização do CPU por espaço de nome|A percentagem de utilização do CPU do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: Nome da entidade|
|Uso do tamanho da memória por espaço de nome|O uso percentual da memória do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: Nome da entidade|

## <a name="metrics-dimensions"></a>Dimensões das métricas

A Azure Service Bus suporta as seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas ao nível do espaço de nome. 

|Nome de dimensão|Description|
| ------------------- | ----------------- |
|Nome da entidade| A Service Bus suporta entidades de mensagens no espaço de nomes.|

## <a name="set-up-alerts-on-metrics"></a>Configurar alertas sobre métricas

1. No separador **Métricas** da página **Service Bus Namespace,** selecione **alertas de configuração**. 

    ![Página de métricas - Menu de alertas de configuração](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecione a opção **destino Selecione** e faça as seguintes ações na página **'Selecione':** 
    1. Selecione **espaços de nomeação de ônibus** de serviço para o **filtro por** tipo de recurso. 
    2. Selecione a sua subscrição para o campo **de subscrição Filter.**
    3. Selecione o **espaço de nome do ônibus** de serviço na lista. 
    4. Selecione **Concluído**. 
    
        ![Selecionar espaço de nomes](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. **Selecione Adicionar critérios** e faça as seguintes ações na página lógica de **sinal de configuração:**
    1. Selecione **métricas** para **o tipo de sinal**. 
    2. Selecione um sinal. Por exemplo: **Erros de serviço**. 

        ![Selecione erros de servidor](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecione **Maior do que** para a **Condição**.
    2. Selecione **Total** para **agregação de tempo**. 
    3. Introduza **5** para **o limiar**. 
    4. Selecione **Concluído**.    

        ![Especifique condição](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na página **'Criar** regras', expandir **detalhes de alerta,** e fazer as seguintes ações:
    1. Insira um **nome** para o alerta. 
    2. Introduza uma **descrição** para o alerta.
    3. Selecione **a gravidade** para o alerta. 

        ![Screenshot da página de regras Criar. Defina detalhes de alerta é expandido e os campos para nome da regra de alerta, Descrição e Severidade são destacados.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na página **de regras Criar,** expandir **Definir grupo de ação,** selecionar **Novo grupo de ação,** e fazer as seguintes ações na página do **grupo de ação Add**. 
    1. Insira um nome para o grupo de ação.
    2. Insira um nome curto para o grupo de ação. 
    3. Selecione a sua subscrição. 
    4. Selecione um grupo de recursos. 
    5. Para este passo, insira **Enviar por email o artigo** **AÇÃO**.
    6. Selecione **Email/SMS/Push/Voice** for **ACTION TYPE**. 
    7. Selecione **Editar detalhes**. 
    8. Na página **Email/SMS/Push/Voice,** faça as seguintes ações:
        1. Selecione **e-mail**. 
        2. Digite o **endereço de e-mail.** 
        3. Selecione **OK**.

            ![Screenshot da página do grupo de ação Add. Uma Ação denominada "Enviar e-mails" com o tipo de Ação Email/SMS/Push/Voice está a ser adicionada ao grupo.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na página do **grupo de ação Add,** selecione **OK**. 
1. Na página **'Criar' regra,** selecione **Criar a regra de alerta**. 

    ![Criar botão de regra de alerta](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passos seguintes

Consulte a visão geral do [Monitor Azure](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png