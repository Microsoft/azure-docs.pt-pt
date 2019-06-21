---
title: Migrar os seus alertas Clássicos no Azure Monitor utilizando a ferramenta de migração voluntária
description: Saiba como utilizar a ferramenta de migração voluntária para migrar as regras do alerta clássicas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 0c8aa00d069ae54584d8e828dab35c22048f1876
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295549"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Utilize a ferramenta de migração voluntária para migrar as regras do alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Setembro de 2019 (foi originalmente de 2019 de Julho). Uma ferramenta de migração está disponível no portal do Azure para os clientes que utilizam regras de alerta clássicas e que desejam acionar a migração propriamente ditas. Este artigo explica como utilizar a ferramenta de migração para migrar voluntariamente as regras do alerta clássicas antes da migração automática é iniciado em Setembro de 2019.

> [!NOTE]
> Devido a atraso no faseada da ferramenta de migração, tem sido a data de retirada para a migração de alertas clássicos [estendido para 31 de Agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a contar da data originalmente anunciado de 30 de Junho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios dos novos alertas

Alertas clássicos estão a ser substituídas pela nova e unificada alertas no Azure Monitor. A nova plataforma de alertas tem as seguintes vantagens:

- Pode alertar sobre uma variedade de métricas multidimensionais para o [muitos serviços do Azure mais](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- A métrica de novos alertas de suporte [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzir significativamente a sobrecarga de gerenciamento de muitas regras.
- O mecanismo de notificação unificada, que suporta:
  - [Grupos de ação](action-groups.md), um mecanismo de notificação modular que funciona com todos os tipos de alerta novo (métrica, registo e o registo de atividades).
  - Novos mecanismos de notificação, como SMS, voz e conector ITSM.
- O [alerta experiência unificada](alerts-overview.md) coloca todos os alertas em diferentes sinais (métrica, registo e o registo de atividades) num único local.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte as regras de alerta clássicas para regras de alerta novos, equivalentes e cria grupos de ação. Durante a preparação, tenha em atenção os seguintes pontos:

- O formato do payload de notificação e as APIs para criar e gerir novas regras de alerta são diferentes das regras de alerta clássicas porque eles oferecem suporte a mais recursos. [Saiba como preparar a migração](alerts-prepare-migration.md).

- Não não possível migrar algumas regras de alerta clássicas com a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com eles](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > O processo de migração não afetarão a avaliação de suas regras de alerta clássicas. Este irá continuar a executar e enviar alertas até serem migradas e as novas regras de alerta entram em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como utilizar a ferramenta de migração

Para acionar a migração de suas regras de alerta clássicas no portal do Azure, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

1. Selecione **alertas**e, em seguida, selecione **gerir regras de alerta** ou **ver alertas clássicos**.

1. Selecione **migre para novas regras** para ir para a página de destino de migração. Esta página mostra uma lista de todas as suas subscrições e o respetivo estado de migração:

    ![destino de migração](media/alerts-migration/migration-landing.png "migrar regras")

    Todas as subscrições que podem ser migradas através da ferramenta são marcadas como **pronto para migrar**.

    > [!NOTE]
    > A ferramenta de migração está a implementar em fases para todas as subscrições que utilizam regras de alerta clássicas. Nas fases iniciais da implementação do, poderá ver algumas subscrições marcadas como não está pronto para migração.

1. Selecione uma ou mais subscrições e, em seguida, selecione **pré-visualização migração**.

    A página resultante mostra os detalhes de regras de alerta clássicos que serão migrados para uma subscrição ao mesmo tempo. Também pode selecionar **transferir os detalhes de migração para esta subscrição** para obter os detalhes num formato CSV.

    ![pré-visualização de migração](media/alerts-migration/migration-preview.png "pré-visualização migração")

1. Especifique um ou mais endereços de e-mail para receber notificações de estado de migração. Irá receber um e-mail quando a migração estiver concluída, ou se é necessária qualquer ação do utilizador.

1. Selecione **iniciar migração**. Ler as informações mostradas na caixa de diálogo de confirmação e confirme que está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma subscrição, não será possível editar ou criar regras de alerta clássicas para essa subscrição. Esta restrição garante que nenhuma alteração para as regras do alerta clássicas é perdida durante a migração para as novas regras. Embora não será possível alterar as regras do alerta clássicas, eles ainda irá continuar para executar e para fornecer alertas até terem sido migrados. Após a migração estar concluída para a sua subscrição, não pode mais usar regras de alerta clássicas.

    ![confirmar a migração](media/alerts-migration/migration-confirm.png "confirmar iniciar migração")

1. Quando a migração estiver concluída, ou se é necessária qualquer ação, receberá um e-mail para os endereços que indicou anteriormente. Também periodicamente pode verificar o estado em que a página de destino de migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Por que motivo a minha subscrição está listada como não pronto para a migração?

A ferramenta de migração está a implementar para os clientes em fases. Nas fases iniciais, poderão ser marcadas como a maioria ou todas as subscrições **não está pronto para migração**. 

Quando uma assinatura fica pronta para a migração, o proprietário da subscrição irá receber uma mensagem de e-mail a indicar que a ferramenta está disponível. Fique atento para esta mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode acionar a migração?

Os utilizadores que têm a função de Contribuidor de monitorização atribuída ao nível da subscrição são capazes de acionar a migração. [Saiba mais sobre o controlo de acesso baseado em funções para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo demorará a migração?

Concluir a migração para a maioria das subscrições em menos de uma hora. Pode manter o controle do progresso da migração sobre a página de destino de migração. Durante a migração, ter a garantia de que os alertas são ainda em execução no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se eu executar um problema durante a migração?

Consulte a [guia de resolução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que poderá enfrentará durante a migração. Se é necessária qualquer ação do utilizador para concluir a migração, será notificado com os endereços de e-mail que indicou quando configurar a ferramenta.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar para a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
