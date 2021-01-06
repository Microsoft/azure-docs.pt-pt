---
title: Diagnósticos com métricas, alertas e saúde de recursos - Azure Standard Load Balancer
description: Utilize as métricas, alertas e informações de saúde dos recursos disponíveis para diagnosticar o seu Balançador de Carga Padrão Azure.
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
ms.openlocfilehash: da4c5f7891b518f4e6393f3fb4e153d464f4f2a2
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955540"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnóstico do Balanceador de Carga Standard com métricas, alertas e estado de funcionamento dos recursos

O Azure Standard Load Balancer expõe as seguintes capacidades de diagnóstico:

* **Métricas e alertas multidimensionais**: Fornece capacidades de diagnóstico multidimensionais através do [Azure Monitor](../azure-monitor/overview.md) para configurações padrão do balanceador de carga. Pode monitorizar, gerir e resolver problemas dos seus recursos padrão de balanceador de carga.

* **Saúde dos recursos**: O estado de saúde dos recursos do seu Balancer de Carga está disponível na página de Saúde dos Recursos no Monitor. Esta verificação automática informa-o da disponibilidade atual do seu recurso Balanceador de Carga.

Este artigo proporciona uma visita rápida a estas capacidades, e oferece formas de usá-las para o Balancer de Carga Padrão. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece métricas multidimensionais através das Métricas Azure no portal Azure, e ajuda-o a obter informações de diagnóstico em tempo real nos recursos do balanceador de carga. 

As várias configurações standard de balançadores de carga fornecem as seguintes métricas:

| Métrica | Tipo de recurso | Description | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade do caminho dos dados | Balanceador de carga público e interno | O Balanceador de Carga Standard exerce continuamente o caminho de dados a partir de uma região para o front-end do balanceador de carga, até à pilha SDN que suporta a sua VM. Enquanto se mantiverem casos saudáveis, a medição segue o mesmo caminho que o tráfego equilibrado da sua aplicação. O caminho de dados que os clientes utilizam também é validado. A medição é invisível para a aplicação e não interfere com outras operações.| Média |
| Estado da sonda de estado de funcionamento | Balanceador de carga público e interno | O Standard Load Balancer utiliza um serviço de sondagem de saúde distribuído que monitoriza a saúde do seu ponto final de aplicação de acordo com as definições de configuração. Esta métrica proporciona uma vista filtrada agregada ou por ponto final de cada ponto final no conjunto do balanceador de carga. Pode ver como o Balanceador de Carga vê o estado de funcionamento da sua aplicação, conforme indicado pela configuração da sonda de estado de funcionamento. |  Média |
| Pacotes SYN (sincronização) | Balanceador de carga público e interno | O Balanceador de Carga Standard não termina as ligações do Protocolo de Controlo de Transmissão (TCP) nem interage com os fluxos de pacotes TCP ou UDP. Os fluxos e os respetivos handshakes estão sempre entre a origem e a instância da VM. Para resolver melhor os problemas relacionados com o protocolo TCP, pode utilizar os contadores de pacotes SYN para compreender quantas tentativas de ligação TCP são feitas. A métrica reporta o número de pacotes SYN de TCP que foram recebidos.| Média |
| Ligações SNAT | Balanceador de carga público |O Balanceador de Carga Standard reporta o número de fluxos de saída mascarados para o front-end do endereço IP público. As portas de tradução de endereços de rede de origem (SNAT) são um recurso renovável. Esta métrica pode dar uma indicação de quão fortemente a sua aplicação está a depender do SNAT para os fluxos de saída originados. Os contadores de fluxos SNAT de saída bem-sucedidos e falhados são reportados e podem ser utilizados para resolver problemas e compreender o estado de funcionamento dos fluxos de saída.| Média |
| Portas SNAT atribuídas | Balanceador de carga público | O Balanceador de Carga Padrão informa o número de portas SNAT atribuídas por instância de backend | Média. |
| Portas SNAT usadas | Balanceador de carga público | O Balancer de Carga Padrão informa o número de portas SNAT que são utilizadas por instância backend. | Média | 
| Contadores de bytes |  Balanceador de carga público e interno | O Balanceador de Carga Standard reporta os dados processados por front-end. Pode notar que os bytes não são distribuídos igualmente pelas instâncias de back-end. Isto é esperado, uma vez que o algoritmo do Balanceador de Carga da Azure é baseado em fluxos | Média |
| Contadores de pacotes |  Balanceador de carga público e interno | O Balanceador de Carga Standard reporta os pacotes processados por front-end.| Média |

  >[!NOTE]
  >Ao utilizar o tráfego de distribuição de um equilibrador de carga interno através de um Pacote de Sínsma NVA ou firewall, as métricas do Contador byte e do Contador de Pacotes não estão disponíveis e mostrarão como zero. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Veja as suas métricas de balançador de carga no portal Azure

