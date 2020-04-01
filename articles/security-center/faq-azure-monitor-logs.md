---
title: Azure Security Center FAQ - perguntas sobre os agentes de Log Analytics existentes
description: Esta FAQ responde a perguntas para os clientes que já utilizam o agente Log Analytics e que consideram o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436155"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>FAQ para clientes que já usam registos do Monitor Azure<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>O Centro de Segurança sobrepor-se a alguma ligação existente entre VMs e espaços de trabalho?

Se um VM já tiver o agente Log Analytics instalado como uma extensão Azure, o Security Center não sobrepor-se à ligação do espaço de trabalho existente. Em vez disso, o Centro de Segurança usa o espaço de trabalho existente. O VM será protegido desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho para o qual está a reportar. 

Uma solução De Security Center está instalada no espaço de trabalho selecionado no ecrã de Recolha de Dados se ainda não estiver presente, e a solução é aplicada apenas aos VMs relevantes. Quando adiciona uma solução, é automaticamente implementada por padrão a todos os agentes Windows e Linux ligados ao seu espaço de trabalho Log Analytics. [O Targeting de Soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito às suas soluções.

> [!TIP]
> Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Security Center não instala o agente Log Analytics e a monitorização de segurança é limitada.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>O Security Center instala soluções nos meus espaços de trabalho de Log Analytics existentes? Quais são as implicações da faturação?
Quando o Security Center identifica que um VM já está ligado a um espaço de trabalho que criou, o Security Center permite soluções neste espaço de trabalho de acordo com o seu nível de preços. As soluções são aplicadas apenas aos VMs Azure relevantes, através da [orientação de soluções,](../operations-management-suite/operations-management-suite-solution-targeting.md)pelo que a faturação permanece a mesma.

- **Free tier** – Security Center instala a solução 'SecurityCenterFree' no espaço de trabalho. Não será cobrado para o free tier.
- **Standard tier** – Security Center instala a solução 'Segurança' no espaço de trabalho.

   ![Soluções sobre o espaço de trabalho padrão](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Já tenho espaços de trabalho no meu ambiente, posso usá-los para recolher dados de segurança?
Se um VM já tiver o agente Log Analytics instalado como uma extensão Azure, o Security Center utiliza o espaço de trabalho conectado existente. Uma solução De Centro de Segurança está instalada no espaço de trabalho se ainda não estiver presente, e a solução é aplicada apenas aos VMs relevantes através [de uma solução direcionada](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando o Security Center instala o agente Log Analytics em VMs, utiliza o espaço de trabalho predefinido criado pelo Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho solução de segurança nos meus espaços de trabalho. Quais são as implicações da faturação?
A solução de auditoria & de segurança é utilizada para permitir funcionalidades de nível padrão do Security Center para VMs Azure. Se a solução de Auditoria & de Segurança já estiver instalada num espaço de trabalho, o Security Center utiliza a solução existente. Não há alteração na faturação.