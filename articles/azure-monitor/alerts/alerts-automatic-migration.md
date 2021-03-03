---
title: Entenda como funciona o processo de migração automática para os seus alertas clássicos do Azure Monitor
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734864"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Compreenda o processo de migração automática para as suas regras clássicas de alerta

Como [anteriormente anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão reformados para os utilizadores da nuvem pública, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.

[Uma ferramenta de migração](alerts-using-migration-tool.md) está disponível no portal Azure para os clientes desencadearem a migração em si. Este artigo explica o processo de migração automática na nuvem pública, que começará depois de 31 de maio de 2021. Também detalha questões e soluções que poderá encontrar.

## <a name="important-things-to-note"></a>Coisas importantes a notar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Os formatos de carga útil de notificação para novas regras de alerta são diferentes das cargas das regras clássicas de alerta, uma vez que suportam mais funcionalidades. Se tiver uma regra de alerta clássica com apps lógicas, runbooks ou webhooks, podem deixar de funcionar como esperado após a migração, devido a diferenças na carga útil. [Saiba como se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas usando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>O que vai acontecer durante o processo de migração automática na nuvem pública?

- A partir de 31 de maio de 2021, não será possível criar novas regras clássicas de alerta e a migração de alertas clássicos será desencadeada em lotes.
- Quaisquer regras clássicas de alerta que estejam a monitorizar os recursos-alvo eliminados ou em [métricas que já não sejam suportadas](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) são consideradas inválidas.
- As regras clássicas de alerta que são inválidas serão removidas algum tempo depois de 31 de maio de 2021.
- Uma vez iniciada a migração para a sua subscrição, deverá estar concluída dentro de uma hora. Os clientes podem monitorizar o estado da migração [na ferramenta de migração no Azure Monitor.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- Os proprietários de subscrições receberão um e-mail sobre o sucesso ou falha da migração.

    > [!NOTE]
    > Se não quiser esperar pelo início do processo de migração automática, ainda pode desencadear a migração voluntariamente utilizando a ferramenta de migração.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários de subscrição receberão um e-mail notificando-os do problema. Pode utilizar a ferramenta de migração no Azure Monitor para ver todos os detalhes do problema. Consulte o [guia de resolução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que possa enfrentar durante a migração.

  > [!NOTE]
  > Caso seja necessária uma ação por parte dos clientes, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de políticas, os clientes terão de resolver tais problemas. Se as questões não forem resolvidas até lá, a migração bem sucedida dos seus alertas clássicos não pode ser garantida.

## <a name="next-steps"></a>Passos seguintes

- [Preparar a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)