---
title: Integre os logs dos recursos do Azure aos seus sistemas SIEM | Microsoft Docs
description: Saiba mais sobre a integração de log do Azure, seus principais recursos e como ele funciona.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727599"
---
# <a name="introduction-to-azure-log-integration"></a>Introdução à integração de log do Azure

>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

A integração de log do Azure foi disponibilizada para simplificar a tarefa de integração dos logs do Azure com o sistema SIEM (gerenciamento de eventos e informações de segurança) local.

 O método recomendado para a integração dos logs do Azure é usar os conectores do fornecedor do SIEM. Azure Monitor fornece a capacidade de transmitir os logs para os hubs de eventos e os fornecedores de SIEM podem gravar conectores para integrar ainda mais os logs do hub de eventos ao SIEM.  Para obter uma descrição de como isso funciona, siga as instruções em [monitorar monitoramento de fluxo para hubs de eventos de dados](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). O artigo também lista os SIEMs para os quais conectores diretos do Azure já estão disponíveis.  

> [!IMPORTANT]
> Se seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores de SIEM inclui essa opção em sua solução. Usar o conector do fornecedor do SIEM é sempre a alternativa preferida.

A documentação sobre o recurso de integração de log do Azure ainda está sendo mantida até que o recurso seja preterido.

Leia mais para saber mais sobre o recurso integração de logs do Azure:

A integração de log do Azure coleta eventos do Windows de logs do Windows Visualizador de Eventos, [logs de atividades do Azure](/azure/azure-monitor/platform/activity-logs-overview), [alertas da central de segurança do Azure](/azure/security-center/security-center-intro)e [logs de diagnóstico do Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) de recursos do Azure. A integração ajuda sua solução SIEM a fornecer um painel unificado para todos os seus ativos, seja localmente ou na nuvem. Você pode usar um painel para receber, agregar, correlacionar e analisar alertas de eventos de segurança.

> [!NOTE]
> Atualmente, a integração de log do Azure dá suporte apenas a nuvens comerciais do Azure e do Azure governamental. Não há suporte para outras nuvens.

![O processo de integração do log do Azure](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?

O Azure produz um log extensivo para cada serviço do Azure. Os logs representam três tipos de log:

* **Logs de controle/gerenciamento**: Forneça visibilidade para as operações de criação, atualização e exclusão do [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) . Um log de atividades do Azure é um exemplo desse tipo de log.
* **Logs do plano de dados**: Forneça visibilidade para eventos que são gerados quando você usa um recurso do Azure. Um exemplo desse tipo de log são os canais de **sistema**, **segurança**e **aplicativo** do Windows visualizador de eventos em uma máquina virtual do Windows. Outro exemplo é Diagnóstico do Azure log, que você configura por meio de Azure Monitor.
* **Eventos processados**: Forneça eventos analisados e informações de alerta que são processados para você. Um exemplo desse tipo de evento são os alertas da central de segurança do Azure. A central de segurança do Azure processa e analisa sua assinatura para fornecer alertas relevantes para sua postura de segurança atual.

A integração de log do Azure dá suporte a ArcSight, QRadar e Splunk. Verifique com seu fornecedor SIEM para avaliar se o fornecedor tem um conector nativo. Não use a integração de log do Azure se um conector nativo estiver disponível.

Se nenhuma outra opção estiver disponível, considere usar a integração de log do Azure. A tabela a seguir inclui nossas recomendações:

|SIEM | O cliente já usa o integrador de logs do Azure | O cliente está investigando opções de integração do SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Comece a migrar para o [complemento Azure monitor para Splunk](https://splunkbase.splunk.com/app/3534/). | Use o [conector do Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migre ou comece a usar o conector do QRadar que está documentado na última seção de [transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Use o conector do QRadar que está documentado na última seção de [transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Continue a usar o integrador de logs do Azure até que um conector esteja disponível e migre para a solução baseada em conector.  | Considere o uso de logs de Azure Monitor como uma alternativa. Não integre a integração de log do Azure, a menos que você esteja disposto a passar pelo processo de migração quando o conector se tornar disponível. |

> [!NOTE]
> Embora a integração de log do Azure seja uma solução gratuita, há custos de armazenamento do Azure associados ao armazenamento de informações do arquivo de log.

Se precisar de assistência, você poderá criar uma [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). Para o serviço, selecione **integração de log**.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresentou a integração de log do Azure. Para saber mais sobre a integração de log do Azure e os tipos de logs com suporte, consulte os seguintes artigos:

* [Introdução à integração de log do Azure](azure-log-integration-get-started.md). Este tutorial orienta você durante a instalação da integração de logs do Azure. Ele também descreve como integrar logs do armazenamento do Windows Diagnóstico do Azure (WAD), logs de atividades do Azure, alertas da central de segurança do Azure e Azure Active Directory logs de auditoria.
* [Perguntas frequentes sobre a integração de logs do Azure](azure-log-integration-faq.md). Estas perguntas frequentes respondem a perguntas comuns sobre a integração de logs do Azure.
* Saiba mais sobre como [transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

