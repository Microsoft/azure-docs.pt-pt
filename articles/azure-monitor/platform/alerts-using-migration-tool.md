---
title: Migrar regras de alerta do Monitor Azure
description: Aprenda a usar a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114258"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta

Como [já tinha sido anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão a ser retirados em setembro de 2019 (foi originalmente julho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras de alerta clássicas e que querem desencadear a migração por si mesmos. Este artigo explica como usar a ferramenta de migração para migrar voluntariamente as suas regras clássicas de alerta antes do início da migração automática em setembro de 2019.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de aposentação dos alertas clássicos de migração foi alargada até 31 de agosto de [2019 a](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) partir da data inicialmente anunciada de 30 de junho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios de novos alertas

Os alertas clássicos estão a ser substituídos por novos alertas unificados no Azure Monitor. A nova plataforma de alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos mais serviços Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Os novos alertas métricos [suportam regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzem consideravelmente a sobrecarga de gestão de muitas regras.
- O mecanismo de notificação unificado, que suporta:
  - [Grupos de ação](action-groups.md), um mecanismo de notificação modular que funciona com todos os novos tipos de alerta (métrica, log e log de atividade).
  - Novos mecanismos de notificação como SMS, voz e CONECTOR ITSM.
- A [experiência de alerta unificada](alerts-overview.md) traz todos os alertas em diferentes sinais (métrica, log e log de atividade) para um só local.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Tanto o formato de carga útil de notificação como as APIs para criar e gerir novas regras de alerta são diferentes das regras clássicas de alerta, uma vez que suportam mais funcionalidades. [Saiba como se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas utilizando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > O processo de migração não terá impacto na avaliação das suas regras clássicas de alerta. Continuarão a correr e a enviar alertas até serem migrados e as novas regras de alerta entrarem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como utilizar a ferramenta de migração

Para desencadear a migração das suas regras clássicas de alerta no portal Azure, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

1. Selecione **Alertas**e, em seguida, **selecione Gerir as regras de alerta** ou **ver alertas clássicos**.

1. **Selecione Migrar para novas regras** para ir para a página de desembarque da migração. Esta página mostra uma lista de todas as suas subscrições e o seu estado de migração:

    ![migração desembarque](media/alerts-migration/migration-landing.png "Regras migratórias")

    Todas as subscrições que podem ser migradas utilizando a ferramenta estão marcadas como **Ready to migrar**.

    > [!NOTE]
    > A ferramenta de migração está a ser lançada por fases a todas as subscrições que utilizam regras clássicas de alerta. Nas fases iniciais do lançamento, poderá ver algumas subscrições marcadas como não prontas para a migração.

1. Selecione uma ou mais subscrições e, em seguida, selecione **a migração de pré-visualização**.

    A página resultante mostra os detalhes das regras clássicas de alerta que serão migradas para uma subscrição de cada vez. Também pode selecionar **Baixar os detalhes da migração para esta subscrição** para obter os detalhes num formato CSV.

    ![pré-visualização da migração](media/alerts-migration/migration-preview.png "Pré-visualização da migração")

1. Especifique um ou mais endereços de e-mail para ser notificado do estado de migração. Receberá e-mail quando a migração estiver completa ou se for necessária alguma ação por si.

1. Selecione **Migração iniciar**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme que está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma subscrição, não poderá editar ou criar regras clássicas de alerta para essa subscrição. Esta restrição garante que não se percam alterações às suas regras clássicas de alerta durante a migração para as novas regras. Embora não consigas alterar as tuas regras clássicas de alerta, elas continuarão a correr e a fornecer alertas até serem migradas. Depois de a migração estar completa para a sua subscrição, já não pode utilizar regras clássicas de alerta.

    ![migração-confirmar](media/alerts-migration/migration-confirm.png "Confirme a migração inicial")

1. Quando a migração estiver completa, ou se for necessária uma ação de si, receberá um e-mail nos endereços que forneceu anteriormente. Também pode verificar periodicamente o estado na página de desembarque da migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Porque é que a minha assinatura não está pronta para a migração?

A ferramenta de migração está a ser lançada para os clientes por fases. Nas fases iniciais, a maioria ou a totalidade das suas subscrições podem ser marcadas como **Não prontas para a migração.** 

Quando uma subscrição estiver pronta para a migração, o proprietário da subscrição receberá uma mensagem de correio eletrónico indicando que a ferramenta está disponível. Fique de olho nesta mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Os utilizadores que tenham a função de Colaborador de Monitorização que lhes é atribuído ao nível da subscrição são capazes de desencadear a migração. [Saiba mais sobre o Controlo de Acesso Baseado em Fun para o processo de migração.](alerts-understand-migration.md#who-can-trigger-the-migration)

### <a name="how-long-will-the-migration-take"></a>Quanto tempo vai demorar a migração?

A migração é concluída para a maioria das subscrições em menos de uma hora. Pode acompanhar o progresso da migração na página de desembarque da migração. Durante a migração, tenha certeza de que os seus alertas ainda estão em funcionamento no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se me der a um problema durante a migração?

Consulte o [guia de resolução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que possa enfrentar durante a migração. Se for necessária alguma ação sua para completar a migração, será notificado nos endereços de e-mail que forneceu quando configurar a ferramenta.

## <a name="next-steps"></a>Próximos passos

- [Preparar para a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
