---
title: Recurso de Monitor de desempenho na solução de Monitor de desempenho de rede no Azure Log Analytics | Documentos da Microsoft
description: A capacidade de Monitor de desempenho no Monitor de desempenho de rede ajuda-o a monitorizar a conectividade de rede em vários pontos na sua rede. Pode monitorizar implementações de nuvem e localizações no local, vários datacenters e filiais e aplicativos de várias camados de missão crítica ou microsserviços.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: de9e6d5c8c6b4dc9652ae64c9a4fd0e38d7a7b87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109543"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Solução de Monitor de desempenho de rede: Monitorização de desempenho

A capacidade de Monitor de desempenho no [Monitor de desempenho de rede](network-performance-monitor.md) ajuda a monitorizar a conectividade de rede em vários pontos na sua rede. Pode monitorizar implementações de nuvem e localizações no local, vários datacenters e filiais e aplicativos de várias camados de missão crítica ou microsserviços. Com o Monitor de desempenho, pode detectar problemas de rede antes dos usuários se queixam. As principais vantagens são que o utilizador pode: 

- Monitorize perdas e latência em várias sub-redes e definir alertas.
- Monitorize todos os caminhos (incluindo caminhos redundantes) na rede.
- Resolva problemas de rede transitórios e de ponto no tempo, o que são difíceis de replicar.
- Determine o segmento específico na rede, o que é responsável pela diminuição do desempenho.
- Monitorize o estado de funcionamento da rede, sem a necessidade de SNMP.


