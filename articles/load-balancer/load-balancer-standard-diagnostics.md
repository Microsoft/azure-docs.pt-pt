---
title: Diagnóstico de Balanceador de carga Standard do Azure
titlesuffix: Azure Load Balancer
description: Utilize as informações de estado de funcionamento e as métricas disponíveis para obter um diagnóstico para o Balanceador de carga Standard do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: Kumud
ms.openlocfilehash: ec68038a5b0fe7edca095e0d9b190d5da09c8e82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754691"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Diagnóstico de estado de funcionamento e de métricas para o Balanceador de carga Standard

Balanceador de carga Standard do Azure expõe o Balanceador de carga Standard do Azure oferece as seguintes capacidades de diagnóstico de recursos do:
* **Métricas multidimensionais**: Fornece novos recursos de diagnóstico multidimensionais através de [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para públicos e internos de configurações de Balanceador de carga. Pode monitorizar, gerir e resolver problemas de seus recursos do Balanceador de carga.

* **Estado de funcionamento dos recursos**: A página de Balanceador de carga no portal do Azure e a página de estado de funcionamento do recurso (sob o Monitor) expõem a secção de estado de funcionamento do recurso para a configuração de Balanceador de carga público do Balanceador de carga Standard.

Este artigo fornece um tour rápido estas capacidades e oferece maneiras de usá-los para o Balanceador de carga Standard.

## <a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Balanceador de carga do Azure fornece novas métricas multidimensionais através de novas métricas de Azure no portal do Azure e ajuda-o a obter informações de diagnóstico em tempo real sobre a sua carga de recursos do Balanceador. 

As várias configurações de Balanceador de carga Standard fornecem as métricas seguintes:

| Métrica | Tipo de recurso | Descrição | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade de caminho de dados (disponibilidade de VIP)| Balanceador de carga público | Balanceador de carga Standard exercita continuamente o caminho de dados de dentro de uma região para a carga balanceador front-end, todo o caminho para a pilha SDN, que oferece suporte a sua VM. Enquanto permanecerem instâncias de bom estado de funcionamento, a medição segue o mesmo caminho que o tráfego com balanceamento de carga da sua aplicação. O caminho de dados que utilizam os seus clientes também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.| Média |
| Estado de sonda de estado de funcionamento (disponibilidade DIP) |  Balanceador de carga internos e públicos | Balanceador de carga Standard utiliza um serviço de pesquisa de estado de funcionamento distribuído que monitoriza o estado de funcionamento do seu aplicativo do ponto de extremidade, de acordo com as definições de configuração. Esta métrica fornece um agregado ou por ponto de extremidade vista filtrada de cada ponto de extremidade de instância no conjunto de Balanceador de carga. Pode ver como o Balanceador de carga visualiza o estado de funcionamento do seu aplicativo, conforme indicado pela sua configuração de sonda de estado de funcionamento. |  Média |
| SYN (sincronizar) pacotes |  Balanceador de carga público | Balanceador de carga Standard não terminar ligações de protocolo de controlo de transmissão (TCP) ou interagir com os fluxos de pacotes TCP ou UDP. Fluxos e seus handshakes são sempre entre a origem e a instância VM. Para resolver melhor seus cenários de protocolo TCP, pode fazer uso de SYN contadores de pacotes para compreender quantos ligação de TCP são feitas tentativas. A métrica relata o número de pacotes de TCP SYN que foram recebidos.| Média |
| Ligações SNAT |  Balanceador de Carga Público |Balanceador de carga Standard relata o número de fluxos de saída que são masqueraded para o IP público endereço front-end. Portas de tradução (SNAT) de endereço de rede de origem são um recurso exhaustible. Esta métrica pode dar uma indicação de quanto seu aplicativo depende SNAT para os fluxos de originados de saída. Contadores para fluxos de SNAT de saída com êxito e falhados são reportadas e podem ser usados para solucionar problemas e compreender o estado de funcionamento dos seus fluxos de saída.| Média |
| Contadores de byte |  Balanceador de carga internos e públicos | Balanceador de carga Standard reporta os dados processados por front-end.| Média |
| Contadores de pacotes |  Balanceador de carga internos e públicos | Os relatórios do Balanceador de carga Standard pacotes processados por front-end.| Média |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Ver as métricas de Balanceador de carga no portal do Azure

O portal do Azure expõe as métricas de Balanceador de carga através da página de métricas, que está disponível na página de recursos do Balanceador de carga para um recurso específico e a página do Azure Monitor. 

Para ver as métricas para os seus recursos do Balanceador de carga Standard:
1. Vá para a página de métricas e efetue um dos seguintes procedimentos:
   * Na página de recursos do Balanceador de carga, selecione o tipo de métrica na lista pendente.
   * Na página do Azure Monitor, selecione o recurso do Balanceador de carga.
2. Defina o tipo de agregação adequada.
3. Opcionalmente, configure a filtragem necessária e o agrupamento.

    ![Métricas para o Balanceador de carga Standard](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Figura: Métrica de disponibilidade de caminho de dados para o Balanceador de carga Standard*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Obter métricas multidimensionais através de programação através de APIs

Para obter orientações de API para recuperação das definições de métricas multidimensionais e os valores, veja [instruções de API do REST de monitorização do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Cenários comuns de diagnóstico e vistas recomendadas

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>É o caminho de dados ativas e disponíveis para meu VIP de Balanceador de carga?

A métrica de disponibilidade de VIP descreve o estado de funcionamento do caminho de dados dentro da região para o anfitrião de computação onde estão localizadas as VMs. A métrica é um reflexo do Estado de funcionamento da infraestrutura do Azure. Pode usar a métrica para:
- Monitorizar a disponibilidade externa do seu serviço
- Aprofunde-se e compreender se a plataforma em que o seu serviço está implementado é bom estado de funcionamento ou se o SO convidado ou a instância da aplicação está em bom estada.
- Isole se um evento está relacionado com o serviço ou o plano de dados subjacente. Não confunda esta métrica com o estado de sonda de estado de funcionamento ("disponibilidade DIP").

Para obter a disponibilidade de caminho de dados para os seus recursos do Balanceador de carga Standard:
1. Certifique-se de que o recurso do Balanceador de carga correta está selecionado. 
2. Na **métrica** na lista pendente, selecione **disponibilidade de caminho de dados**. 
3. Na **agregação** na lista pendente, selecione **média**. 
4. Além disso, adicione um filtro no endereço IP de front-end ou a porta de front-end como a dimensão com o endereço IP de front-end necessário ou a porta de front-end e, em seguida, agrupá-los pela dimensão selecionada.

![VIP de pesquisa](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Detalhes de pesquisa no front-end Balanceador de carga*

A métrica é gerada por uma medida de Active Directory, em banda. Um serviço de pesquisa dentro da região se origina o tráfego para a medição. O serviço é ativado quando cria uma implementação com um front-end público, e ele continua até remover o front-end. 

>[!NOTE]
>Internos front-ends não são suportadas neste momento. 

Um pacote de front-end da sua implementação e a regra de correspondência é gerado periodicamente. Ele percorre a região da origem para o anfitrião onde está localizada a uma VM no conjunto de back-end. A infraestrutura de Balanceador de carga executa as mesmas operações de tradução e balanceamento de carga para de todos os outros tráfegos. Esta pesquisa é em banda no seu ponto final com balanceamento de carga. Depois da sonda é recebido no anfitrião de computação, onde uma bom estado de funcionamento VM no conjunto de back-end está localizada, o anfitrião de computação gera uma resposta para o serviço de pesquisa. A VM não vê este tráfego.

Disponibilidade de VIP falha pelos seguintes motivos:
- A sua implementação tem sem VMs em bom estado restantes no conjunto de back-end. 
- Ocorreu uma falha de infraestrutura.

Para fins de diagnóstico, pode utilizar o [métrica de disponibilidade de caminho de dados, juntamente com o estado de sonda de estado de funcionamento](#vipavailabilityandhealthprobes).

Uso **média** como a agregação para a maioria dos cenários.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>As instâncias de back-end para meu VIP estão a responder às sondas?

A métrica de estado de sonda de estado de funcionamento descreve o estado de funcionamento da implementação da aplicação, conforme configurado por si, ao configurar a sonda de estado de funcionamento do seu Balanceador de carga. O Balanceador de carga utiliza o estado da sonda de estado de funcionamento para determinar para onde enviar novos fluxos. Sondas de estado de funcionamento se originam a partir de um endereço de infraestrutura do Azure e são visíveis no SO convidado da VM.

Para obter o estado de sonda de estado de funcionamento de recursos do Balanceador de carga Standard:
1. Selecione o **estado de sonda de estado de funcionamento** métrica com **média** tipo de agregação. 
2. Aplique um filtro de endereço IP de front-end ou porta (ou ambos).

Sondas de estado de funcionamento falharem pelos seguintes motivos:
- Configurar uma sonda de estado de funcionamento para uma porta que está a escutar não ou não está a responder ou está a utilizar o protocolo de errado. Se o seu serviço está a utilizar devolução direta do servidor (DSR ou IP flutuante) regras, certifique-se de que o serviço está a escutar no endereço IP da configuração de IP do NIC e não apenas no que está configurado com o IP de front-end loopback de endereços.
- Sua pesquisa não é permitida pelo grupo de segurança de rede, firewall de SO convidado da VM ou os filtros de camada de aplicativo.

Uso **média** como a agregação para a maioria dos cenários.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como posso ver meu estatísticas de ligação de saída? 

A métrica de ligações SNAT descreve o volume de ligações com êxito ou falhadas para [fluxos de saída](https://aka.ms/lboutbound).

Um volume de ligações falhadas de maior que zero indica exaustão de porta SNAT. Deve investigar mais aprofundadamente para determinar o que possam estar causando essas falhas. Exaustão de porta SNAT manifesta como uma falha ao estabelecer uma [fluxo de saída](https://aka.ms/lboutbound). Consulte o artigo sobre ligações de saída para compreender os cenários e mecanismos no trabalho e para aprender a mitigar e conceção para evitar o esgotamento de porta SNAT. 

Para obter estatísticas de ligações SNAT:
1. Selecione **ligações SNAT** tipo de métrica e **soma** como agregação. 
2. Agrupar por **estado de ligação** para com êxito ou falhadas contagens de ligação SNAT que são representadas por linhas diferentes. 

![Ligação de SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Total de ligações de SNAT de Balanceador de carga*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como posso ver as tentativas de ligação de entrada/saída para o meu serviço?

Uma métrica de pacotes SYN descreve o volume de pacotes de TCP SYN, que chegaram ou foram enviados (para [fluxos de saída](https://aka.ms/lboutbound)) que estão associados um front-end específico. Pode utilizar esta métrica para compreender as tentativas de ligação TCP ao seu serviço.

Uso **Total** como a agregação para a maioria dos cenários.

![Ligação de SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN de Balanceador de carga*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como posso ver meu consumo de largura de banda de rede? 

Os bytes e contadores de pacotes métrica descreve o volume de bytes e pacotes que são enviadas ou recebidas pelo seu serviço numa base por-front-end.

Uso **Total** como a agregação para a maioria dos cenários.

Para obter estatísticas de contagem de bytes ou pacote:
1. Selecione o **contagem de Bytes** e/ou **contagem de pacotes** tipo de métrica com **média** como a agregação. 
2. Efetue um dos seguintes procedimentos:
   * Aplique um filtro de um IP de front-end específico, a porta de front-end, o IP de back-end ou a porta de back-end.
   * Obtenha estatísticas gerais para o seu recurso do Balanceador de carga sem filtragem.

![Contagem de bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de bytes do Balanceador de carga*

#### <a name = "vipavailabilityandhealthprobes"></a>Como posso diagnosticar minha implementação do Balanceador de carga?

Ao utilizar uma combinação da disponibilidade de VIP e métricas de sonda de estado de funcionamento num único gráfico pode identificar onde procurar o problema e resolver o problema. Pode obter a garantia de que o Azure está a funcionar corretamente e use esse conhecimento para determinar as consequências que a aplicação ou de configuração é a causa raiz.

Pode utilizar as métricas de sonda de estado de funcionamento para compreender como o Azure visualiza o estado de funcionamento da sua implementação de acordo com a configuração que forneceu. Olhando para sondas de estado de funcionamento é sempre um ótimo primeiro passo na monitorização ou determinar uma causa.

Pode dar um passo adicional e utilize métricas de disponibilidade de VIP para obter informações sobre como o Azure visualiza o estado de funcionamento ao plano de dados subjacente, que é responsável por sua implementação específica. Ao combinar as duas métricas, pode isolar em que a falha pode ser, conforme ilustrado neste exemplo:

![A combinação de métricas de disponibilidade de caminho de dados e estado de sonda de estado de funcionamento](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: A combinação de métricas de disponibilidade de caminho de dados e estado de sonda de estado de funcionamento*

O gráfico apresenta as seguintes informações:
- A infraestrutura que aloja as suas VMs era indisponível e por cento de 0 no início do gráfico. Mais tarde, a infra-estrutura foi bom estado de funcionamento e as VMs foram acessíveis e mais do que uma VM foi colocada no back-end. Esta informação é indicada pelo rastreio de azul para disponibilidade de caminho de dados (disponibilidade de VIP), que era posterior de 100 por cento. 
- O estado de sonda de estado de funcionamento (disponibilidade DIP), indicado pelo rastreio roxa, é de 0% no início do gráfico. A área dentro de um círculo em verde destaques em que o estado de sonda de estado de funcionamento (disponibilidade DIP) tornou-se em bom estado, e, nesse ponto a implementação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite aos clientes resolver problemas relacionados com a implementação por conta própria sem ter de adivinhar ou peça ao suporte se outros problemas estão a ocorrer. O serviço não estava disponível, porque as sondas de estado de funcionamento, ocorreram falhas devido a uma configuração incorreta ou uma aplicação que falhou.

### <a name = "Limitations"></a>Limitações

Disponibilidade de VIP está atualmente disponível apenas para o públicos front-ends.

## <a name = "ResourceHealth"></a>Estado de funcionamento do recurso

Estado de funcionamento dos recursos do Balanceador de carga Standard é exposto por meio de existente **estado de funcionamento do recurso** sob **Monitor > Service Health**.

>[!NOTE]
>Estado de funcionamento do recurso de Balanceador de carga está atualmente disponível para configuração pública de Balanceador de carga Standard apenas. Recursos de SKUs de Balanceador de carga básico ou de recursos do Balanceador de carga interno não expõem o estado de funcionamento do recurso.

Para ver o estado de funcionamento dos seus recursos do Balanceador de carga Standard públicos:
1. Selecione **Monitor** > **estado de funcionamento do serviço**.

   ![Página objeto da monitorização](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: A ligação de estado de funcionamento do serviço no Azure Monitor*

2. Selecione **Resource Health**e, em seguida, certifique-se de que **ID de subscrição** e **tipo de recurso = Balanceador de carga** estão selecionadas.

   ![Estado de funcionamento do recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selecione o recurso para a vista de estado de funcionamento*

3. Na lista, selecione o recurso de Balanceador de carga para ver o estado do histórico do Estado de funcionamento.

    ![Estado de funcionamento do Balanceador de carga](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Vista de estado de funcionamento do recurso de Balanceador de carga*
 
Os vários Estados de estado de funcionamento do recurso e suas descrições estão listadas na tabela a seguir: 

| Estado de funcionamento do recurso | Descrição |
| --- | --- |
| Disponível | O recurso de Balanceador de carga standard público é bom estado de funcionamento e está disponível. |
| Não disponível | O recurso de Balanceador de carga standard público não está em bom estado. Diagnosticar o estado de funcionamento, selecionando **do Azure Monitor** > **métricas**.<br>(*Indisponível* estado também poderá significar que o recurso não está ligado com o Balanceador de carga standard público.) |
| Desconhecido | Estado de funcionamento do recurso para o seu recurso do Balanceador de carga standard público ainda não foi atualizado.<br>(*Desconhecido* estado também poderá significar que o recurso não está ligado com o Balanceador de carga standard público.)  |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre sua [conectividade de saída do Balanceador de carga](https://aka.ms/lboutbound).
- Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Saiba mais sobre o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) e [como obter métricas através da REST API](/rest/api/monitor/metrics/list).


