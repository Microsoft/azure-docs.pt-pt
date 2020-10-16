---
title: Azure Security Center FAQ - perguntas sobre os agentes existentes do Log Analytics
description: Esta FAQ responde a perguntas para clientes que já usam o agente Log Analytics e consideram o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças.
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
ms.openlocfilehash: c4af0e8eda818fcb57ea9e050b760c3754c46e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613650"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>FAQ para clientes que já utilizam registos do Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>O Centro de Segurança sobrepõe-se a quaisquer ligações existentes entre VMs e espaços de trabalho?

Se um VM já tiver o agente Log Analytics instalado como uma extensão Azure, o Security Center não substitui a ligação existente no espaço de trabalho. Em vez disso, o Centro de Segurança utiliza o espaço de trabalho existente. O VM será protegido desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho a que está a reportar. 

Uma solução do Centro de Segurança é instalada no espaço de trabalho selecionado no ecrã de Recolha de Dados se ainda não estiver presente, e a solução é aplicada apenas aos VM relevantes. Quando adiciona uma solução, é automaticamente implementada por padrão a todos os agentes Windows e Linux ligados ao seu espaço de trabalho Log Analytics. [O Targeting de soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito às suas soluções.

> [!TIP]
> Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Centro de Segurança não instala o agente Log Analytics e a monitorização de segurança é limitada.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>O Security Center instala soluções nos meus espaços de trabalho existentes do Log Analytics? Quais são as implicações da faturação?
Quando o Security Center identifica que um VM já está ligado a um espaço de trabalho que criou, o Security Center permite soluções neste espaço de trabalho de acordo com a configuração dos preços. As soluções aplicam-se apenas aos VMs Azure relevantes, através de [uma solução-alvo,](../operations-management-suite/operations-management-suite-solution-targeting.md)pelo que a faturação permanece a mesma.

- **Azure Defender off** – O Security Center instala a solução 'SecurityCenterFree' no espaço de trabalho. Não será cobrado.
- **O Azure Defender on** – Security Center instala a solução 'Security' no espaço de trabalho.

   ![Soluções no espaço de trabalho predefinido](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Já tenho espaços de trabalho no meu ambiente, posso usá-los para recolher dados de segurança?
Se um VM já tiver o agente Log Analytics instalado como uma extensão Azure, o Security Center utiliza o espaço de trabalho ligado existente. Uma solução do Centro de Segurança é instalada no espaço de trabalho se ainda não estiver presente, e a solução é aplicada apenas aos VM relevantes através de [uma solução de orientação.](../operations-management-suite/operations-management-suite-solution-targeting.md)

Quando o Security Center instala o agente Log Analytics em VMs, utiliza o espaço de trabalho predefinido criado pelo Security Center se o Centro de Segurança não estiver apontado para um espaço de trabalho existente.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho uma solução de segurança nos meus espaços de trabalho. Quais são as implicações da faturação?
A solução de Auditoria & de Segurança é utilizada para permitir o **Azure Defender para servidores**. Se a solução de Auditoria & de Segurança já estiver instalada num espaço de trabalho, o Centro de Segurança utiliza a solução existente. Não há mudança na faturação.
