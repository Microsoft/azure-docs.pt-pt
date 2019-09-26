---
title: Alta disponibilidade e confiabilidade-Agendador do Azure
description: Saiba mais sobre a alta disponibilidade e confiabilidade no Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 3cc15d173ad735d77505f636bd230e0876371271
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300939"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Alta disponibilidade e confiabilidade para o Agendador do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 

O Agendador do Azure fornece [alta disponibilidade](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) e confiabilidade para seus trabalhos. Para obter mais informações, consulte [SLA para o Agendador](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Elevada disponibilidade

O Agendador do Azure é [altamente disponível] e usa a implantação de serviço com redundância geográfica e a replicação de trabalho regional geográfica.

### <a name="geo-redundant-service-deployment"></a>Implantação de serviço com redundância geográfica

O Agendador do Azure está disponível no portal do Azure em quase [todas as regiões geográficas com suporte do Azure hoje](https://azure.microsoft.com/global-infrastructure/regions/#services). Portanto, se um datacenter do Azure em uma região hospedada ficar indisponível, você ainda poderá usar o Agendador do Azure porque os recursos de failover do serviço disponibilizam o Agendador de outro datacenter.

### <a name="geo-regional-job-replication"></a>Replicação geográfica regional de trabalho

Seus próprios trabalhos no Agendador do Azure são replicados nas regiões do Azure. Portanto, se uma região tiver uma interrupção, o Agendador do Azure fará failover e garantirá que seu trabalho seja executado de outro datacenter na região geográfica emparelhada.

Por exemplo, se você criar um trabalho no Sul EUA Central, o Agendador do Azure replicará automaticamente esse trabalho no norte EUA Central. Se ocorrer uma falha no EUA Central do Sul, o Agendador do Azure executará o trabalho no norte EUA Central. 

![Replicação geográfica regional de trabalho](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

O Agendador do Azure também garante que seus dados permaneçam dentro da mesma região geográfica, mas mais ampla, caso ocorra uma falha no Azure. Portanto, você não precisa duplicar seus trabalhos quando quiser a alta disponibilidade. O Agendador do Azure fornece automaticamente alta disponibilidade para seus trabalhos.

## <a name="reliability"></a>Fiabilidade

O Agendador do Azure garante sua própria alta disponibilidade, mas usa uma abordagem diferente para os trabalhos criados pelo usuário. Por exemplo, suponha que seu trabalho invoque um ponto de extremidade HTTP que não está disponível. O Agendador do Azure ainda tenta executar seu trabalho com êxito fornecendo maneiras alternativas de lidar com falhas: 

* Configure as políticas de repetição.
* Configurar pontos de extremidade alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Repetir políticas

O Agendador do Azure permite que você configure políticas de repetição. Se um trabalho falhar, por padrão, o Agendador tentará novamente o trabalho quatro vezes em intervalos de 30 segundos. Você pode tornar essa política de repetição mais agressiva, como 10 vezes em intervalos de 30 segundos, ou menos agressivas, como duas vezes em intervalos diários.

Por exemplo, suponha que você crie um trabalho semanal que chama um ponto de extremidade HTTP. Se o ponto de extremidade HTTP ficar indisponível por algumas horas quando o trabalho for executado, talvez você não queira aguardar outra semana para que o trabalho seja executado novamente, o que acontece porque a política de repetição padrão não funcionará nesse caso. Portanto, talvez você queira alterar a política de repetição padrão para que as novas tentativas ocorram, por exemplo, a cada três horas, em vez de a cada 30 segundos. 

Para saber como configurar uma política de repetição, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Pontos de extremidade alternativos

Se o trabalho do Agendador do Azure chama um ponto de extremidade que está inacessível, mesmo depois de seguir a política de repetição, o Agendador volta para um ponto de extremidade alternativo que pode lidar com esses erros. Portanto, se você configurar esse ponto de extremidade, o Agendador chamará esse ponto de extremidade, o que torna seus próprios trabalhos altamente disponíveis quando ocorrerem falhas.

Por exemplo, este diagrama mostra como o Agendador segue a política de repetição ao chamar um serviço Web em Nova York. Se as tentativas falharem, o Agendador verificará se há um ponto de extremidade alternativo. Se o ponto de extremidade existir, o Agendador começará a enviar solicitações para o ponto de extremidade alternativo. A mesma política de repetição se aplica à ação original e à ação alternativa.

![Comportamento do Agendador com política de repetição e ponto de extremidade alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

O tipo de ação para a ação alternativa pode ser diferente da ação original. Por exemplo, embora a ação original chame um ponto de extremidade HTTP, a ação alternativa pode registrar erros usando uma fila de armazenamento, uma fila do barramento de serviço ou uma ação de tópico do barramento de serviço.

Para saber como configurar um ponto de extremidade alternativo, consulte [ErrorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md)
* [Limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)