![Monitor de Desempenho da Rede](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração de Monitor de desempenho de rede, abra a [solução Monitor de desempenho de rede](network-performance-monitor.md)e selecione **configurar**.

![Configurar o Monitor de desempenho de rede](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Monitor de desempenho de rede é um contentor lógico do sub-redes. Ele ajuda a organizar a monitorização da infraestrutura da rede, de acordo com suas necessidades. Pode criar uma rede com um nome amigável e adicionar sub-redes a ela, de acordo com a lógica de negócio. Por exemplo, pode criar uma rede com o nome Londres e adicionar todas as sub-redes no seu Datacenter de Londres. Ou pode criar uma rede com o nome *ContosoFrontEnd* e adicionar a esta rede todas as sub-redes com o nome Contoso que servem o front-end da sua aplicação. A solução cria automaticamente uma rede predefinida, que contém todas as sub-redes detetadas no seu ambiente. 

Sempre que cria uma rede, adicione uma sub-rede para o mesmo. Em seguida, que a sub-rede é removida da rede padrão. Se eliminar uma rede, todas as respetivas sub-redes são automaticamente retornadas para a rede predefinida. A rede predefinida atua como um contêiner para todas as sub-redes que não estão contidos em qualquer rede definida pelo utilizador. Não é possível editar ou eliminar a rede predefinida. Permanece sempre no sistema. Pode criar tantas redes personalizadas conforme necessário. Na maioria dos casos, as sub-redes na sua organização são organizadas em mais de uma rede. Crie uma ou mais redes para agrupar as sub-redes para a lógica de negócio.

Para criar uma nova rede:


1. Selecione o **redes** separador.
1. Selecione **adicionar rede**e, em seguida, introduza o nome de rede e descrição. 
2. Selecione uma ou mais sub-redes e, em seguida, selecione **adicionar**. 
3. Selecione **guardar** para guardar a configuração. 


### <a name="create-monitoring-rules"></a>Criar regras de monitorização 

Monitor de desempenho gera eventos de estado de funcionamento quando o limiar de desempenho de conexões de rede entre duas sub-redes ou entre duas redes é quebrado. O sistema pode aprender, automaticamente, esses limites. Também pode fornecer limiares personalizados. O sistema cria automaticamente uma regra predefinida, que gera um evento de estado de funcionamento sempre que a perda ou latência entre qualquer par de rede ou de sub-rede liga violações, o limiar aprendidas de sistema. Este processo ajuda a monitorizar a sua infraestrutura de rede até que ainda não criou quaisquer regras de monitorização explicitamente de solução. Se a regra predefinida estiver ativada, todos os nós enviam transações sintéticas para todos os outros nós que ativado para a monitorização. A regra predefinida é útil com pequenas redes. Um exemplo é um cenário onde tem um pequeno número de servidores que executam um microsserviço e pretender certificar-se de que todos os servidores têm conectividade entre si.

>[!NOTE]
> Recomendamos que desative a regra predefinida e criar regras de monitorização personalizadas, especialmente com grandes redes onde utiliza um grande número de nós de monitorização. Personalizar regras de monitorização pode reduzir o tráfego gerado pela solução e o ajudam a que organizar a monitorização da sua rede.

Crie regras de monitorização, de acordo com a lógica de negócio. Um exemplo é se pretende monitorizar o desempenho a conectividade de rede de dois sites do office para a sede. Todas as sub-redes no office site1 na rede O1 do grupo. Em seguida, agrupe todas as sub-redes no office site2 na rede 2. Por fim, grupo todas as sub-redes na sede na rede H. criar duas regras de monitorização - um entre O1 e H e outro entre 2. e H. 

Para criar regras de monitorização personalizadas:

1. Selecione **Adicionar regra** sobre o **Monitor** separador e introduza o nome da regra e descrição.
2. Selecione o par de ligações de rede ou sub-rede para monitorizar listas. 
3. Selecione a rede que contém a sub-redes que pretender a partir da rede na lista pendente. Em seguida, selecione a sub-redes da lista de lista pendente de sub-rede correspondente. Se pretender monitorizar todas as sub-redes numa ligação de rede, selecione **todas as sub-redes**. Da mesma forma, selecione a outra sub-redes que quiser. Para excluir a monitorização para ligações de subrede específico de seleções que fez, selecione **adicionar exceção**. 
4. Escolha entre o ICMP e TCP em protocolos para executar transações sintéticas. 
5. Se não pretender criar eventos de estado de funcionamento para os itens que selecionou, desmarque **ativar a monitorização de estado de funcionamento nas ligações abrangidas por esta regra**. 
6. Escolha a monitorização de condições. Para definir limiares personalizados para a geração de evento de estado de funcionamento, introduza os valores de limiar. Sempre que o valor da condição exceder o limiar selecionado para a rede selecionada ou par sub-rede, é gerado um evento de estado de funcionamento. 
7. Selecione **guardar** para guardar a configuração. 

Depois de guardar uma regra de monitorização, pode integrar essa regra com a gestão de alertas, selecionando **criar alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa. Outros parâmetros necessários são preenchidos automaticamente. Utilizar uma regra de alerta, pode receber alertas baseadas em email, além dos alertas existentes no Monitor de desempenho de rede. Os alertas também podem acionar ações de correção com runbooks ou podem integrar com soluções de gestão de serviço existente ao utilizar webhooks. Selecione **gerir alerta** para editar as definições de alerta. 

Agora pode criar mais regras de Monitor de desempenho ou mudar para o dashboard da solução para utilizar a capacidade.

### <a name="choose-the-protocol"></a>Selecione o protocolo

Monitor de desempenho de rede utiliza transações sintéticas para calcular as métricas de desempenho de rede, como a latência de perda e a ligação do pacote. Para compreender melhor esse conceito, considere um agente de Monitor de desempenho de rede ligado a uma extremidade de uma ligação de rede. Este agente de Monitor de desempenho de rede envia pacotes de sonda para um segundo agente de Monitor de desempenho de rede ligado a outro final da rede. O agente de segundo responde com pacotes de resposta. Este processo repete-se algumas vezes. Medindo o número de respostas e o tempo decorrido para receber cada resposta, o primeiro agente de Monitor de desempenho de rede avalia a latência da ligação e perda de pacotes. 

O formato, o tamanho e a sequência desses pacotes é determinado pelo protocolo que escolheu ao criar regras de monitorização. Com base no protocolo dos pacotes, os dispositivos de rede intermediários, tais como routers e comutadores, podem processar estes pacotes de forma diferente. Conseqüentemente, a sua escolha de protocolo afeta a precisão dos resultados. Sua preferência de protocolo também determina se tem de efetuar quaisquer passos manuais depois de implementar a solução de Monitor de desempenho de rede. 

Monitor de desempenho de rede oferece a opção entre protocolos ICMP e de TCP para transações sintéticas em execução. Se optar por ICMP quando cria uma regra de transação sintética, os agentes do Monitor de desempenho de rede utilizam mensagens de eco ICMP para calcular a latência de rede e perda de pacotes. ECO de ICMP utiliza a mesma mensagem que é enviada, o utilitário de ping convencional. Quando utilizar TCP como protocolo, agentes do Monitor de desempenho de rede enviam TCP SYN pacotes através da rede. Este passo é seguido por uma conclusão de handshake TCP e a ligação é removida utilizando pacotes de RST. 

Antes de escolher um protocolo, considere as seguintes informações: 

* **Deteção de várias rotas de rede.** TCP é mais precisa quando várias rotas a detetar e tem menos de agentes em cada sub-rede. Por exemplo, um ou dois agentes que utilizam o TCP podem detetar todos os caminhos redundantes entre sub-redes. Terá de vários agentes que usam o ICMP para alcançar resultados semelhantes. Utilizar o ICMP, se tiver um número de rotas entre duas sub-redes, é necessário mais do que 5N agentes na sub-rede de uma origem ou de destino.

* **Precisão dos resultados.** Routers e comutadores tendem a atribuir prioridade mais baixa para os pacotes de eco ICMP em comparação comparados pacotes TCP. Em determinadas situações, quando os dispositivos de rede estão bastante carregados, refletem os dados obtidos pelo TCP mais de perto a perda e latência apresentados pelos aplicativos. Isto ocorre porque a maior parte do tráfego de aplicativo flui através de TCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação comparados o TCP. 

* **Configuração da firewall.** Protocolo TCP exige que os pacotes TCP são enviados para uma porta de destino. A porta predefinida utilizada pelos agentes do Monitor de desempenho de rede é 8084. Pode alterar a porta ao configurar agentes. Certifique-se de que suas firewalls de rede ou regras de grupo (NSG) de segurança de rede (no Azure) permitem tráfego na porta. Terá também de certificar-se de que a firewall local nos computadores onde estão instalados os agentes está configurada para permitir o tráfego nesta porta. Pode utilizar scripts do PowerShell para configurar regras de firewall nos computadores a executar o Windows, mas tem de configurar manualmente a firewall de rede. Por outro lado, o ICMP não funciona, utilizando uma porta. Na maioria dos cenários de empresa, o tráfego ICMP é permitido através de firewalls para permitir-lhe utilizar ferramentas de diagnóstico de rede, como o utilitário de ping. Se pode enviar ping uma máquina de outro, pode utilizar o protocolo ICMP sem ter de configurar manualmente a firewalls.

>[!NOTE] 
> Alguns firewalls poderão bloquear o ICMP, que pode conduzir a retransmissão que resulta num grande número de eventos no seu sistema de gerenciamento de informações e eventos da segurança. Certifique-se de que o protocolo que escolher não está bloqueado por uma firewall de rede ou o NSG. Caso contrário, o Monitor de desempenho de rede não é possível monitorizar o segmento de rede. Recomendamos que utilize o TCP para monitorização. Utilize o ICMP em cenários onde não é possível utilizar o TCP, como quando: 
>
> - Utilizar nós com base no cliente do Windows, porque os sockets TCP raw não são permitidos em clientes do Windows.
> - A firewall de rede ou o NSG bloqueia TCP.
> - Não sabe como mudar o protocolo.

Se optar por utilizar o ICMP durante a implementação, pode mudar para TCP em qualquer altura ao editar a regra de monitorização de predefinição.

1. Aceda a **desempenho de rede** > **Monitor** > **configurar**   >  **Monitor**. Em seguida, selecione **regra predefinida**. 
2. Desloque-se para o **protocolo** secção e selecione o protocolo que pretende utilizar. 
3. Selecione **guardar** para aplicar a definição. 

Mesmo que a regra predefinida utiliza um protocolo específico, pode criar novas regras com um protocolo diferente. Pode até mesmo criar uma mistura de regras em que algumas regras usam o ICMP e outras usam TCP. 

## <a name="walkthrough"></a>Instruções 

Ver agora uma simple investigação sobre a causa de raiz para um evento de estado de funcionamento.

No dashboard de solução, um evento de estado de funcionamento mostra que uma ligação de rede está danificada. Para investigar o problema, selecione o **rede liga a ser monitorizado** mosaico.

A página de desagregação mostra que o **DMZ2 DMZ1** ligação de rede está em mau estado de funcionamento. Selecione **ver ligações de sub-rede** para esta ligação de rede. 


A página de desagregação mostra todas as ligações de sub-rede no **DMZ2 DMZ1** ligação de rede. Para ambas as ligações de sub-rede, a latência ultrapassou o limiar, o que faz a ligação de rede em mau estado de funcionamento. Também pode ver as tendências de latência de ambas as ligações de sub-rede. Utilize a seleção de hora a controlar o gráfico para concentrar-se no período de tempo necessário. Pode ver a hora do dia quando a latência atingido seu pico. Procure os registos mais tarde durante este período de tempo investigar o problema. Selecione **ver ligações de nó** para desagregar ainda mais. 
 
 ![Página de ligações de sub-rede](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Semelhante à página anterior, as ligações de nó constituinte da lista a página de desagregação para a ligação de sub-rede específica. Pode efetuar ações semelhantes aqui, tal como fez no passo anterior. Selecione **ver a topologia** para ver a topologia entre os dois nós. 
 
 ![Página de ligações de nó](media/network-performance-monitor-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados são desenhados no mapa de topologia. É possível visualizar a topologia salto por salto de rotas entre dois nós no mapa de topologia. Dá-lhe uma visão clara dos quantas rotas existem entre os dois nós e quais caminhos de levar os pacotes de dados. Os afunilamentos de desempenho de rede são exibidos em vermelho. Para localizar uma ligação de rede com falhas ou um dispositivo de rede com falhas, observe os elementos vermelhos no mapa de topologia. 

 ![Dashboard de topologia com o mapa de topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Pode rever a perda e latência e o número de saltos em cada caminho no **ação** painel. Utilize a barra de deslocamento para ver os detalhes dos caminhos de mau estado de funcionamento. Utilize os filtros para selecionar os caminhos com o salto mau estado de funcionamento para que a topologia para apenas os caminhos selecionados é desenhada. Para aplicar zoom dentro ou fora do mapa de topologia, utilize a roda do mouse. 

Na imagem seguinte, a causa de raiz das áreas problemáticas para a secção específica da rede aparecer na caminhos vermelhos e saltos. Selecione um nó no mapa de topologia para revelar as propriedades do nó, que inclui o FQDN e o endereço IP. Selecionar um salto mostra o endereço IP do salto. 
 
![Mapa de topologia com propriedades de nó selecionado](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passos Seguintes
[Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para ver os registos de dados de desempenho de rede detalhada.
