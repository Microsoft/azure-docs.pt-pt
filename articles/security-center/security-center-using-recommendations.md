---
title: Use recomendações do Azure Security Center para aumentar a segurança Microsoft Docs
description: " Aprenda a usar políticas e recomendações de segurança no Centro de Segurança Azure para ajudar a mitigar um ataque de segurança. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603278"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Use recomendações do Azure Security Center para aumentar a segurança
Pode reduzir as hipóteses de um evento de segurança significativo configurando uma política de segurança e, em seguida, implementando as recomendações fornecidas pelo Azure Security Center. Este artigo mostra-lhe como usar políticas de segurança e recomendações no Centro de Segurança para ajudar a mitigar um ataque de segurança. 

O Security Center realiza automaticamente exames contínuos para analisar o estado de segurança dos seus recursos Azure. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos de segurança necessários. O Security Center atualiza as suas recomendações dentro de 24 horas, com as seguintes exceções:

- As recomendações de configuração de segurança do sistema operativo são atualizadas dentro de 48 horas
- Recomendações de proteção do ponto final são atualizadas dentro de 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra-lhe como usar o Centro de Segurança para ajudar a reduzir as chances de um incidente de segurança, monitorizando as recomendações do Security Center e tomando medidas. O cenário utiliza a empresa fictícia, Contoso, e as funções apresentadas no guia de [planeamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)do Centro de Segurança. Neste cenário, estamos focados nos papéis das seguintes personalidades:

![Papéis de cenário](./media/security-center-using-recommendations/scenario-roles.png)

Contoso emigrou recentemente alguns dos seus recursos para o Azure. Contoso quer proteger os seus recursos e reduzir a vulnerabilidade dos seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Use o Centro de Segurança Azure
David, da segurança de TI de Contoso, já escolheu embarcar no Centro de Segurança das assinaturas de Contoso para o Azure Security Center para prevenir e detetar vulnerabilidades de segurança. 

O Security Center analisa automaticamente o estado de segurança dos recursos Azure de Contoso e aplica políticas de segurança padrão. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações com base nos **controlos** estabelecidos na política de segurança. 

David gere o nível padrão da Azure Security, em todas as suas subscrições para obter todo o conjunto de recomendações e funcionalidades de segurança disponíveis. Jeff também está a bordo de todos os seus servidores existentes no local que ainda não foram migrados para a nuvem para que possam tirar partido do suporte híbrido do Security Center através dos seus servidores [Windows](quick-onboard-windows-computer.md) e [Linux.](quick-onboard-linux-computer.md)

Jeff é um dono de uma carga de trabalho na nuvem. Jeff é responsável pela aplicação de controlos de segurança de acordo com as políticas de segurança de Contoso. 

Jeff executa as seguintes tarefas:

- Monitorize as recomendações de segurança fornecidas pelo Security Center
- Avalie as recomendações de segurança e decida se devem aplicar ou rejeitar as recomendações.
- Aplicar recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Remediar ameaças usando recomendações
Como parte das suas atividades diárias de monitorização, Jeff entra no Azure e abre o Security Center. 

1. Jeff seleciona as assinaturas da carga de trabalho.

2. Jeff verifica o **Secure Score** para obter uma imagem geral de quão seguras são as subscrições e vê que a pontuação é 548.

3. Jeff tem que decidir quais recomendações para lidar primeiro. Então Jeff clica em Secure Score e começa a lidar com recomendações com base no quanto melhora o seu [impacto secure score](security-center-secure-score.md).

4. Como o Jeff tem muitos VMs e servidores conectados, o Jeff decide focar-se na **Compute e nas aplicações.**

5. Quando Jeff clica em **Compute e apps,** eles vêem uma lista de recomendações e as tratam de acordo com o impacto secure Score.

6. Jeff tem inúmeros VMs virados para a Internet, e como as suas portas estão expostas, estão preocupados que um intruso possa ganhar controlo sobre os servidores. Então jeff escolhe usar [**acesso VM just-in-time**](security-center-just-in-time.md).

Jeff continua a passar pelas recomendações de alta prioridade e prioridade média, e toma decisões sobre a implementação. Para cada recomendação, Jeff analisa as informações detalhadas fornecidas pelo Security Center para entender quais os recursos impactados, qual é o impacto da Pontuação Segura, o que cada recomendação significa e medidas de reparação para como mitigar cada problema.

## <a name="conclusion"></a>Conclusão
Monitorizar recomendações no Centro de Segurança ajuda-o a eliminar vulnerabilidades de segurança antes de ocorrer um ataque. Ao remediar as recomendações, a sua Pontuação Segura e a postura de segurança das suas cargas de trabalho melhoram. O Security Center descobre automaticamente novos recursos que implementa, avalia-os contra a sua política de segurança e fornece novas recomendações para os assegurar.


## <a name="next-steps"></a>Passos seguintes
Certifique-se de que tem um processo de monitorização em vigor, no qual verifica regularmente as recomendações no Centro de Segurança para que possa manter os seus recursos seguros ao longo do tempo.

Este cenário mostrou-lhe como usar políticas de segurança e recomendações no Centro de Segurança para ajudar a mitigar um ataque de segurança.

Saiba como responder a ameaças com [Gestão e resposta a alertas](security-center-managing-and-responding-alerts.md)de segurança .
