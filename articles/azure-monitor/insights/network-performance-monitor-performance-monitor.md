---
title: Monitor de Desempenho no Monitor de Desempenho da Rede
description: A capacidade do Monitor de Desempenho no Monitor de Desempenho da Rede ajuda-o a monitorizar a conectividade da rede em vários pontos da sua rede. Você pode monitorizar implementações em nuvem e locais no local, vários centros de dados e filiais, e aplicações ou microserviços multimais críticos da missão.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80055694"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Solução de Monitor de Desempenho da Rede: Monitorização do desempenho

A capacidade do Monitor de Desempenho no Monitor de Desempenho da [Rede](network-performance-monitor.md) ajuda-o a monitorizar a conectividade da rede em vários pontos da sua rede. Você pode monitorizar implementações em nuvem e locais no local, vários centros de dados e filiais, e aplicações ou microserviços multimais críticos da missão. Com o Monitor de Desempenho, pode detetar problemas de rede antes de os seus utilizadores se queixarem. As principais vantagens são que pode: 

- Monitorize a perda e a latência em várias subredes e detetete alertas.
- Monitorize todos os caminhos (incluindo caminhos redundantes) na rede.
- Problemas transitórios e problemas de rede pontual, que são difíceis de replicar.
- Determine o segmento específico da rede, que é responsável pelo desempenho degradado.
- Monitorize a saúde da rede, sem necessidade de SNMP.


