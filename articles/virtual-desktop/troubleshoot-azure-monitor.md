---
title: Monitor de resolução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas com o Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a104f4d33e1bd38e130101b34d3fd2021de27cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445487"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Troubleshoot Monitor Azure para Windows Virtual Desktop

Este artigo apresenta problemas e soluções conhecidos para problemas comuns no Azure Monitor para o Windows Virtual Desktop.

## <a name="issues-with-configuration-and-setup"></a>Problemas com configuração e configuração

Se o livro de configuração não estiver a funcionar corretamente para automatizar a instalação, pode utilizar estes recursos para configurar o seu ambiente manualmente:

- Para ativar manualmente diagnósticos ou aceder ao espaço de trabalho Do Log Analytics, consulte [enviar diagnósticos de ambiente de trabalho virtual do Windows para registar análises.](diagnostics-log-analytics.md)
- Para instalar a extensão Log Analytics num anfitrião de sessão manualmente, consulte a [extensão da máquina virtual Log Analytics para windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho log Analytics, consulte [criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/logs/quick-create-workspace.md).
- Para adicionar, remover ou editar contadores de desempenho, consulte [balcões de desempenho configurados](../azure-monitor/agents/data-sources-performance-counters.md).
- Para configurar os Registos de Eventos do Windows para um espaço de trabalho do Log Analytics, consulte [obter fontes de dados de registo de eventos do Windows com o agente Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Os meus dados não estão a exibir-se corretamente.

Se os seus dados não estiverem a ser apresentados corretamente, verifique as seguintes soluções comuns:

- Em primeiro lugar, certifique-se de que se configura corretamente com o livro de configuração descrito no [Use Azure Monitor para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md). Se faltar algum contador ou evento, os dados associados a eles não aparecerão no portal Azure.
- Consulte as suas permissões de acesso & contacte os proprietários de recursos para solicitar permissões em falta; qualquer pessoa que monitorize o Windows Virtual Desktop requer as seguintes permissões:
    - Read-access às subscrições do Azure que detêm os seus recursos de Desktop Virtual do Windows
    - Read-access aos grupos de recursos da subscrição que detêm os anfitriões da sessão virtual do Windows 
    - Read-access a qualquer espaço de trabalho do Log Analytics que você está usando
- Poderá ser necessário abrir portas de saída na firewall do seu servidor para permitir que o Azure Monitor e o Log Analytics enviem dados para o portal. Para aprender a fazê-lo, consulte os seguintes artigos:
      - [Portas de saída do Monitor Azure](../azure-monitor/app/ip-addresses.md)
      - [Registar requisitos de firewall de analíticos](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Não ver dados de atividades recentes? É melhor esperar 15 minutos e refrescar o alimento. O Azure Monitor tem um período de latência de 15 minutos para a povoação de dados de registo. Para saber mais, consulte [o tempo de ingestão de dados do Registo no Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

Se não estiver a perder nenhuma informação, mas os seus dados ainda não estiverem a ser apresentados corretamente, pode haver um problema na consulta ou nas fontes de dados. Rever [questões e limitações conhecidas.](#known-issues-and-limitations) 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar o Monitor Azure para o Windows Virtual Desktop

O Monitor Azure para o Ambiente de Trabalho Virtual do Windows utiliza livros de trabalho do Monitor Azure. Os livros de trabalho permitem guardar uma cópia do modelo de livro virtual do Windows desktop e fazer as suas próprias personalizações.

Por design, os modelos de livro personalizados não adotarão automaticamente atualizações do grupo de produtos. Para obter mais informações, consulte [insights baseados em livros de resolução de problemas](../azure-monitor/insights/troubleshoot-workbooks.md) e a visão geral dos [livros de trabalho.](../azure-monitor/visualize/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Não posso interpretar os dados.

Saiba mais sobre os termos de dados no [Azure Monitor para o glossário virtual de desktop da janela](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados que preciso não estão disponíveis

Se pretender monitorizar mais contadores de desempenho ou Registos de Eventos do Windows, pode habilitar-lhes a enviar informações de diagnóstico para o seu espaço de trabalho Log Analytics e monitorizá-los em **Diagnósticos de Anfitrião: Navegador anfitrião**. 

- Para adicionar contadores de desempenho, consulte [balcões de desempenho configurados](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Para adicionar Eventos do Windows, consulte [configurar registos de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos feedback!

- Para aprender a deixar feedback, consulte [a visão geral de resolução de problemas, feedback e suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Seguem-se questões e limitações que conhecemos e trabalhamos para corrigir:

- Só se pode monitorizar uma piscina de cada vez. 
- Para guardar as definições favoritas, tem de guardar um modelo personalizado do livro. Os modelos personalizados não adotam automaticamente atualizações do grupo de produtos.
- O livro de configuração por vezes mostra erros de "consulta falhada" ao carregar as suas seleções. Refresque a consulta, reentre na sua seleção se necessário, e o erro deve resolver-se sozinho. 
- Algumas mensagens de erro não são formuladas de forma amigável e nem todas as mensagens de erro são descritas em documentação.
- O contador de desempenho total de sessões pode contar de mais por um pequeno número e as suas sessões totais podem parecer superiores ao limite das Suas Sessões Max.
- A contagem de sessões disponíveis não reflete as políticas de escala na piscina anfitriã.   
- Vê tempos de ligação contraditórios ou inesperados? Embora raro, o evento de conclusão de uma ligação pode desaparecer e pode afetar alguns visuais e métricas.
- O tempo de ligação inclui o tempo que os utilizadores demoram a introduzir as suas credenciais; isto relaciona-se com a experiência, mas em alguns casos pode mostrar picos falsos. 
    

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [o Use Azure Monitor para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md).
- Para estimar, medir e gerir os seus custos de armazenamento de dados, consulte [os custos do Monitor De Estimativa Azure](azure-monitor-costs.md).
- Consulte o nosso [glossário](azure-monitor-glossary.md) para saber mais sobre termos e conceitos relacionados com o Azure Monitor para o Windows Virtual Desktop.
