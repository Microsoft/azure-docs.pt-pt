---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: a8882a810d18d06b33d6382bd8bd86ffe75b39d8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766818"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

O gateway de aplicativo publica pontos de dados, chamados métricas, para [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu gateway de aplicativo e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [Azure monitor métricas](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

O Application Gateway fornece várias métricas de tempo incorporadas relacionadas com o pedido e resposta que são todas medidas em milissegundos. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no gateway de aplicativo, sempre filtre pela dimensão do *ouvinte* , comparando métricas de latência diferentes para obter uma inferência significativa.

- **Tempo de conexão de back-end**

  Tempo gasto a estabelecer uma ligação com o pedido de backend. 

  Isto inclui a latência da rede, bem como o tempo devida pela pilha de TCP do servidor de backend para estabelecer novas ligações. No caso da SSL, também inclui o tempo gasto em aperto de mão. 

- **Tempo de resposta do primeiro byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o primeiro byte do cabeçalho de resposta. 

  Isto aproxima-se da soma do tempo de *ligação backend*, tempo tomado pelo pedido de chegar ao backend do Application Gateway, tempo tomado pela aplicação backend para responder (o tempo que o servidor demorou a gerar conteúdo, potencialmente buscar consultas de base de dados), e o tempo dedo pela primeira vez pela resposta para chegar ao Gateway de aplicação a partir do backend.

- **Tempo de resposta do último byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o último byte do corpo de resposta. 

  Isto aproxima-se da soma do tempo de resposta do *backend first byte* e do tempo de transferência de dados (este número pode variar muito dependendo do tamanho dos objetos solicitados e da latência da rede do servidor).

- **Tempo total do gateway de aplicativo**

  O tempo médio que leva para que um pedido seja recebido, processado e a sua resposta seja enviada. 

  Este é o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte do pedido HTTP para o momento em que o byte de última resposta foi enviado ao cliente. Isto inclui o tempo de processamento tomado pelo Application Gateway, o *backend último tempo*de resposta, tempo detempo decretado pela Application Gateway para enviar toda a resposta e o *Cliente RTT*.

- **RTT do cliente**

  Tempo médio de ida e volta entre clientes e o gateway de aplicativo.



Estas métricas podem ser usadas para determinar se a desaceleração observada se deve à rede de clientes, ao desempenho do Application Gateway, à rede de backend e à saturação da pilha de pilhas TCP do servidor de backend, ao desempenho da aplicação de backend ou ao tamanho de um ficheiro grande.

Por exemplo, Se houver um pico na tendência de tempo de *resposta backend primeiro byte,* mas a tendência de tempo de *ligação Backend* é estável, então pode ser inferido que a porta de aplicação para apoiar a latência e o tempo necessário para estabelecer a ligação é estável, e o pico é causado devido a um aumento do tempo de resposta da aplicação backend. Por outro lado, se o pico no primeiro tempo de resposta do *byte Backend* estiver associado a um pico correspondente no tempo de *ligação backend*, então pode ser deduzido que a rede entre o Application Gateway e o servidor backend ou a pilha de TCP do servidor de backend saturou. 

Se notar um pico no tempo de resposta do *byte Backend,* mas o tempo de resposta do *byte backend é* estável, então pode ser deduzido que o pico é devido a um ficheiro maior sendo solicitado.

Da mesma forma, se o tempo total do *gateway da Aplicação* tiver um pico mas o tempo de *resposta do backend é* estável, então pode ser um sinal de estrangulamento de desempenho no Gateway de Aplicação ou um estrangulamento na rede entre o cliente e o Application Gateway. Além disso, se o *cliente RTT* também tem um pico correspondente, então isso indica que a degradação se deve à rede entre cliente e Gateway de Aplicação.

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

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Utilização da CPU**

  Exibe a utilização das CPUs alocadas para o gateway de aplicativo.  Em condições normais, o uso de CPU não deve exceder regularmente 90%, uma vez que isso pode causar latência nos websites hospedados por trás do Gateway de Aplicação e perturbar a experiência do cliente. Você pode controlar indiretamente ou melhorar a utilização da CPU modificando a configuração do gateway de aplicativo aumentando a contagem de instâncias ou movendo para um tamanho de SKU maior ou fazendo ambos.

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

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.

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