![Monitor de Desempenho da Rede](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração do Monitor de Desempenho da Rede, abra a [solução](network-performance-monitor.md)do Monitor de Desempenho da Rede, e selecione **Configurar**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Monitor de Desempenho da Rede é um recipiente lógico para subredes. Ajuda-o a organizar a monitorização da infraestrutura de rede de acordo com as suas necessidades. Pode criar uma rede com um nome amigável e adicionar-lhe subnets de acordo com a sua lógica de negócio. Por exemplo, pode criar uma rede chamada Londres e adicionar todas as subredes no seu centro de dados de Londres. Ou pode criar uma rede chamada *ContosoFrontEnd* e adicionar a esta rede todas as subredes chamadas Contoso que servem a parte frontal da sua app. A solução cria automaticamente uma rede predefinida, que contém todas as subredes descobertas no seu ambiente. 

Sempre que cria uma rede, adiciona-lhe uma sub-rede. Em seguida, a sub-rede é removida da rede predefinida. Se eliminar uma rede, todas as suas subredes são automaticamente devolvidas à rede predefinida. A rede predefinida funciona como um recipiente para todas as subredes que não estão contidas em nenhuma rede definida pelo utilizador. Não é possível editar ou eliminar a rede predefinida. Permanece sempre no sistema. Pode criar as redes personalizadas que precisar. Na maioria dos casos, as subredes da sua organização estão dispostas em mais de uma rede. Crie uma ou mais redes para agrupar as suas subredes para a sua lógica de negócio.

Para criar uma nova rede:


1. Selecione o separador **Redes.**
1. Selecione **Adicionar rede**e, em seguida, introduzir o nome e descrição da rede. 
2. Selecione uma ou mais subredes e, em seguida, selecione **Adicionar**. 
3. Selecione **Guardar** para salvar a configuração. 


### <a name="create-monitoring-rules"></a>Criar regras de monitorização 

O Performance Monitor gera eventos de saúde quando o limiar do desempenho das ligações de rede entre duas subredes ou entre duas redes é violado. O sistema pode aprender estes limiares automaticamente. Também pode fornecer limiares personalizados. O sistema cria automaticamente uma regra predefinida, que gera um evento de saúde sempre que a perda ou a latência entre qualquer par de ligações de rede ou subrede infringe o limiar aprendido pelo sistema. Este processo ajuda a solução a monitorizar a sua infraestrutura de rede até que não tenha criado explicitamente quaisquer regras de monitorização. Se a regra de predefinição estiver ativada, todos os nós enviam transações sintéticas para todos os outros nós que habilitaste para monitorização. A regra padrão é útil com redes pequenas. Um exemplo é um cenário em que você tem um pequeno número de servidores executando um microserviço e você quer ter certeza de que todos os servidores têm conectividade uns com os outros.

>[!NOTE]
> Recomendamos que desative a regra predefinida e crie regras de monitorização personalizadas, especialmente com grandes redes onde utiliza um grande número de nós para monitorização. As regras de monitorização personalizadas podem reduzir o tráfego gerado pela solução e ajudá-lo a organizar a monitorização da sua rede.

Crie regras de monitorização de acordo com a sua lógica de negócio. Um exemplo é se quiser monitorizar o desempenho da conectividade da rede de dois sites de escritórios para sede. Agrupar todas as subredes no site do escritório1 na rede O1. Em seguida, agrupar todas as subredes no site do escritório2 na rede O2. Finalmente, agrupar todas as subredes na sede da rede H. Crie duas regras de monitorização- uma entre O1 e H e a outra entre O2 e H. 

Para criar regras de monitorização personalizadas:

1. **Selecione Adicionar Regra** no separador **Monitor** e introduza o nome e a descrição da regra.
2. Selecione o par de ligações de rede ou subrede para monitorizar a partir das listas. 
3. Selecione a rede que contém as subredes que deseja da lista de desaposição da rede. Em seguida, selecione as subredes da lista de entrega de sub-rede correspondente. Se pretender monitorizar todas as subredes numa ligação de rede, selecione **Todas as subredes**. Da mesma forma, selecione as outras subredes que deseja. Para excluir a monitorização de ligações de subrede específicas das seleções que fez, **selecione Adicionar Exceção**. 
4. Escolha entre protocolos ICMP e TCP para executar transações sintéticas. 
5. Se não quiser criar eventos de saúde para os itens selecionados, limpe **a Monitorização da Saúde nas ligações abrangidas por esta regra**. 
6. Escolha as condições de monitorização. Para definir limiares personalizados para a geração de eventos de saúde, insira os valores-limiar. Sempre que o valor da circunstância exceda o seu limiar selecionado para o par de rede ou sub-rede selecionado, gera-se um evento de saúde. 
7. Selecione **Guardar** para salvar a configuração. 

Depois de guardar uma regra de monitorização, pode integrar essa regra com a Gestão de Alertas selecionando Criar **Alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa. Outros parâmetros necessários são automaticamente preenchidos. Utilizando uma regra de alerta, pode receber alertas por e-mail, além dos alertas existentes dentro do Monitor de Desempenho da Rede. Os alertas também podem desencadear ações corretivas com livros de execução, ou podem integrar-se com as soluções de gestão de serviços existentes utilizando webhooks. Selecione **'Gerir's Alert** para editar as definições de alerta. 

Agora pode criar mais regras do Monitor de Desempenho ou passar para o dashboard de solução para utilizar a capacidade.

### <a name="choose-the-protocol"></a>Escolha o protocolo

O Network Performance Monitor utiliza transações sintéticas para calcular métricas de desempenho da rede, como perda de pacotes e latência de ligação. Para compreender melhor este conceito, considere um agente do Network Performance Monitor ligado a uma extremidade de uma ligação de rede. Este agente do Monitor de Desempenho da Rede envia pacotes de sonda para um segundo agente do Monitor de Desempenho da Rede ligado a outra extremidade da rede. O segundo agente responde com pacotes de resposta. Este processo repete-se algumas vezes. Ao medir o número de respostas e o tempo de receção de cada resposta, o primeiro agente do Network Performance Monitor avalia a latência de ligação e as quedas de pacotes. 

O formato, tamanho e sequência destes pacotes é determinado pelo protocolo que escolhe quando cria regras de monitorização. Com base no protocolo dos pacotes, os dispositivos de rede intermédios, como routers e interruptores, podem processar estes pacotes de forma diferente. Consequentemente, a escolha do seu protocolo afeta a precisão dos resultados. A escolha do protocolo também determina se deve tomar quaisquer passos manuais depois de implementar a solução de Monitor de Desempenho da Rede. 

O Network Performance Monitor oferece-lhe a escolha entre protocolos ICMP e TCP para a execução de transações sintéticas. Se escolher o ICMP quando cria uma regra de transação sintética, os agentes do Network Performance Monitor utilizam mensagens Echo do ICMP para calcular a latência da rede e a perda de pacotes. O ICMP ECHO usa a mesma mensagem que é enviada pela utilidade convencional de ping. Quando utiliza o TCP como protocolo, os agentes do Network Performance Monitor enviam pacotes SYN TCP pela rede. Este passo é seguido por uma conclusão do aperto de mão da TCP, e a ligação é removida utilizando pacotes RST. 

Considere as seguintes informações antes de escolher um protocolo: 

* **Descoberta de várias rotas de rede.** O TCP é mais preciso na descoberta de várias rotas, e precisa de menos agentes em cada subnet. Por exemplo, um ou dois agentes que usam TCP podem descobrir todos os caminhos redundantes entre subredes. Precisa de vários agentes que usam o ICMP para obter resultados semelhantes. Utilizando o ICMP, se tiver uma série de rotas entre duas subredes, precisa de mais de 5N agentes numa sub-rede de origem ou de destino.

* **Precisão dos resultados.** Os routers e os interruptores tendem a atribuir menor prioridade aos pacotes ICMP ECHO em comparação com os pacotes TCP. Em determinadas situações, quando os dispositivos de rede são fortemente carregados, os dados obtidos pela TCP refletem mais de perto a perda e a latência sentida pelas aplicações. Isto ocorre porque a maioria dos fluxos de tráfego de aplicações sobre tCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação com o TCP. 

* **Configuração da firewall.** O protocolo TCP exige que os pacotes de TCP sejam enviados para uma porta de destino. A porta predefinida utilizada pelos agentes do Monitor de Desempenho da Rede é 8084. Pode alterar a porta quando configurar agentes. Certifique-se de que as suas firewalls de rede ou as regras do grupo de segurança de rede (NSG) (em Azure) permitem o tráfego na porta. Também precisa de se certificar de que a firewall local nos computadores onde os agentes estão instalados está configurada para permitir o tráfego nesta porta. Pode utilizar scripts PowerShell para configurar regras de firewall nos seus computadores que executam o Windows, mas tem de configurar manualmente a firewall da sua rede. Em contraste, o ICMP não funciona utilizando uma porta. Na maioria dos cenários empresariais, o tráfego do ICMP é permitido através das firewalls para permitir que você use ferramentas de diagnóstico de rede como o utilitário de ping. Se conseguir introduzir uma máquina de outra, pode utilizar o protocolo ICMP sem ter de configurar as firewalls manualmente.

>[!NOTE] 
> Algumas firewalls podem bloquear o ICMP, o que pode levar à retransmissão que resulta num grande número de eventos no seu sistema de informação de segurança e gestão de eventos. Certifique-se de que o protocolo que escolhe não está bloqueado por uma firewall de rede ou NSG. Caso contrário, o Monitor de Desempenho da Rede não pode monitorizar o segmento de rede. Recomendamos que utilize TCP para monitorização. Utilize o ICMP em cenários em que não pode utilizar TCP, como quando: 
>
> - Utiliza nós baseados no cliente do Windows, porque as tomadas cruas da TCP não são permitidas nos clientes do Windows.
> - A sua firewall de rede ou nsg bloqueia o TCP.
> - Não sabe como mudar o protocolo.

Se optar por utilizar o ICMP durante a implementação, pode mudar para TCP a qualquer momento, editando a regra de monitorização predefinida.

1. Vá ao > **Monitor** >de **Monitor** **Configuração** >de Desempenho da **Rede**. Em seguida, selecione a **regra padrão**. 
2. Percorra a secção **Protocol** e selecione o protocolo que pretende utilizar. 
3. Selecione **Guardar** para aplicar a definição. 

Mesmo que a regra padrão utilize um protocolo específico, pode criar novas regras com um protocolo diferente. Pode até criar uma mistura de regras onde algumas regras usam o ICMP e outras usam TCP. 

## <a name="walkthrough"></a>Instruções 

Agora olhe para uma simples investigação sobre a causa principal para um evento de saúde.

No painel de solução, um evento de saúde mostra que uma ligação de rede não é saudável. Para investigar o problema, selecione as ligações da Rede que **estão a ser monitorizadas.**

A página de perfuração mostra que a ligação de rede **DMZ2-DMZ1** não é saudável. Selecione ver links de **sub-rede** para esta ligação de rede. 


A página de perfuração mostra todas as ligações de subrede na ligação de rede **DMZ2-DMZ1.** Para ambas as ligações de subrede, a latência ultrapassou o limiar, o que torna a ligação da rede pouco saudável. Também pode ver as tendências de latência de ambas as ligações de subrede. Utilize o controlo de seleção de tempo no gráfico para se concentrar na faixa de tempo necessária. Dá para ver a hora do dia em que a latência atingiu o seu auge. Procure nos registos mais tarde para este período de tempo para investigar o problema. Selecione **Ver ligações** do nó para perfurar mais. 
 
 ![Página de Links de Subrede](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Semelhante à página anterior, a página de perfuração para a ligação de subrede específica lista as suas ligações de nó constituinte. Pode realizar ações semelhantes aqui, como fez no passo anterior. Selecione **Ver topologia** para ver a topologia entre os dois nós. 
 
 ![Página de Links do Nó](media/network-performance-monitor-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados estão traçados no mapa de topologia. Você pode visualizar a topologia hop-by-hop de rotas entre dois nós no mapa de topologia. Dá-lhe uma imagem clara de quantas rotas existem entre os dois nós e que caminhos os pacotes de dados tomam. Os estrangulamentos de desempenho da rede são mostrados a vermelho. Para localizar uma ligação de rede defeituosa ou um dispositivo de rede defeituoso, veja os elementos vermelhos no mapa de topologia. 

 ![Painel de Topologia com mapa de topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Você pode rever a perda, latência, e o número de lúpulos em cada caminho no painel **de ação.** Utilize a barra de deslocação para ver os detalhes dos caminhos pouco saudáveis. Utilize os filtros para selecionar os caminhos com o lúpulo pouco saudável para que a topologia apenas para os caminhos selecionados seja traçada. Para ampliar dentro ou fora do mapa de topologia, use a roda do rato. 

Na imagem seguinte, a causa principal das áreas problemáticas para a secção específica da rede aparece nos caminhos vermelhos e no lúpulo. Selecione um nó no mapa de topologia para revelar as propriedades do nó, que inclui o endereço FQDN e IP. A seleção de um lúpulo mostra o endereço IP do lúpulo. 
 
![Mapa de topologia com propriedades do nó selecionadas](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar registos de desempenho de rede detalhados.
