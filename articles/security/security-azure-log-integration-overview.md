---
title: Integrar registos de recursos do Azure com os seus sistemas SIEM | Documentos da Microsoft
description: Saiba mais sobre o Azure Log Integration, suas principais capacidades e como ele funciona.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: c644d81b647fc1151b10a94fb636e3eeaba1ce5c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245051"
---
# <a name="introduction-to-azure-log-integration"></a>Introdução à integração de registos do Azure

>[!IMPORTANT]
> A funcionalidade de integração de registo do Azure vai ser preterida até 06/01/2019. Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integração de registos do Azure foi disponibilizada simplificar a tarefa de integração do Azure registos com o seu sistema de informações de segurança e gestão de eventos (SIEM) no local.

 O método recomendado para integração de registos do Azure está a utilizar os conectores do seu fornecedor SIEM. O Azure Monitor proporciona a capacidade de transmitir os registos para os hubs de eventos e fornecedores SIEM, podem escrever conectores para ainda mais integrar registos do hub de eventos para o SIEM.  Para obter uma descrição de como isso funciona, siga as instruções em [monitorização para os dados dos hubs de eventos de fluxo de Monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). O artigo também apresenta uma lista de SIEMs para o qual diretos conectores do Azure já estão disponíveis.  

> [!IMPORTANT]
> Se o seu interesse principal está a recolher registos de máquina virtual, a maioria dos fornecedores SIEM incluir esta opção nas suas soluções. Usando o SIEM conector do fornecedor é sempre a alternativa preferida.

A documentação sobre a funcionalidade de integração de registos do Azure ainda está a ser mantida até que a funcionalidade foi preterida.

Leia mais para saber mais sobre a funcionalidade de integração de registos do Azure:

Integração de registos do Azure recolhe eventos do Windows a partir de registos do Visualizador de eventos do Windows, [registos de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md), [alertas do Centro de segurança do Azure](../security-center/security-center-intro.md), e [registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md) do Recursos do Azure. A integração ajuda a fornecer um dashboard unificado para todos os seus ativos, se sua solução de SIEM no local ou na cloud. Pode utilizar um dashboard para receber, Agregar, correlacionar e analisar os alertas para eventos de segurança.

> [!NOTE]
> Atualmente, o Azure Log Integration suporta apenas o Azure comercial e a nuvens do Azure Government. Não são suportadas outras clouds.

![O processo de integração de registos do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Registos de quais posso integrar?

Azure produz o Registro em log extenso para cada serviço do Azure. Os registos representam três tipos de registo:

* **Registos de controlo/gestão**: Proporcionar visibilidade para o [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) operações CREATE, UPDATE e DELETE. Um registo de atividades do Azure é um exemplo desse tipo de registo.
* **Registos do plano de dados**: Proporcionar visibilidade para eventos que são gerados quando utiliza um recurso do Azure. Um exemplo desse tipo de registo é o Visualizador de eventos de Windows **System**, **Security**, e **aplicação** canais numa máquina virtual do Windows. Outro exemplo é o registo de diagnóstico do Azure, que configura através do Azure Monitor.
* **Processar eventos**: Fornece eventos analisado e informações de alertas que são processados por si. Um exemplo deste tipo de evento é alertas do Centro de segurança do Azure. Centro de segurança do Azure processa e analisa a sua subscrição para fornecer alertas que são relevantes para a sua postura de segurança atual.

Integração de registos do Azure suporta Splunk, QRadar e cso. Verifique junto do fornecedor do SIEM para avaliar se o fornecedor tem um conector nativo. Não utilize o Azure Log Integration se está disponível um conector nativo.

Se não existem outras opções estão disponíveis, considere utilizar o Azure Log Integration. A tabela seguinte inclui as nossas recomendações:

|SIEM | O cliente já utiliza o integrador de registos do Azure | Cliente está a investigar as opções de integração de SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Começar a migrar para o [suplemento do Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/). | Utilize o [Splunk conector](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrar para ou a começar a utilizar o conector de QRadar documentada na última seção [Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | Utilizar o conector de QRadar documentada na última seção [Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continuar a utilizar o integrador de registos do Azure até que um conector esteja disponível e, em seguida, migrar para a solução baseada no conector.  | Considere a utilização de registos do Azure Monitor como alternativa. Não incluir a integração de registos do Azure, a menos que está disposto a passar pelo processo de migração quando o conector for disponibilizado. |

> [!NOTE]
> Embora a integração de registos do Azure é uma solução gratuita, existem custos de armazenamento do Azure associados com o armazenamento de informações de ficheiros de registo.

Se precisar de assistência, pode criar uma [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **integração de registos**.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresentou-o para a integração de registos do Azure. Para saber mais sobre a integração de registos do Azure e os tipos de registos que são suportados, veja os artigos seguintes:

* [Introdução ao Azure Log Integration](security-azure-log-integration-get-started.md). Este tutorial orienta-o através da instalação do Azure Log Integration. Ele também descreve como integrar registos do armazenamento do Windows Azure Diagnostics (WAD), os registos de atividades do Azure, alertas do Centro de segurança do Azure e os registos de auditoria do Azure Active Directory.
* [Integração de registos do Azure perguntas mais frequentes (FAQ)](security-azure-log-integration-faq.md). Encontre respostas para perguntas comuns sobre a integração de registos do Azure.
* Saiba mais sobre como [transmitir dados para um hub de eventos para consumo por uma ferramenta externa de monitorização do Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
