---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 8d75dbe5d4ab819e5bbe64e20ad84eb1c26a87a3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75777823"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

O gateway de aplicativo publica pontos de dados, chamados métricas, para [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu gateway de aplicativo e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [Azure monitor métricas](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

As métricas a seguir relacionadas ao tempo da solicitação e resposta estão disponíveis. Ao analisar essas métricas para um ouvinte específico, você pode determinar se a lentidão no aplicativo devido à WAN, ao gateway de aplicativo, à rede entre o gateway de aplicativo e o aplicativo de back-end ou o desempenho do aplicativo de back-end.

> [!NOTE]
>
> Se houver mais de um ouvinte no gateway de aplicativo, sempre filtre pela dimensão do *ouvinte* , comparando métricas de latência diferentes para obter uma inferência significativa.

- **RTT do cliente**

  Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações. 

- **Tempo total do gateway de aplicativo**

  Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.
  
Após a filtragem por ouvinte, se o *RTT do cliente* for muito mais do que o *tempo total do gateway de aplicativo*, ele poderá ser deduzido que a latência observada pelo cliente é devido à conectividade de rede entre o cliente e o gateway de aplicativo. Se ambas as latências forem comparáveis, a alta latência poderá ser devido a qualquer um dos seguintes: gateway de aplicativo, a rede entre o gateway de aplicativo e o aplicativo de back-end ou o desempenho do aplicativo de back-end.

- **Tempo de resposta do primeiro byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end

- **Tempo de resposta do último byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta
  
Se o *tempo total do gateway de aplicativo* for muito maior do que o *tempo de resposta do último byte de back-end* para um ouvinte específico, ele poderá ser deduzido de que a alta latência pode ser devido ao gateway de aplicativo. Por outro lado, se as duas métricas forem comparáveis, o problema poderá ser devido à rede entre o gateway de aplicativo e o aplicativo de back-end, ou o desempenho do aplicativo de back-end.

- **Tempo de conexão de back-end**

  Tempo gasto estabelecendo uma conexão com um aplicativo de back-end. No caso do SSL, ele inclui o tempo gasto no handshake. Observe que essa métrica é diferente das outras métricas de latência, pois isso só mede o tempo de conexão e, portanto, não deve ser diretamente comparado a magnitude com as outras latências. No entanto, a comparação do padrão de *tempo de conexão de back-end* com o padrão das outras latências pode indicar se um aumento em outras latências pode ser deduzido devido a uma variação na rede entre o aplicativo gateway e o aplicativo back-end. 
  

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo gateway de aplicativo dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pelo gateway de aplicativo para os clientes

- **Protocolo TLS do cliente**

   Contagem de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas. As unidades de capacidade medem o custo com base no consumo que é cobrado além do custo fixo. Há três determinantes para unidade de computação de unidade de capacidade, conexões persistentes e taxa de transferência. Cada unidade de capacidade é composta de no máximo: 1 unidade de computação ou 2500 conexões persistentes ou taxa de transferência de 2,22 Mbps.

- **Unidades de computação atuais**

   Contagem de capacidade de processador consumida. Fatores que afetam a unidade de computação são conexões TLS/s, computações de regravação de URL e processamento de regra WAF. 

- **Conexões atuais**

   Contagem de conexões atuais estabelecidas com o gateway de aplicativo

- **Solicitações com falha**

   Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.


- **Status da resposta**

   Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

   Número de bytes por segundo atendidos pelo gateway de aplicativo

- **Total de solicitações**

   Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Status da resposta de back-end**

  Contagem de códigos de status de resposta HTTP retornados pelos back-ends. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts íntegros/não íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts não íntegros em um pool de back-end específico.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Utilização da CPU**

  Exibe a utilização das CPUs alocadas para o gateway de aplicativo.  Em condições normais, o uso da CPU não deve exceder regularmente 90%, pois isso pode causar latência nos sites hospedados atrás do gateway de aplicativo e interromper a experiência do cliente. Você pode controlar indiretamente ou melhorar a utilização da CPU modificando a configuração do gateway de aplicativo aumentando a contagem de instâncias ou movendo para um tamanho de SKU maior ou fazendo ambos.

- **Conexões atuais**

  Contagem de conexões atuais estabelecidas com o gateway de aplicativo

- **Solicitações com falha**

  Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Status da resposta**

  Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

  Número de bytes por segundo atendidos pelo gateway de aplicativo

- **Total de solicitações**

  Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts íntegros/não íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts não íntegros em um pool de back-end específico.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue até um gateway de aplicativo, em **monitoramento** selecione **métricas**. Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

Na imagem a seguir, você verá um exemplo com três métricas exibidas nos últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [métricas com suporte com Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta pode chamar um webhook ou enviar um email para um administrador se a taxa de transferência do gateway de aplicativo estiver acima, abaixo ou em um limite para um período especificado.

O exemplo a seguir orienta você pela criação de uma regra de alerta que envia um email para um administrador depois que a taxa de transferência viola um limite:

1. Selecione **adicionar alerta de métrica** para abrir a página **Adicionar regra** . Você também pode acessar essa página na página métricas.

   ![Botão "adicionar alerta de métrica"][6]

2. Na página **Adicionar regra** , preencha as seções nome, condição e notificar e selecione **OK**.

   * No seletor de **condição** , selecione um dos quatro valores: **maior que**, **maior ou igual**a, **menor que**ou **menor ou igual a**.

   * No seletor de **período** , selecione um período de cinco minutos a seis horas.

   * Se você selecionar **proprietários, colaboradores e leitores de email**, o email poderá ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, você pode fornecer uma lista separada por vírgulas de usuários na caixa **emails de administrador adicionais** .

   ![Página Adicionar regra][7]

Se o limite for violado, um email semelhante ao da seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida depois que você cria um alerta de métrica. Ele fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, confira [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para entender mais sobre WebHooks e como você pode usá-los com alertas, visite [configurar um webhook em um alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passos seguintes

* Visualize logs de contador e de eventos usando [logs de Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o log de atividades do Azure com](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) a postagem de blog Power bi.
* [Exiba e analise os logs de atividades do Azure em Power bi e mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagens no blog.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
