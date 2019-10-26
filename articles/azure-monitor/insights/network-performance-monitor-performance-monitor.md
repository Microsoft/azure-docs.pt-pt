---
title: Recurso de monitor de desempenho na solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs
description: O recurso de monitor de desempenho no Monitor de Desempenho de Rede ajuda a monitorar a conectividade de rede em vários pontos em sua rede. Você pode monitorar implantações de nuvem e locais no local, vários data centers e filiais e aplicativos multicamadas de missão crítica ou microservices.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 745ec6ee8e69ad911e42b6360b3408d79d660718
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898840"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Solução de Monitor de Desempenho de Rede: monitoramento de desempenho

O recurso de monitor de desempenho no [Monitor de desempenho de rede](network-performance-monitor.md) ajuda a monitorar a conectividade de rede em vários pontos em sua rede. Você pode monitorar implantações de nuvem e locais no local, vários data centers e filiais e aplicativos multicamadas de missão crítica ou microservices. Com o monitor de desempenho, você pode detectar problemas de rede antes que seus usuários se queixam. As principais vantagens são que você pode: 

- Monitore a perda e a latência em várias sub-redes e defina alertas.
- Monitore todos os caminhos (incluindo caminhos redundantes) na rede.
- Solucione problemas de rede transitórios e point-in-time, que são difíceis de replicar.
- Determine o segmento específico na rede, que é responsável pelo desempenho degradado.
- Monitore a integridade da rede, sem a necessidade de SNMP.


![Monitor de Desempenho da Rede](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho de rede](network-performance-monitor.md)e selecione **Configurar**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Monitor de Desempenho de Rede é um contêiner lógico para sub-redes. Ele ajuda a organizar o monitoramento de sua infraestrutura de rede de acordo com suas necessidades. Você pode criar uma rede com um nome amigável e adicionar sub-redes a ela de acordo com sua lógica de negócios. Por exemplo, você pode criar uma rede chamada Londres e adicionar todas as sub-redes em seu data center de Londres. Ou você pode criar uma rede chamada *ContosoFrontEnd* e adicioná-la a essa rede todas as sub-redes chamadas contoso que atendem ao front-end do seu aplicativo. A solução cria automaticamente uma rede padrão, que contém todas as sub-redes descobertas em seu ambiente. 

Sempre que você criar uma rede, adicione uma sub-rede a ela. Em seguida, essa sub-rede é removida da rede padrão. Se você excluir uma rede, todas as suas sub-redes serão retornadas automaticamente para a rede padrão. A rede padrão atua como um contêiner para todas as sub-redes que não estão contidas em nenhuma rede definida pelo usuário. Você não pode editar ou excluir a rede padrão. Ele sempre permanece no sistema. Você pode criar quantas redes personalizadas forem necessárias. Na maioria dos casos, as sub-redes em sua organização são organizadas em mais de uma rede. Crie uma ou mais redes para agrupar suas sub-redes para sua lógica de negócios.

Para criar uma nova rede:


1. Selecione a guia **redes** .
1. Selecione **Adicionar rede**e, em seguida, insira o nome e a descrição da rede. 
2. Selecione uma ou mais sub-redes e, em seguida, selecione **Adicionar**. 
3. Selecione **salvar** para salvar a configuração. 


### <a name="create-monitoring-rules"></a>Criar regras de monitoramento 

O monitor de desempenho gera eventos de integridade quando o limite do desempenho de conexões de rede entre duas sub-redes ou entre duas redes é violado. O sistema pode aprender esses limites automaticamente. Você também pode fornecer limites personalizados. O sistema cria automaticamente uma regra padrão, que gera um evento de integridade sempre que a perda ou a latência entre qualquer par de links de rede ou sub-rede viola o limite aprendido pelo sistema. Esse processo ajuda a solução a monitorar sua infraestrutura de rede até que você não tenha criado explicitamente nenhuma regra de monitoramento. Se a regra padrão estiver habilitada, todos os nós enviarão transações sintéticas para todos os outros nós que você habilitou para monitoramento. A regra padrão é útil com redes pequenas. Um exemplo é um cenário em que você tem um pequeno número de servidores que executam um microserviço e você deseja garantir que todos os servidores tenham conectividade entre si.

>[!NOTE]
> Recomendamos que você desabilite a regra padrão e crie regras de monitoramento personalizadas, especialmente com grandes redes em que você usa um grande número de nós para monitoramento. As regras de monitoramento personalizadas podem reduzir o tráfego gerado pela solução e ajudá-lo a organizar o monitoramento de sua rede.

Crie regras de monitoramento de acordo com sua lógica de negócios. Um exemplo é se você quiser monitorar o desempenho da conectividade de rede de dois sites do Office para a sede. Agrupe todas as sub-redes no Office site1 na rede O1. Em seguida, Agrupe todas as sub-redes no Office site2 na rede O2. Por fim, Agrupe todas as sub-redes na matriz na rede H. crie duas regras de monitoramento – uma entre O1 e H e a outra entre O2 e H. 

