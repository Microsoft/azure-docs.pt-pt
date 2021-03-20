---
title: Como responder aos alertas do Azure Defender para o Gestor de Recursos
description: Conheça os passos necessários para responder aos alertas do Azure Defender para o Gestor de Recursos
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754743"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Responder a alertas do Azure Defender para Resource Manager

Quando receber um alerta do Azure Defender para o Gestor de Recursos, recomendamos que investigue e responda ao alerta conforme descrito abaixo. O Azure Defender for Resource Manager protege todos os recursos conectados, por isso mesmo que esteja familiarizado com a aplicação ou utilizador que desencadeou o alerta, é importante verificar a situação em torno de cada alerta.  


## <a name="step-1-contact"></a>Passo 1. Contacto

1. Contacte o titular do recurso para determinar se o comportamento era esperado ou intencional.
1. Se a atividade for esperada, desprezem o alerta.
1. Se a atividade for inesperada, trate as contas de utilizador relacionadas, subscrições e máquinas virtuais como comprometidas e atenuadas como descrito no passo seguinte.

## <a name="step-2-immediate-mitigation"></a>Passo 2. Atenuação imediata 

1. Remediar contas de utilizadores comprometidas:
    - Se não são familiares, apague-os como podem ter sido criados por um ator ameaça.
    - Se forem familiares, mudem as suas credenciais de autenticação.
    - Utilize registos de atividades Azure para rever todas as atividades realizadas pelo utilizador e identificar quaisquer que sejam suspeitos

1. Remediar subscrições comprometidas:
    - Remova quaisquer Runbooks desconhecidos da conta de automação comprometida
    - Reveja permissões do IAM para a subscrição e remova permissões para qualquer conta de utilizador desconhecida
    - Reveja todos os recursos da Azure na subscrição e elimine qualquer um que não seja familiar
    - Reveja e investigue quaisquer alertas de segurança para a subscrição no Azure Security Center
    - Utilize registos de atividades Azure para rever todas as atividades realizadas na subscrição e identificar quaisquer que sejam suspeitos

1. Remediar as máquinas virtuais comprometidas
    - Alterar as palavras-passe para todos os utilizadores
    - Executar uma varredura completa de antimalware na máquina
    - Reimagem as máquinas de uma fonte sem malware


## <a name="next-steps"></a>Passos seguintes

Esta página explicou o processo de resposta a um alerta do Azure Defender para o Gestor de Recursos. Para obter informações relacionadas consulte as seguintes páginas:

- [Introdução ao Azure Defender para Gestor de Recursos](defender-for-resource-manager-introduction.md)
- [Alertas de Supressão do Azure Defender](alerts-suppression-rules.md)
- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)