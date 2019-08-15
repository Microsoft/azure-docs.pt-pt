---
title: Habilitar o agente de VM na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da central de segurança do Azure **habilitar o agente de VM**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60916298"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Habilitar o agente de VM na central de segurança do Azure
O agente de VM deve ser instalado em VMs (máquinas virtuais) para [habilitar a coleta de dados](security-center-enable-data-collection.md).  A central de segurança do Azure permite que você veja quais VMs exigem o agente de VM e recomendará que você habilite o agente de VM nessas VMs.

O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **folha recomendações**, selecione **habilitar agente de VM**.
   ![Ativar o Agente de VM][1]
2. Isso abre a folha o **agente de VM está ausente ou não**está respondendo. Essa folha lista as VMs que exigem o agente de VM. Siga as instruções na folha para instalar o agente de VM.
   ![O agente de VM está ausente][2]

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
