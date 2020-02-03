---
title: Diagnósticos com métricas, alertas e integridade de recursos-Azure Standard Load Balancer
description: Use as informações disponíveis sobre métricas, alertas e recursos de integridade para diagnosticar sua Standard Load Balancer do Azure.
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
ms.openlocfilehash: 9fd1e72568b4f0c8813a5d050ce7fa7214ca7cd9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722446"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnóstico do Balanceador de Carga Standard com métricas, alertas e estado de funcionamento dos recursos

O Standard Load Balancer do Azure expõe os seguintes recursos de diagnóstico:

* **Métricas e alertas multidimensionais:** Fornece capacidades de diagnóstico multidimensionais através [do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para configurações padrão de equilíbrio de carga. Você pode monitorar, gerenciar e solucionar problemas de seus recursos padrão do Load Balancer.

* **Saúde dos recursos**: A página balanceiro de carga no portal Azure e na página de Saúde de Recursos (sob monitor) expõem a secção de Saúde de Recursos para O Equilíbrio de Carga Padrão. 

Este artigo fornece um tour rápido por esses recursos e oferece maneiras de usá-los para Standard Load Balancer. 

## <a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece métricas multidimensionais por meio das métricas do Azure na portal do Azure e ajuda você a obter informações de diagnóstico em tempo real sobre os recursos do balanceador de carga. 

As várias configurações de Standard Load Balancer fornecem as seguintes métricas:

| Métrica | Tipo de recurso | Descrição | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade do caminho de dados (disponibilidade VIP)| Balanceador de carga público e interno | Standard Load Balancer exercita continuamente o caminho de dados de dentro de uma região para o front-end do balanceador de carga, até a pilha SDN que dá suporte à sua VM. Desde que as instâncias íntegras permaneçam, a medida segue o mesmo caminho que o tráfego com balanceamento de carga do aplicativo. O caminho de dados que os clientes usam também é validado. A medida é invisível para seu aplicativo e não interfere em outras operações.| Média |
| Status da investigação de integridade (disponibilidade DIP) | Balanceador de carga público e interno | Standard Load Balancer usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com suas definições de configuração. Essa métrica fornece uma exibição filtrada por ponto de extremidade ou agregado de cada ponto de extremidade de instância no pool do balanceador de carga. Você pode ver como Load Balancer exibe a integridade do seu aplicativo, conforme indicado pela configuração da investigação de integridade. |  Média |
| Pacotes SYN (sincronizar) | Balanceador de carga público e interno | Standard Load Balancer não encerra as conexões de protocolo TCP ou interage com os fluxos de pacotes TCP ou UDP. Os fluxos e seus Handshakes sempre estão entre a origem e a instância de VM. Para solucionar melhor os cenários de protocolo TCP, você pode fazer uso de contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica relata o número de pacotes TCP SYN que foram recebidos.| Média |
| Conexões SNAT | Balanceador de carga público |Standard Load Balancer relata o número de fluxos de saída mascarados para o front-end do endereço IP público. As portas SNAT (conversão de endereço de rede de origem) são um recurso esse esgotável. Essa métrica pode dar uma indicação de quanto seu aplicativo está contando com SNAT para fluxos originados de saída. Os contadores de fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.| Média |
| Contadores de bytes |  Balanceador de carga público e interno | Standard Load Balancer relata os dados processados por front-end. Pode notar que os bytes não são distribuídos igualmente pelos casos de backend. Isto é esperado como o algoritmo de Balancer de Carga do Azure é baseado em fluxos | Média |
| Contadores de pacotes |  Balanceador de carga público e interno | Standard Load Balancer relata os pacotes processados por front-end.| Média |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Exibir as métricas do balanceador de carga no portal do Azure

O portal do Azure expõe as métricas do balanceador de carga por meio da página métricas, que está disponível na página de recursos do balanceador de carga para um recurso específico e a página Azure Monitor. 

Para exibir as métricas para seus recursos de Standard Load Balancer:
1. Vá para a página métricas e siga um destes procedimentos:
   * Na página de recursos do balanceador de carga, selecione o tipo de métrica na lista suspensa.
   * Na página Azure Monitor, selecione o recurso de balanceador de carga.
2. Defina o tipo de agregação apropriado.
3. Opcionalmente, configure a filtragem e o agrupamento necessários.

    ![Métricas para Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Figura: Métrica de disponibilidade de caminhos de dados para o equilíbrio de carga padrão*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar métricas multidimensionais programaticamente via APIs

Para obter orientação da API para recuperar definições e valores métricos multidimensionais, consulte a passagem da API de [Monitorização do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Estas métricas podem ser escritas numa conta de armazenamento apenas através da opção 'All Metrics'. 

### <a name = "DiagnosticScenarios"></a>Cenários de diagnóstico comuns e vistas recomendadas

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>O caminho de dados está ativo e disponível para o VIP do balanceador de carga?

A métrica de disponibilidade de VIP descreve a integridade do caminho de dados dentro da região para o host de computação onde as VMs estão localizadas. A métrica é uma reflexão da integridade da infraestrutura do Azure. Você pode usar a métrica para:
- Monitorar a disponibilidade externa do seu serviço
- Aprofunde-se e entenda se a plataforma na qual seu serviço está implantado está íntegra ou se a instância do sistema operacional convidado ou do aplicativo está íntegra.
- Isole se um evento está relacionado ao seu serviço ou ao plano de dados subjacente. Não confunda essa métrica com o status da investigação de integridade ("disponibilidade DIP").

Para obter a disponibilidade do caminho de dados para seus recursos de Standard Load Balancer:
1. Verifique se o recurso correto do balanceador de carga está selecionado. 
2. Na lista de drop-down **métrica,** selecione Disponibilidade de **Caminho de Dados**. 
3. Na lista de **agregação,** selecione **Avg**. 
4. Além disso, adicione um filtro no endereço IP de front-end ou na porta de front-end como a dimensão com o endereço IP de front-end ou a porta de front-ends necessária e agrupe-os pela dimensão selecionada.

![Investigação de VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Carregador De equilíbrio Frontal detalhes*

A métrica é gerada por uma medida ativa e em banda. Um serviço de investigação dentro da região origina o tráfego para a medição. O serviço é ativado assim que você cria uma implantação com um front-end público e continua até que você remova o front-end. 

Um pacote correspondente ao front-end e à regra da sua implantação é gerado periodicamente. Ele percorre a região da origem para o host em que uma VM no pool de back-end está localizada. A infraestrutura do balanceador de carga executa as mesmas operações de balanceamento de carga e tradução que o faz para todos os outros tráfegos. Essa investigação é em banda em seu ponto de extremidade com balanceamento de carga. Depois que a investigação chega no host de computação, onde uma VM íntegra no pool de back-end está localizada, o host de computação gera uma resposta para o serviço de investigação. Sua VM não vê esse tráfego.

A disponibilidade do VIP falha pelos seguintes motivos:
- Sua implantação não tem VMs íntegras restantes no pool de back-end. 
- Ocorreu uma interrupção de infraestrutura.

Para fins de diagnóstico, pode utilizar a métrica de disponibilidade do [caminho de dados juntamente com o estado da sonda de saúde](#vipavailabilityandhealthprobes).

Use a **Média** como a gregação para a maioria dos cenários.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>As instâncias de back-end para meu VIP estão respondendo às investigações?

A métrica de status de investigação de integridade descreve a integridade da implantação do aplicativo conforme configurado por você quando você configura a investigação de integridade do balanceador de carga. O balanceador de carga usa o status da investigação de integridade para determinar para onde enviar novos fluxos. As investigações de integridade originam-se de um endereço de infraestrutura do Azure e são visíveis no SO convidado da VM.

Para obter o status da investigação de integridade para seus recursos de Standard Load Balancer:
1. Selecione a métrica do Estado da **Sonda de Saúde** com o tipo de agregação **Avg.** 
2. Aplique um filtro na porta ou endereço IP de front-end necessário (ou ambos).

As investigações de integridade falham pelos seguintes motivos:
- Você configura uma investigação de integridade para uma porta que não está escutando ou não respondendo ou está usando o protocolo errado. Se o serviço estiver usando regras de retorno de servidor direto (DSR ou IP flutuante), verifique se o serviço está escutando no endereço IP da configuração de IP da NIC e não apenas no auto-retorno configurado com o endereço IP de front-end.
- Sua investigação não é permitida pelo grupo de segurança de rede, pelo firewall do sistema operacional convidado da VM ou pelos filtros da camada de aplicativo.

Use a **Média** como a gregação para a maioria dos cenários.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como fazer verificar minhas estatísticas de conexão de saída? 

A métrica de ligações SNAT descreve o volume de ligações bem sucedidas e falhadas para [os fluxos de saída](https://aka.ms/lboutbound).

Um volume de conexões com falha maior que zero indica esgotamento de porta SNAT. Você deve investigar ainda mais para determinar o que pode estar causando essas falhas. A exaustão da porta SNAT manifesta-se como uma falha na [criação](https://aka.ms/lboutbound)de um fluxo de saída . Examine o artigo sobre conexões de saída para entender os cenários e mecanismos no trabalho e para saber como mitigar e projetar para evitar o esgotamento da porta SNAT. 

Para obter estatísticas de conexão SNAT:
1. Selecione o tipo métrico de **ligações SNAT** e a **soma** como agregação. 
2. Grupo por **Connection State** para contagens de ligação SNAT bem sucedidas e falhadas que são representadas por diferentes linhas. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Contagem de ligação SNAT do balancedor de carga*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como fazer verificar tentativas de conexão de entrada/saída para meu serviço?

Uma métrica de pacotes SYN descreve o volume de pacotes De SYN TCP, que chegaram ou foram enviados (para [fluxos de saída](https://aka.ms/lboutbound)) que estão associados a uma extremidade frontal específica. Você pode usar essa métrica para entender as tentativas de conexão TCP com o serviço.

Use a **Total** como agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN do Balancer de Carga*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como fazer verificar o consumo de largura de banda da rede? 

A métrica bytes e contadores de pacotes descreve o volume de bytes e os pacotes que são enviados ou recebidos pelo seu serviço em uma base por front-end.

Use a **Total** como agregação para a maioria dos cenários.

Para obter estatísticas de contagem de pacotes ou bytes:
1. Selecione o tipo métrico **de Contagem de Bytes** e/ou Contagem de **Pacotes,** com **Avg** como agregação. 
2. Efetue um dos seguintes procedimentos:
   * Aplique um filtro em um IP de front-end específico, porta de front-end, IP de back-end ou porta de back-end.
   * Obtenha estatísticas gerais para o recurso de balanceador de carga sem nenhuma filtragem.

![Contagem de bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de byte saqueta do balancer de carga*

#### <a name = "vipavailabilityandhealthprobes"></a>Como diagnostico a minha implantação de equilibradores de carga?

Usando uma combinação das métricas de disponibilidade de VIP e de investigação de integridade em um único gráfico, você pode identificar onde procurar o problema e resolver o problema. Você pode ter a garantia de que o Azure está funcionando corretamente e usar esse conhecimento para determinar de forma conclusiva que a configuração ou o aplicativo é a causa raiz.

Você pode usar métricas de investigação de integridade para entender como o Azure exibe a integridade de sua implantação de acordo com a configuração que você forneceu. Examinar as investigações de integridade é sempre uma ótima primeira etapa no monitoramento ou na determinação de uma causa.

Você pode levar um passo adiante e usar métricas de disponibilidade de VIP para obter informações sobre como o Azure exibe a integridade do plano de dados subjacente que é responsável pela sua implantação específica. Ao combinar ambas as métricas, você pode isolar onde a falha pode ser, conforme ilustrado neste exemplo:

![Combinando métricas de status de investigação de integridade e disponibilidade de caminho de dados](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: Combinando disponibilidade de caminhos de dados e métricas de estado da sonda de saúde*

O gráfico exibe as seguintes informações:
- A infraestrutura que hospeda suas VMs não estava disponível e está em 0% no início do gráfico. Posteriormente, a infraestrutura estava íntegra e as VMs estavam acessíveis e mais de uma VM foi colocada no back-end. Essas informações são indicadas pelo rastreamento azul para disponibilidade de caminho de dados (disponibilidade de VIP), que foi posterior em 100%. 
- O status da investigação de integridade (disponibilidade DIP), indicado pelo rastreamento roxo, está em 0% no início do gráfico. A área circulada em verde destaca onde o status da investigação de integridade (disponibilidade DIP) se tornou íntegro e, em que ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes solucionem problemas de implantação por conta própria sem precisar adivinhar ou perguntar ao suporte se outros problemas estão ocorrendo. O serviço não estava disponível porque as investigações de integridade estavam falhando devido a uma configuração incorreta ou a um aplicativo com falha.

## <a name = "ResourceHealth"></a>Estado de saúde dos recursos

O estado de saúde dos recursos standard load balancer é exposto através da **saúde de recursos** existente no **Monitor > Service Health**.

Para exibir a integridade de seus recursos de Standard Load Balancer públicos:
1. Selecione **Monitor** > **Saúde**do Serviço .

   ![Página de monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: A ligação de saúde de serviço no Monitor Azure*

2. Selecione **Saúde de Recursos**, e, em seguida, certifique-se de que **o ID de subscrição** e o **tipo de recurso = Balancer de carga** são selecionados.

   ![Status de integridade do recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selecione recurso para vista para a saúde*

3. Na lista, selecione o recurso de Load Balancer para exibir seu status de integridade histórico.

    ![Status de integridade do Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Visão de saúde dos recursos do Balancer de Carga*
 
Os vários status de integridade de recursos e suas descrições são listados na tabela a seguir: 

| Status de integridade do recurso | Descrição |
| --- | --- |
| Disponível | O recurso padrão do Load Balancer está íntegro e disponível. |
| Indisponível | O recurso padrão do Load Balancer não está íntegro. Diagnosticar a saúde selecionando **O Monitor > ** **Métricas.**<br>(O estado*indisponível* também pode significar que o recurso não está ligado ao seu equilibrador de carga padrão.) |
| Desconhecido | O status de integridade do recurso para o recurso padrão do Load Balancer ainda não foi atualizado.<br>(O estado*desconhecido* também pode significar que o recurso não está ligado ao seu equilibrador de carga padrão.)  |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre a sua conectividade de saída do [seu balancer de carga.](https://aka.ms/lboutbound)
- Saiba mais sobre [o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/overview)
- Saiba mais sobre a [API REST Do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) e [como recuperar métricas através da API REST](/rest/api/monitor/metrics/list).
