---
title: Monitorização de problemas Monitor Windows Visualização virtual desktop - Azure
description: Como resolver problemas com o Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467674"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Troubleshoot Azure Monitor para Windows Virtual Desktop (pré-visualização)

>[!IMPORTANT]
>O Monitor Azure para o Windows Virtual Desktop encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo apresenta problemas e soluções conhecidos para problemas comuns no Azure Monitor para o Windows Virtual Desktop (pré-visualização).

## <a name="the-configuration-workbook-isnt-working-properly"></a>O livro de configuração não está a funcionar corretamente.

Se o livro de configuração do Monitor Azure não estiver a funcionar, pode utilizar estes recursos para configurar as suas peças manualmente:

- Para ativar manualmente diagnósticos ou aceder ao espaço de trabalho Do Log Analytics, consulte [enviar diagnósticos de ambiente de trabalho virtual do Windows para registar análises.](diagnostics-log-analytics.md)
- Para instalar manualmente a extensão Log Analytics num anfitrião, consulte a [extensão da máquina virtual Log Analytics para windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho log Analytics, consulte [criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md).
- Para adicionar ou remover contadores de desempenho, consulte [balcões de desempenho configurados](../azure-monitor/platform/data-sources-performance-counters.md).
- Para configurar eventos para um espaço de trabalho do Log Analytics, consulte [recolher fontes de dados de registo de eventos do Windows com o agente Log Analytics](../azure-monitor/platform/data-sources-windows-events.md).

Em alternativa, o problema pode ser causado pela falta de recursos ou por não ter as permissões necessárias.

Se a subscrição não tiver quaisquer recursos de Desktop Virtual do Windows, não aparecerá no parâmetro *de Subscrição.*

Se não tiver lido o acesso às subscrições corretas, elas não aparecerão no parâmetro *de Subscrição* e não verá os seus dados no painel de instrumentos. Para resolver este problema, contacte o proprietário da subscrição e peça acesso à leitura.

## <a name="my-data-isnt-displaying-properly"></a>Os meus dados não estão a exibir-se corretamente.

Se os seus dados não estiverem a ser apresentados corretamente, pode ter acontecido alguma coisa durante o processo de configuração do Monitor Azure. Em primeiro lugar, certifique-se de que preencheu todos os campos do livro de configuração descrito no [Use Azure Monitor para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md). Pode alterar as definições para ambientes novos e existentes a qualquer momento. Se faltar algum contador ou evento, os dados associados a eles não aparecerão no portal Azure.

Se não estiver a perder nenhuma informação, mas os seus dados ainda não estiverem a ser apresentados corretamente, pode haver um problema na consulta ou nas fontes de dados. 

Se não vir erros de configuração e ainda não vir os dados que espera, pode esperar 15 minutos e refrescar o feed. O Azure Monitor tem um período de latência de 15 minutos para a povoação de dados de registo. Para saber mais, consulte [o tempo de ingestão de dados do Registo no Azure Monitor](../azure-monitor/platform/data-ingestion-time.md).

Finalmente, se não estiver a perder nenhuma informação, mas os seus dados ainda não aparecerem, pode haver um problema na consulta ou nas fontes de dados. Pode ter de contactar o Apoio para resolver o problema, se for esse o caso.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar o Monitor Azure para o Windows Virtual Desktop

O Monitor Azure para o Ambiente de Trabalho Virtual do Windows utiliza livros de trabalho do Monitor Azure. Os livros de trabalho permitem guardar uma cópia do modelo de livro virtual do Windows desktop e fazer as suas próprias personalizações.

Os modelos personalizados não serão atualizados quando o grupo de produtos atualizar o modelo original. Isto é por design na ferramenta de livros de trabalho, você precisará guardar uma cópia do modelo atualizado e reconstruir as suas personalizações para adotar atualizações. Para obter mais informações, consulte [insights baseados em livros de resolução de problemas](../azure-monitor/insights/troubleshoot-workbooks.md) e a visão geral dos [livros de trabalho.](../azure-monitor/platform/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Não posso interpretar os dados.

Saiba mais sobre os termos de dados no [Azure Monitor para o glossário virtual de desktop da janela](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados que preciso não estão disponíveis

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos feedback!

- Para aprender a deixar feedback, consulte [a visão geral de resolução de problemas, feedback e suporte para o Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) ou no nosso fórum [UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Problemas conhecidos

Estas são as questões que estamos atualmente cientes e trabalhando para corrigir:

- Atualmente só pode selecionar uma subscrição, grupo de recursos e piscina de anfitrião para monitorizar de cada vez. Por isso, ao utilizar a página 'Relatórios do Utilizador' para compreender a experiência de um utilizador, é necessário verificar se tem o conjunto de anfitriões correto que o utilizador tem vindo a utilizar ou que os seus dados não irão preencher os visuais.

- Atualmente, não é possível guardar as definições favoritas no Azure Monitor, a menos que guarde um modelo personalizado do livro. Isto significa que os administradores de TI terão de introduzir o seu nome de subscrição, nomes de grupos de recursos e preferências de piscina de anfitrião sempre que abrirem o Monitor Azure para o Windows Virtual Desktop.

- Atualmente não existe uma forma de exportar dados do Azure Monitor para o Windows Virtual Desktop para o Excel.

- Todos os alertas de severidade 1 Azure Monitor para todos os produtos dentro da subscrição selecionada aparecerão na página 'Vista Geral'. Isto é por design, uma vez que os alertas de outros produtos na subscrição podem ter impacto no Windows Virtual Desktop. Neste momento, a consulta está limitada à severidade 1 alertas, excluindo alertas de severidade de alta prioridade 0 da página do Visão Geral.

- Algumas mensagens de erro não são formuladas de forma amigável e nem todas as mensagens de erro são descritas na documentação.

## <a name="next-steps"></a>Passos seguintes

Se não tem a certeza de como interpretar os dados ou se pretende saber mais sobre termos comuns, consulte o [Azure Monitor para o glossário virtual do Windows Desktop](azure-monitor-glossary.md). Se quiser aprender a configurar e utilizar o Monitor Azure para o Ambiente de Trabalho Virtual do Windows, consulte [o Monitor Azure para windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md).