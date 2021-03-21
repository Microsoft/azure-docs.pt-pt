---
title: Migrar regras de alerta do Monitor Azure
description: Aprenda a usar a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fa487bec49ab9faa0f7c3dce752a30e4440fb873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037681"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar as suas regras clássicas de alerta

Como [anteriormente anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão reformados para os utilizadores da nuvem pública, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.

Uma ferramenta de migração está disponível no portal Azure para clientes que usaram regras de alerta clássicas e que querem desencadear a migração por si mesmos. Este artigo explica como usar a ferramenta de migração.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Tanto o formato de carga útil de notificação como as APIs para criar e gerir novas regras de alerta são diferentes das regras clássicas de alerta, porque suportam mais funcionalidades. [Saiba como se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas utilizando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)

    > [!NOTE]
    > O processo de migração não terá impacto na avaliação das suas regras clássicas de alerta. Continuarão a correr e a enviar alertas até serem migrados e as novas regras de alerta entrarem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como utilizar a ferramenta de migração

Para desencadear a migração das suas regras clássicas de alerta no portal Azure, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

1. Selecione **Alertas** e, em seguida, **selecione Gerir as regras de alerta** ou **ver alertas clássicos**.

1. **Selecione Migrar para novas regras** para ir para a página de desembarque da migração. Esta página mostra uma lista de todas as suas subscrições e o seu estado de migração:

    ![A screenshot mostra a página de regras de alerta de Migração.](media/alerts-using-migration-tool/migration-landing.png "Regras migratórias")

    Todas as subscrições que podem ser migradas utilizando a ferramenta estão marcadas como **Ready to migrar**.

    > [!NOTE]
    > A ferramenta de migração está a ser lançada por fases a todas as subscrições que utilizam regras clássicas de alerta. Nas fases iniciais do lançamento, poderá ver algumas subscrições marcadas como não prontas para a migração.

1. Selecione uma ou mais subscrições e, em seguida, selecione **a migração de pré-visualização**.

    A página resultante mostra os detalhes das regras clássicas de alerta que serão migradas para uma subscrição de cada vez. Também pode selecionar **Baixar os detalhes da migração para esta subscrição** para obter os detalhes num formato CSV.

    ![O Screenshot mostra a página de regras de alerta migração com um link para Baixar detalhes da migração para esta subscrição e pode especificar e-mail para notificação de migração.](media/alerts-using-migration-tool/migration-preview.png "Pré-visualização da migração")

1. Especifique um ou mais endereços de e-mail para ser notificado do estado de migração. Receberá e-mail quando a migração estiver completa ou se for necessária alguma ação por si.

1. Selecione **Migração iniciar**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme que está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma subscrição, não poderá editar ou criar regras clássicas de alerta para essa subscrição. Esta restrição garante que não se percam alterações às suas regras clássicas de alerta durante a migração para as novas regras. Embora não consigas alterar as tuas regras clássicas de alerta, elas continuarão a correr e a fornecer alertas até serem migradas. Depois de a migração estar completa para a sua subscrição, já não pode utilizar regras clássicas de alerta.

    ![O Screenshot mostra um pedido de confirmação para a sua migração, incluindo informações importantes com links para aprender mais antes de prosseguir.](media/alerts-using-migration-tool/migration-confirm.png "Confirme a migração inicial")

1. Quando a migração estiver completa, ou se for necessária uma ação de si, receberá um e-mail nos endereços que forneceu anteriormente. Também pode verificar periodicamente o estado na página de desembarque da migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Porque é que a minha assinatura não está pronta para a migração?

A ferramenta de migração está a ser lançada para os clientes por fases. Nas fases iniciais, a maioria ou a totalidade das suas subscrições podem ser marcadas como **Não prontas para a migração.** 

Quando uma subscrição estiver pronta para a migração, o proprietário da subscrição receberá uma mensagem de correio eletrónico indicando que a ferramenta está disponível. Fique de olho nesta mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Os utilizadores que tenham a função de Colaborador de Monitorização que lhes é atribuída ao nível da subscrição podem desencadear a migração. [Saiba mais sobre o controlo de acesso baseado em funções da Azure para o processo de migração.](alerts-understand-migration.md#who-can-trigger-the-migration)

### <a name="how-long-will-the-migration-take"></a>Quanto tempo vai demorar a migração?

A migração é concluída para a maioria das subscrições em menos de uma hora. Pode acompanhar o progresso da migração na página de desembarque da migração. Durante a migração, tenha certeza de que os seus alertas ainda estão em funcionamento no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se me der a um problema durante a migração?

Consulte o [guia de resolução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que possa enfrentar durante a migração. Se for necessária alguma ação sua para completar a migração, será notificado nos endereços de e-mail que forneceu quando configurar a ferramenta.

## <a name="next-steps"></a>Passos seguintes

- [Preparar a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)