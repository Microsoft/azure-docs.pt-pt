---
title: Monitorize as suas cópias de segurança com o Backup Explorer
description: Um artigo que descreve como usar o Backup Explorer para realizar a monitorização em tempo real de backups através de cofres, subscrições, regiões e inquilinos
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992186"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorize as suas cópias de segurança com o Backup Explorer

À medida que as organizações recuam cada vez mais máquinas para a nuvem, torna-se importante ter uma localização central para ver informações operacionais sobre backups em uma grande propriedade, para monitorizar eficientemente os backups.

O Livro de Trabalho do Explorador de Backup é um livro de trabalho azure monitor incorporado que permite aos clientes de backup ter um único painel de vidro para realizar atividades de monitorização operacional relacionadas com backup em toda a propriedade de backup em Azure (abrangendo inquilinos, locais, assinaturas, grupos de recursos e cofres), todos de um lugar central. Em termos gerais, fornece as seguintes capacidades:

* **Na perspetiva de escala** - Uma visão agregada dos itens de backup, empregos, alertas, políticas e recursos não configurados para backup em toda a propriedade de backup. 
* **Faça análises** – Pode optar por obter informações detalhadas de cada uma das entidades – os empregos, alertas, políticas e itens de backup, tudo a partir de um único local.
* **Interfaces acionáveis** – Uma vez identificado um problema, pode optar por realizar ações navegando sem problemas até ao item de reserva ou ao recurso Azure.

As capacidades acima são fornecidas fora da caixa por integração nativa com o Azure Resource Graph e o Azure Monitor Books.

> [!NOTE]
> * O Backup Explorer está atualmente disponível apenas para dados De VM Azure.
> * O Backup Explorer destina-se a ser um dashboard operacional para visualizar informações sobre as suas cópias de segurança nos últimos 7 dias (máximo).
> * Atualmente, a personalização do modelo do Backup Explorer não é suportada. Além disso, não recomendamos atualmente a escrita de automatizações personalizadas em dados do Azure Resource Graph.

## <a name="getting-started"></a>Introdução

Pode aceder ao Explorador de Backup navegando em qualquer um dos seus Cofres de Serviços de Recuperação e clicando no link **Do Explorador de Backup** na página **'Overview'.**

![Ligação Rápida do Cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Clicar no link abre o Backup Explorer que proporciona uma vista agregada em todos os cofres e subscrições a que tem acesso. Se estiver a usar uma conta azure lighthouse, pode ver dados em todos os inquilinos a que tem acesso (ver mais na secção abaixo sobre vistas de inquilinos cruzados).

![Página de aterragem do explorador](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de utilização do explorador de backup

O Backup Explorer é composto por vários separadores, cada separador que fornece informações detalhadas sobre um tipo específico de artefacto de backup, por exemplo, itens de backup, empregos, políticas, etc. Esta secção fornece uma breve visão geral de cada um dos separadores. Os vídeos fornecem casos de utilização de amostras para cada um dos separadores, juntamente com uma descrição dos vários controlos disponíveis para o utilizador.

### <a name="summary"></a>Resumo

O separador Resumo permite-lhe ter uma rápida olhada na condição geral da sua propriedade de reserva. Pode ver informações como o número de itens protegidos, o número de itens para os quais a proteção não foi ativada, quantos postos de trabalho foram bem sucedidos nas últimas 24 horas, e assim por diante. 

Cada um dos outros separadores representa uma entidade distinta – por exemplo: Backup Itens, Backup Jobs, Backup Alerts e Políticas de Backup. Também pode visualizar informações sobre máquinas para as quais o Backup não foi configurado. Clicar em qualquer um destes separadores mostrará informações específicas a essa entidade.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Itens de backup

Pode visualizar cada um dos seus itens de backup filtrados por subscrição, cofre e outros parâmetros. Clicar no nome de um item de reserva permite-lhe abrir a lâmina Azure para o item de reserva. Por exemplo, a partir da mesa, pode observar que a última cópia de segurança falhou no item 'X'. Clicar em 'X' abre a lâmina de backup para este item, onde pode desencadear uma operação de backup a pedido.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Tarefas

Pode ver os detalhes de todos os trabalhos que foram desencadeados nos últimos sete dias, navegando até ao separador Jobs. Aqui, pode filtrar por Operação de Emprego, Estado de Trabalho e Código de Erro (no caso de trabalhos falhados).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Alertas

Pode ver detalhes de todos os alertas que foram disparados nos seus cofres nos últimos sete dias, clicando no separador Alertas. Aqui, pode filtrar registos pelo tipo de alerta (por exemplo, Falha de Cópia de Segurança, Falha de Restauro), estado atual do alerta (por exemplo, Ativo ou Resolvido) e a gravidade do alerta (por exemplo, Crítico, Aviso, Informação). Também pode navegar para o Azure VM clicando no link para o VM e tomando as medidas necessárias.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Políticas

Clicar no separador Políticas permite-lhe visualizar informações chave sobre todas as políticas de backup que foram criadas em toda a sua propriedade de backup. Pode ver quantos itens estão associados a cada política, juntamente com o intervalo de retenção e frequência de backup especificada por cada política.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Backup não ativado

É importante que o administrador de reserva de uma organização identifique rapidamente quais as máquinas da organização que ainda não têm backup habilitado, para que o backup possa ser ativado para todas as máquinas que precisam de proteção. Clicar no separador **Backup Não Ativado** ajuda-o nesta tarefa.

Neste separador, verá uma tabela contendo a lista de itens que não estão protegidos. Se a sua organização seguir a prática de atribuir diferentes tags a máquinas de produção e máquinas de teste, ou máquinas que servem diferentes funções, cada uma das quais pode necessitar de uma política de backup separada, filtrar por tags ajuda-o a ver informações específicas a um certas classes de máquinas. Clicar no nome de qualquer item redireciona-o para a lâmina de **backup Configure** para esse item, onde pode optar por fazer backup com uma política de backup apropriada.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exportação para Excel

Clicar no botão de seta para baixo na parte superior direita de qualquer widget (tabela/gráfico) exporta o conteúdo desse widget como folha Excel, como é com os filtros existentes aplicados. Para exportar mais linhas de uma tabela para excel, pode aumentar o número de linhas exibidas na página utilizando o dropdown **Rows Per Page** na parte superior de cada separador.

## <a name="pinning-to-dashboard"></a>Fixação ao Painel de Instrumentos

Pode clicar no Ícone Pin na parte superior de cada widget para fixar esse widget no seu portal Azure. Isto ajuda-o a criar dashboards personalizados à medida para mostrar as informações mais importantes de que necessita.

## <a name="cross-tenant-views"></a>Vistas de inquilinos cruzados

Se for um utilizador do Farol Azure com acesso delegado a subscrições em vários ambientes de inquilinos, pode utilizar o filtro de subscrição predefinido (clicando no ícone do filtro no canto superior direito do portal Azure) para selecionar todas as subscrições que deseja ver dados para. Ao fazê-lo, o Explorador de Backup irá agregar informações sobre todos os cofres através destas subscrições. Saiba mais sobre o Farol Azure [aqui.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>Próximos Passos

[Saiba como usar o Monitor Azure para obter informações sobre os seus dados de backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)