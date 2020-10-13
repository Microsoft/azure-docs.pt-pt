---
title: Monitorize as suas cópias de segurança com o Backup Explorer
description: Este artigo descreve como usar o Backup Explorer para realizar monitorização em tempo real de backups através de cofres, subscrições, regiões e inquilinos.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: a50b9ee05be48113221f2a12f968540bd3a00b3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88824417"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorize as suas cópias de segurança com o Backup Explorer

À medida que as organizações recuam cada vez mais máquinas para a nuvem, torna-se cada vez mais importante monitorizar estas cópias de segurança de forma eficiente. A melhor maneira de começar é usar uma localização central para visualizar informações operacionais em uma grande propriedade.

Backup Explorer é um livro de trabalho incorporado do Azure Monitor que dá aos clientes Azure Backup esta localização central única. O Backup Explorer ajuda-o a monitorizar as atividades operacionais em toda a propriedade backup em Azure, abrangendo inquilinos, localizações, subscrições, grupos de recursos e cofres. Em termos gerais, o Backup Explorer fornece as seguintes capacidades:

* **Perspetiva em escala**: Obtenha uma visão agregada dos itens de backup, empregos, alertas, políticas e recursos que ainda não estão configurados para apoio em toda a propriedade.
* **Análise de perfuração**: Exiba informações detalhadas sobre cada um dos seus trabalhos, alertas, políticas e itens de backup, tudo num só local.
* **Interfaces accuáveis**: Depois de identificar um problema, pode resolvê-lo indo perfeitamente para o item de backup relevante ou recurso Azure.

Estas capacidades são fornecidas fora de caixa por integração nativa com os livros de trabalho Azure Resource Graph e Azure Monitor.

> [!NOTE]
>
> * O Backup Explorer está atualmente disponível apenas para os dados das máquinas virtuais Azure (VMs).
> * O Backup Explorer deve ser um dashboard operacional para visualizar informações sobre as suas cópias de segurança nos últimos 7 dias (máximo).
> * O Backup Explorer não é suportado atualmente nas nuvens nacionais.
> * Atualmente, a personalização do modelo backup Explorer não é suportada.
> * Não recomendamos a escrita de automatizações personalizadas em dados do Azure Resource Graph.
> * Atualmente, o Backup Explorer permite-lhe monitorizar backups através de um máximo de 1000 subscrições (entre inquilinos).

## <a name="get-started"></a>Introdução

Pode aceder ao Backup Explorer indo a qualquer um dos seus cofres dos Serviços de Recuperação e selecionando o link **Backup Explorer** no painel **de visão** geral.

![Ligação rápida do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A seleção do link abre o Backup Explorer, que proporciona uma visão agregada em todos os cofres e subscrições a que tem acesso. Se estiver a usar uma conta no Farol Azure, pode ver dados em todos os inquilinos a que tem acesso. Para mais informações, consulte a secção "Vistas cruzadas" no final deste artigo.

![Página de aterragem do Explorador de Backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de utilização do Backup Explorer

O Backup Explorer apresenta vários separadores, cada um fornecendo informações detalhadas sobre um artefacto de backup específico (por exemplo, um item de backup, trabalho ou política). Esta secção fornece uma breve visão geral de cada um dos separadores. Os vídeos fornecem casos de utilização de amostras para cada artefacto de reserva, juntamente com descrições dos controlos disponíveis.

### <a name="the-summary-tab"></a>O separador Resumo

O **separador Resumo** fornece um rápido olhar para o estado geral da sua propriedade de reserva. Por exemplo, pode ver o número de itens protegidos, o número de itens para os quais a proteção não foi ativada, ou quantos empregos foram bem sucedidos nas últimas 24 horas.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>O separador Itens de Reserva

Pode filtrar e visualizar cada um dos seus itens de reserva por subscrição, cofre e outras características. Ao selecionar o nome de um item de reserva, pode abrir o painel Azure para esse item. Por exemplo, a partir da tabela, pode observar que a última cópia de segurança falhou no ponto *X*. Ao selecionar *X,* pode abrir o painel **de backup** do item, onde pode desencadear uma operação de backup a pedido.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>O separador Jobs

Selecione o separador **Jobs** para ver os detalhes de todos os trabalhos que foram desencadeados nos últimos 7 dias. Aqui, pode filtrar por *Operação de Trabalho,* *Estado do Trabalho*e Código de *Erro* (para trabalhos falhados).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>O separador Alertas

Selecione o separador **Alertas** para ver os detalhes de todos os alertas que foram gerados nos seus cofres nos últimos 7 dias. Pode filtrar alertas por tipo *(Falha de backup* ou *falha de restauro),* estado atual *(Ativo* ou *Resolvido)* e gravidade *(Crítico,* *Aviso*ou *Informação).* Também pode selecionar um link para ir ao Azure VM e tomar todas as medidas necessárias.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>O separador Políticas

Pode selecionar o separador **Políticas** para visualizar informações chave sobre todas as políticas de backup que foram criadas em toda a sua propriedade de backup. Pode ver o número de itens associados a cada política, juntamente com a gama de retenção e a frequência de backup especificada pela política.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>O separador backup não ativado

A cópia de segurança deve ser ativada para todas as máquinas que necessitem de proteção. Com o Backup Explorer, os administradores de backup podem identificar rapidamente quais as máquinas de uma organização que ainda não estão protegidas por cópias de segurança. Para ver estas informações, selecione o **separador Backup Não Ativado.**

O **painel de cópias de segurança não ativados** apresenta uma tabela com uma lista de máquinas desprotegidas. A sua organização pode atribuir etiquetas diferentes a máquinas de produção e máquinas de teste, ou a máquinas que servem uma variedade de funções. Como cada classe de máquinas precisa de uma política de backup separada, a filtragem por tags ajuda-o a ver informações específicas para cada uma. Selecionar o nome de qualquer máquina redireciona-o para o painel de **cópias de segurança configure** da máquina, onde pode optar por aplicar uma política de backup apropriada.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportar para o Excel

Pode exportar o conteúdo de qualquer tabela ou gráfico como uma folha de cálculo do Excel. O conteúdo é exportado como está, com os filtros existentes aplicados. Para exportar linhas de mesa adicionais, pode aumentar o número de linhas a serem apresentadas na página utilizando a lista de down-down **Rows Per Page** no topo de cada separador.

## <a name="pin-to-the-dashboard"></a>Pino no tablier

Pode selecionar o ícone "pin" na parte superior de cada mesa ou gráfico para fixá-lo no painel de instrumentos do portal Azure. Fixar esta informação ajuda-o a criar um dashboard personalizado que é personalizado para exibir a informação que é mais importante para si.

## <a name="cross-tenant-views"></a>Vistas de inquilinos cruzados

Se você é um utilizador do Azure Lighthouse com acesso delegado a subscrições em vários ambientes de inquilinos, você pode usar o filtro de subscrição padrão. Exibe as subscrições para as quais deseja ver dados selecionando o ícone "filtro" no canto superior direito do portal Azure. Quando utiliza esta funcionalidade, o Backup Explorer agrega informações sobre todos os cofres nas subscrições selecionadas. Para saber mais, veja [o que é o Farol de Azure?](../lighthouse/overview.md)

## <a name="next-steps"></a>Passos seguintes

[Saiba como utilizar o Azure Monitor para obter informações sobre os seus dados de backup](./backup-azure-monitoring-use-azuremonitor.md)