Para criar regras de monitoramento personalizadas:

1. Selecione **Adicionar regra** na guia **monitorar** e insira o nome da regra e a descrição.
2. Selecione o par de links de rede ou de sub-rede a serem monitorados nas listas. 
3. Selecione a rede que contém as sub-redes desejadas na lista suspensa rede. Em seguida, selecione as sub-redes na lista suspensa subrede correspondente. Se você quiser monitorar todas as sub-redes em um link de rede, selecione **todas as sub-redes**. Da mesma forma, selecione as outras sub-redes desejadas. Para excluir o monitoramento de links de sub-rede específicos das seleções feitas, selecione **Adicionar exceção**. 
4. Escolha entre os protocolos TCP e ICMP para executar transações sintéticas. 
5. Se você não quiser criar eventos de integridade para os itens selecionados, desmarque **habilitar monitoramento de integridade nos links cobertos por essa regra**. 
6. Escolha as condições de monitoramento. Para definir limites personalizados para geração de eventos de integridade, insira valores de limite. Sempre que o valor da condição exceder seu limite selecionado para o par de rede ou de sub-rede selecionado, um evento de integridade será gerado. 
7. Selecione **salvar** para salvar a configuração. 

Depois de salvar uma regra de monitoramento, você pode integrar essa regra com Gerenciamento de Alertas selecionando **criar alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa. Outros parâmetros necessários são preenchidos automaticamente. Usando uma regra de alerta, você pode receber alertas baseados em email, além dos alertas existentes no Monitor de Desempenho de Rede. Os alertas também podem disparar ações corretivas com runbooks, ou podem se integrar a soluções de gerenciamento de serviços existentes usando WebHooks. Selecione **gerenciar alerta** para editar as configurações de alerta. 

Agora você pode criar mais regras de monitor de desempenho ou mover para o painel de solução para usar a funcionalidade.

### <a name="choose-the-protocol"></a>Escolha o protocolo

Monitor de Desempenho de Rede usa transações sintéticas para calcular métricas de desempenho de rede, como perda de pacotes e latência de link. Para entender melhor esse conceito, considere um agente de Monitor de Desempenho de Rede conectado a uma extremidade de um link de rede. Esse agente de Monitor de Desempenho de Rede envia pacotes de investigação para um segundo agente de Monitor de Desempenho de Rede conectado a outra extremidade da rede. O segundo agente responde com pacotes de resposta. Esse processo se repete algumas vezes. Medindo o número de respostas e o tempo necessário para receber cada resposta, o primeiro agente de Monitor de Desempenho de Rede avalia a latência de link e os descartes de pacotes. 

O formato, o tamanho e a sequência desses pacotes são determinados pelo protocolo que você escolhe ao criar regras de monitoramento. Com base no protocolo dos pacotes, os dispositivos de rede intermediários, como roteadores e comutadores, podem processar esses pacotes de forma diferente. Consequentemente, sua escolha de protocolo afeta a precisão dos resultados. Sua escolha de protocolo também determina se você deve executar etapas manuais depois de implantar a solução de Monitor de Desempenho de Rede. 

Monitor de Desempenho de Rede oferece a opção entre os protocolos ICMP e TCP para executar transações sintéticas. Se você escolher ICMP ao criar uma regra de transação sintética, os agentes de Monitor de Desempenho de Rede usarão mensagens de eco ICMP para calcular a latência de rede e a perda de pacotes. O eco ICMP usa a mesma mensagem enviada pelo utilitário de ping convencional. Quando você usa TCP como protocolo, os agentes de Monitor de Desempenho de Rede enviam pacotes TCP SYN pela rede. Essa etapa é seguida por uma conclusão de handshake TCP e a conexão é removida usando pacotes RST. 

Considere as seguintes informações antes de escolher um protocolo: 

* **Descoberta de várias rotas de rede.** O TCP é mais preciso ao descobrir várias rotas e precisa de menos agentes em cada sub-rede. Por exemplo, um ou dois agentes que usam TCP podem descobrir todos os caminhos redundantes entre sub-redes. Você precisa de vários agentes que usam o ICMP para obter resultados semelhantes. Usando o ICMP, se você tiver um número de rotas entre duas sub-redes, precisará de mais de agentes 5N em uma sub-rede de origem ou de destino.

* **Precisão dos resultados.** Roteadores e comutadores tendem a atribuir prioridade mais baixa aos pacotes de eco ICMP em comparação com os pacotes TCP. Em determinadas situações, quando os dispositivos de rede são muito carregados, os dados obtidos pelo TCP refletem mais de acordo a perda e a latência experimentada pelos aplicativos. Isso ocorre porque a maior parte do tráfego do aplicativo flui sobre TCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação com o TCP. 

