---
title: Entenda como funciona o processo de migração automática para os seus alertas clássicos do Azure Monitor
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 9ce3550dcef174a4d970917fabfda3c4fd2a8df4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526556"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Compreenda o processo de migração automática para as suas regras clássicas de alerta

Como [já foi anunciado](monitoring-classic-retirement.md), os alertas clássicos no Azure Monitor são retirados para os utilizadores da nuvem pública, embora ainda em uso limitado para recursos que ainda não suportam os novos alertas. Como parte do processo de aposentação, está disponível [uma ferramenta de migração](alerts-using-migration-tool.md) no portal Azure para que os clientes desencadeiem a migração em si.
Este artigo acompanha-o através do processo de migração automática e ajuda-o a resolver quaisquer problemas que possa encontrar.

  > [!NOTE]
  > Este artigo aplica-se apenas à nuvem pública de Azure. O processo de aposentação dos alertas clássicos do Azure Monitor na nuvem do Governo de Azure e no Azure China 21Vianet será anunciado em data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>O que vai acontecer durante o processo de migração automática?

- A partir de 1 de setembro de **2019,** os clientes não poderão criar novas regras clássicas de alerta, exceto em [determinadas métricas.](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)
- Para as exceções, o cliente pode continuar a criar novas regras clássicas de alerta e usar os seus alertas clássicos até novo anúncio.
- A partir de 1 de setembro de **2019,** a migração de alertas clássicos será desencadeada em lotes para qualquer cliente que tenha alertas clássicos.
- Tal como acontece com a ferramenta de migração voluntária, certas regras clássicas de alerta que não são migraveis serão deixadas como estão. Estas regras clássicas de alerta continuarão a ser apoiadas até novo anúncio. No entanto, quaisquer regras de alerta clássicas inválidas serão eliminadas por não serem funcionais.
Quaisquer regras clássicas de alerta que estejam a monitorizar os recursos-alvo eliminados ou em [métricas que já não sejam suportadas](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) são consideradas inválidas.
- Uma vez iniciada a migração para a sua subscrição, a menos que haja problemas, a migração deve estar completa dentro de uma hora. Os clientes podem monitorizar o estado da migração [na lâmina de migração no Azure Monitor.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- Os proprietários de subscrições receberão um e-mail sobre a conclusão bem sucedida da migração.
- Se houver algum problema durante a migração, os proprietários de subscrição também receberão um e-mail informando-os do mesmo. Os clientes podem usar a lâmina de migração para ver todos os detalhes do problema.
- Caso seja necessária uma ação por parte dos clientes, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de políticas, os clientes terão de resolver tais problemas. Se as questões não forem resolvidas até lá, a migração bem sucedida dos seus alertas clássicos não pode ser garantida.

    > [!NOTE]
    > Se não quiser esperar pelo início do processo de migração automática, ainda pode desencadear a migração voluntariamente utilizando a ferramenta de migração.

## <a name="important-things-to-note"></a>Coisas importantes a notar

O processo de migração converte regras clássicas de alerta para novas regras de alerta equivalentes e cria grupos de ação. Em preparação, esteja atento aos seguintes pontos:

- Os formatos de carga útil de notificação para novas regras de alerta são diferentes dos das regras clássicas de alerta, uma vez que suportam mais funcionalidades. Se tiver aplicações lógicas, runbooks ou webhooks que são desencadeados pela regra clássica de alerta, podem deixar de funcionar como esperado uma vez que a migração esteja completa devido a diferenças nas cargas de notificação. [Saiba como se preparar para a migração.](alerts-prepare-migration.md)

- Algumas regras clássicas de alerta não podem ser migradas usando a ferramenta. [Saiba quais as regras que não podem ser migradas e o que fazer com elas.](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)

    > [!NOTE]
    > O processo de migração não terá impacto na avaliação das suas regras clássicas de alerta. Continuarão a correr e a enviar alertas até serem migrados e as novas regras de alerta entrarem em vigor.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários de subscrição receberão um e-mail notificando-os do problema. Pode utilizar a lâmina de migração no Azure Monitor para ver todos os detalhes do problema.

Consulte o [guia de resolução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que possa enfrentar durante a migração.

  > [!NOTE]
  > Caso seja necessária uma ação por parte dos clientes, como desativar temporariamente um bloqueio de recursos ou alterar uma atribuição de políticas, os clientes terão de resolver tais problemas. Se as questões não forem resolvidas até lá, a migração bem sucedida dos seus alertas clássicos não pode ser garantida.

## <a name="next-steps"></a>Passos seguintes

- [Preparar a migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
