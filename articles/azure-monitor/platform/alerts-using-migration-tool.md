---
title: Emigra os seus alertas clássicos no Monitor Azure utilizando a ferramenta de migração voluntária
description: Aprenda a usar a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 537d84639523a74cbd9403d4ad25c34a798b7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665107"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta

Como [já foi anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão a ser retirados em setembro de 2019 (foi originalmente julho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras clássicas de alerta e que querem desencadear a migração por si mesmas. Este artigo explica como usar a ferramenta de migração para migrar voluntariamente as suas regras clássicas de alerta antes do início da migração automática em setembro de 2019.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de reforma para alertas clássicos migração foi alargada para 31 de agosto de [2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a partir da data inicialmente anunciada de 30 de junho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios de novos alertas

Os alertas clássicos estão a ser substituídos por novos alertas unificados no Monitor Azure. A nova plataforma de alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos mais serviços Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Os novos alertas métricos suportam [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzem consideravelmente a sobrecarga de gestão de muitas regras.
- O mecanismo de notificação unificado, que apoia:
  - [Grupos de ação](action-groups.md), um mecanismo modular de notificação que funciona com todos os novos tipos de alerta (registo métrico, diário e de atividade).
  - Novos mecanismos de notificação como SMS, voz e Conector ITSM.
- A [experiência de alerta unificada](alerts-overview.md) traz todos os alertas em diferentes sinais (registo métrico, diário e registo de atividade) para um único local.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Tanto o formato de carga de notificação como as APIs para criar e gerir novas regras de alerta são diferentes dos das regras clássicas de alerta porque suportam mais funcionalidades. [Aprenda a preparar-se para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas utilizando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > O processo de migração não terá impacto na avaliação das suas regras clássicas de alerta. Continuarão a correr e a enviar alertas até que sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como utilizar a ferramenta de migração

Para desencadear a migração das suas regras clássicas de alerta no portal Azure, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

1. Selecione **Alertas,** e, em seguida, **selecione Gerir regras** de alerta ou ver **alertas clássicos**.

1. **Selecione Migrar para novas regras** para ir para a página de aterragem da migração. Esta página mostra uma lista de todas as suas subscrições e o seu estado de migração:

    ![migração-desembarque](media/alerts-migration/migration-landing.png "Regras de migração")

    Todas as subscrições que podem ser migradas utilizando a ferramenta estão marcadas como **Prontas a migrar**.

    > [!NOTE]
    > A ferramenta de migração está a desenrolar-se por fases para todas as subscrições que usam regras clássicas de alerta. Nas primeiras fases do lançamento, poderá ver algumas subscrições marcadas como não prontas para a migração.

1. Selecione uma ou mais subscrições e, em seguida, selecione **a migração de pré-visualização**.

    A página resultante mostra os detalhes das regras clássicas de alerta que serão migradas para uma subscrição de cada vez. Também pode selecionar **Descarregue os detalhes da migração para esta subscrição** para obter os detalhes num formato CSV.

    ![migração-pré-visualização](media/alerts-migration/migration-preview.png "Pré-visualização migração")

1. Especifique um ou mais endereços de e-mail a notificar do estado de migração. Receberá e-mail quando a migração estiver completa ou se for necessária alguma ação por si.

1. Selecione **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme que está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma subscrição, não poderá editar ou criar regras clássicas de alerta para essa subscrição. Esta restrição garante que não se perdem alterações às suas regras clássicas de alerta durante a migração para as novas regras. Embora não consigas alterar as tuas regras clássicas de alerta, eles continuarão a correr e a prestar alertas até serem migrados. Depois da migração estar completa para a sua subscrição, já não pode utilizar regras clássicas de alerta.

    ![migração-confirmar](media/alerts-migration/migration-confirm.png "Confirmar a migração inicial")

1. Quando a migração estiver concluída, ou se for necessária uma ação sua, receberá um e-mail nos endereços que forneceu anteriormente. Também pode verificar periodicamente o estado na página de aterragem da migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Porque é que a minha subscrição não está pronta para a migração?

A ferramenta de migração está a ser lançada para os clientes por fases. Nas fases iniciais, a maioria ou todas as suas subscrições podem estar marcadas como **Não prontas para a migração.** 

Quando uma subscrição estiver pronta para a migração, o proprietário da subscrição receberá uma mensagem de e-mail indicando que a ferramenta está disponível. Fique de olho nesta mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Os utilizadores que tenham a função de Colaborador Monitorado atribuída a eles ao nível da subscrição são capazes de desencadear a migração. [Saiba mais sobre o Controlo de Acesso baseado em Papéis para o processo de migração.](alerts-understand-migration.md#who-can-trigger-the-migration)

### <a name="how-long-will-the-migration-take"></a>Quanto tempo demorará a migração?

A migração está concluída para a maioria das subscrições em menos de uma hora. Pode acompanhar o progresso da migração na página de aterragem da migração. Durante a migração, certifique-se de que os seus alertas ainda estão em execução no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se encontrar um problema durante a migração?

Consulte o guia de resolução de [problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que poderá enfrentar durante a migração. Se for necessária alguma ação sua para completar a migração, será notificado nos endereços de e-mail que forneceu quando configurar a ferramenta.

## <a name="next-steps"></a>Passos seguintes

- [Preparar para a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
