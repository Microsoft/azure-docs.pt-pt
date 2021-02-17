---
title: Monitor de desempenho no Monitor de Desempenho da Rede
description: A capacidade do Monitor de Desempenho no Monitor de Desempenho da Rede ajuda-o a monitorizar a conectividade da rede em vários pontos da sua rede. Você pode monitorizar implementações em nuvem e locais no local, vários centros de dados e escritórios de sucursais, e aplicações multi-tiers ou microserviços críticos da missão.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 5335882c11ee6a1a7352ef3dc9822cfa88eda196
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573513"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Solução monitor de desempenho da rede: Monitorização de desempenho

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Pode continuar a utilizar os testes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede para o novo Monitor de Ligação](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) no Azure Network Watcher antes de 29 de fevereiro de 2024.

A capacidade do Monitor de Desempenho no [Monitor de Desempenho da Rede](network-performance-monitor.md) ajuda-o a monitorizar a conectividade da rede em vários pontos da sua rede. Você pode monitorizar implementações em nuvem e locais no local, vários centros de dados e escritórios de sucursais, e aplicações multi-tiers ou microserviços críticos da missão. Com o Monitor de Desempenho, pode detetar problemas de rede antes que os seus utilizadores se queixem. As principais vantagens são que pode: 

- Monitorize a perda e a latência em vários sub-redes e desconte alertas.
- Monitorize todos os caminhos (incluindo caminhos redundantes) na rede.
- Problemas de resolução de problemas transitórios e problemas pontuais de rede, que são difíceis de replicar.
- Determine o segmento específico da rede, que é responsável pelo desempenho degradado.
- Monitorize a saúde da rede, sem a necessidade de SNMP.


![Monitor de Desempenho da Rede](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho](network-performance-monitor.md)da Rede e selecione **Configure**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Network Performance Monitor é um recipiente lógico para sub-redes. Ajuda-o a organizar a monitorização da sua infraestrutura de rede de acordo com as suas necessidades. Pode criar uma rede com um nome amigável e adicionar-lhe sub-redes de acordo com a sua lógica de negócio. Por exemplo, pode criar uma rede chamada Londres e adicionar todas as sub-redes no seu centro de dados de Londres. Ou pode criar uma rede chamada *ContosoFrontEnd* e adicionar a esta rede todas as sub-redes chamadas Contoso que servem a parte frontal da sua aplicação. A solução cria automaticamente uma rede padrão, que contém todas as sub-redes descobertas no seu ambiente. 

Sempre que se cria uma rede, adiciona-lhe uma sub-rede. Em seguida, a sub-rede é removida da rede predefinido. Se eliminar uma rede, todas as suas sub-redes são automaticamente devolvidas à rede predefinido. A rede predefinida funciona como um recipiente para todas as sub-redes que não estão contidas em nenhuma rede definida pelo utilizador. Não é possível editar ou eliminar a rede predefinida. Sempre permanece no sistema. Pode criar todas as redes personalizadas que precisar. Na maioria dos casos, as sub-redes da sua organização estão dispostas em mais de uma rede. Crie uma ou mais redes para agrupar as suas sub-redes para a sua lógica de negócio.

Para criar uma nova rede:


1. Selecione o separador **Redes.**
1.  **Selecione Adicionar a rede** e, em seguida, insira o nome e descrição da rede. 
2. Selecione uma ou mais sub-redes e, em seguida, **selecione Adicionar**. 
3. **Selecione Guardar** para guardar a configuração. 


### <a name="create-monitoring-rules"></a>Criar regras de monitorização 

O Monitor de Desempenho gera eventos de saúde quando o limiar de desempenho das ligações de rede entre duas sub-redes ou entre duas redes é violado. O sistema pode aprender estes limiares automaticamente. Também pode fornecer limiares personalizados. O sistema cria automaticamente uma regra predefinitiva, que gera um evento de saúde sempre que a perda ou latência entre qualquer par de ligações de rede ou sub-rede viola o limiar aprendido pelo sistema. Este processo ajuda a solução a monitorizar a sua infraestrutura de rede até não ter criado nenhuma regra de monitorização explicitamente. Se a regra predefinida estiver ativada, todos os nós enviam transações sintéticas para todos os outros nós que ativou para monitorização. A regra padrão é útil com redes pequenas. Um exemplo é um cenário em que temos um pequeno número de servidores a executar um microserviço e queremos certificar-nos de que todos os servidores têm conectividade uns com os outros.

>[!NOTE]
> Recomendamos que desative a regra padrão e crie regras de monitorização personalizadas, especialmente com grandes redes onde utiliza um grande número de nós para monitorização. As regras de monitorização personalizadas podem reduzir o tráfego gerado pela solução e ajudá-lo a organizar a monitorização da sua rede.

Crie regras de monitorização de acordo com a sua lógica de negócio. Um exemplo é se quiser monitorizar o desempenho da conectividade de rede de dois escritórios para a sede. Agrupe todas as sub-redes no site do escritório1 na rede O1. Em seguida, agrupe todas as sub-redes no site do escritório2 na rede O2. Finalmente, agrupam todas as sub-redes da sede da rede H. Crie duas regras de monitorização-- uma entre O1 e H e outra entre O2 e H. 

