---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: 1fa9c72f7ca305a03cdc90ea02cefe973932792b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046324"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

Application Gateway publica pontos de dados, chamados métricas, para [o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu Gateway de Aplicação e instâncias de backend. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para mais informações, consulte [as métricas do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

O Application Gateway fornece várias métricas de tempo incorporadas relacionadas com o pedido e resposta que são todas medidas em milissegundos. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no Gateway da Aplicação, então filtre sempre pela dimensão *do Ouvinte,* comparando diferentes métricas de latência para obter inferência significativa.

- **Tempo de ligação backend**

  Tempo gasto a estabelecer uma ligação com o pedido de backend. 

  Isto inclui a latência da rede, bem como o tempo devida pela pilha de TCP do servidor de backend para estabelecer novas ligações. No caso da SSL, também inclui o tempo gasto em aperto de mão. 

- **Apoiar primeiro tempo de resposta byte**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o primeiro byte do cabeçalho de resposta. 

  Isto aproxima-se da soma do tempo de *ligação backend*, tempo tomado pelo pedido de chegar ao backend do Application Gateway, tempo tomado pela aplicação backend para responder (o tempo que o servidor demorou a gerar conteúdo, potencialmente buscar consultas de base de dados), e o tempo dedo pela primeira vez pela resposta para chegar ao Gateway de aplicação a partir do backend.

- **Tempo de resposta de byte último byte**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o último byte do corpo de resposta. 

  Isto aproxima-se da soma do tempo de resposta do *backend first byte* e do tempo de transferência de dados (este número pode variar muito dependendo do tamanho dos objetos solicitados e da latência da rede do servidor).

- **Tempo total de gateway de aplicação**

  O tempo médio que leva para que um pedido seja recebido, processado e a sua resposta seja enviada. 

  Este é o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte do pedido HTTP para o momento em que o byte de última resposta foi enviado ao cliente. Isto inclui o tempo de processamento tomado pelo Application Gateway, o *backend último tempo*de resposta, tempo detempo decretado pela Application Gateway para enviar toda a resposta e o *Cliente RTT*.

- **Cliente RTT**

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

- **Protocolo TLS cliente**

   Contagem de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.

- **Unidades de capacidade atual**

   Contagem de unidades de capacidade consumidas. As unidades de capacidade medem o custo com base no consumo que é cobrado além do custo fixo. Há três determinantes para unidade de computação de unidade de capacidade, conexões persistentes e taxa de transferência. Cada unidade de capacidade é composta de no máximo: 1 unidade de computação ou 2500 conexões persistentes ou taxa de transferência de 2,22 Mbps.

- **Unidades de computação atuais**

   Contagem de capacidade de processador consumida. Fatores que afetam a unidade de computação são conexões TLS/s, computações de regravação de URL e processamento de regra WAF. 

- **Ligações atuais**

   O número total de ligações simultâneas ativas dos clientes ao Gateway de Aplicação

- **Pedidos Falhados**

   Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.


- **Estado de Resposta**

   Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

   Número de bytes por segundo atendidos pelo gateway de aplicativo

- **Total de Pedidos**

   Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Firewall de aplicação web regras correspondidas**

- **Firewall de aplicação web desencadeou regras**

### <a name="backend-metrics"></a>Métricas de back-end

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Estado de resposta backend**

  Contagem de códigos de status de resposta HTTP retornados pelos back-ends. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem saudável do hospedeiro**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de hospedeiros pouco saudável**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.
  
- **Pedidos por minuto por Anfitrião Saudável** O número médio de pedidos recebidos por cada membro saudável numa piscina de backend em um minuto. Deve especificar a piscina de backend utilizando a dimensão *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Utilização do CPU**

  Exibe a utilização das CPUs alocadas para o gateway de aplicativo.  Em condições normais, o uso de CPU não deve exceder regularmente 90%, uma vez que isso pode causar latência nos websites hospedados por trás do Gateway de Aplicação e perturbar a experiência do cliente. Você pode controlar indiretamente ou melhorar a utilização da CPU modificando a configuração do gateway de aplicativo aumentando a contagem de instâncias ou movendo para um tamanho de SKU maior ou fazendo ambos.

- **Ligações atuais**

  Contagem de conexões atuais estabelecidas com o gateway de aplicativo

- **Pedidos Falhados**

  Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Estado de Resposta**

  Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição de código de status de resposta pode ser categorizada ainda mais para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

  Número de bytes por segundo atendidos pelo gateway de aplicativo

- **Total de Pedidos**

  Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Firewall de aplicação web regras correspondidas**

- **Firewall de aplicação web desencadeou regras**

### <a name="backend-metrics"></a>Métricas de back-end

Para o gateway de aplicativo, as seguintes métricas estão disponíveis:

- **Contagem saudável do hospedeiro**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de hospedeiros pouco saudável**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue num gateway de aplicação, sob **monitorização** de **métricas selecionadas.** Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

Na imagem a seguir, você verá um exemplo com três métricas exibidas nos últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [métricas suportadas com o Monitor Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta pode chamar um webhook ou enviar um email para um administrador se a taxa de transferência do gateway de aplicativo estiver acima, abaixo ou em um limite para um período especificado.

O exemplo a seguir orienta você pela criação de uma regra de alerta que envia um email para um administrador depois que a taxa de transferência viola um limite:

1. **selecione Adicionar alerta métrico** para abrir a página da regra **Adicionar.** Você também pode acessar essa página na página métricas.

   ![Botão "adicionar alerta de métrica"][6]

2. Na página de **regra Adicionar,** preencha o nome, condição e notifique as secções e selecione **OK**.

   * No seletor **de condições,** selecione um dos quatro valores: **Maior do que**, maior do que ou **igual,** **inferior**a , ou inferior ou **igual a**.

   * No **seletor** period, selecione um período de cinco minutos a seis horas.

   * Se selecionar **os proprietários, colaboradores e leitores**de email, o e-mail pode ser dinâmico com base nos utilizadores que tenham acesso a esse recurso. Caso contrário, pode fornecer uma lista separada de utilizadores na caixa **de e-mail adicional** do administrador.

   ![Página Adicionar regra][7]

Se o limite for violado, um email semelhante ao da seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida depois que você cria um alerta de métrica. Ele fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [Receber notificações](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)de alerta .

Para saber mais sobre webhooks e como pode usá-los com alertas, visite [Configure um webhook em um alerta métrico Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passos seguintes

* Visualizar os registos de contadores e eventos utilizando [registos do Monitor Azure](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o seu log](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) de atividade do Azure com a publicação de blog Power BI.
* Ver e analisar registos de atividade do [Azure no Power BI e mais posts](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) de blog.

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
