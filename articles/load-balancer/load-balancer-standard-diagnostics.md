---
title: Diagnósticos com métricas, alertas e saúde de recursos - Azure Standard Load Balancer
description: Utilize as métricas, alertas e informações de saúde de recursos disponíveis para diagnosticar o seu Equilíbrio de Carga Padrão Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 861961bb66adc7ed9509eab973516a964cb67492
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521056"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnóstico do Balanceador de Carga Standard com métricas, alertas e estado de funcionamento dos recursos

O Azure Standard Load Balancer expõe as seguintes capacidades de diagnóstico:

* **Métricas e alertas multidimensionais:** Fornece capacidades de diagnóstico multidimensionais através [do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para configurações padrão de equilíbrio de carga. Pode monitorizar, gerir e resolver problemas com os seus recursos de equilíbrio de carga padrão.

* **Saúde dos recursos**: A página balanceiro de carga no portal Azure e na página de Saúde de Recursos (sob monitor) expõem a secção de Saúde de Recursos para O Equilíbrio de Carga Padrão. 

Este artigo proporciona uma rápida visita a estas capacidades, e oferece formas de usá-las para o Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece métricas multidimensionais através das Métricas Azure no portal Azure, e ajuda-o a obter informações de diagnóstico em tempo real sobre os seus recursos de equilíbrio de carga. 

As várias configurações de Balancer de Carga Padrão fornecem as seguintes métricas:

| Métrica | Tipo de recurso | Descrição | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade de caminhos de dados (disponibilidade VIP)| Equilibrador de carga público e interno | O Standard Load Balancer exerce continuamente o caminho de dados de dentro de uma região para a extremidade frontal do equilibrador de carga, até à pilha SDN que suporta o seu VM. Enquanto forem casos saudáveis, a medição segue o mesmo caminho que o tráfego equilibrado da sua aplicação. O caminho de dados que os seus clientes utilizam também é validado. A medição é invisível à sua aplicação e não interfere com outras operações.| Média |
| Estado da sonda de saúde (disponibilidade dip) | Equilibrador de carga público e interno | O Standard Load Balancer utiliza um serviço de sondagem de saúde distribuído que monitoriza a saúde do ponto final da sua aplicação de acordo com as definições de configuração. Esta métrica proporciona uma visão filtrada agregada ou por ponto final de cada ponto final na piscina de equilíbrio de carga. Pode ver como o Balancer de Carga vê a saúde da sua aplicação, conforme indicado pela sua configuração de sonda de saúde. |  Média |
| Pacotes SYN (sincronização) | Equilibrador de carga público e interno | O Balancer de Carga Standard não termina as ligações do Protocolo de Controlo de Transmissão (TCP) nem interage com os fluxos de pacotes TCP ou UDP. Os fluxos e os seus apertos de mão estão sempre entre a fonte e a instância VM. Para melhor resolver os seus cenários de protocolo TCP, pode utilizar os contadores de pacotes SYN para entender quantas tentativas de ligação tCP são feitas. A métrica relata o número de pacotes de SYN tCP que foram recebidos.| Média |
| Ligações SNAT | Equilibrador de carga pública |A Standard Load Balancer informa o número de fluxos de saída que são mascarados para a extremidade frontal do endereço IP público. As portas de tradução de endereços de rede de origem (SNAT) são um recurso de escape. Esta métrica pode dar uma indicação de quão fortemente a sua aplicação está a depender do SNAT para fluxos originados de saída. Os contadores para fluxos sNAT bem sucedidos e falhados são relatados e podem ser usados para resolver problemas e entender a saúde dos seus fluxos de saída.| Média |
| Portas SNAT atribuídas | Equilibrador de carga pública | A Standard Load Balancer informa o número de portas SNAT atribuídas por instância de backend | A média. |
| Portas SNAT usadas | Equilibrador de carga pública | O Standard Load Balancer informa o número de portas SNAT que são utilizadas por instância de backend. | Média | 
| Contadores byte |  Equilibrador de carga público e interno | O Standard Load Balancer informa os dados tratados por extremidade frontal. Pode notar que os bytes não são distribuídos igualmente pelos casos de backend. Isto é esperado como o algoritmo de Balancer de Carga do Azure é baseado em fluxos | Média |
| Contadores de pacotes |  Equilibrador de carga público e interno | O Standard Load Balancer informa os pacotes processados por frente.| Média |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Veja as métricas do seu equilíbrio de carga no portal Azure

O portal Azure expõe as métricas do equilíbrio de carga através da página Métricas, que está disponível tanto na página de recursos do equilibrador de carga para um determinado recurso como na página Do Monitor Azure. 

Para visualizar as métricas dos seus recursos Standard Load Balancer:
1. Vá à página métricas e faça qualquer um dos seguintes:
   * Na página de recursos do equilibrista de carga, selecione o tipo métrico na lista de drop-down.
   * Na página Do Monitor Azure, selecione o recurso do equilibrador de carga.
2. Desdefinir o tipo de agregação métrica apropriado.
3. Opcionalmente, configure a filtragem e agrupamento necessários.
4. Opcionalmente, configure o intervalo de tempo e a agregação. Por predefinição, o tempo é apresentado na UTC.

  >[!NOTE] 
  >A agregação do tempo é importante na interpretação de certas métricas, uma vez que os dados são amostrados uma vez por minuto. Se a agregação do tempo for fixada para cinco minutos e o tipo de agregação métrica Sum for utilizado para métricas como a atribuição de SNAT, o seu gráfico apresentará cinco vezes as portas SNAT totais atribuídas. 

![Métricas para Balancer de Carga Padrão](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Figura: Métrica de disponibilidade de caminhos de dados para o equilíbrio de carga padrão*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar as métricas multidimensionais programáticamente através de APIs

Para obter orientação da API para recuperar definições e valores métricos multidimensionais, consulte a passagem da API de [Monitorização do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Estas métricas podem ser escritas numa conta de armazenamento apenas através da opção 'All Metrics'. 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Cenários de diagnóstico comuns e vistas recomendadas

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Configurar alertas para métricas multidimensionais ###

O Azure Standard Load Balancer suporta alertas facilmente configuráveis para métricas multidimensionais. Configure limiares personalizados para métricas específicas para desencadear alertas com diferentes níveis de gravidade para capacitar uma experiência de monitorização de recursos sem toque.

Para configurar alertas:
1. Vá à sublâmina de alerta para o equilibrador de carga
1. Criar nova regra de alerta
    1.  Configurar condição de alerta
    1.  (Opcional) Adicionar grupo de ação para reparação automatizada
    1.  Atribuir gravidade, nome e descrição de alerta que permita uma reação intuitiva


  >[!NOTE]
  >A janela de configuração da condição de alerta mostrará séries de tempo para o histórico de sinais. Existe uma opção para filtrar esta série de tempo por dimensões como o Backend IP. Isto filtrará o gráfico da série de tempo, mas **não** o próprio alerta. Não é possível configurar alertas para endereços IP específicos do Backend.
  
#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>O caminho dos dados está disponível para o meu equilibrador de carga VIP?

A métrica de disponibilidade VIP descreve a saúde do caminho dos dados dentro da região para o hospedeiro da computação onde estão localizados os seus VMs. A métrica é um reflexo da saúde da infraestrutura Azure. Pode usar a métrica para:
- Monitorize a disponibilidade externa do seu serviço
- Investigue mais profundamente e compreenda se a plataforma em que o seu serviço é implantado é saudável ou se o seu hóspede OS ou a instância de aplicação são saudáveis.
- Isole se um evento está relacionado com o seu serviço ou com o plano de dados subjacente. Não confunda esta métrica com o estado da sonda de saúde ("disponibilidade dip").

Para obter a disponibilidade do Caminho de Dados para os seus recursos de Balancer De Carga Padrão:
1. Certifique-se de que o recurso correto do equilíbrio de carga está selecionado. 
2. Na lista de drop-down **métrica,** selecione Disponibilidade de **Caminho de Dados**. 
3. Na lista de **agregação,** selecione **Avg**. 
4. Além disso, adicione um filtro no endereço IP frontend ou na porta Frontend como a dimensão com o endereço IP frontal necessário ou porta frontal e, em seguida, agrupá-los pela dimensão selecionada.

![Sondagem VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Carregador De equilíbrio Frontal detalhes*

A métrica é gerada por uma medição ativa e em banda. Um serviço de sondagem na região origina tráfego para a medição. O serviço é ativado assim que cria uma implantação com uma extremidade frontal pública, e continua até remover a extremidade frontal. 

Um pacote que corresponde à extremidade dianteira da sua implantação e regra é gerado periodicamente. Atravessa a região desde a nascente até ao hospedeiro onde está localizado um VM na piscina traseira. A infraestrutura de equilíbrio de carga realiza as mesmas operações de equilíbrio de carga e tradução que faz para todos os outros tráfegos. Esta sonda está em banda no seu ponto final equilibrado. Depois da sonda chegar ao hospedeiro da computação, onde está localizado um VM saudável na piscina traseira, o anfitrião da computação gera uma resposta ao serviço de sondagem. O seu VM não vê este trânsito.

A disponibilidade VIP falha pelas seguintes razões:
- A sua implantação não tem VMs saudáveis restantes na piscina traseira. 
- Ocorreu uma falha na infraestrutura.

Para fins de diagnóstico, pode utilizar a métrica de disponibilidade do [caminho de dados juntamente com o estado da sonda de saúde](#vipavailabilityandhealthprobes).

Use a **Média** como a gregação para a maioria dos cenários.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Os casos de back-end para o meu VIP respondem às sondas?

A métrica do estado da sonda de saúde descreve a saúde da implementação da sua aplicação como configurada por si quando configura a sonda de saúde do seu equilibrador de carga. O equilibrador de carga utiliza o estado da sonda de saúde para determinar para onde enviar novos fluxos. As sondas sanitárias originam-se de um endereço de infraestrutura Azure e são visíveis dentro do os so do VM convidado.

Para obter o estado da sonda de saúde para os seus recursos Standard Load Balancer:
1. Selecione a métrica do Estado da **Sonda de Saúde** com o tipo de agregação **Avg.** 
2. Aplique um filtro no endereço IP ou porta IP frontend necessário (ou ambos).

As sondas de saúde falham pelas seguintes razões:
- Configura uma sonda de saúde para uma porta que não está a ouvir ou não está a responder ou que está a usar o protocolo errado. Se o seu serviço estiver a utilizar regras de devolução de servidores diretos (DSR, ou IP flutuante), certifique-se de que o serviço está a ouvir o endereço IP da configuração IP do NIC e não apenas no loopback configurado com o endereço IP frontal.
- A sua sonda não é permitida pelo Grupo de Segurança da Rede, pela firewall do VM ou pelos filtros da camada de aplicação.

Use a **Média** como a gregação para a maioria dos cenários.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como posso verificar as minhas estatísticas de ligação de saída? 

A métrica de ligações SNAT descreve o volume de ligações bem sucedidas e falhadas para [os fluxos de saída](https://aka.ms/lboutbound).

Um volume de ligações falhado superior a zero indica a exaustão da porta SNAT. Deve investigar mais para determinar o que pode estar a causar estas falhas. A exaustão da porta SNAT manifesta-se como uma falha na [criação](https://aka.ms/lboutbound)de um fluxo de saída . Reveja o artigo sobre ligações de saída para compreender os cenários e mecanismos no trabalho e para aprender a mitigar e projetar para evitar a exaustão da porta SNAT. 

Para obter estatísticas de ligação SNAT:
1. Selecione o tipo métrico de **ligações SNAT** e a **soma** como agregação. 
2. Grupo por **Connection State** para contagens de ligação SNAT bem sucedidas e falhadas que são representadas por diferentes linhas. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Contagem de ligação SNAT do balancedor de carga*


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Como posso verificar o uso e alocação da porta SNAT?

A métrica de utilização do SNAT indica quantos fluxos únicos são estabelecidos entre uma fonte de internet e um vm de backend ou um conjunto de escala de máquina virtual que está por trás de um equilibrista de carga e não tem um endereço IP público. Ao comparar isto com a métrica de atribuição de SNAT, pode determinar se o seu serviço está a experimentar ou em risco de exaustão sNAT e consequente falha de fluxo de saída. 

Se as suas métricas indicarem risco de falha de fluxo de [saída,](https://aka.ms/lboutbound) faça referência ao artigo e tome medidas para mitigar isso para garantir a saúde do serviço.

Para ver a utilização e alocação da porta SNAT:
1. Detete a agregação de tempo do gráfico para 1 minuto para garantir que os dados desejados são apresentados.
1. Selecione a utilização de **SNAT** e/ou a locação de **SNAT** como o tipo métrico e **a média** como a agregação
    * Por predefinição, este é o número médio de portas SNAT atribuídas ou utilizadas por cada VMs ou VMSSes de backend, correspondente a todos os IPs públicos frontend mapeados para o Balancer de Carga, agregados sobre TCP e UDP.
    * Para ver as portas SNAT totais utilizadas ou atribuídas para o balanço de carga utilizar a gregação métrica **Soma**
1. Filtrar para um tipo de **protocolo**específico, um conjunto de IPs de **backend,** e/ou **IPs frontend**.
1. Para monitorizar a saúde por extremidade inferior ou instância frontal, aplique a divisão. 
    * A divisão de nota apenas permite que uma única métrica seja exibida de cada vez. 
1. Por exemplo, monitorizar a utilização de SNAT para os fluxos de TCP por máquina, agregado por **média,** dividido por **IPs de backend** e filtro por **Tipo protocolo**. 

![Atribuição e utilização de SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figura: Alocação e utilização média da porta TCP SNAT para um conjunto de VMs de backend*

![Utilização de SNAT por instância de backend](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figura: Utilização da porta TCP SNAT por instância de backend*

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como posso verificar as tentativas de ligação de entrada/saída para o meu serviço?

Uma métrica de pacotes SYN descreve o volume de pacotes De SYN TCP, que chegaram ou foram enviados (para [fluxos de saída](https://aka.ms/lboutbound)) que estão associados a uma extremidade frontal específica. Pode utilizar esta métrica para entender as tentativas de ligação tCP ao seu serviço.

Use a **Total** como agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN do Balancer de Carga*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como posso verificar o consumo de largura de banda da rede? 

A métrica de bytes e contadores de pacotes descreve o volume de bytes e pacotes que são enviados ou recebidos pelo seu serviço numa base frontal.

Use a **Total** como agregação para a maioria dos cenários.

Para obter estatísticas de byte ou contagem de pacotes:
1. Selecione o tipo métrico **de Contagem de Bytes** e/ou Contagem de **Pacotes,** com **Avg** como agregação. 
2. Efetue um dos seguintes procedimentos:
   * Aplique um filtro numa porta ip frontal específica, porta frontal, IP traseira ou porta traseira.
   * Obtenha estatísticas globais para o seu recurso de equilíbrio de carga sem filtrar.

![Contagem de byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de byte saqueta do balancer de carga*

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Como diagnostico a minha implantação de equilibradores de carga?

Ao utilizar uma combinação das métricas de disponibilidade VIP e sonda de saúde num único gráfico, pode identificar onde procurar o problema e resolver o problema. Pode obter a garantia de que o Azure está a trabalhar corretamente e usar este conhecimento para determinar conclusivamente que a configuração ou aplicação é a causa principal.

Pode utilizar métricas de sonda de saúde para entender como o Azure vê a saúde da sua implantação de acordo com a configuração que forneceu. Olhar para as sondas de saúde é sempre um grande primeiro passo na monitorização ou determinação de uma causa.

Pode dar um passo mais além e usar métricas de disponibilidade VIP para obter informações sobre como o Azure vê a saúde do plano de dados subjacente que é responsável pela sua implementação específica. Quando combinar ambas as métricas, pode isolar onde a falha pode estar, como ilustrado neste exemplo:

![Combinando disponibilidade de caminhos de dados e métricas de estado da sonda de saúde](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: Combinando disponibilidade de caminhos de dados e métricas de estado da sonda de saúde*

O gráfico apresenta as seguintes informações:
- A infraestrutura que alberga os seus VMs estava indisponível e a 0% no início da tabela. Mais tarde, a infraestrutura estava saudável e os VMs eram acessíveis, e mais de um VM foi colocado na parte de trás. Esta informação é indicada pelo traço azul para a disponibilidade da trajetória de dados (disponibilidade VIP), que foi posteriormente a 100 por cento. 
- O estado da sonda de saúde (disponibilidade dip), indicado pelo traço roxo, está a 0% no início da tabela. A área circulou em destaques verdes onde o estado da sonda de saúde (disponibilidade dip) tornou-se saudável, e nesse ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes problemáticos a implementação por si só sem ter que adivinhar ou perguntar apoio se outros problemas estão acontecendo. O serviço não estava disponível porque as sondas de saúde estavam a falhar devido a uma configuração errada ou a uma aplicação falhada.

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Estado de saúde dos recursos

O estado de saúde dos recursos standard load balancer é exposto através da **saúde de recursos** existente no âmbito do **Monitor > Saúde**do Serviço.

Para ver a saúde dos seus recursos públicos standard load balancer:
1. Selecione **Monitor Service** > **Health**.

   ![Página de monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: A ligação de saúde de serviço no Monitor Azure*

2. Selecione **Saúde de Recursos**, e, em seguida, certifique-se de que **o ID de subscrição** e o **tipo de recurso = Balancer de carga** são selecionados.

   ![Estado de saúde dos recursos](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selecione recurso para vista para a saúde*

3. Na lista, selecione o recurso Load Balancer para ver o seu estado histórico de saúde.

    ![Estado de saúde do Balancer de Carga](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Visão de saúde dos recursos do Balancer de Carga*
 
Os vários estados de saúde dos recursos e as suas descrições estão listados no quadro seguinte: 

| Estado de saúde dos recursos | Descrição |
| --- | --- |
| Disponível | O seu recurso de equilíbrio de carga padrão é saudável e disponível. |
| Indisponível | O seu recurso de equilíbrio de carga padrão não é saudável. Diagnosticar a saúde selecionando > **As Métricas**do **Monitor Azure**.<br>(O estado*indisponível* também pode significar que o recurso não está ligado ao seu equilibrador de carga padrão.) |
| Desconhecido | O estado de saúde dos recursos para o seu recurso de equilíbrio de carga padrão ainda não foi atualizado.<br>(O estado*desconhecido* também pode significar que o recurso não está ligado ao seu equilibrador de carga padrão.)  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre a sua conectividade de saída do [seu balancer de carga.](https://aka.ms/lboutbound)
- Saiba mais sobre [o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/overview)
- Saiba mais sobre a [API REST Do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) e [como recuperar métricas através da API REST](/rest/api/monitor/metrics/list).