Para criar regras de monitorização personalizadas:

1. Selecione **Adicionar Regra** no **separador Monitor** e introduza o nome e descrição da regra.
2. Selecione o par de links de rede ou sub-rede para monitorizar a partir das listas. 
3. Selecione a rede que contém as sub-redes que pretende da lista de down-down da rede. Em seguida, selecione as sub-redes da lista de retirada da sub-rede correspondente. Se pretender monitorizar todas as sub-redes numa ligação de rede, selecione **Todas as sub-redes**. Da mesma forma, selecione as outras sub-redes que pretende. Para excluir a monitorização de ligações específicas de sub-rede das seleções efetuadas, selecione **Add Exception**. 
4. Escolha entre protocolos ICMP e TCP para executar transações sintéticas. 
5. Se não quiser criar eventos de saúde para os itens selecionados, **desabrisse a monitorização de saúde nos links abrangidos por esta regra.** 
6. Escolha as condições de monitorização. Para definir limiares personalizados para a geração de eventos de saúde, insira valores limiares. Sempre que o valor da condição exceder o limiar selecionado para o par de rede ou sub-rede selecionado, é gerado um evento de saúde. 
7. **Selecione Guardar** para guardar a configuração. 

Depois de guardar uma regra de monitorização, pode integrar essa regra com a Gestão de Alerta selecionando **Criar Alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa. Outros parâmetros necessários são preenchidos automaticamente. Utilizando uma regra de alerta, pode receber alertas baseados em e-mail, além dos alertas existentes no Network Performance Monitor. Os alertas também podem desencadear ações corretivas com runbooks, ou podem integrar-se com as soluções de Gestão de Serviços existentes utilizando webhooks. **Selecione 'Gestão' alerta** para editar as definições de alerta. 

Pode agora criar mais regras do Monitor de Desempenho ou mover-se para o painel de instrumentos de solução para utilizar a capacidade.

### <a name="choose-the-protocol"></a>Escolha o protocolo

O Network Performance Monitor utiliza transações sintéticas para calcular métricas de desempenho da rede, como perda de pacote e latência de ligação. Para compreender melhor este conceito, considere um agente do Monitor de Desempenho de Rede ligado a uma extremidade de uma ligação de rede. Este agente do Monitor de Desempenho da Rede envia pacotes de sonda para um segundo agente do Monitor de Desempenho da Rede ligado a outra extremidade da rede. O segundo agente responde com pacotes de resposta. Este processo repete-se algumas vezes. Ao medir o número de respostas e o tempo necessário para receber cada resposta, o primeiro agente do Monitor de Desempenho da Rede avalia a latência da ligação e as quedas de pacote. 

O formato, tamanho e sequência destes pacotes é determinado pelo protocolo que escolhe quando cria regras de monitorização. Com base no protocolo dos pacotes, os dispositivos de rede intermédia, como routers e comutadores, podem processar estes pacotes de forma diferente. Consequentemente, a sua escolha de protocolo afeta a precisão dos resultados. A sua escolha de protocolo também determina se deve tomar quaisquer passos manuais depois de implementar a solução Monitor de Desempenho da Rede. 

O Monitor de Desempenho da Rede oferece-lhe a escolha entre os protocolos ICMP e TCP para a execução de transações sintéticas. Se escolher o ICMP quando criar uma regra de transação sintética, os agentes do Monitor de Desempenho da Rede utilizam mensagens ICMP ECHO para calcular a latência da rede e a perda de pacotes. O ICMP ECHO utiliza a mesma mensagem que é enviada pelo utilitário de ping convencional. Quando utiliza o TCP como protocolo, os agentes do Monitor de Desempenho da Rede enviam pacotes TCP SYN pela rede. Este passo é seguido por um aperto de mão TCP, e a ligação é removida usando pacotes RST. 

Considere as seguintes informações antes de escolher um protocolo: 

* **Descoberta de várias rotas de rede.** O TCP é mais preciso ao descobrir várias rotas, e precisa de menos agentes em cada sub-rede. Por exemplo, um ou dois agentes que usam TCP podem descobrir todos os caminhos redundantes entre sub-redes. Precisa de vários agentes que utilizem o ICMP para obter resultados semelhantes. Utilizando o ICMP, se tiver uma série de rotas entre duas sub-redes, precisa de mais de agentes 5N numa sub-rede de origem ou destino.

* **Precisão dos resultados.** Os routers e os comutadores tendem a atribuir uma menor prioridade aos pacotes ICMP ECHO em comparação com os pacotes TCP. Em determinadas situações, quando os dispositivos de rede são fortemente carregados, os dados obtidos pela TCP refletem mais de perto a perda e a latência sentidas pelas aplicações. Isto ocorre porque a maioria do tráfego de aplicações flui sobre tCP. Nestes casos, o ICMP fornece resultados menos precisos em comparação com o TCP. 

