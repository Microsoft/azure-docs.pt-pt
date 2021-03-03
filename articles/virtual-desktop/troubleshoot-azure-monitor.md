---
title: Monitorização de problemas Monitor Windows Visualização virtual desktop - Azure
description: Como resolver problemas com o Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709177"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Troubleshoot Azure Monitor para Windows Virtual Desktop (pré-visualização)

>[!IMPORTANT]
>O Monitor Azure para o Windows Virtual Desktop encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo apresenta problemas e soluções conhecidos para problemas comuns no Azure Monitor para o Windows Virtual Desktop (pré-visualização).

## <a name="issues-with-configuration-and-setup"></a>Problemas com configuração e configuração

Se o livro de configuração não estiver a funcionar corretamente para automatizar a instalação, pode utilizar estes recursos para configurar o seu ambiente manualmente:

- Para ativar manualmente diagnósticos ou aceder ao espaço de trabalho Do Log Analytics, consulte [enviar diagnósticos de ambiente de trabalho virtual do Windows para registar análises.](diagnostics-log-analytics.md)
- Para instalar manualmente a extensão Log Analytics num anfitrião, consulte a [extensão da máquina virtual Log Analytics para windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho log Analytics, consulte [criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/logs/quick-create-workspace.md).
- Para adicionar ou remover contadores de desempenho, consulte [balcões de desempenho configurados](../azure-monitor/agents/data-sources-performance-counters.md).
- Para configurar eventos para um espaço de trabalho do Log Analytics, consulte [recolher fontes de dados de registo de eventos do Windows com o agente Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Os meus dados não estão a exibir-se corretamente.

Se os seus dados não estiverem a ser apresentados corretamente, verifique a sua configuração, permissões e verifique se os endereços IP necessários estão desbloqueados. 

- Em primeiro lugar, certifique-se de que preencheu todos os campos do livro de configuração descrito no [Use Azure Monitor para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md). Se faltar algum contador ou evento, os dados associados a eles não aparecerão no portal Azure.

- Consulte as suas permissões de acesso & contacte os proprietários de recursos para solicitar permissões em falta; qualquer pessoa que monitorize o Windows Virtual Desktop requer as seguintes permissões:

    - Read-access às subscrições do Azure que detêm os seus recursos de Desktop Virtual do Windows
    - Read-access aos grupos de recursos da subscrição que detêm os anfitriões da sessão virtual do Windows 
    - Read-access para o log analytics workspace

- Poderá ser necessário abrir portas de saída na firewall do seu servidor para permitir que o Azure Monitor envie dados para o portal, consulte [as portas outgoing](../azure-monitor/app/ip-addresses.md). 

- Não ver dados de atividades recentes? É melhor esperar 15 minutos e refrescar o alimento. O Azure Monitor tem um período de latência de 15 minutos para a povoação de dados de registo. Para saber mais, consulte [o tempo de ingestão de dados do Registo no Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

Se não estiver a perder nenhuma informação, mas os seus dados ainda não estiverem a ser apresentados corretamente, pode haver um problema na consulta ou nas fontes de dados. Reveja as nossas questões e limitações conhecidas. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar o Monitor Azure para o Windows Virtual Desktop

O Monitor Azure para o Ambiente de Trabalho Virtual do Windows utiliza livros de trabalho do Monitor Azure. Os livros de trabalho permitem guardar uma cópia do modelo de livro virtual do Windows desktop e fazer as suas próprias personalizações.

Por design, os modelos de livro personalizados não adotarão automaticamente atualizações do grupo de produtos. Para obter mais informações, consulte [insights baseados em livros de resolução de problemas](../azure-monitor/insights/troubleshoot-workbooks.md) e a visão geral dos [livros de trabalho.](../azure-monitor/visualize/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Não posso interpretar os dados.

Saiba mais sobre os termos de dados no [Azure Monitor para o glossário virtual de desktop da janela](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados que preciso não estão disponíveis

Se pretender monitorizar mais Contadores de Desempenho ou Eventos, pode habilitado-os a enviar para o seu espaço de trabalho Log Analytics e monitorizá-los em Diagnósticos anfitrião: Navegador anfitrião. 

- Para adicionar contadores de desempenho, consulte [balcões de desempenho configurados](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Para adicionar Eventos do Windows, consulte [registos de eventos do Windows configurados](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos feedback!

- Para aprender a deixar feedback, consulte [a visão geral de resolução de problemas, feedback e suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) ou no nosso fórum [UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Estas são questões e limitações que estamos atualmente cientes e trabalhando para corrigir:

- Só se pode monitorizar uma piscina de cada vez. 

- Para guardar as definições favoritas, tem de guardar um modelo personalizado do livro. Os modelos personalizados não adotam automaticamente atualizações do grupo de produtos.

- Algumas mensagens de erro não são formuladas de forma amigável e nem todas as mensagens de erro são descritas na documentação.

- O contador de desempenho total de sessões pode contar de mais por um pequeno número e as suas sessões totais podem parecer superiores ao limite das Suas Sessões Max.

- A contagem de sessões disponíveis não reflete as políticas de escala na piscina anfitriã. 
    
- Embora raro, o evento de conclusão de uma ligação pode desaparecer e isso pode afetar alguns visuais como ligações ao longo do tempo e o estado de ligação do utilizador.  
    
- O livro de configuração suporta apenas a configuração dos anfitriões dentro da mesma região que o seu grupo de recursos. 

- O tempo de ligação inclui o tempo que os utilizadores demoram a introduzir as suas credenciais; isto relaciona-se com a experiência, mas em alguns casos pode mostrar picos falsos. 
    

## <a name="next-steps"></a>Passos seguintes

Se não tem a certeza de como interpretar os dados ou se pretende saber mais sobre termos comuns, consulte o [Azure Monitor para o glossário virtual do Windows Desktop](azure-monitor-glossary.md). Se quiser aprender a configurar e utilizar o Monitor Azure para o Ambiente de Trabalho Virtual do Windows, consulte [o Monitor Azure para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md).