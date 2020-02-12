---
title: Monitorize as suas cópias de segurança com o Backup Explorer
description: Este artigo descreve como usar o Backup Explorer para realizar a monitorização em tempo real de backups através de cofres, subscrições, regiões e inquilinos.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b65f68e33b53dff341ee72f6b9e9f42e344c49b1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149581"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorize as suas cópias de segurança com o Backup Explorer

À medida que as organizações recuam cada vez mais máquinas para a nuvem, torna-se cada vez mais importante monitorizar estas cópias de segurança de forma eficiente. A melhor maneira de começar é usar uma localização central para visualizar informações operacionais em uma grande propriedade.

Backup Explorer é um livro de trabalho azure monitor incorporado que dá aos clientes Azure Backup esta localização única e central. O Backup Explorer ajuda-o a monitorizar as atividades operacionais em toda a propriedade de Backup em Azure, abrangendo inquilinos, localizações, subscrições, grupos de recursos e cofres. Em termos gerais, o Backup Explorer fornece as seguintes capacidades:

* **Perspetiva à escala**: Obtenha uma visão agregada dos itens de backup, empregos, alertas, políticas e recursos que ainda não estão configurados para backup em toda a propriedade. 
* **Análise de perfuração**: Mostrar informações detalhadas sobre cada um dos seus trabalhos, alertas, políticas e itens de backup, tudo num só local.
* **Interfaces atoladas**: Depois de identificar um problema, pode resolvê-lo indo perfeitamente para o item de backup relevante ou recurso Azure.

Estas capacidades são fornecidas fora da caixa por integração nativa com o Azure Resource Graph e os livros azure Monitor.

> [!NOTE]
> * O Backup Explorer está atualmente disponível apenas para dados de máquinas virtuais Azure (VMs).
> * O Backup Explorer destina-se a ser um dashboard operacional para visualizar informações sobre as suas cópias de segurança nos últimos 7 dias (máximo).
> * Atualmente, personalizar o modelo do Explorador de Backup não é suportado. 
> * Não recomendamos a escrita de automatizações personalizadas em dados do Azure Resource Graph.

## <a name="get-started"></a>Introdução

Pode aceder ao Backup Explorer indo a qualquer um dos seus cofres de Serviços de Recuperação e selecionando o link **Backup Explorer** no painel **overview.**

![Ligação rápida do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A seleção do link abre o Backup Explorer, que proporciona uma vista agregada em todos os cofres e subscrições a que tem acesso. Se estiver a usar uma conta azure lighthouse, pode ver dados em todos os inquilinos a que tem acesso. Para mais informações, consulte a secção "Cross-tenant views" no final deste artigo.

![Página de aterragem do Explorador de Backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Backup Explorer usa casos

O Backup Explorer exibe vários separadores, cada um fornecendo informações detalhadas sobre um artefacto de backup específico (por exemplo, um item de backup, trabalho ou política). Esta secção fornece uma breve visão geral de cada um dos separadores. Os vídeos fornecem casos de utilização de amostras para cada artefacto de reserva, juntamente com descrições dos controlos disponíveis.

### <a name="the-summary-tab"></a>O separador resumo

O separador **Resumo** proporciona uma rápida olhada na condição geral da sua propriedade de reserva. Por exemplo, pode ver o número de itens protegidos, o número de itens para os quais a proteção não foi ativada, ou quantos empregos foram bem sucedidos nas últimas 24 horas.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>O separador 'Itens de Backup'

Pode filtrar e visualizar cada um dos seus itens de backup por subscrição, cofre e outras características. Ao selecionar o nome de um item de reserva, pode abrir o painel Azure para esse item. Por exemplo, a partir da mesa, pode observar que a última cópia de segurança falhou no ponto *X*. Ao selecionar *X,* pode abrir o painel de **backup** do artigo, onde pode desencadear uma operação de backup a pedido.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>O separador Jobs

Selecione o separador **Jobs** para ver os detalhes de todos os trabalhos que foram desencadeados nos últimos 7 dias. Aqui, pode filtrar por *Operação de Trabalho,* *Estado de Trabalho*e Código de *Erro* (para trabalhos falhados).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>O separador Alertas

Selecione o separador **Alertas** para ver detalhes de todos os alertas que foram gerados nos seus cofres ao longo dos últimos 7 dias. Pode filtrar alertas por tipo (*Falha de Cópia de Segurança* ou Falha de *Restauro),* estado atual *(Ativo* ou *Resolvido)* e gravidade (*Crítica,* *Aviso*ou *Informação).* Também pode selecionar um link para ir ao Azure VM e tomar todas as medidas necessárias.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>O separador Políticas

Pode selecionar o separador **Políticas** para ver informações chave sobre todas as políticas de backup que foram criadas em toda a sua propriedade de backup. Pode visualizar o número de itens associados a cada política, juntamente com o intervalo de retenção e a frequência de backup especificada pela apólice.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>O separador de backup não ativado

A cópia de segurança deve ser ativada para todas as máquinas que necessitem de proteção. Com o Backup Explorer, os administradores de backup podem identificar rapidamente quais as máquinas de uma organização ainda não estão protegidas por backup. Para ver estas informações, selecione o separador **'Backup' não ativado.**

O painel **de backup não ativado** exibe uma tabela com uma lista de máquinas desprotegidas. A sua organização pode atribuir etiquetas diferentes a máquinas de produção e máquinas de teste, ou a máquinas que servem uma variedade de funções. Porque cada classe de máquinas precisa de uma política de backup separada, filtrar por tags ajuda-o a ver informações específicas para cada um. A seleção do nome de qualquer máquina redireciona-o para o painel de **backup configure** da máquina, onde pode optar por aplicar uma política de backup apropriada.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportação para Excel

Pode exportar o conteúdo de qualquer tabela ou gráfico como uma folha de cálculo do Excel. Os conteúdos são exportados tal como estão, com os filtros existentes aplicados. Para exportar linhas de mesa adicionais, pode aumentar o número de linhas a serem exibidas na página utilizando a lista de down-down **Rows Per Page** no topo de cada separador.

## <a name="pin-to-the-dashboard"></a>Pin o tablier

Pode selecionar o ícone "pin" na parte superior de cada tabela ou gráfico para fixá-lo no seu portal Azure. A fixação desta informação ajuda-o a criar um dashboard personalizado que é personalizado para mostrar a informação que é mais importante para si.

## <a name="cross-tenant-views"></a>Vistas de inquilinos cruzados

Se for um utilizador do Farol Azure com acesso delegado a subscrições em vários ambientes de inquilinos, pode utilizar o filtro de subscrição predefinido. Exibe as subscrições para as quais pretende ver dados selecionando o ícone "filtro" na parte superior direita do portal Azure. Quando utiliza esta funcionalidade, o Backup Explorer agrega informações sobre todos os cofres através das suas subscrições selecionadas. Para saber mais, veja [o que é o Farol Azure?](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>Passos seguintes

[Saiba como usar o Monitor Azure para obter informações sobre os seus dados de backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
