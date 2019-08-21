---
title: Entenda como funciona o processo de migração automática para seus alertas Azure Monitor clássicos
description: Saiba como funciona o processo de migração automática.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: fbc024b6ae6e3ebfed6f64067158352e8201aef5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642568"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Entender o processo de migração automática para suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor estão sendo desativados em setembro de 2019 (foi originalmente 2019 de julho). Como parte do processo de aposentadoria, [uma ferramenta de migração](alerts-using-migration-tool.md) está disponível no portal do Azure para que os clientes disparem a migração em si. Se você não tiver usado a ferramenta de migração até 31 de agosto de 2019, o Azure Monitor iniciará o processo de migração automática de seus alertas clássicos a partir de 1º de setembro de 2019.
Este artigo orienta você pelo processo de migração automática e ajuda você a resolver quaisquer problemas que você possa encontrar.

  > [!NOTE]
  > Este artigo se aplica somente à nuvem pública do Azure. O processo de aposentadoria para alertas clássicos Azure Monitor na nuvem do Azure governamental e no Azure China 21Vianet será anunciado em data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>O que acontecerá durante o processo de migração automática?

- A partir de **1º de setembro de 2019**, os clientes não poderão criar novas regras de alerta clássicas, exceto em [determinadas métricas](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Para as exceções, o cliente pode continuar a criar novas regras de alerta clássicas e usar seus alertas clássicos até junho de 2020.
- A partir de **1º de setembro de 2019**, a migração de alertas clássicos será disparada em lotes para qualquer cliente que tenha alertas clássicos.
- Assim como acontece com a ferramenta de migração voluntária, certas regras de alerta clássicas que não são migrada serão deixadas como estão. Essas regras de alerta clássicas continuarão a ter suporte até 2020 de junho. No entanto, qualquer regra de alerta clássica inválida será excluída, pois elas não são funcionais.
Todas as regras de alerta clássicas que estão monitorando recursos de destino excluídos ou em [métricas que não são mais](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) suportadas são consideradas inválidas.
- Depois que a migração para sua assinatura for iniciada, a menos que haja problemas, a migração deve ser concluída em uma hora. Os clientes podem monitorar o status da migração na [folha de migração no Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Os proprietários da assinatura receberão um email sobre a conclusão bem-sucedida da migração.
- Se houver problemas durante a migração, os proprietários da assinatura também receberão um email informando que eles são os mesmos. Os clientes podem usar a folha de migração para ver os detalhes completos do problema.
- Caso uma ação seja necessária para o cliente, como desabilitar temporariamente um bloqueio de recurso ou alterar uma atribuição de política, os clientes precisarão resolver quaisquer problemas até 31 de outubro de 2019. Se os problemas não forem resolvidos por enquanto, a migração bem-sucedida de seus alertas clássicos não poderá ser garantida.

    > [!NOTE]
    > Se você não quiser aguardar a inicialização do processo de migração automática, ainda poderá disparar a migração voluntariamente usando a ferramenta de migração.

## <a name="important-things-to-note"></a>Coisas importantes a observar

O processo de migração converte as regras de alerta clássicas em regras de alerta novas e equivalentes e cria grupos de ações. Em preparação, lembre-se dos seguintes pontos:

- Os formatos de carga de notificação para novas regras de alerta são diferentes daqueles das regras de alerta clássicas, pois oferecem suporte a mais recursos. Se você tiver aplicativos lógicos, runbooks ou WebHooks disparados pela regra de alerta clássica, eles poderão parar de funcionar conforme o esperado quando a migração for concluída devido a diferenças nas cargas de notificação. [Saiba como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras de alerta clássicas. Eles continuarão a executar e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários da assinatura receberão um email notificando-os sobre o problema. Você pode usar a folha migração no Azure Monitor para ver os detalhes completos do problema.

Consulte o [Guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração.

  > [!NOTE]
  > Caso uma ação seja necessária para o cliente, como desabilitar temporariamente um bloqueio de recurso ou alterar uma atribuição de política, os clientes precisarão resolver quaisquer problemas até 31 de outubro de 2019. Se os problemas não forem resolvidos por enquanto, a migração bem-sucedida de seus alertas clássicos não poderá ser garantida.

## <a name="next-steps"></a>Passos seguintes

- [Preparar para a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