O portal Azure expõe as métricas do balanceador de carga através da página Métricas, que está disponível tanto na página de recursos do equilibrador de carga para um determinado recurso como na página do Monitor Azure. 

Para ver as métricas dos seus recursos standard balanceador de carga:
1. Vá à página Métricas e faça qualquer uma das seguintes:
   * Na página de recursos do balanceador de carga, selecione o tipo métrico na lista de drop-down.
   * Na página Azure Monitor, selecione o recurso do balançador de carga.
2. Desagregando o tipo de agregação métrica apropriado.
3. Opcionalmente, configuure a filtragem e o agrupamento necessários.
4. Opcionalmente, configurar o intervalo de tempo e agregação. Por padrão o tempo é apresentado na UTC.

  >[!NOTE] 
  >A agregação do tempo é importante na interpretação de certas métricas à medida que os dados são amostrados uma vez por minuto. Se a agregação de tempo estiver definida para cinco minutos e o tipo de agregação métrica Sum for utilizado para métricas como a atribuição de SNAT, o seu gráfico apresentará cinco vezes mais do que as portas SNAT alocadas. 

![Métricas para Balanceador de Carga Padrão](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Figura: Métrica de disponibilidade de caminho de dados para balanceador de carga padrão*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar métricas multidimensionais programáticamente através de APIs

Para obter orientações da API para a recuperação de definições e valores métricos multidimensionais, consulte [a Azure Monitoring REST API walkthrough](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Estas métricas podem ser escritas numa conta de armazenamento apenas através da opção 'Todas as Métricas'. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Alertas de configuração para métricas multidimensionais ###

O Azure Standard Load Balancer suporta alertas facilmente configuráveis para métricas multidimensionais. Configure limiares personalizados para métricas específicas para desencadear alertas com diferentes níveis de gravidade para capacitar uma experiência de monitorização de recursos sem toque.

Para configurar alertas:
1. Vá ao sub-lâmina de alerta para o equilibrador de carga
1. Criar nova regra de alerta
    1.  Condição de alerta de configuração
    1.  (Opcional) Adicione grupo de ação para reparação automatizada
    1.  Atribua gravidade, nome e descrição de alerta que permita uma reação intuitiva

  >[!NOTE]
  >A janela de configuração do estado de alerta mostrará séries de tempo para o histórico de sinais. Existe uma opção para filtrar esta série de tempo por dimensões como o Backend IP. Isto irá filtrar o gráfico da série de tempo, mas **não** o próprio alerta. Não é possível configurar alertas para endereços IP específicos do Backend.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Cenários de diagnóstico comuns e pontos de vista recomendados

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>O caminho dos dados está disponível para o meu Frontend do Balancer de Carga?
<details><summary>Expandir</summary>

A métrica de disponibilidade de caminhos de dados descreve a saúde da trajetória de dados dentro da região para o hospedeiro computacional onde os seus VMs estão localizados. A métrica é um reflexo da saúde da infraestrutura Azure. Pode utilizar a métrica para:
- Monitorize a disponibilidade externa do seu serviço
- Investigue mais profundamente e compreenda se a plataforma em que o seu serviço é implementado é saudável ou se o seu so ou instância de aplicação é saudável.
- Isole se um evento está relacionado com o seu serviço ou com o plano de dados subjacente. Não confunda esta métrica com o estado da sonda de saúde ("Disponibilidade de Backend Instance").

Para obter a disponibilidade do caminho de dados para os seus recursos padrão do Balancer de Carga:
1. Certifique-se de que o recurso correto do balanceador de carga está selecionado. 
2. Na lista de drop-down **métrica,** selecione **Data Path Availability**. 
3. Na lista de recuos da **agregação,** selecione **Avg**. 
4. Adicionalmente, adicione um filtro no endereço FRONTend IP ou na porta Frontend como a dimensão com o endereço IP frontal ou porta frontal necessário e, em seguida, agrupá-lo pela dimensão selecionada.

![Sondagem VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Detalhes de sondagem do balançor de carga*

A métrica é gerada por uma medição ativa em banda. Um serviço de sondagem na região origina o tráfego para a medição. O serviço é ativado assim que criar uma implantação com uma frente pública, e continua até remover a extremidade frontal. 

Um pacote que corresponda à parte frontal e à regra da sua implantação é gerado periodicamente. Percorre a região desde a nascente até ao hospedeiro, onde está localizado um VM na piscina traseira. A infraestrutura do balanceador de carga realiza as mesmas operações de equilíbrio e tradução de carga como para todos os outros tráfegos. Esta sonda está em banda no seu ponto final equilibrado de carga. Depois de a sonda chegar ao hospedeiro computacional, onde está localizado um VM saudável na piscina traseira, o hospedeiro computacional gera uma resposta ao serviço de sondagem. O seu VM não vê este tráfego.

A disponibilidade de datapath falha pelas seguintes razões:
- A sua implantação não tem VMs saudáveis restantes na piscina traseira. 
- Ocorreu uma falha na infraestrutura.

Para fins de diagnóstico, pode utilizar a métrica de disponibilidade do caminho de [dados juntamente com o estado da sonda de saúde](#vipavailabilityandhealthprobes).

Use **a Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>As instâncias backend para o meu Balancer de Carga estão a responder às sondas?
<details>
  <summary>Expandir</summary>
A métrica do estado da sonda de saúde descreve a saúde da sua implementação da aplicação como configurada por si quando configura a sonda de saúde do seu equilibrador de carga. O equilibrador de carga utiliza o estado da sonda de saúde para determinar para onde enviar novos fluxos. As sondas de saúde originam-se de um endereço de infraestrutura Azure e são visíveis dentro do so de hóspedes do VM.

Para obter o estado da sonda de saúde para os seus recursos standard balancer de carga:
1. Selecione a métrica **do Estado da Sonda de Saúde** com o tipo de agregação **Avg.** 
2. Aplique um filtro no endereço IP ou porta frontend necessário (ou ambos).

As sondas sanitárias falham pelas seguintes razões:
- Configura uma sonda de saúde para uma porta que não está a ouvir ou não responde ou está a usar o protocolo errado. Se o seu serviço estiver a utilizar as regras de devolução direta do servidor (DSR, ou IP flutuante), certifique-se de que o serviço está a ouvir no endereço IP da configuração IP do NIC e não apenas no loopback configurado com o endereço IP frontal.
- A sua sonda não é permitida pelo Grupo de Segurança da Rede, pela firewall oss do VM ou pelos filtros da camada de aplicação.

Use **a Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como verifico as estatísticas de ligação de saída? 
<details>
  <summary>Expandir</summary>
A métrica das ligações SNAT descreve o volume de ligações bem sucedidas e falhadas para [os fluxos de saída](./load-balancer-outbound-connections.md).

Um volume de ligações falhado superior a zero indica a exaustão da porta SNAT. Deve investigar mais para determinar o que pode estar a causar estas falhas. A exaustão portuária SNAT manifesta-se como uma falha no estabelecimento de um [fluxo de saída](./load-balancer-outbound-connections.md). Reveja o artigo sobre as ligações de saída para entender os cenários e mecanismos no trabalho, e para aprender a mitigar e projetar para evitar a exaustão do porto SNAT. 

Para obter estatísticas de conexão SNAT:
1. Selecione o tipo métrico **de ligações SNAT** e **Soma** como agregação. 
2. Estado de grupo por **ligação** para contagens de conexão SNAT bem sucedidas e falhadas que são representadas por diferentes linhas. 

![Ligação SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Contagem de ligação SNAT do balanceador de carga*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Como verifico o uso e alocação do porto SNAT?
<details>
  <summary>Expandir</summary>
A métrica dos portos SNAT usados rastreia quantas portas SNAT estão sendo consumidas para manter os fluxos de saída. Isto indica quantos fluxos únicos são estabelecidos entre uma fonte de internet e um VM de backend ou conjunto de escala de máquina virtual que está por trás de um equilibrador de carga e não tem um endereço IP público. Ao comparar o número de portas SNAT que está a utilizar com a métrica dos Portos SNAT atribuídos, pode determinar se o seu serviço está a sofrer ou em risco de exaustão SNAT e consequente falha de fluxo de saída. 

Se as suas métricas indicarem o risco de falha de fluxo de [saída,](./load-balancer-outbound-connections.md) consulte o artigo e tome medidas para mitigar isto para garantir a saúde do serviço.

Para visualizar a utilização e atribuição da porta SNAT:
1. Desagrega a agregação de tempo do gráfico para 1 minuto para garantir que os dados desejados são apresentados.
1. Selecione **portas SNAT usadas** e/ou **Portas SNAT atribuídas** como o tipo métrico e **média** como agregação
    * Por predefinição, estas métricas são o número médio de portas SNAT atribuídas ou utilizadas por cada VM ou VMSS backend, correspondente a todos os IPs públicos frontend mapeados para o Balanceador de Carga, agregados sobre TCP e UDP.
    * Para visualizar as portas SNAT totais utilizadas ou atribuídas para o balanceador de carga utilizar a agregação métrica **Soma**
1. Filtrar para um tipo de **protocolo** específico , um conjunto de **IPs backend** e/ou **IPs frontend**.
1. Para monitorizar a saúde por caso de backend ou frontend, aplique a divisão. 
    * A divisão de notas só permite visualizar uma única métrica de cada vez. 
1. Por exemplo, monitorizar a utilização do SNAT para os fluxos de TCP por máquina, agregado por **média,** dividido por **IPs de backend** e filtro por **Tipo de Protocolo**. 

![Alocação e utilização do SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figura: Alocação e utilização médias da porta TCP SNAT para um conjunto de VMs backend*

![Utilização do SNAT por instância de backend](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figura: Utilização da porta TCP SNAT por instância de backend*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como verifico as tentativas de ligação de entrada/saída para o meu serviço?
<details>
  <summary>Expandir</summary>
Uma métrica de pacotes SYN descreve o volume de pacotes TCP SYN, que chegaram ou foram enviados (para [fluxos de saída)](./load-balancer-outbound-connections.md)que estão associados a uma extremidade frontal específica. Pode utilizar esta métrica para compreender as tentativas de ligação TCP ao seu serviço.

Use **a Total** como agregação para a maioria dos cenários.

![Ligação SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN do balançador de carga*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como verifico o consumo de largura de banda da rede? 
<details>
  <summary>Expandir</summary>
A métrica dos bytes e dos contadores de pacotes descreve o volume de bytes e pacotes que são enviados ou recebidos pelo seu serviço numa base frontal.

Use **a Total** como agregação para a maioria dos cenários.

Para obter estatísticas de byte ou contagem de pacotes:
1. Selecione o tipo métrico **Bytes Count** e/ou **Packet Count,** com **o Avg** como agregação. 
2. Efetue um dos seguintes procedimentos:
   * Aplique um filtro num IP frontal específico, na porta frontal, no IP traseiro ou na porta traseira.
   * Obtenha estatísticas globais para o seu recurso de balançador de carga sem qualquer filtragem.

![Contagem de byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de bytes do balançador de carga*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Como diagnostico a minha implantação do balanceador de carga?
<details>
  <summary>Expandir</summary>
Ao utilizar uma combinação das métricas de Disponibilidade de Caminho de Dados e Estado da Sonda de Saúde num único gráfico, pode identificar onde procurar o problema e resolver o problema. Pode obter a garantia de que o Azure está a trabalhar corretamente e utilizar este conhecimento para determinar conclusivamente que a configuração ou aplicação é a causa principal.

Pode utilizar métricas de sonda de saúde para entender como a Azure vê a saúde da sua implantação de acordo com a configuração que forneceu. Olhar para as sondas de saúde é sempre um grande primeiro passo na monitorização ou determinação de uma causa.

Pode ir mais longe e usar a métrica de disponibilidade do Data Path para obter uma visão de como o Azure vê a saúde do plano de dados subjacente que é responsável pela sua implementação específica. Ao combinar ambas as métricas, pode isolar onde a falha pode estar, como ilustrado neste exemplo:

![Combinando disponibilidade de caminho de dados e métricas de estado da sonda de saúde](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: Combinando disponibilidade de caminho de dados e métricas de estado da sonda de saúde*

O gráfico apresenta as seguintes informações:
- A infraestrutura que hospeda os seus VMs não estava disponível e a 0% no início da tabela. Mais tarde, a infraestrutura era saudável e os VMs eram alcançáveis, e mais de um VM foi colocado na parte de trás. Esta informação é indicada pelo traço azul para a disponibilidade de trajetória de dados, que foi mais tarde a 100 por cento. 
- O estado da sonda de saúde, indicado pelo traço roxo, está a 0% no início da tabela. A área circular em destaque verde onde o estado da sonda de saúde se tornou saudável, e nessa altura a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes resolvam a implementação por conta própria sem terem de adivinhar ou pedir apoio se estão a ocorrer outras questões. O serviço estava indisponível porque as sondas de saúde estavam a falhar devido a uma configuração errada ou a uma aplicação falhada.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Estado da saúde dos recursos

O estado de saúde dos recursos do Balanceador de Carga Padrão é exposto através da **saúde dos recursos** existentes no **âmbito do Serviço de > Saúde** do Serviço de Monitorização . É avaliado de **dois em dois minutos** medindo a disponibilidade do caminho de dados que determina se os seus pontos finais de equilíbrio de carga frontal estão disponíveis.

| Estado da saúde dos recursos | Description |
| --- | --- |
| Disponível | O seu recurso balanceador de carga padrão é saudável e disponível. |
| Degradado | O seu balanceador de carga padrão tem eventos iniciados pela plataforma ou pelo utilizador com impacto no desempenho. A métrica de Disponibilidade do DataPath comunicou um estado de funcionamento inferior a 90%, mas superior a 25% durante, pelo menos, dois minutos. Você vai experimentar um impacto de desempenho moderado a grave. [Siga o guia RHC de resolução de problemas](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) para determinar se existem eventos iniciados pelo utilizador que causam impacto na sua disponibilidade.
| Indisponível | O seu recurso padrão de balanceador de carga não é saudável. A métrica de Disponibilidade de Datapath reportou menos 25% de saúde durante pelo menos dois minutos. Você sentirá um impacto significativo no desempenho ou falta de disponibilidade para a conectividade de entrada. Pode haver eventos de utilizador ou plataforma que causem indisponibilidade. [Siga o guia RHC de resolução de problemas](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) para determinar se existem eventos iniciados pelo utilizador com impacto na sua disponibilidade. |
| Desconhecido | O estado de saúde dos recursos para o seu recurso balanceador de carga padrão ainda não foi atualizado ou não recebeu informações de disponibilidade do Data Path nos últimos 10 minutos. Este estado deve ser transitório e refletirá o estado correto assim que os dados forem recebidos. |

Para ver a saúde dos seus recursos públicos standard balancer:
1. Selecione **Monitor**  >  **Service Health**.

   ![Página do monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: A ligação de saúde de serviço no Monitor Azure*

2. Selecione **a Saúde dos Recursos** e, em seguida, certifique-se de que o ID de **subscrição** e **o tipo de recurso = Balanceador de Carga** são selecionados.

   ![Estado da saúde dos recursos](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selecione recurso para vista para a saúde*

3. Na lista, selecione o recurso Load Balancer para visualizar o seu estado histórico de saúde.

    ![Estado de saúde do balanceador de carga](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Visão de saúde do equilibrador de recursos de carga*
 
A descrição genérica do estado de saúde dos recursos está disponível na documentação do [RHC](../service-health/resource-health-overview.md). Para os estatutos específicos do Balançador de Carga Azure estão listados na tabela abaixo: 


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Balancer de Carga Padrão](./load-balancer-overview.md).
- Saiba mais sobre a conectividade de saída do seu [balanceador de carga](./load-balancer-outbound-connections.md).
- Saiba mais sobre [o Azure Monitor](../azure-monitor/overview.md).
- Saiba mais sobre a [API do Monitor Azure](/rest/api/monitor/) e [como recuperar métricas através da REST API](/rest/api/monitor/metrics/list).
