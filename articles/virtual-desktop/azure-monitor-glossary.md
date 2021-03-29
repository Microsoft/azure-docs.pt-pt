---
title: Monitor Windows Virtual Desktop glossário - Azure
description: Um glossário de termos e conceitos relacionados com o Azure Monitor para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 38f34324a0cda518645f8b9231e08bafbd75cca0
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709536"
---
# <a name="azure-monitor-for-windows-virtual-desktop-glossary"></a>Monitor Azure para glossário virtual de ambiente de trabalho do Windows

Este artigo lista e descreve brevemente termos e conceitos chave relacionados com o Azure Monitor para o Windows Virtual Desktop (pré-visualização).

## <a name="alerts"></a>Alertas

Quaisquer alertas ativos do Azure Monitor que tenha configurado na subscrição e classificados como [gravidade 0](#severity-0-alerts) aparecerão na página geral. Para aprender a configurar alertas, consulte [Responder a eventos com alertas do Monitor Azure.](../azure-monitor/alerts/tutorial-response.md)

## <a name="available-sessions"></a>Sessões disponíveis

As sessões disponíveis mostram o número de sessões disponíveis na piscina anfitriã. O serviço calcula este número multiplicando o número de máquinas virtuais (VMs) pelo número máximo de sessões permitidas por máquina virtual, subtraindo depois o total das sessões.

## <a name="connection-success"></a>Sucesso de conexão

Este item mostra a saúde da ligação. "Sucesso de ligação" significa que a ligação pode chegar ao hospedeiro, como confirmado pela pilha naquela máquina virtual. Uma ligação falhada significa que a ligação não chegou ao hospedeiro.

## <a name="daily-active-users-dau"></a>Utilizadores ativos diários (DAU)

O número total de utilizadores que iniciaram uma sessão nas últimas 24 horas.

## <a name="daily-alerts"></a>Alertas diários

O número total de alertas desencadeados todos os dias.

## <a name="daily-connections-and-reconnections"></a>Ligações e restabelecimento de ligações diários

O número total de ligações e reconexões iniciadas ou concluídas nas últimas 24 horas.

## <a name="daily-connected-hours"></a>Horas de ligação diárias

O número total de horas passadas ligadas a uma sessão entre utilizadores nas últimas 24 horas.

## <a name="diagnostics-and-errors"></a>Diagnósticos e erros

Quando um erro ou alerta aparece no Azure Monitor para o Windows Virtual Desktop, é categorizado por três coisas:

- Tipo de atividade: esta categoria é como o erro é categorizado por diagnósticos de Desktop Virtual do Windows. As categorias são atividades de gestão, feeds, conexões, registos de anfitriões, erros e postos de controlo. Saiba mais sobre estas categorias no [Use Log Analytics para a funcionalidade de diagnóstico.](diagnostics-log-analytics.md)

- Tipo: esta categoria mostra a localização do erro. 

     - Erros marcados como "service" ou "ServiceError = TRUE" ocorreram no serviço de ambiente de trabalho virtual do Windows.
     - Erros marcados como "implementação" ou marcados "ServiceError = FALSE" ocorreram fora do serviço de ambiente de trabalho virtual do Windows.
     - Para saber mais sobre a etiqueta ServiceError, consulte [cenários de erro comuns](diagnostics-role-service.md#common-error-scenarios).

- Fonte: esta categoria dá uma descrição mais específica de onde o erro aconteceu.

     - Diagnósticos: a função de serviço responsável pela monitorização e reporte da atividade do serviço para permitir que os utilizadores observem e diagnosticem problemas de implantação.

     - RDBroker: a função de serviço responsável por orquestrar atividades de implantação, manter o estado dos objetos, validar a autenticação, e muito mais.

     - RDGateway: a função de serviço responsável pelo manuseamento da conectividade da rede entre utilizadores finais e máquinas virtuais.

     - RDStack: um componente de software instalado nos seus VMs para permitir que comuniquem com o serviço de desktop virtual do Windows.

     - Cliente: software em execução na máquina de utilizador final que fornece a interface para o serviço de ambiente de trabalho virtual do Windows. Apresenta a lista de recursos publicados e acolhe a ligação Remote Desktop uma vez que tenha feito uma seleção.

Cada problema ou erro de diagnóstico inclui uma mensagem que explica o que correu mal. Para saber mais sobre erros de resolução de problemas, consulte [identificar e diagnosticar problemas de Ambiente de Trabalho Virtual do Windows](diagnostics-role-service.md).

## <a name="input-delay"></a>Atraso de entrada

"Atraso de entrada" no Monitor Azure para o Ambiente de Trabalho Virtual do Windows significa o atraso de entrada por contador de desempenho do processo para cada sessão. Na página de desempenho do anfitrião [aka.ms/azmonwvdi,](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)este contador de desempenho está configurado para enviar um relatório ao serviço uma vez a cada 30 segundos. Estes intervalos de 30 segundos são chamados de "amostras", e o relatório é o pior caso naquela janela. Os valores medianos e p95 refletem o percentil mediano e 95 em todas as amostras.

Sob **atraso de entrada por anfitrião,** pode selecionar uma linha de anfitrião de sessão para filtrar todos os outros visuais da página para o anfitrião. Também pode selecionar um nome de processo para filtrar o atraso médio de entrada ao longo do gráfico de tempo.

Colocamos atrasos nas seguintes categorias:

- Bom: abaixo de 150 milissegundos.
- Aceitável: 150-500 milissegundos.
- Pobre: 500-2.000 milissegundos (abaixo de 2 segundos).
- Mau: mais de 2.000 milissegundos (2 segundos e mais).

Para saber mais sobre o funcionamento do contador de atraso de entrada, consulte os [contadores de desempenho do Atraso de Entrada do Utilizador](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Utilizadores Ativos Mensais (MAU)

O número total de utilizadores que iniciaram uma sessão nos últimos 28 dias. Se armazenar dados durante 30 dias ou menos, poderá ver valores DE MAU e Conexão inferiores ao esperado durante períodos em que tenha menos de 28 dias de dados disponíveis.

## <a name="performance-counters"></a>Contadores de desempenho

Os contadores de desempenho mostram o desempenho de componentes de hardware, sistemas operativos e aplicações.

A tabela que se segue lista os contadores de desempenho recomendados e intervalos de tempo que o Azure Monitor utiliza para o Ambiente de Trabalho Virtual do Windows:

|Nome do contador de desempenho|Intervalo de tempo|
|---|---|
|Disco lógico (C:) \\ Avg. Comprimento da fila do disco|30 segundos|
|Disco lógico(C:) \\ Avg. Disco seg/Transferência|60 segundos|
|Comprimento da fila do disco lógico (C:) \\ Comprimento da fila do disco|30 segundos|
|Memória( \* ) \\ Mbytes disponíveis|30 segundos|
|Memória. \* ) Falhas de \\ página/seg|30 segundos|
|Memória. \* \\ ) Páginas/seg|30 segundos|
|Memory. \* \\ % Bytes comprometidos em uso|30 segundos|
|PhysicalDisk( \* ) \\ Avg. Comprimento da fila do disco|30 segundos|
|PhysicalDisk. \* \\ ) Avg. Disco sec/Read|30 segundos|
|PhysicalDisk( \* ) \\ Avg. Disco sec/Transfer|30 segundos|
|PhysicalDisk( \* ) \\ Avg. Disco sec/Write|30 segundos|
|Informação do processador (_Total) \\ % tempo do processador|30 segundos|
|Serviços de \* Terminais. ) \\ Sessões Ativas|60 segundos|
|Serviços de \* Terminais. ) \\ Sessões Inativas|60 segundos|
|Serviços \* terminais. \\|60 segundos|
|\*Atraso de entrada do utilizador por \* processo. \\|30 segundos|
|\*Atraso de entrada do utilizador por \* sessão. \\|30 segundos|
|Rede \* RemoteFX. \\|30 segundos|
|Rede \* RemoteFX. \\|30 segundos|

