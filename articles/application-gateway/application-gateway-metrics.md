---
title: Métricas do Monitor Azure para Gateway de Aplicação
description: Aprenda a usar métricas para monitorizar o desempenho do gateway da aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457880"
---
# <a name="metrics-for-application-gateway"></a>Métricas para Gateway de Aplicação

Application Gateway publica pontos de dados, chamados métricas, para [o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu Gateway de Aplicação e instâncias de backend. Estas métricas são valores numéricos num conjunto ordenado de dados da série temporal que descrevem algum aspeto do gateway da sua aplicação num determinado momento. Se houver pedidos que fluem através do Gateway de Aplicação, mede e envia as suas métricas em intervalos de 60 segundos. Se não houver pedidos que fluam através do Gateway de Aplicação ou não existam dados para uma métrica, a métrica não é reportada. Para mais informações, consulte [as métricas do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas suportadas por Application Gateway V2 SKU

### <a name="timing-metrics"></a>Métricas de tempo

Application Gateway fornece várias métricas de tempo incorporadas relacionadas com o pedido e resposta, que são todas medidas em milissegundos. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no Gateway da Aplicação, então filtre sempre pela dimensão *do Ouvinte,* comparando diferentes métricas de latência para obter inferência significativa.

- **Tempo de ligação backend**

  Tempo gasto a estabelecer uma ligação com o pedido de backend. 

  Isto inclui a latência da rede, bem como o tempo devida pela pilha de TCP do servidor de backend para estabelecer novas ligações. No caso de TLS, inclui também o tempo decorrido no handshake. 

- **Apoiar primeiro tempo de resposta byte**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o primeiro byte do cabeçalho de resposta. 

  Isto aproxima-se da soma do tempo de *ligação backend*, tempo tomado pelo pedido de chegar ao backend do Application Gateway, tempo tomado pela aplicação backend para responder (o tempo que o servidor demorou a gerar conteúdo, potencialmente buscar consultas de base de dados), e o tempo dedo pela primeira vez pela resposta para chegar ao Gateway de aplicação a partir do backend.

- **Tempo de resposta de byte último byte**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o último byte do corpo de resposta. 

  Isto aproxima a soma do *Tempo de resposta do primeiro byte de back-end* e do tempo de transferência de dados (este número pode variar significativamente consoante o tamanho dos objetos pedidos e a latência da rede do servidor).

- **Tempo total de gateway de aplicação**

  O tempo médio que leva para que um pedido seja recebido, processado e a sua resposta seja enviada. 

  Este é o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte do pedido HTTP para o momento em que o byte de última resposta foi enviado ao cliente. Isto inclui o tempo de processamento tomado pelo Application Gateway, o *backend último tempo*de resposta, tempo detempo decretado pela Application Gateway para enviar toda a resposta e o *Cliente RTT*.

- **Cliente RTT**

  Tempo médio de ida e volta entre clientes e Gateway de aplicação.



Estas métricas podem ser usadas para determinar se a desaceleração observada se deve à rede de clientes, ao desempenho do Application Gateway, à rede de backend e à saturação da pilha de pilhas TCP do servidor de backend, ao desempenho da aplicação de backend ou ao tamanho de um ficheiro grande.

Por exemplo, Se houver um pico na tendência de tempo de *resposta backend primeiro byte,* mas a tendência de tempo de *ligação Backend* é estável, então pode ser inferido que a porta de aplicação para apoiar a latência e o tempo necessário para estabelecer a ligação é estável, e o pico é causado devido a um aumento do tempo de resposta da aplicação backend. Por outro lado, se o pico no primeiro tempo de resposta do *byte Backend* estiver associado a um pico correspondente no tempo de *ligação backend*, então pode ser deduzido que a rede entre o Application Gateway e o servidor backend ou a pilha de TCP do servidor de backend saturou. 

Se notar um pico no tempo de resposta do *byte Backend,* mas o tempo de resposta do *byte backend é* estável, então pode ser deduzido que o pico é devido a um ficheiro maior sendo solicitado.

Da mesma forma, se o tempo total do *gateway da Aplicação* tiver um pico mas o tempo de *resposta do backend é* estável, então pode ser um sinal de estrangulamento de desempenho no Gateway de Aplicação ou um estrangulamento na rede entre o cliente e o Application Gateway. Além disso, se o *cliente RTT* também tem um pico correspondente, então indica que a degradação se deve à rede entre cliente e Gateway de Aplicação.

### <a name="application-gateway-metrics"></a>Métricas de Gateway de aplicação

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo Gateway de Aplicação dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pela Porta de Entrada de Aplicação aos clientes

- **Protocolo TLS cliente**

   Contagem de TLS e pedidos não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para ver a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas para realizar o balanceamento de carga do tráfego. Existem três determinantes para a unidade de capacidade - unidade de computação, ligações persistentes e entrada. Cada unidade de capacidade é composta no máximo por: 1 unidade de cálculo, ou 2500 ligações persistentes, ou 2,22 Mbps de potência.

- **Unidades de computação atuais**

   Contagem da capacidade do processador consumida. Os fatores que afetam a unidade de computação são as ligações TLS/seg., as computações de Reescrita de URLs e o processamento de regras WAF. 

- **Ligações atuais**

   O número total de ligações simultâneas ativas dos clientes ao Gateway de Aplicação
   
- **Unidades de Capacidade Faturadas Estimadas**

  Com o SKU v2, o modelo de preços é orientado pelo consumo. As unidades de capacidade medem o custo baseado no consumo cobrado além do custo fixo. *As unidades estimadas* de capacidade faturada indicam o número de unidades de capacidade que utilizam as quais a faturação é estimada. Isto é calculado como o maior valor entre as *Unidades de capacidade atuais* (as unidades de capacidade necessárias para realizar o balanceamento de carga do tráfego) e as *Unidades de capacidade faturadas fixas* (as unidades de capacidade mínimas mantidas em aprovisionamento).

- **Pedidos Falhados**

  Número de pedidos que o Application Gateway serviu com códigos de erro de servidor 5xx. Isto inclui os códigos 5xx que são gerados a partir do Gateway de Aplicação, bem como os códigos 5xx que são gerados a partir do backend. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada/combinação específica de definição de pool-http.
   
- **Unidades de Capacidade Faturadas Fixas**

  O número mínimo de unidades de capacidade mantidas em aprovisionamento de acordo com a definição *Unidades de escala mínimas* (uma instância traduz-se em 10 unidades de capacidade) na configuração do Gateway de Aplicação.
   
 - **Novas ligações por segundo**

   O número médio de novas ligações TCP por segundo estabelecidas desde clientes ao Gateway de Aplicação e desde o Gateway de Aplicação aos membros do backend.


- **Estado de Resposta**

   Estado de resposta HTTP devolvido pelo Application Gateway. A distribuição do código de estado de resposta pode ser ainda categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

   Número de bytes por segundo o Gateway de Aplicação serviu

- **Total de Pedidos**

   Contagem de pedidos bem sucedidos que o Application Gateway serviu. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada/combinação específica de definição de pool-http.

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Estado de resposta backend**

  Contagem dos códigos de estado de resposta HTTP devolvidos pelos backends. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway da Aplicação. A distribuição do código de estado de resposta pode ser ainda categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de anfitriões em bom estado de funcionamento**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de anfitriões em mau estado de funcionamento**

  O número de backends que são determinados insalubres pela sonda de saúde. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.
  
- **Pedidos por minuto por Anfitrião Saudável**

  O número médio de pedidos recebidos por cada membro saudável numa piscina de backend em um minuto. Deve especificar a piscina de backend utilizando a dimensão *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas suportadas por Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Métricas de Gateway de aplicação

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Utilização da CPU**

  Apresenta a utilização das CPUs atribuídas ao Gateway de Aplicação.  Em condições normais, a utilização da CPU não deve exceder regularmente os 90%, uma vez que isso pode causar latência nos sites alojados por trás do Gateway de Aplicação e perturbar a experiência do cliente. Pode controlar ou melhorar indiretamente a utilização da CPU ao modificar a configuração do Gateway de Aplicação, aumentar a contagem de instâncias ou mover para um tamanho de SKU maior.

- **Ligações atuais**

  Contagem das ligações atuais estabelecidas com gateway de aplicação

- **Pedidos Falhados**

  Número de pedidos que o Application Gateway serviu com códigos de erro de servidor 5xx. Isto inclui os códigos 5xx que são gerados a partir do Gateway de Aplicação, bem como os códigos 5xx que são gerados a partir do backend. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada/combinação específica de definição de pool-http.

- **Estado de Resposta**

  Estado de resposta HTTP devolvido pelo Application Gateway. A distribuição do código de estado de resposta pode ser ainda categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

  Número de bytes por segundo o Gateway de Aplicação serviu

- **Total de Pedidos**

  Contagem de pedidos bem sucedidos que o Application Gateway serviu. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada/combinação específica de definição de pool-http.

- **Contagem de pedidos bloqueados por firewall de aplicação web**
- **Distribuição de pedidos bloqueados por firewall de aplicação web**
- **Distribuição total de regras de firewall de aplicação web**

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Contagem de anfitriões em bom estado de funcionamento**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões saudáveis em uma piscina específica de backend.

- **Contagem de anfitriões em mau estado de funcionamento**

  O número de backends que são determinados insalubres pela sonda de saúde. Você pode filtrar em uma base de piscina de backend para mostrar o número de anfitriões não saudáveis em uma piscina específica de backend.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue num gateway de aplicação, sob **monitorização** de **métricas selecionadas.** Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

Na seguinte imagem, vê-se um exemplo com três métricas exibidas nos últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [métricas suportadas com o Monitor Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Pode iniciar regras de alerta com base em métricas para um recurso. Por exemplo, um alerta pode chamar um webhook ou enviar um e-mail a um administrador se a entrada do gateway da aplicação estiver acima, abaixo ou num limiar durante um período determinado.

O exemplo que se segue leva-o através da criação de uma regra de alerta que envia um e-mail a um administrador após a entrada violar um limiar:

1. **selecione Adicionar alerta métrico** para abrir a página da regra **Adicionar.** Também pode chegar a esta página a partir da página das métricas.

   ![Botão "Adicionar alerta métrico"][6]

2. Na página de **regra Adicionar,** preencha o nome, condição e notifique as secções e selecione **OK**.

   * No seletor **de condições,** selecione um dos quatro valores: **Maior do que**, maior do que ou **igual,** **inferior**a , ou inferior ou **igual a**.

   * No **seletor** period, selecione um período de cinco minutos a seis horas.

   * Se selecionar **os proprietários, colaboradores e leitores**de email, o e-mail pode ser dinâmico com base nos utilizadores que tenham acesso a esse recurso. Caso contrário, pode fornecer uma lista separada de utilizadores na caixa **de e-mail adicional** do administrador.

   ![Adicionar página de regras][7]

Se o limiar for violado, chega um e-mail semelhante ao da seguinte imagem:

![E-mail para limiar violado][8]

Uma lista de alertas aparece depois de criar um alerta métrico. Fornece uma visão geral de todas as regras de alerta.

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
