---
title: Métricas do Monitor Azure para Gateway de Aplicação
description: Saiba como usar métricas para monitorizar o desempenho do gateway de aplicações
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: 9faa3a284aa7151880526c1ee70cfadc3dbf3089
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576113"
---
# <a name="metrics-for-application-gateway"></a>Métricas para Gateway de Aplicação

Application Gateway publica pontos de dados, chamados métricas, ao [Azure Monitor](../azure-monitor/overview.md) para o desempenho do seu Gateway de Aplicação e instâncias de backend. Estas métricas são valores numéricos num conjunto ordenado de dados da série de tempo que descrevem algum aspeto do gateway da sua aplicação num determinado momento. Se houver pedidos que fluam através do Gateway de Aplicação, mede e envia as suas métricas em intervalos de 60 segundos. Se não houver pedidos que fluam através do Gateway de Aplicação ou não houver dados para uma métrica, a métrica não é reportada. Para obter mais informações, consulte as [métricas do Monitor Azure.](../azure-monitor/essentials/data-platform-metrics.md)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas suportadas por Application Gateway V2 SKU

### <a name="timing-metrics"></a>Métricas de tempo

A Application Gateway fornece várias métricas de tempo incorporadas relacionadas com o pedido e resposta, que são todas medidas em milissegundos. 

