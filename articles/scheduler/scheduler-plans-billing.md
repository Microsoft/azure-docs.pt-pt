---
title: Planos e cobrança – Agendador do Azure
description: Saiba mais sobre planos e cobrança do Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b63367ab9686eee66bf3f00dddc2e2efe4cb941
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300874"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planos e cobrança do Agendador do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

## <a name="job-collection-plans"></a>Planos de coleção de trabalhos

No Agendador do Azure, uma coleção de trabalhos contém um número específico de trabalhos. A coleção de trabalhos é a entidade Faturável e vem nos planos standard, P10 Premium e P20 Premium, que são descritos aqui: 

| Plano de coleção de trabalhos | Máximo de trabalhos por coleção | Recorrência máxima | Máximo de coleções de trabalhos por assinatura | Limites | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 trabalhos por coleção | Um por minuto. Não é possível executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 100 coleções de trabalhos padrão. | Acesso ao conjunto de recursos completos do Agendador | 
| **P10 Premium** | 50 trabalhos por coleção | Um por minuto. Não é possível executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 10.000 P10 de coleta de trabalhos Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">entre em contato conosco</a>. | Acesso ao conjunto de recursos completos do Agendador |
| **P20 Premium** | 1000 trabalhos por coleção | Um por minuto. Não é possível executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 5.000 P20 de coleta de trabalhos Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">entre em contato conosco</a>. | Acesso ao conjunto de recursos completos do Agendador |
|||||| 

## <a name="pricing"></a>Preços

Para obter detalhes de preços, consulte [preços do Agendador](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Planos de upgrade ou downgrade

A qualquer momento, você pode atualizar ou fazer downgrade de um plano de coleção de trabalhos nos planos standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Status ativo e cobrança

As coleções de trabalho estão sempre ativas, a menos que toda a sua assinatura do Azure entre em um estado temporário desabilitado devido a problemas de cobrança. E, embora você possa desabilitar todos os trabalhos em uma coleção de trabalhos por meio de uma única operação, essa ação não altera o status de cobrança da coleção de trabalhos, portanto, a coleção de trabalhos *ainda* é cobrada. As coleções de trabalhos vazias são consideradas ativas e são cobradas.

Para garantir que uma coleção de trabalhos não seja cobrada, você deve excluir a coleção de trabalhos.

## <a name="standard-billable-units"></a>Unidades Faturáveis padrão

Uma unidade Faturável padrão pode ter até 10 coleções de trabalhos padrão. Como uma coleção de trabalhos padrão pode ter até 50 trabalhos por coleção, uma unidade de cobrança padrão permite que sua assinatura do Azure tenha até 500 trabalhos ou até quase 22 *milhões* de execuções de trabalho por mês. Esta lista explica como você é cobrado com base em vários números de coleções de trabalhos padrão:

* Se você tiver entre 1 e 10 coleções de trabalhos padrão, será cobrado por uma unidade de cobrança padrão. 

* Se você tiver entre 11 e 20 coleções de trabalhos padrão, será cobrado por duas unidades de cobrança padrão. 

* Se você tiver entre 21 e 30 coleções de trabalhos padrão, será cobrado por três unidades de cobrança padrão e assim por diante.

## <a name="p10-premium-billable-units"></a>Unidades faturáveis P10 Premium

Uma unidade Faturável P10 Premium pode ter até 10.000 P10 de coleta de trabalhos Premium. Como uma coleção de trabalhos P10 Premium pode ter até 50 trabalhos por coleção, uma unidade de cobrança Premium do P10 permite que sua assinatura do Azure tenha até 500.000 trabalhos ou até quase 22 *bilhões* de execuções de trabalho por mês. 

As coleções de trabalhos do P10 Premium fornecem os mesmos recursos que as coleções de trabalho padrão, mas oferecem uma quebra de preço para aplicativos que exigem muitas coleções de trabalho e fornecem mais escalabilidade. Esta lista explica como você é cobrado com base em vários números de coleções de trabalhos do P10 Premium:

* Se você tiver entre 1 e 10.000 P10 de coleta de trabalhos Premium, você será cobrado por uma unidade de cobrança Premium do P10. 

* Se você tiver entre 10.001 e 20.000 P10 de coleta de trabalhos Premium, você será cobrado por duas unidades de cobrança Premium do P10 e assim por diante.

## <a name="p20-premium-billable-units"></a>Unidades faturáveis P20 Premium

Uma unidade Faturável P20 Premium pode ter até 5.000 P20 de coleta de trabalhos Premium. Como uma coleção de trabalhos P20 Premium pode ter até 1.000 trabalhos por coleção de trabalhos, uma unidade de cobrança Premium do P20 permite que sua assinatura do Azure tenha até 5 milhões trabalhos ou até quase 220 *bilhões* de execuções de trabalho por mês.

As coleções de trabalhos do P20 Premium fornecem os mesmos recursos que as coleções de trabalhos do P10 Premium, mas também dão suporte a um número maior de trabalhos por coleção e um número total maior de trabalhos gerais do que o P10 Premium, fornecendo mais escalabilidade.

## <a name="plan-comparison"></a>Comparação de planos

* Se você tiver mais de 100 coleções de trabalhos padrão (10 unidades de cobrança padrão), poderá obter um melhor acordo com todas as coleções de trabalhos em um plano Premium.

* Se você tiver uma coleção de trabalhos padrão e uma coleção de trabalhos Premium, será cobrado por uma unidade de cobrança padrão e uma unidade *de* cobrança Premium.

  O serviço de Agendador cobra com base no número de coleções de trabalhos ativas que são standard ou Premium.

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)