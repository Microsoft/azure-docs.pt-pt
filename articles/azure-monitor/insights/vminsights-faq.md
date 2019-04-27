---
title: Monitor do Azure para VMs (pré-visualização) perguntas mais frequentes | Documentos da Microsoft
description: Monitor do Azure para VMs é uma solução no Azure que combina o estado de funcionamento e desempenho de monitorização do sistema de operacional de VM do Azure, bem como detetar automaticamente os componentes da aplicação e dependências com outros recursos e mapeia a comunicação entre -los. Este artigo responde a perguntas comuns.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: ade12225a470b64278b9d27676ceab768f64d904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596592"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Monitor do Azure para VMs (pré-visualização) perguntas mais frequentes
O FAQ do Microsoft é uma lista de perguntas freqüentes sobre o Azure Monitor para as VMs. Se tiver perguntas adicionais sobre a solução, vá para o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Posso carregar para uma área de trabalho existente?
Se as máquinas virtuais já estiverem ligadas a uma área de trabalho do Log Analytics, pode continuar a usar essa área de trabalho quando a integração para o Azure Monitor para as VMs, fornecidas for em uma das regiões suportadas listadas [aqui](vminsights-onboard.md#prerequisites).

Quando a integração, podemos configurar contadores de desempenho para a área de trabalho que fará com que todas as VMs a comunicar dados para a área de trabalho para iniciar a recolha destas informações para exibição e análise no Azure Monitor para as VMs.  Como resultado, verá os dados de desempenho de todas as VMs ligadas à área de trabalho selecionada.  As funcionalidades de estado de funcionamento e o mapa apenas estão ativadas para as VMs que especificou para carregar.

Para obter mais informações sobre o desempenho do que os contadores estão ativados, consulte a nossa [inclusão](vminsights-onboard.md) artigo.

## <a name="can-i-onboard-to-a-new-workspace"></a>Posso carregar para uma nova área de trabalho? 
Se as suas VMs não estiverem atualmente ligadas numa área de trabalho do Log Analytics existente, tem de criar uma nova área de trabalho para armazenar os dados.  Criar uma nova área de trabalho padrão é feito automaticamente se configurar o numa única VM do Azure para o Azure Monitor para VMs através do portal do Azure.

Se optar por utilizar o método baseado em script, essas etapas são abordadas os [inclusão](vminsights-onboard.md) artigo. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que devo fazer se a minha VM já está a comunicar com uma área de trabalho existente?
Se já está a recolher dados das suas máquinas virtuais, poderá ter já configurado-lo a dados de relatórios numa área de trabalho do Log Analytics existente.  Desde que essa área de trabalho é de uma de nossas regiões suportadas, pode ativar o Azure Monitor para VMs a essa área de trabalho já existente.  Se já estiver a utilizar a área de trabalho não estiver em uma de nossas regiões suportadas, não será capaz de carregar para o Azure Monitor para VMs neste momento.  Estamos a trabalhar ativamente para oferecer suporte a mais regiões.

>[!NOTE]
>Podemos configurar contadores de desempenho para a área de trabalho que afeta todas as VMs que reportam à área de trabalho, se é ou não tiver optado por carregá-los para o Azure Monitor para as VMs. Para obter mais detalhes sobre como os contadores de desempenho são configurados para a área de trabalho, consulte nosso [documentação](../../azure-monitor/platform/data-sources-performance-counters.md). Para obter informações sobre os contadores configurado para o Azure Monitor para VMs, consulte nosso [documentação de inclusão](vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Por que a minha VM não conseguir carregar?
Quando uma VM do Azure no portal do Azure de integração, ocorrem os seguintes passos:

* Uma área de trabalho do Log Analytics do padrão é criada, se que a opção tiver sido selecionada.
* Os contadores de desempenho são configurados para a área de trabalho selecionada. Caso este passo falhe, observe que algumas das tabelas e gráficos de desempenho não estão a mostrar dados para a VM efetuou. Pode corrigir este problema ao executar o script do PowerShell documentado [aqui](vminsights-onboard.md#enable-with-powershell).
* O agente Log Analytics está instalado em VMs do Azure com uma extensão de VM, se for determinado é necessário.  
* O Azure Monitor para agente de dependência de mapa de VMs é instalado em VMs do Azure com uma extensão, se for determinado é necessário.  
* Componentes de Monitor do Azure que suporta a funcionalidade de estado de funcionamento estão configurados, se necessário, e a VM está configurada para dados de estado de funcionamento do relatório.

Durante o processo de inclusão, verificamos para obter o estado em cada um dos acima descritos para retornar um status de notificação para si no portal. Configuração de área de trabalho e a instalação do agente normalmente demora 5 a 10 minutos. Visualização de dados de monitorização e estado de funcionamento no portal do demoram um 5 a 10 minutos adicionais.  

Se iniciou a integração e consulte as mensagens que indica que a VM tem de ser carregada, permitem até 30 minutos para a VM concluir o processo. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Apenas eu ativado o Azure Monitor para as VMs, por que razão vejo todas as minhas VMs monitorizadas pela funcionalidade de estado de funcionamento?
A funcionalidade de estado de funcionamento está ativada para todas as VMs que estão ligadas à área de trabalho do Log Analytics, mesmo quando a ação de início de uma única VM.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Pode modificar a agenda para quando os critérios de estado de funcionamento avalia uma condição?
Não, o período de tempo e a frequência dos critérios de estado de funcionamento não podem ser modificados com esta versão. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Pode desativar os critérios de estado de funcionamento de uma condição, não preciso monitorizar?
Critérios de estado de funcionamento não não possível desativar nesta versão.

## <a name="are-the-health-alert-severities-configurable"></a>É o estado de funcionamento gravidades alerta configuráveis?  
Não é possível alterar a gravidade do alerta de estado de funcionamento, eles só podem ser ativados ou desativados. Além disso, alguns alerta gravidades atualizar com base no estado de critérios de estado de funcionamento. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Se eu reconfigurar as definições de um critério de estado de funcionamento específico, pode ela ser confinada para uma instância específica?  
Se modificar qualquer definição de uma instância de critério de estado de funcionamento, todas as instâncias de critérios de estado de funcionamento do mesmo tipo na VM do Azure são modificadas. Por exemplo, se o limiar da instância de critério de estado de funcionamento do espaço livre de disco que corresponde ao disco lógico c: for modificado, este limite aplica-se a todos os outros discos lógicos que são detetados e monitorizados para a mesma VM.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>A funcionalidade de estado de funcionamento monitoriza processadores lógicos e núcleos?
Não, processador individual e critérios de estado de funcionamento ao nível do processador lógico não está incluída para um Windows, apenas a utilização Total de CPU é monitorizada por predefinição para avaliar efetivamente a pressão de CPU com base no número total de CPUs lógicas disponíveis para a VM do Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>É o estado de funcionamento de todos os limiares de critérios configuráveis?  
Os limites de critérios de estado de funcionamento que se destinam uma VM do Windows não são fáceis de serem modificados, porque os Estados de funcionamento estão definidos como *em execução* ou *disponível*. Quando consulta o estado de funcionamento do [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), ele exibe a *comparisonOperator* valor de **LessThan** ou **GreaterThan** com um *limiar* o valor de **4** para a entidade ou o serviço se:
   - Estado de funcionamento do serviço do cliente DNS – serviço não está em execução. 
   - Estado de funcionamento do serviço do cliente DHCP – serviço não está em execução. 
   - Estado de funcionamento do serviço RPC – serviço não está em execução. 
   - Estado de funcionamento do serviço do firewall de Windows – serviço não está em execução.
   - Estado de funcionamento do serviço do registo de eventos de Windows – serviço não está em execução. 
   - Estado de funcionamento do servidor service – serviço não está em execução. 
   - Funcionamento de serviço de gestão remota do Windows – serviço não está em execução. 
   - Erro de sistema de ficheiros ou corrupção – disco lógico não está disponível.

Limiares para os seguintes critérios de estado de funcionamento do Linux não são fáceis de serem modificados, porque o respetivo estado de funcionamento já está definido como *true*. Apresenta o estado de funcionamento a *comparisonOperator* com um valor **LessThan** e *limiar* valor de **1** quando consultados a partir do Carga de trabalho monitorização o API para a entidade, dependendo de seu contexto:
   - Estado do disco lógico – o disco lógico não está online / disponíveis
   - Estado do disco – o disco não está online / disponíveis
   - Estado do adaptador de rede - placa de rede está desativado

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Como posso modificar alertas que estão incluídas com a funcionalidade de estado de funcionamento?
Regras de alerta que estão definidas para cada critério de estado de funcionamento não são apresentadas no portal do Azure. Pode ativar ou desativar um alerta de estado de funcionamento apenas na regra a [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Além disso, não é possível atribuir um [grupo de ação do Azure Monitor](../../azure-monitor/platform/action-groups.md) para alertas de estado de funcionamento no portal do Azure. Só pode utilizar a API de definição de notificação para configurar um grupo de ação para ser acionada sempre que é acionado um alerta de estado de funcionamento. Atualmente, pode atribuir grupos de ação em relação a uma VM para que todos os *alertas de estado de funcionamento* disparado contra o acionador VM, os mesmos grupos de ação. Ao contrário dos alertas do Azure tradicionais, não há conceito de um grupo de ação separada para cada regra de alerta de estado de funcionamento. Além disso, apenas os grupos de ação que estão configurados para fornecer e-mail ou notificações por SMS são suportados quando são acionados alertas de estado de funcionamento. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo algumas ou quaisquer dados nos gráficos de desempenho para a minha VM
Se não vir dados de desempenho na tabela de disco ou em alguns dos gráficos de desempenho, em seguida, os contadores de desempenho não podem ser configurados na área de trabalho. Para resolver, execute o seguinte [script do PowerShell](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>O que diferencia Azure Monitor para a funcionalidade de mapa de VMs do mapa de serviço?
O Azure Monitor para a funcionalidade de mapa de VMs é baseado em mapa de serviço, mas tem as seguintes diferenças:

* A vista do mapa pode ser acessada do painel da VM e do Azure Monitor para VMs do Azure Monitor.
* As ligações no mapa estão agora clicáveis e apresentam uma vista dos dados de métrica de ligação no painel lateral para a ligação selecionada.
* Há uma nova API, que é utilizada para criar os mapas para suportar melhor mapas mais complexos.
* VMs monitorizadas agora estão incluídas no nó do grupo de cliente e o gráfico em anel mostra a proporção de máquinas virtuais não monitorizadas do vs monitorizados no grupo.  Também pode ser utilizado para filtrar a lista de máquinas quando o grupo é expandido.
* Monitorizado máquinas de virtuais agora são incluídas em nós de grupo de porta de servidor e o gráfico em anel mostra a proporção de máquinas não monitorizadas do vs monitorizados no grupo.  Também pode ser utilizado para filtrar a lista de máquinas quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o mapa da aplicação do Application insights.
* Os painéis do lado foram atualizados e não tem o conjunto completo de integração que eram suportadas no mapa de serviço - Gestão de atualizações, controlo de alterações, segurança e serviço de atendimento. 
* A opção para escolher os grupos e as máquinas para mapear foi atualizada e agora suporta subscrições, grupos de recursos, os conjuntos de dimensionamento de máquina virtual do Azure e serviços em nuvem.
* Não é possível criar novos grupos de máquina de mapa de serviço no Azure Monitor para a funcionalidade de mapa de VMs.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que motivo é que meu gráficos de desempenho mostra linhas pontilhadas?
Isto pode ocorrer por diversos motivos.  Em casos onde há uma lacuna na recolha de dados Vamos descrever as linhas como pontilhada.  Se modificar a frequência de amostragem de dados para os contadores de desempenho ativado (a predefinição é recolher dados de 60 em 60 segundos), pode ver linhas pontilhadas no gráfico, se optar por um intervalo de tempo de estreito para o gráfico e a frequência de amostragem é inferior a o tamanho de registo utilizado no gráfico (por exemplo, a frequência de amostragem é de 10 minutos e cada bucket no gráfico é de 5 minutos).  Escolher um intervalo de tempo maior para ver deve fazer com que as linhas do gráfico para aparecem como linhas sólidas em vez de pontos neste caso.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>São os grupos suportados com o Azure Monitor para VMs?
Sim, depois de instalar o agente de dependência informações que recolhemos as VMs para apresentar grupos com base na subscrição, grupo de recursos, máquina virtual conjuntos de dimensionamento e serviços cloud.  Se tem estado a utilizar o mapa de serviço e criar grupos de máquinas, estas serão apresentadas também.  Grupos de computadores também serão apresentada no filtro de grupos, se tiver criado para a área de trabalho que está a visualizar. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como posso ver os detalhes para o que está orientando o é o percentil 95 gráficos de linhas no agregado desempenho?
Por predefinição, a lista é ordenada para mostrar a as VMs que tenham o valor mais alto para o percentil 95 para a métrica selecionada, exceto para o gráfico de memória disponível, que mostra as máquinas com o menor valor de percentil de 5.  Ao clicar no gráfico abrirá o **lista de N principais** vista com a métrica apropriada selecionada.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como a funcionalidade de mapa com IPs duplicado em diferentes vnets e sub-redes?
Se está duplicando intervalos de IP com VMs ou máquina virtual do Azure com conjuntos de dimensionamento em vnets e sub-redes, pode causar Azure Monitor para o mapa de VMs apresentar informações incorretas. Este é um problema conhecido e estamos a analisar opções para melhorar esta experiência.

## <a name="does-map-feature-support-ipv6"></a>É mapeado suporte a recursos IPv6?
Funcionalidade do mapa atualmente suporta apenas IPv4 e estamos a analisar o suporte para IPv6. Também suportamos IPv4 encapsulado dentro de IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Quando carrego um mapa de um grupo de recursos ou outro grupo grandes mapa é difícil ver
Embora tenhamos feito melhorias ao mapa para lidar com configurações de grandes e complexas, percebemos que um mapa pode ter muitos de nós, ligações e a funcionar como um cluster de nó.  Estamos empenhados em continuar melhorar o suporte para aumentar a escalabilidade.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia desempenho ter um aspeto diferente no gráfico de rede na página de descrição geral de VM do Azure?

A página de descrição geral para uma VM do Azure apresenta gráficos com base em medição do anfitrião da atividade na VM do convidado.  Para o gráfico de rede na descrição de geral de VM do Azure, mostra apenas o tráfego de rede que será cobrado.  Isso não inclui o tráfego de inter-vnet.  Os dados e gráficos mostrados para o Azure Monitor para VMs é baseado nos dados da VM do convidado e a rede de gráficos apresenta todo o tráfego de TCP/IP que é a entrada e saída para essa VM, incluindo inter-vnet.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como o tempo de resposta é medido para dados armazenados no VMConnection e apresentado no painel de ligação e pastas de trabalho?

Tempo de resposta é uma aproximação. Uma vez que não podemos instrumentar o código do aplicativo, que não realmente sabemos quando começa um pedido e ao receber a resposta. Em vez disso, vamos observar a ser enviados numa conexão de dados e, em seguida, dados que regressam nessa ligação. Nosso agente controla estes envia e recebe e tenta pareá-las: recebe uma seqüência de envia, seguido de uma seqüência de é interpretado como um par de solicitação/resposta. O período de tempo entre estas operações é o tempo de resposta. Ele inclui a latência de rede e o tempo de processamento do servidor.

Essa aproximação funciona bem para protocolos que são de solicitação/resposta com base em: uma única solicitação sai na ligação e uma única resposta chega. Esse é o caso para HTTP (S) (sem o pipelining), mas não satisfeita para outros protocolos.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>São as suas limitações se estou no plano de preços gratuito do Log Analytics?
Se tiver configurado o Azure Monitor com uma área de trabalho do Log Analytics a utilizar o *gratuito* preços escalão, o Azure Monitor para a funcionalidade de mapa de VMs só irá suportar cinco máquinas ligadas ligado à área de trabalho. Se tiver cinco VMs ligadas a uma área de trabalho gratuita, desligar uma das VMs e, em seguida, ligar mais tarde uma nova VM, a nova VM não é monitorizada e refletida na página de mapa.  

Sob essa condição, serão solicitadas com o **experimentar agora** opção quando abrir a VM e selecione **Insights (pré-visualização)** no painel esquerdo, até mesmo depois que tiver sido já instalado na VM.  No entanto, não obterá opções tal como faria normalmente se esta VM não foram carregadas para o Azure Monitor para as VMs. 

## <a name="next-steps"></a>Passos Seguintes
Revisão [carregar Monitor do Azure para VMs](vminsights-onboard.md) para compreender os requisitos e métodos para ativar a monitorização das suas máquinas virtuais.