![Diagrama de métricas de tempo, para o Gateway de Aplicação.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no Gateway de Aplicação, então sempre filtre pela dimensão *listener,* comparando diferentes métricas de latência para obter uma inferência significativa.

- **Tempo de ligação de backend**

  Tempo gasto a estabelecer uma ligação com o pedido de backend. 

  Isto inclui a latência da rede, bem como o tempo tomado pela pilha TCP do servidor backend para estabelecer novas ligações. No caso de TLS, inclui também o tempo decorrido no handshake. 

- **Tempo de resposta byte first backend**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e a receção do primeiro byte do cabeçalho de resposta. 

  Isto aproxima a soma do tempo de *ligação backend,* tempo demorado pelo pedido de chegar ao backend do Application Gateway, tempo despendado pela aplicação backend para responder (o tempo que o servidor demorou a gerar conteúdo, potencialmente buscar consultas de base de dados), e o tempo de primeiro byte da resposta para chegar ao Gateway de Aplicação a partir do backend.

- **Tempo de resposta do último byte de backend**

  Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do último byte do corpo de resposta. 

  Isto aproxima a soma do *Tempo de resposta do primeiro byte de back-end* e do tempo de transferência de dados (este número pode variar significativamente consoante o tamanho dos objetos pedidos e a latência da rede do servidor).

- **Tempo total do gateway de aplicação**

  O tempo médio que leva para que um pedido seja recebido, processado e a sua resposta seja enviada. 

  Este é o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte do pedido HTTP até ao momento em que o último byte de resposta foi enviado ao cliente. Isto inclui o tempo de processamento tomado pela Application Gateway, o *último tempo de resposta byte,* tempo demorado pela Application Gateway para enviar toda a resposta e o Cliente *RTT*.

- **RTT cliente**

  Tempo médio de ida e volta entre clientes e Application Gateway.



Estas métricas podem ser usadas para determinar se o abrandamento observado se deve à rede do cliente, ao desempenho do Gateway de Aplicação, à rede de backend e à saturação da pilha de backend, ao desempenho da aplicação de backend ou ao tamanho do ficheiro grande.

Por exemplo, se houver um pico na tendência do tempo de *resposta do backend first byte,* mas a tendência *do tempo de ligação backend* é estável, então pode ser deduzido que a porta de entrada da Aplicação para retrocedimentar a latência e o tempo necessário para estabelecer a ligação é estável, e o pico é causado devido a um aumento no tempo de resposta da aplicação backend. Por outro lado, se o pico no *tempo de resposta do byte de Backend estiver* associado a um pico correspondente no tempo de *ligação backend*, então pode deduzir-se que a rede entre o Gateway de Aplicação e o servidor backend ou a pilha de backend do servidor TCP saturaram. 

Se notar um pico no *tempo de resposta do último byte do Backend,* mas o tempo de resposta do *byte backend first* é estável, então pode ser deduzido que o pico é devido a um ficheiro maior sendo solicitado.

Da mesma forma, se o tempo total do *gateway de aplicação* tiver um pico, mas o tempo de *resposta do último byte backend* for estável, então pode ser um sinal de estrangulamento de desempenho no Gateway de Aplicação ou um estrangulamento na rede entre o cliente e o Gateway de aplicação. Além disso, se o *cliente RTT* também tem um pico correspondente, então indica que a degradação se deve à rede entre cliente e Gateway de aplicação.

### <a name="application-gateway-metrics"></a>Métricas do Gateway de Aplicação

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Bytes recebidos**

   Contagem de bytes recebidos pela Gateway de Aplicação dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pela Gateway de Aplicação aos clientes

- **Protocolo TLS do Cliente**

   Conde de pedidos de TLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas para realizar o balanceamento de carga do tráfego. Existem três determinantes para a unidade de capacidade - unidade de computação, ligações persistentes e produção. Cada unidade de capacidade é composta por, no máximo: 1 unidade de computação, ou 2500 ligações persistentes, ou produção de 2,22-Mbps.

- **Unidades de computação atuais**

   Contagem da capacidade do processador consumida. Os fatores que afetam a unidade de computação são as ligações TLS/seg., as computações de Reescrita de URLs e o processamento de regras WAF. 

- **Ligações atuais**

   O número total de ligações simultâneas ativas de clientes para o Gateway de Aplicações
   
- **Unidades de Capacidade Faturadas Estimadas**

  Com o SKU v2, o modelo de preços é orientado pelo consumo. As unidades de capacidade medem o custo baseado no consumo cobrado além do custo fixo. *As unidades de capacidade faturada estimadas* indicam o número de unidades de capacidade que utilizam a faturação. Isto é calculado como o maior valor entre as *Unidades de capacidade atuais* (as unidades de capacidade necessárias para realizar o balanceamento de carga do tráfego) e as *Unidades de capacidade faturadas fixas* (as unidades de capacidade mínimas mantidas em aprovisionamento).

- **Pedidos Falhados**

  Número de pedidos que o Application Gateway serviu com códigos de erro do servidor 5xx. Isto inclui os códigos 5xx que são gerados a partir do Gateway de Aplicação, bem como os códigos 5xx que são gerados a partir do backend. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada combinação de definição de pool-http de backend/específico.
   
- **Unidades de Capacidade Faturadas Fixas**

  O número mínimo de unidades de capacidade mantidas em aprovisionamento de acordo com a definição *Unidades de escala mínimas* (uma instância traduz-se em 10 unidades de capacidade) na configuração do Gateway de Aplicação.
   
 - **Novas ligações por segundo**

   O número médio de novas ligações TCP por segundo estabelecidas de clientes para o Gateway de Aplicação e do Gateway de Aplicação para os membros backend.


- **Estado de resposta**

   Estado de resposta HTTP devolvido pelo Application Gateway. A distribuição do código de estado de resposta pode ser categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

   Número de bytes por segundo que o Gateway de Aplicação serviu

- **Total de Pedidos**

   Contagem de pedidos bem sucedidos que o Application Gateway serviu. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada combinação de definição de pool-http de backend/específico.

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Estado de resposta de backend**

  Conde dos códigos de estado de resposta HTTP devolvidos pelos backends. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway de Aplicação. A distribuição do código de estado de resposta pode ser categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de anfitriões em bom estado de funcionamento**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar numa base de piscina por backend para mostrar o número de anfitriões saudáveis em uma piscina de backend específica.

- **Contagem de anfitriões em mau estado de funcionamento**

  O número de backends que são determinados pouco saudáveis pela sonda de saúde. Você pode filtrar numa base de piscina por backend para mostrar o número de anfitriões não saudáveis em uma piscina de backend específica.
  
- **Pedidos por minuto por Anfitrião Saudável**

  O número médio de pedidos recebidos por cada membro saudável numa piscina de backend em um minuto. Tem de especificar a piscina de backend utilizando a dimensão *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas suportadas por Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Métricas do Gateway de Aplicação

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Utilização da CPU**

  Apresenta a utilização das CPUs atribuídas ao Gateway de Aplicação.  Em condições normais, a utilização da CPU não deve exceder regularmente os 90%, uma vez que isso pode causar latência nos sites alojados por trás do Gateway de Aplicação e perturbar a experiência do cliente. Pode controlar ou melhorar indiretamente a utilização da CPU ao modificar a configuração do Gateway de Aplicação, aumentar a contagem de instâncias ou mover para um tamanho de SKU maior.

- **Ligações atuais**

  Contagem de ligações atuais estabelecidas com Gateway de Aplicação

- **Pedidos Falhados**

  Número de pedidos que falharam devido a problemas de ligação. Esta contagem inclui pedidos que falharam devido ao excesso da definição HTTP "Pedido de tempo" e pedidos que falharam devido a problemas de ligação entre o gateway de aplicação e o backend. Esta contagem não inclui falhas devido à não disponibilização de backend saudável. As respostas 4xx e 5xx do backend também não são consideradas como parte desta métrica.

- **Estado de resposta**

  Estado de resposta HTTP devolvido pelo Application Gateway. A distribuição do código de estado de resposta pode ser categorizada para mostrar respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Débito**

  Número de bytes por segundo que o Gateway de Aplicação serviu

- **Total de Pedidos**

  Contagem de pedidos bem sucedidos que o Application Gateway serviu. A contagem de pedidos pode ser filtrada para mostrar a contagem por cada combinação de definição de pool-http de backend/específico.

- **Contagem de pedidos bloqueados de firewall de aplicação web**
- **Firewall de aplicação web bloqueou distribuição de pedidos**
- **Distribuição total de regras de firewall de aplicação web**

### <a name="backend-metrics"></a>Métricas de backend

Para o Gateway de Aplicação, estão disponíveis as seguintes métricas:

- **Contagem de anfitriões em bom estado de funcionamento**

  O número de backends que são determinados saudáveis pela sonda de saúde. Você pode filtrar numa base de piscina por backend para mostrar o número de anfitriões saudáveis em uma piscina de backend específica.

- **Contagem de anfitriões em mau estado de funcionamento**

  O número de backends que são determinados pouco saudáveis pela sonda de saúde. Você pode filtrar numa base de piscina por backend para mostrar o número de anfitriões não saudáveis em uma piscina de backend específica.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue por um gateway de aplicações, em **Monitorização** de **Métricas selecionadas**. Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

Na imagem a seguir, vê-se um exemplo com três métricas exibidas nos últimos 30 minutos:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Vista métrica." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Para ver uma lista atual de métricas, consulte [métricas suportadas com monitor Azure](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Pode iniciar regras de alerta com base em métricas para um recurso. Por exemplo, um alerta pode chamar um webhook ou e-mail de um administrador se o prazo de produção do gateway de aplicação estiver acima, abaixo ou a um limiar por um período especificado.

O exemplo a seguir acompanha-o através da criação de uma regra de alerta que envia um e-mail a um administrador após a produção violar um limiar:

1. **selecione Adicionar alerta métrico** para abrir a página **de regra Adicionar.** Também pode chegar a esta página a partir da página de métricas.

   ![Botão "Adicionar alerta métrico"][6]

2. Na página **de regras Adicionar,** preencha o nome, condição e notifique secções, e selecione **OK**.

   * No seletor **de Condições,** selecione um dos quatro valores: **Maior do que,** Maior do que **igual,** **inferior** ou **igual a**.

   * No seletor **period,** selecione um período de cinco minutos a seis horas.

   * Se selecionar **e-mails proprietários, colaboradores e leitores,** o e-mail pode ser dinâmico com base nos utilizadores que tenham acesso a esse recurso. Caso contrário, pode fornecer uma lista separada de vírgulas de utilizadores na **caixa de e-mails de administrador adicional.**

   ![Adicionar página de regras][7]

Se o limiar for violado, um e-mail semelhante ao da imagem seguinte chega:

![E-mail para limiar violado][8]

Uma lista de alertas aparece depois de criar um alerta métrico. Fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [receber notificações de alerta.](../azure-monitor/alerts/alerts-overview.md)

Para saber mais sobre webhooks e como pode usá-los com alertas, visite [Configure um webhook em um alerta métrico Azure](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Passos seguintes

* Visualizar registos de contador e evento utilizando [registos do Monitor Azure](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o seu registo de atividades Azure com o](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) post de blog power BI.
* [Ver e analisar registos de atividades do Azure no Power BI e mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) post de blog.

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