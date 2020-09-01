---
title: Novidades com o agente de servidores (pré-visualização) do Azure Arc
description: Este artigo tem notas de lançamento para o agente ativado por servidores (pré-visualização) do Azure Arc. Para muitas das questões resumidas, existem ligações a detalhes adicionais.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236770"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Novidades com o agente de servidores (pré-visualização) do Azure Arc

O agente Azure Arc ativado (pré-visualização) Connected Machine recebe melhorias numa base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros

## <a name="august-2020"></a>Agosto de 2020

Versão: 0.11

- Apoio a Ubuntu 20.04.

- Melhorias de fiabilidade para as implementações de extensões.

### <a name="known-issues"></a>Problemas conhecidos

Se estiver a utilizar uma versão mais antiga do agente Linux e o configurar para utilizar um servidor proxy, tem de reconfigurar a definição do servidor proxy após a atualização. Para fazer isto, `sudo azcmagent_proxy add http://proxyserver.local:83` corra.

## <a name="next-steps"></a>Passos seguintes

Antes de avaliar ou ativar os servidores (pré-visualização) do Arc através de várias máquinas híbridas, [reveja](agent-overview.md) a visão geral do agente da Máquina Conectada para compreender os requisitos, detalhes técnicos sobre o agente e métodos de implementação.