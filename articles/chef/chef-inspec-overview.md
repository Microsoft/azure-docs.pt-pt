---
title: Use inspec para a automação de conformidade da sua infraestrutura do Azure
description: Saiba como usar o inspec para detectar problemas em suas implantações do Azure
keywords: Azure, chefe, DevOps, máquinas virtuais, visão geral, automatizar, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158222"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Use inspec para a automação de conformidade da sua infraestrutura do Azure

[Inspec](https://www.chef.io/inspec/) é a linguagem de software livre do chefe para descrever as regras de conformidade de & de segurança que podem ser compartilhadas entre engenheiros de software, operações e engenheiros de segurança. O inspec funciona comparando o estado real da sua infraestrutura com o estado desejado que você expressa em um código inspec de fácil leitura e fácil de escrever. O inspec detecta violações e exibe as descobertas na forma de um relatório, mas coloca você no controle da correção.

Você pode usar inspecs para validar o estado de recursos e grupos de recursos em uma assinatura, incluindo máquinas virtuais, configurações de rede, configurações de Azure Active Directory e muito mais.

Este artigo descreve os benefícios de usar o inspec para facilitar a segurança e a conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Torne a conformidade fácil de entender e avaliar

A documentação de conformidade escrita em planilhas ou documentos do Word deixa os requisitos abertos para interpretação. Com o inspec, você transforma seus requisitos em código com versão, executável e legível por humanos. O código substitui as conversas sobre o que deve ser avaliado em favor de testes tangíveis com uma intenção clara.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detectar problemas em toda a frota e priorizar sua correção

O modo de detecção sem agente do inspec permite que você avalie rapidamente em escala – seu nível de exposição. Os metadados internos para a pontuação de impacto/severidade ajudam a determinar em quais áreas você deve se concentrar para a correção. Você também pode escrever regras rapidamente em resposta a novas vulnerabilidades ou regulamentos e distribuí-las imediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Auditar máquinas virtuais do Azure com configuração de convidado de política

O Azure suporta diretamente o uso de definições de inspecs do chefe para auditar máquinas virtuais do Azure por meio da [configuração de convidado Azure Policy](/azure/governance/policy/concepts/guest-configuration). A configuração de convidado avalia uma máquina virtual Linux para uma definição de inspec de chefe fornecida e relatórios de acordo com a Azure Policy. Os resultados dessas auditorias também são relatados por meio de logs de Azure Monitor; habilitação de alertas e outros cenários de automação.

## <a name="satisfy-audits"></a>Satisfazer auditorias

Com o inspec, você pode responder a perguntas de auditoria a qualquer momento, não apenas em intervalos predeterminados, como trimestral ou anual. Ao executar continuamente testes inspecs, você insere um ciclo de auditoria sabendo sua postura e histórico de conformidade exatos, em vez de ficar surpreso com as descobertas de um auditor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Experimente o inspec no Azure Cloud Shell](https://shell.azure.com)