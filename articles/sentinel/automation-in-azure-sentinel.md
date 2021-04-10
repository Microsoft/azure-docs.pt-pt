---
title: Introdução à automação em Azure Sentinel | Microsoft Docs
description: Este artigo introduz as capacidades de Orquestração de Segurança, Automação e Resposta (SOAR) do Azure Sentinel e descreve os seus componentes SOAR - regras de automação e playbooks.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609803"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Orquestração de Segurança, Automação e Resposta (SOAR) em Azure Sentinel

Este artigo descreve as capacidades de Orquestração de Segurança, Automação e Resposta (SOAR) do Azure Sentinel, e mostra como o uso de regras de automação e playbooks em resposta a ameaças de segurança aumenta a eficácia do seu SOC e poupa-lhe tempo e recursos.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel como solução SOAR

### <a name="the-problem"></a>O problema

As equipas do SIEM/SOC são normalmente inundadas com alertas de segurança e incidentes regularmente, em volumes tão grandes que o pessoal disponível está sobrecarregado. Isto resulta demasiadas vezes em situações em que muitos alertas são ignorados e muitos incidentes não são investigados, deixando a organização vulnerável a ataques que passam despercebidos.

### <a name="the-solution"></a>A solução

Azure Sentinel, além de ser um sistema de Gestão de Informação de Segurança e Eventos (SIEM), é também uma plataforma de Orquestração de Segurança, Automação e Resposta (SOAR). Um dos seus principais objetivos é automatizar quaisquer tarefas recorrentes e previsíveis de enriquecimento, resposta e reparação que sejam da responsabilidade do seu Centro de Operações de Segurança e pessoal (SOC/SecOps), libertando tempo e recursos para uma investigação mais aprofundada e caçando ameaças avançadas. A automatização assume algumas formas diferentes no Azure Sentinel, desde regras de automação que gerem centralmente a automatização do tratamento e resposta de incidentes, até aos playbooks que executam sequências pré-determinadas de ações para fornecer automatização avançada poderosa e flexível às suas tarefas de resposta a ameaças.

## <a name="automation-rules"></a>Regras de automação

As regras de automação são um novo conceito no Azure Sentinel. Esta funcionalidade permite aos utilizadores gerir centralmente a automatização do tratamento de incidentes. Além de permitir atribuir playbooks a incidentes (não apenas a alertas como antes), as regras de automação também permitem automatizar respostas para várias regras de análise ao mesmo tempo, marcar, atribuir ou fechar incidentes sem necessidade de playbooks, e controlar a ordem de ações que são executadas. As regras de automação irão simplificar o uso da automatização no Azure Sentinel e permitir-lhe-ão simplificar fluxos de trabalho complexos para os seus processos de orquestração de incidentes.

Saiba mais com esta [explicação completa das regras de automação.](automate-incident-handling-with-automation-rules.md)

> [!IMPORTANT]
>
> - A funcionalidade **de regras de automatização** encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="playbooks"></a>Manuais de procedimentos

Um livro de jogadas é uma coleção de ações de resposta e remediação e lógica que pode ser executado a partir de Azure Sentinel como uma rotina. Um livro de jogadas pode ajudar a automatizar e orquestrar a sua resposta de ameaça, pode integrar-se com outros sistemas tanto internos como externos, e pode ser configurado para funcionar automaticamente em resposta a alertas ou incidentes específicos, quando desencadeado por uma regra de análise ou uma regra de automação, respectivamente. Também pode ser executado manualmente a pedido, em resposta a alertas, a partir da página de incidentes.

Os playbooks em Azure Sentinel são baseados em fluxos de trabalho construídos em [Azure Logic Apps](../logic-apps/logic-apps-overview.md), um serviço de nuvem que o ajuda a agendar, automatizar e orquestrar tarefas e fluxos de trabalho em todos os sistemas em toda a empresa. Isto significa que os playbooks podem tirar partido de todo o poder e personalização das capacidades de integração e orquestração da Logic Apps e ferramentas de design fáceis de usar, bem como a escalabilidade, fiabilidade e nível de serviço de um serviço de Azure Tier 1.

Saiba mais com esta [explicação completa dos livros de jogadas.](automate-responses-with-playbooks.md)

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu como a Azure Sentinel usa a automação para ajudar o seu SOC a operar de forma mais eficaz e eficiente.

- Para saber mais sobre a automatização do manuseamento de incidentes, consulte [o manuseamento de incidentes da Automamate em Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Para saber mais sobre opções avançadas de automatização, consulte [a resposta da ameaça automatizada com os playbooks em Azure Sentinel.](automate-responses-with-playbooks.md)
- Para obter ajuda na implementação de regras de automação e playbooks, consulte [Tutorial: Use playbooks para automatizar respostas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).