* **Configuração do firewall.** O protocolo TCP requer que os pacotes TCP sejam enviados a uma porta de destino. A porta padrão usada pelos agentes de Monitor de Desempenho de Rede é 8084. Você pode alterar a porta ao configurar agentes. Verifique se os firewalls de rede ou as regras de NSG (grupo de segurança de rede) (no Azure) permitem o tráfego na porta. Você também precisa certificar-se de que o firewall local nos computadores em que os agentes estão instalados esteja configurado para permitir o tráfego nessa porta. Você pode usar scripts do PowerShell para configurar regras de firewall em seus computadores que executam o Windows, mas você precisa configurar o firewall de rede manualmente. Por outro lado, o ICMP não funciona usando uma porta. Na maioria dos cenários empresariais, o tráfego ICMP é permitido por meio de firewalls para permitir que você use ferramentas de diagnóstico de rede como o utilitário Ping. Se você puder executar o ping de um computador de outro, poderá usar o protocolo ICMP sem precisar configurar firewalls manualmente.

>[!NOTE] 
> Alguns firewalls podem bloquear o ICMP, o que pode levar à retransmissão que resulta em um grande número de eventos em seu sistema de gerenciamento de eventos e informações de segurança. Verifique se o protocolo escolhido não está bloqueado por um firewall de rede ou NSG. Caso contrário, Monitor de Desempenho de Rede não poderá monitorar o segmento de rede. Recomendamos que você use TCP para monitoramento. Use o ICMP em cenários em que você não pode usar TCP, como quando: 
>
> - Você usa nós baseados no cliente Windows, porque os soquetes TCP brutos não são permitidos em clientes Windows.
> - O firewall de rede ou o NSG bloqueia o TCP.
> - Você não sabe como alternar o protocolo.

Se você optar por usar o ICMP durante a implantação, poderá alternar para o TCP a qualquer momento editando a regra de monitoramento padrão.

1. Vá para **desempenho de rede** > **Monitor** > **Configurar** > **Monitor**. Em seguida, selecione **regra padrão**. 
2. Role até a seção **protocolo** e selecione o protocolo que você deseja usar. 
3. Selecione **salvar** para aplicar a configuração. 

Mesmo que a regra padrão Use um protocolo específico, você pode criar novas regras com um protocolo diferente. Você pode até mesmo criar uma mistura de regras em que algumas regras usam ICMP e outras usam TCP. 

## <a name="walkthrough"></a>Instruções 

Agora, examine uma investigação simples na causa raiz de um evento de integridade.

No painel da solução, um evento de integridade mostra que um link de rede não está íntegro. Para investigar o problema, selecione o bloco **links de rede sendo monitorados** .

A página de busca detalhada mostra que o link de rede **link-DMZ1** não está íntegro. Selecione **exibir links de sub-rede** para este link de rede. 


A página de busca detalhada mostra todos os links de sub-rede no link de rede **link-DMZ1** . Para ambos os links de sub-rede, a latência ultrapassou o limite, o que torna o link de rede não íntegro. Você também pode ver as tendências de latência de ambos os links de sub-rede. Use o controle de seleção de tempo no grafo para se concentrar no intervalo de tempo necessário. Você pode ver a hora do dia em que a latência atingiu seu pico. Pesquise os logs mais tarde neste período de tempo para investigar o problema. Selecione **exibir links de nó** para detalhar ainda mais. 
 
 ![Página de links de sub-rede](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Semelhante à página anterior, a página de busca detalhada do link de sub-rede específico lista seus links de nós constituintes. Você pode executar ações semelhantes aqui, como fez na etapa anterior. Selecione **Exibir topologia** para exibir a topologia entre os dois nós. 
 
 ![Página links de nó](media/network-performance-monitor-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados são plotados no mapa de topologia. Você pode visualizar a topologia de salto a salto de rotas entre dois nós no mapa de topologia. Ele fornece uma visão clara de quantas rotas existem entre os dois nós e quais caminhos os pacotes de dados adotam. Os afunilamentos de desempenho de rede são mostrados em vermelho. Para localizar uma conexão de rede com falha ou um dispositivo de rede com falha, examine os elementos vermelhos no mapa de topologia. 

 ![Painel de topologia com mapa de topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Você pode examinar a perda, a latência e o número de saltos em cada caminho no painel de **ação** . Use a barra de rolagem para exibir os detalhes dos caminhos não íntegros. Use os filtros para selecionar os caminhos com o salto não íntegro para que a topologia somente para os caminhos selecionados seja plotada. Para ampliar ou reduzir o mapa de topologia, use a roda do mouse. 

Na imagem a seguir, a causa raiz das áreas problemáticas para a seção específica da rede aparece nos caminhos e saltos vermelhos. Selecione um nó no mapa de topologia para revelar as propriedades do nó, que inclui o FQDN e o endereço IP. Selecionar um salto mostra o endereço IP do salto. 
 
![Mapa de topologia com propriedades de nó selecionadas](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar logs](../../azure-monitor/log-query/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.
