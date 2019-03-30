---
title: Migrar os seus alertas Clássicos no Azure Monitor utilizando a ferramenta de migração voluntária
description: Saiba como utilizar a ferramenta de migração voluntária para migrar as regras do alerta clássicas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632518"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Utilize a ferramenta de migração voluntária para migrar as regras do alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Julho de 2019. A ferramenta de migração para acionar a migração voluntariamente está disponível no portal do Azure e está a implementar para os clientes que utilizam regras de alerta clássicas. Este artigo explica como utilizar a ferramenta de migração para migrar voluntariamente as regras do alerta clássicas antes da migração automática começa em Julho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios dos novos alertas

Alertas clássicos serão substituídas por novos alertas unificada no Azure Monitor. A nova plataforma de alertas tem as seguintes vantagens:

- Alerta sobre uma variedade de métricas multidimensionais para [muitos mais serviços do Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Suporte de alertas de nova métrica [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzir significativamente a sobrecarga de gerenciamento de muitas regras.
- Mecanismo de notificação unificada
  - [Grupos de ação](action-groups.md) é um mecanismo de notificação modular que funciona com todos os tipos de alerta novo (métrica, registo e o registo de atividade)
  - Também poderá tirar partido dos novos mecanismos de notificação, como SMS, voz e conector ITSM
- O [alerta experiência unificada](alerts-overview.md) traz todos os alertas em sinais diferentes (métrica, atividade no registo e registo) num único local

## <a name="before-you-migrate"></a>Antes de migrar

Como parte da migração, as regras de alerta clássicas são convertidas para equivalentes novas regras de alerta e grupos de ação são criados.

- O formato do payload de notificação, bem como as APIs para criar e gerir novas regras de alerta é diferentes das regras de alerta clássicas como eles oferecem suporte a mais recursos. Saiba mais [como preparar a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não não possível migrar com a ferramenta. [Saiba quais regras não são migradas e veja como migrá-los](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Processo de migração não irá afetar a avaliação de suas regras de alerta clássicas. Irão continuar a executar e enviar alertas, até que eles são migrados e novas regras de alerta começar a avaliar.


## <a name="how-to-use-the-migration-tool"></a>Como utilizar a ferramenta de migração

O procedimento seguinte descreve como acionar a migração de suas regras de alerta clássicas no portal do Azure:

1. Na [portal do Azure](https://portal.azure.com), clique em **Monitor**.

2. Clique em **alertas** , em seguida, clique em **gerir regras de alerta** ou **ver alertas clássicos**.

3. Clique em **migre para novas regras** para ir para a página de destino de migração. Esta página mostra uma lista de todas as suas subscrições e o estado de migração para os mesmos.

    ![destino de migração](media/alerts-migration/migration-landing.png "migrar regras")

4. Todas as subscrições que podem ser migradas usando a ferramenta serão marcadas como **pronto para migrar**.

    > [!NOTE]
    > A ferramenta de migração está a implementar em fases para todas as subscrições que utilizam regras de alerta clássicas. Nas primeiras fases da implementação, poderá ver algumas subscrições como não pronto para a migração.

5. Selecione uma ou mais subscrições e clique em **pré-visualização migração**

6. Nesta página, pode ver os detalhes de regras de alerta clássicos que serão migrados para uma subscrição ao mesmo tempo. Também pode **transferir os detalhes de migração para esta subscrição** num formato. csv.

    ![pré-visualização de migração](media/alerts-migration/migration-preview.png "pré-visualização migração")

7. Fornecer um ou mais **endereços de e-mail** para serem notificadas do Estado de migração. Nós enviaremos um e-mail ao concluir a migração ou é necessária uma ação do utilizador.

8. Clique em **iniciar migração**. Ler as informações mostradas na caixa de diálogo de confirmação e confirme se está pronto para iniciar o processo de migração.

    >[!IMPORTANT]
    > Depois de iniciar o processo de migração para uma subscrição, não será capaz de editar/criar regras de alerta clássicas para a subscrição. No entanto, as regras do alerta clássicas continuará em execução e dando-lhe alertas até que eles são migrados. Isso serve para garantir a fidelidade entre as regras de alerta clássicas e as novas regras criadas durante a migração. Quando a migração estiver concluída para a sua subscrição, não pode utilizar regras de alerta clássicas mais.

    ![confirmar a migração](media/alerts-migration/migration-confirm.png "confirmar iniciar migração")

9. Como podemos concluir a migração ou precisar de uma ação do utilizador, receberá um e-mail para os endereços de e-mail fornecidos no passo 8. Também periodicamente pode verificar o estado da página de destino de migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Por que motivo é minha subscrição ou subscrições listadas como não pronto para a migração?**

A ferramenta de migração está a implementar em fases para todos os clientes. Nas primeiras fases, a maioria ou todas as suas subscrições, poderão ser marcadas como **não está pronto para migração**. No entanto, por empresas de médio Abril, todas as subscrições devem estar prontas para migrar.

Quando uma assinatura fica pronta para a migração, os proprietários de subscrições irá receber um e-mail a notificar a disponibilidade da ferramenta. Fique atento para obter esta notificação.

### <a name="who-can-trigger-the-migration"></a>**Quem pode acionar a migração?**

Os utilizadores que tenham a função de Contribuidor de monitorização atribuída ao nível da subscrição serão capazes de acionar a migração. Saiba mais sobre [função de controlo de acesso com base para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Quanto a migração será necessária?**

Para a maioria das subscrições, migração geralmente é concluída com menos de uma hora. Pode manter o controle do progresso da migração da página de destino da migração.  Durante este período, ser garantiu que os alertas são ainda em execução no sistema de alertas clássicos ou novo.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**O que posso fazer se eu executar num problema durante a migração?**

Siga os [guia para ver os passos de remediação para quaisquer problemas que poderá enfrentará durante a migração de resolução de problemas](alerts-understand-migration.md#common-issues-and-remediations). Se é necessária qualquer ação do utilizador para concluir a migração, será notificado sobre os endereços de e-mail que indicou durante a migração.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar para migração](alerts-prepare-migration.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