* **Configuração de firewall.** O protocolo TCP requer que os pacotes TCP sejam enviados para uma porta de destino. A porta predefinida utilizada pelos agentes do Monitor de Desempenho da Rede é 8084. Pode mudar a porta quando configurar agentes. Certifique-se de que as suas firewalls de rede ou regras do grupo de segurança da rede (NSG) (em Azure) permitem o tráfego na porta. Também é necessário certificar-se de que a firewall local nos computadores onde os agentes estão instalados está configurada para permitir o tráfego nesta porta. Pode utilizar scripts PowerShell para configurar as regras de firewall nos seus computadores que executam o Windows, mas precisa de configurar manualmente a sua firewall de rede. Em contrapartida, o ICMP não funciona utilizando uma porta. Na maioria dos cenários empresariais, o tráfego ICMP é permitido através das firewalls para permitir que você use ferramentas de diagnóstico de rede como o utilitário ping. Se conseguir tirar uma máquina de outra, pode utilizar o protocolo ICMP sem ter de configurar firewalls manualmente.

>[!NOTE] 
> Algumas firewalls podem bloquear o ICMP, o que pode levar à retransmissão que resulta num grande número de eventos no seu sistema de informação de segurança e gestão de eventos. Certifique-se de que o protocolo que escolher não está bloqueado por uma firewall de rede ou NSG. Caso contrário, o Monitor de Desempenho da Rede não pode monitorizar o segmento de rede. Recomendamos que utilize o TCP para monitorização. Utilize o ICMP em cenários onde não possa utilizar o TCP, como quando: 
>
> - Usa nós baseados no cliente do Windows, porque as tomadas cruas TCP não são permitidas nos clientes windows.
> - A sua firewall de rede ou NSG bloqueia o TCP.
> - Não sabes como mudar o protocolo.

Se optar por utilizar o ICMP durante a implementação, pode mudar para TCP a qualquer momento editando a regra de monitorização predefinido.

1. Vá ao Monitor de Configuração do Monitor **de Desempenho da**   >  ****   >  ****   >  **** Rede. Em seguida, selecione **a regra padrão**. 
2. Percorra a secção **Protocolo** e selecione o protocolo que pretende utilizar. 
3. **Selecione Guardar** para aplicar a definição. 

Mesmo que a regra predefinida utilize um protocolo específico, pode criar novas regras com um protocolo diferente. Pode até criar um misto de regras onde algumas regras usam ICMP e outras usam TCP. 

## <a name="walkthrough"></a>Instruções 

Agora olhe para uma simples investigação sobre a causa principal de um evento de saúde.

No painel de instrumentos de solução, um evento de saúde mostra que uma ligação de rede não é saudável. Para investigar o problema, selecione as **ligações de rede que estão a ser monitorizadas.**

A página de perfuração mostra que a ligação de rede **DMZ2-DMZ1** não é saudável. Selecione **Ver links de sub-rede** para esta ligação de rede. 


A página de perfuração mostra todas as ligações de sub-rede na ligação de rede **DMZ2-DMZ1.** Para ambas as ligações de sub-rede, a latência ultrapassou o limiar, o que torna a ligação de rede pouco saudável. Também pode ver as tendências de latência de ambas as ligações de sub-rede. Utilize o controlo de seleção de tempo no gráfico para se concentrar no intervalo de tempo necessário. Pode-se ver a hora do dia em que a latência atingiu o seu auge. Procure nos registos mais tarde para este período de tempo para investigar o problema. Selecione **Ver as ligações do nó** para perfurar mais. 
 
 ![Página de links de sub-rede](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Semelhante à página anterior, a página de broca para baixo para o link de sub-rede particular lista os seus links de nó constituinte. Pode executar ações semelhantes aqui como fez no passo anterior. Selecione **Ver topologia** para ver a topologia entre os dois nós. 
 
 ![Página de Links de Nó](media/network-performance-monitor-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados estão traçados no mapa da topologia. Você pode visualizar a topologia hop-by-hop de rotas entre dois nós no mapa de topologia. Dá-lhe uma imagem clara de quantas rotas existem entre os dois nós e que caminhos os pacotes de dados tomam. Os estrangulamentos de desempenho da rede são mostrados a vermelho. Para localizar uma ligação de rede defeituosa ou um dispositivo de rede defeituoso, veja os elementos vermelhos no mapa da topologia. 

 ![Painel de Topologia com mapa de topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Você pode rever a perda, latência, e o número de lúpulo em cada caminho no painel de **ação.** Utilize a barra de deslocação para ver os detalhes dos caminhos insalubres. Utilize os filtros para selecionar os caminhos com o lúpulo pouco saudável de modo a que a topologia apenas para os caminhos selecionados seja traçada. Para fazer zoom dentro ou fora do mapa da topologia, use a roda do rato. 

Na imagem a seguir, a causa principal das áreas problemáticas para a secção específica da rede aparece nos caminhos vermelhos e no lúpulo. Selecione um nó no mapa da topologia para revelar as propriedades do nó, que inclui o endereço FQDN e IP. A seleção de um lúpulo mostra o endereço IP do lúpulo. 
 
![Mapa de topologia com propriedades de nó selecionados](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passos seguintes
[Faça uma pesquisa de registos](../logs/log-query-overview.md) para visualizar registos de dados de desempenho detalhados da rede.

