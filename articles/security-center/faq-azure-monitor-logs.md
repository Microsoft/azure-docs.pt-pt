---
title: Azure Security Center FAQ - perguntas sobre mmas existentes
description: Esta FAQ responde a perguntas para os clientes que já utilizam o Microsoft Monitoring Agent e consideram o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças.
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
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599512"
---
# FAQ para clientes que já usam registos do Monitor Azure<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>O Centro de segurança substituir quaisquer ligações existentes entre as VMs e áreas de trabalho?

Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, o Centro de segurança não substitui a ligação de área de trabalho existente. Em vez disso, o Centro de segurança utiliza a área de trabalho existente. O VM será protegido desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho para o qual está a reportar. 

Uma solução De Security Center está instalada no espaço de trabalho selecionado no ecrã de Recolha de Dados se ainda não estiver presente, e a solução é aplicada apenas aos VMs relevantes. Quando adiciona uma solução, ele será automaticamente implantado por predefinição para todos os agentes Windows e Linux ligados à sua área de trabalho do Log Analytics. [O Targeting de Soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito às suas soluções.

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o Microsoft Monitoring Agent e monitorização de segurança é limitada.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Centro de segurança instalar soluções no meu áreas de trabalho do Log Analytics existentes? Quais são as implicações de faturas?
Quando o Centro de segurança identifica que uma VM já está ligada a uma área de trabalho que criou, o Centro de segurança permite soluções nesta área de trabalho, de acordo com o escalão de preço. As soluções são aplicadas apenas aos VMs Azure relevantes, através da [orientação de soluções,](../operations-management-suite/operations-management-suite-solution-targeting.md)pelo que a faturação permanece a mesma.

- **Free tier** – Security Center instala a solução 'SecurityCenterFree' no espaço de trabalho. Não será cobrado para o free tier.
- **Standard tier** – Security Center instala a solução 'Segurança' no espaço de trabalho.

   ![Soluções em área de trabalho predefinida][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Já tem áreas de trabalho no meu ambiente, posso usá-los para recolher dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, o Centro de segurança utiliza a área de trabalho ligada existente. Uma solução De Centro de Segurança está instalada no espaço de trabalho se ainda não estiver presente, e a solução é aplicada apenas aos VMs relevantes através [de uma solução direcionada](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando o Centro de segurança instala o Microsoft Monitoring Agent nas VMs, ele usa as áreas de trabalho predefinida criadas pelo centro de segurança.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho solução de segurança na minha área de trabalho. Quais são as implicações de faturas?
A solução de segurança e auditoria é utilizada para ativar funcionalidades do escalão Standard do Centro de segurança para VMs do Azure. Se a solução de segurança e auditoria já estiver instalada numa área de trabalho, o Centro de segurança utiliza a solução existente. Não há nenhuma alteração na faturação.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png