Para saber mais sobre como ler contadores de desempenho, consulte [balcões de desempenho configurados.](../azure-monitor/agents/data-sources-performance-counters.md)

Para saber mais sobre os contadores de desempenho de atraso de entrada, consulte os [contadores de desempenho do Atraso de Entrada do Utilizador](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Potenciais problemas de conectividade

Problemas potenciais de conectividade mostram os anfitriões, utilizadores, recursos publicados e clientes com uma elevada taxa de falha de ligação. Uma vez que escolha um filtro "report by", pode avaliar a gravidade da questão verificando os valores nestas colunas:

- Tentativas (número de tentativas de ligação)
- Recursos (número de aplicações publicadas ou desktops)
- Anfitriões (número de VMs)
- Clientes

Por exemplo, se selecionar o filtro **By,** pode verificar se as tentativas de ligação de cada utilizador estão na coluna **Tentativas.**

Se notar que um problema de ligação abrange vários anfitriões, utilizadores, recursos ou clientes, é provável que o problema afete todo o sistema. Se não, é uma questão menor que é a menor prioridade.

Também pode selecionar entradas para visualizar informações adicionais. Pode ver quais os anfitriões, recursos e versões do cliente envolvidas no problema. O visor também mostrará quaisquer erros relatados durante as tentativas de ligação.

## <a name="round-trip-time-rtt"></a>Tempo de ida e volta (RTT)

O tempo de ida e volta (RTT) é uma estimativa do tempo de ida e volta da ligação entre a localização do utilizador final e a região de Azure do anfitrião da sessão. Para ver quais as localizações que têm a melhor latência, procure a localização desejada na [ferramenta de estimativa de experiência virtual do Windows Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Histórico das sessões

O item **Sessions** mostra o estado de todas as sessões, conectadas e desligadas. **As sessões inativas** apenas mostram as sessões desligadas.

## <a name="severity-0-alerts"></a>Alertas de Severidade 0

Os itens mais urgentes que precisa de tratar imediatamente. Se não resolver estes problemas, podem fazer com que a sua implementação virtual do Windows para parar de funcionar.

## <a name="time-to-connect"></a>Tempo para fazer a ligação

Tempo de ligação é o momento entre quando um utilizador inicia a sua sessão e quando é contado como sendo inscrito no serviço. O estabelecimento de novas ligações tende a demorar mais do que restabelecer as ligações existentes.

## <a name="user-report"></a>Relatório do utilizador

A página do relatório do utilizador permite-lhe visualizar o histórico de ligação de um utilizador específico e informações de diagnóstico. Cada relatório do utilizador mostra padrões de utilização, feedback do utilizador e quaisquer erros que os utilizadores tenham encontrado durante as suas sessões. A maioria dos problemas mais pequenos podem ser resolvidos com o feedback do utilizador. Se precisar de cavar mais fundo, também pode filtrar informações sobre um ID de ligação específico ou um período de tempo.

## <a name="users-per-core"></a>Utilizadores por núcleo

Este é o número de utilizadores em cada núcleo de máquinas virtuais. Rastrear o número máximo de utilizadores por núcleo ao longo do tempo pode ajudá-lo a identificar se o ambiente funciona consistentemente a um número elevado, baixo ou flutuante de utilizadores por núcleo. Saber quantos utilizadores estão ativos irá ajudá-lo a aumentar eficientemente o ambiente.

## <a name="windows-event-logs"></a>Registos de Eventos do Windows

Os Registos de Eventos do Windows são fontes de dados recolhidas por agentes do Log Analytics em máquinas virtuais do Windows. Pode recolher eventos a partir de registos padrão como Sistema e Aplicação, bem como registos personalizados criados por aplicações que precisa de monitorizar.

A tabela que se segue lista os registos de eventos do Windows necessários para o Monitor Azure para o Ambiente de Trabalho Virtual do Windows:

|Nome do evento|Tipo de evento|
|---|---|
|Aplicação|Erro e Aviso|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin|Erro, Aviso e Informação|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operacional|Erro, Aviso e Informação|
|Sistema|Erro e Aviso|
| Microsoft-FSLogix-Apps/Operacional|Erro, Aviso e Informação|
|Microsoft-FSLogix-Apps/Admin|Erro, Aviso e Informação|

Para saber mais sobre os Registos de Eventos do Windows, consulte [as propriedades dos registos do Windows Event](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Monitor para o Windows Virtual Desktop, confira estes artigos:

- [Utilize o Monitor Azure para o Windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md)
- [Monitor Azure para resolução de problemas do Windows Virtual Desktop](troubleshoot-azure-monitor.md)

Também pode configurar o Azure Advisor para ajudá-lo a descobrir como resolver ou prevenir questões comuns. Saiba mais no [Use Azure Advisor com o Windows Virtual Desktop](azure-advisor.md).

Se precisa de ajuda ou tiver alguma dúvida, consulte os nossos recursos comunitários:

- Faça perguntas ou faça sugestões à comunidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Para aprender a deixar feedback, consulte [a visão geral de resolução de problemas, feedback e suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md#report-issues).

- Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
