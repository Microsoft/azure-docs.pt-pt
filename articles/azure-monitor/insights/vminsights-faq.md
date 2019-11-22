---
title: Perguntas frequentes sobre Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Respostas a perguntas comuns para Azure Monitor para VMs que monitora a integridade e o desempenho das VMs do Azure, além de descobrir e mapear automaticamente os componentes do aplicativo e suas dependências.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 579538996e934c7068c397a284d819f5ddb92f08
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305462"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Perguntas frequentes sobre Azure Monitor para VMs (versão prévia)
Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Azure Monitor para VMs. Se você tiver outras dúvidas sobre a solução, vá para o [Fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Posso carregar um espaço de trabalho existente?
Se suas máquinas virtuais já estiverem conectadas a um espaço de trabalho Log Analytics, você poderá continuar a usar esse espaço de trabalho ao realizar a integração com o Azure Monitor para VMs, desde que ele esteja em uma das regiões com suporte listadas [aqui](vminsights-enable-overview.md#prerequisites).

Ao realizar a integração, configuramos contadores de desempenho para o espaço de trabalho que fará com que todas as VMs que relatam dados para o espaço de trabalho comecem a coletar essas informações para exibição e análise no Azure Monitor para VMs.  Como resultado, você verá dados de desempenho de todas as VMs conectadas ao espaço de trabalho selecionado.  Os recursos de integridade e de mapa são habilitados somente para as VMs que você especificou para carregar.

Para obter mais informações sobre quais contadores de desempenho estão habilitados, consulte nosso artigo [habilitar visão geral](vminsights-enable-overview.md#performance-counters-enabled) .

## <a name="can-i-onboard-to-a-new-workspace"></a>Posso carregar um novo espaço de trabalho? 
Se suas VMs não estiverem atualmente conectadas a um espaço de trabalho Log Analytics existente, você precisará criar um novo espaço de trabalho para armazenar seus dados. A criação de um novo espaço de trabalho padrão será feita automaticamente se você configurar uma única VM do Azure para Azure Monitor para VMs por meio do portal do Azure.

Se você optar por usar o método baseado em script, essas etapas serão abordadas no artigo [habilitar Azure monitor para VMs (visualização) usando o Azure PowerShell ou o modelo do Resource Manager](vminsights-enable-at-scale-powershell.md) . 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que devo fazer se minha VM já estiver se comunicando a um espaço de trabalho existente?
Se você já estiver coletando dados de suas máquinas virtuais, talvez já o tenha configurado para relatar dados a um espaço de trabalho Log Analytics existente.  Contanto que esse espaço de trabalho esteja em uma de nossas regiões com suporte, você pode habilitar Azure Monitor para VMs para esse espaço de trabalho já existente.  Se o espaço de trabalho que você já está usando não estiver em uma de nossas regiões com suporte, você não poderá integrar ao Azure Monitor para VMs no momento.  Estamos a trabalhar ativamente para oferecer suporte a mais regiões.

>[!NOTE]
>Configuramos contadores de desempenho para o espaço de trabalho que afeta todas as VMs que se reportam ao espaço de trabalho, independentemente de você ter optado por integrá-las ao Azure Monitor para VMs. Para obter mais detalhes sobre como os contadores de desempenho são configurados para o espaço de trabalho, consulte nossa [documentação](../../azure-monitor/platform/data-sources-performance-counters.md). Para obter informações sobre os contadores configurados para Azure Monitor para VMs, consulte nosso artigo [habilitar Azure monitor para VMs](vminsights-enable-overview.md#performance-counters-enabled) .  

## <a name="why-did-my-vm-fail-to-onboard"></a>Por que minha VM falhou ao carregar?
Ao integrar uma VM do Azure da portal do Azure, ocorrem as seguintes etapas:

* Uma área de trabalho do Log Analytics do padrão é criada, se que a opção tiver sido selecionada.
* Os contadores de desempenho são configurados para o espaço de trabalho selecionado. Se essa etapa falhar, você observará que alguns dos gráficos de desempenho e tabelas não estão mostrando dados para a VM que você integrou. Você pode corrigir isso executando o script do PowerShell documentado [aqui](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* O agente de Log Analytics é instalado em VMs do Azure usando uma extensão de VM, se determinado for necessário.  
* O agente de dependência do mapa de Azure Monitor para VMs é instalado em VMs do Azure usando uma extensão, se determinado for necessário.  
* Azure Monitor componentes que dão suporte ao recurso de integridade são configurados, se necessário, e a VM é configurada para relatar dados de integridade.

Durante o processo integrado, verificamos o status em cada um dos itens acima para retornar um status de notificação para você no Portal. Configuração de área de trabalho e a instalação do agente normalmente demora 5 a 10 minutos. A exibição de dados de monitoramento e integridade no portal leva um adicional de 5 a 10 minutos.  

Se você iniciou a integração e confira mensagens indicando que a VM precisa ser integrada, aguarde até 30 minutos para que a VM conclua o processo. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Habilitei apenas Azure Monitor para VMs, por que vejo todas as minhas VMs monitoradas pelo recurso de integridade?
O recurso de integridade é habilitado para todas as VMs que estão conectadas ao espaço de trabalho Log Analytics, mesmo quando a ação é iniciada para uma única VM.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Posso modificar o agendamento de quando os critérios de integridade avaliam uma condição?
Não, o período de tempo e a frequência dos critérios de integridade não podem ser modificados com esta versão. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Posso desabilitar critérios de integridade para uma condição que não preciso monitorar?
Os critérios de integridade não podem ser desabilitados nesta versão.

## <a name="are-the-health-alert-severities-configurable"></a>As severidades de alerta de integridade são configuráveis?  
A severidade do alerta de integridade não pode ser modificada, só pode ser habilitada ou desabilitada. Além disso, algumas severidades de alerta são atualizadas com base no estado dos critérios de integridade. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Se eu reconfigurar as configurações de um determinado critério de integridade, ele poderá ser definido como escopo para uma instância específica?  
Se você modificar qualquer configuração de uma instância de critério de integridade, todas as instâncias de critérios de integridade do mesmo tipo na VM do Azure serão modificadas. Por exemplo, se o limiar da instância de critério de estado de funcionamento do espaço livre de disco que corresponde ao disco lógico c: for modificado, este limite aplica-se a todos os outros discos lógicos que são detetados e monitorizados para a mesma VM.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>O recurso de integridade monitora processadores e núcleos lógicos?
Não, os critérios de integridade de processador individual e de nível de processador lógico não estão incluídos para um Windows, apenas a utilização total da CPU é monitorada por padrão para avaliar efetivamente a pressão da CPU com base no número total de CPUs lógicas disponíveis para a VM do Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Todos os limites de critérios de integridade são configuráveis?  
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

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Como fazer modificar alertas incluídos com o recurso de integridade?
Regras de alerta que estão definidas para cada critério de estado de funcionamento não são apresentadas no portal do Azure. Pode ativar ou desativar um alerta de estado de funcionamento apenas na regra a [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Além disso, você não pode atribuir um [grupo de ação Azure monitor](../../azure-monitor/platform/action-groups.md) para alertas de integridade no portal do Azure. Você só pode usar a API de configuração de notificação para configurar um grupo de ações a ser disparado sempre que um alerta de integridade for acionado. Atualmente, pode atribuir grupos de ação em relação a uma VM para que todos os *alertas de estado de funcionamento* disparado contra o acionador VM, os mesmos grupos de ação. Ao contrário dos alertas do Azure tradicionais, não há conceito de um grupo de ação separada para cada regra de alerta de estado de funcionamento. Além disso, apenas os grupos de ação que estão configurados para fornecer e-mail ou notificações por SMS são suportados quando são acionados alertas de estado de funcionamento. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo algumas ou quaisquer dados nos gráficos de desempenho para a minha VM
Se você não vir dados de desempenho na tabela de disco ou em alguns dos gráficos de desempenho, os contadores de desempenho não poderão ser configurados no espaço de trabalho. Para resolver, execute o seguinte [script do PowerShell](vminsights-enable-at-scale-powershell.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Como Azure Monitor para VMs recurso de mapa é diferente do Mapa do Serviço?
O recurso de mapa de Azure Monitor para VMs é baseado em Mapa do Serviço, mas tem as seguintes diferenças:

* O modo de exibição de mapa pode ser acessado na folha da VM e de Azure Monitor para VMs em Azure Monitor.
* As conexões no mapa agora são clicáveis e exibem uma exibição dos dados de métrica de conexão no painel lateral da conexão selecionada.
* Há uma nova API que é usada para criar os mapas para dar melhor suporte a mapas mais complexos.
* As VMs monitoradas agora estão incluídas no nó do grupo de clientes e o gráfico de rosca mostra a proporção de máquinas virtuais monitoradas vs não monitoradas no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* As máquinas virtuais monitoradas agora estão incluídas nos nós do grupo de portas do servidor e o gráfico de rosca mostra a proporção de computadores monitorados vs não monitorados no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o mapa de aplicativos do Application insights.
* Os painéis do lado foram atualizados e não têm o conjunto completo de integração que foram compatíveis com Mapa do Serviço Gerenciamento de Atualizações, Controle de Alterações, segurança e central de serviços. 
* A opção de escolher grupos e máquinas para mapear foi atualizada e agora dá suporte a assinaturas, grupos de recursos, conjuntos de dimensionamento de máquinas virtuais do Azure e serviços de nuvem.
* Você não pode criar novos grupos de computadores Mapa do Serviço no recurso de mapa de Azure Monitor para VMs.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que motivo é que meu gráficos de desempenho mostra linhas pontilhadas?
Isso pode ocorrer por alguns motivos.  Em casos em que há uma lacuna na coleta de dados, descrevemos as linhas como pontilhadas.  Se você tiver modificado a frequência de amostragem de dados para os contadores de desempenho habilitados (a configuração padrão é coletar dados a cada 60 segundos), você poderá ver linhas pontilhadas no gráfico se escolher um intervalo de tempo estreito para o gráfico e sua frequência de amostragem for menor que o tamanho do Bucket usado no gráfico (por exemplo, a frequência de amostragem é a cada 10 minutos e cada bucket no gráfico é de 5 minutos).  Escolher um intervalo de tempo maior para exibir deve fazer com que as linhas do gráfico sejam exibidas como linhas sólidas em vez de pontos nesse caso.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>São os grupos suportados com o Azure Monitor para VMs?
Sim, depois de instalar o agente de dependência, coletamos informações das VMs para exibir grupos com base na assinatura, grupo de recursos, conjuntos de dimensionamento de máquinas virtuais e serviços de nuvem.  Se você estiver usando Mapa do Serviço e tiver criado grupos de computadores, eles também serão exibidos.  Os grupos de computadores também serão exibidos no filtro grupos se você os tiver criado para o espaço de trabalho que está sendo exibido. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como fazer Ver os detalhes do que está conduzindo a linha do 95 º percentil nos gráficos de desempenho agregados?
Por padrão, a lista é classificada para mostrar as VMs que têm o valor mais alto para o percentil de 95 º para a métrica selecionada, exceto para o gráfico de memória disponível, que mostra as máquinas com o menor valor do quinto percentil.  Clicar no gráfico abrirá o modo de exibição de **lista N superior** com a métrica apropriada selecionada.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como o recurso de mapa lida com IPs duplicados entre diferentes vnets e sub-redes?
Se você estiver duplicando intervalos de IP com VMs ou conjuntos de dimensionamento de máquinas virtuais do Azure em sub-redes e vnets, isso poderá fazer com que Azure Monitor para VMs mapa exiba informações incorretas. Esse é um problema conhecido e estamos investigando opções para melhorar essa experiência.

## <a name="does-map-feature-support-ipv6"></a>O recurso de mapa dá suporte a IPv6?
Atualmente, o recurso de mapa dá suporte apenas para IPv4 e estamos investigando suporte para IPv6. Também há suporte para IPv4 que é túnel dentro do IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Ao carregar um mapa para um grupo de recursos ou outro grupo grande, é difícil exibir o mapa
Embora tenhamos feito melhorias para mapear para lidar com configurações grandes e complexas, percebemos que um mapa pode ter muitos nós, conexões e nó funcionando como um cluster.  Estamos empenhados em continuar melhorar o suporte para aumentar a escalabilidade.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia desempenho é diferente do gráfico de rede na página Visão geral da VM do Azure?

A página de visão geral de uma VM do Azure exibe gráficos com base na medição de atividade do host na VM convidada.  Para o gráfico de rede na visão geral da VM do Azure, ele exibe apenas o tráfego de rede que será cobrado.  Isso não inclui tráfego entre redes virtuais.  Os dados e gráficos mostrados para Azure Monitor para VMs baseiam-se nos dados da VM convidada e o gráfico de rede exibe todo o tráfego TCP/IP que é de entrada e saída para essa VM, incluindo inter-vnet.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como o tempo de resposta é medido para os dados armazenados em VMConnection e exibidos no painel de conexão e nas pastas de trabalho?

O tempo de resposta é uma aproximação. Como não instrumento o código do aplicativo, não sabemos realmente quando uma solicitação começa e quando chega a resposta. Em vez disso, observamos os dados que estão sendo enviados em uma conexão e, em seguida, os dados voltam para essa conexão. Nosso agente controla esses envios e recebe e tenta emparelhar: uma sequência de envios, seguida por uma sequência de receives, é interpretada como um par de solicitação/resposta. O intervalo entre essas operações é o tempo de resposta. Incluirá a latência de rede e o tempo de processamento do servidor.

Essa aproximação funciona bem para protocolos que são baseados em solicitação/resposta: uma única solicitação sai da conexão e uma única resposta chega. Esse é o caso para HTTP (S) (sem Pipeline), mas não é atendido para outros protocolos.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Suas limitações se eu estiver no plano de preços de Log Analytics gratuito?
Se você tiver configurado Azure Monitor com um espaço de trabalho Log Analytics usando o tipo de preço *gratuito* , Azure monitor para VMs recurso de mapa dará suporte apenas a cinco computadores conectados conectados ao espaço de trabalho. Se você tiver cinco VMs conectadas a um espaço de trabalho gratuito, desconecte uma das VMs e, posteriormente, conecte uma nova VM, a nova VM não será monitorada e refletida na página do mapa.  

Sob essa condição, você será avisado com a opção **Experimente agora** quando abrir a VM e selecionar **insights (versão prévia)** no painel esquerdo, mesmo depois que ela já tiver sido instalada na VM.  No entanto, as opções não são solicitadas como normalmente ocorrerão se essa VM não estivesse integrada a Azure Monitor para VMs. 

## <a name="next-steps"></a>Passos seguintes
Examine [habilitar Azure monitor para VMs](vminsights-enable-overview.md) para entender os requisitos e os métodos para habilitar o monitoramento de suas máquinas virtuais.
