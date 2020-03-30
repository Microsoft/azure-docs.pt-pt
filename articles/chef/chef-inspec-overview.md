---
title: Utilizar o InSpec para automatização de conformidade da sua infraestrutura Azure
description: Saiba como usar o InSpec para detetar problemas nas suas implementações do Azure
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar, inspeção
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158222"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Utilizar o InSpec para automatização de conformidade da sua infraestrutura Azure

[InSpec](https://www.chef.io/inspec/) é a língua de código aberto do Chef para descrever a segurança & regras de conformidade que podem ser partilhadas entre engenheiros de software, operações e engenheiros de segurança. O InSpec funciona comparando o estado real da sua infraestrutura com o estado desejado que expressa no código InSpec de fácil leitura e fácil de escrever. A InSpec deteta violações e exibe descobertas sob a forma de um relatório, mas coloca-o no controlo da reparação.

Pode utilizar o InSpec para validar o estado dos recursos e grupos de recursos numa subscrição, incluindo máquinas virtuais, configurações de rede, configurações de Diretório Ativo Azure e muito mais.

Este artigo descreve os benefícios de usar o InSpec para facilitar a segurança e a conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Tornar a conformidade fácil de compreender e avaliar

Documentação de conformidade escrita em folhas de cálculo ou documentos do Word deixa requisitos abertos à interpretação. Com o InSpec, transformas os teus requisitos em código versonizado, executável e legível pelo homem. O código substitui as conversas sobre o que deve ser avaliado a favor de testes tangíveis com intenção clara.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detetar problemas em toda a frota e priorizar a sua reparação

O modo de deteção sem agente do InSpec permite-lhe avaliar rapidamente - à escala - o seu nível de exposição. Metadados incorporados para pontuação de impacto/gravidade ajudam a determinar em que áreas se concentrar para a reparação. Também pode escrever regras rapidamente em resposta a novas vulnerabilidades ou regulamentos e lançá-las imediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Audite máquinas virtuais Azure com Configuração de Hóspedes Políticas

O Azure apoia diretamente o uso das definições do Chef InSpec para auditar máquinas virtuais Azure através da [Configuração de Hóspedes de Política Azure.](/azure/governance/policy/concepts/guest-configuration) A Configuração de Hóspedes avalia uma máquina virtual Linux a uma definição fornecida pelo Chef InSpec e reporta a conformidade através da Política Azure. Os resultados destas auditorias são também reportados através de registos do Monitor Do Azure; permitindo alertas e outros cenários de automação.

## <a name="satisfy-audits"></a>Satisfazer auditorias

Com o InSpec, pode responder a perguntas de auditoria a qualquer momento - e não apenas em intervalos pré-determinados, como trimestral ou anual. Ao executar continuamente os testes inSpec, você entra num ciclo de auditoria sabendo a sua postura e histórico de conformidade exata, em vez de ser surpreendido pelas descobertas de um auditor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Experimente inspete na casca de nuvem azure](https://shell.azure.com)