---
title: Use recomendações do Azure Security Center para reforçar a segurança Microsoft Docs
description: " Saiba como usar políticas de segurança e recomendações no Azure Security Center para ajudar a mitigar um ataque de segurança. "
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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322143"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Use Azure Security Center recommendations to enhance security (Utilizar as recomendações do Centro de Segurança do Azure para melhorar a segurança)

Você pode reduzir as chances de um evento de segurança significativo configurando uma política de segurança e, em seguida, implementando as recomendações fornecidas pelo Azure Security Center. Este artigo mostra-lhe como usar políticas de segurança e recomendações no Centro de Segurança para ajudar a mitigar um ataque de segurança. 

O Centro de Segurança executa automaticamente exames contínuos para analisar o estado de segurança dos seus recursos Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos de segurança necessários. O Security Center atualiza as suas recomendações dentro de 24 horas, com as seguintes exceções:

- As recomendações de configuração de segurança do sistema operativo são atualizadas dentro de 48 horas
- As recomendações de endpoint protection são atualizadas dentro de 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra-lhe como usar o Centro de Segurança para ajudar a reduzir as chances de um incidente de segurança, monitorizando as recomendações do Centro de Segurança e tomando medidas. O cenário utiliza a empresa fictícia, Contoso, e as funções apresentadas no Guia de [Planeamento e Operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)do Centro de Segurança. Neste cenário, estamos focados nos papéis das seguintes personalidades:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

Contoso emigrou recentemente alguns dos seus recursos no local para Azure. Contoso quer proteger os seus recursos e reduzir a vulnerabilidade dos seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Utilizar o Centro de Segurança do Azure
David, da segurança de TI da Contoso, já escolheu embarcar no Centro de Segurança nas assinaturas da Contoso para o Azure Security Center para prevenir e detetar vulnerabilidades de segurança. 

O Centro de Segurança analisa automaticamente o estado de segurança dos recursos Azure da Contoso e aplica políticas de segurança predefinidas. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria **recomendações baseadas nos controlos definidos** na política de segurança. 

David executa a Azure Security com o Azure Defender habilitado, em todas as suas subscrições, a obter o conjunto completo de recomendações e funcionalidades de segurança disponíveis. Jeff também está a bordo de todos os seus servidores existentes no local que ainda não foram migrados para a nuvem para que possam aproveitar o suporte híbrido do Security Center através dos seus [servidores Windows e Linux.](quickstart-onboard-machines.md)

Jeff é dono de uma carga de trabalho na nuvem. Jeff é responsável pela aplicação de controlos de segurança de acordo com as políticas de segurança de Contoso. 

Jeff executa as seguintes tarefas:

- Monitorize recomendações de segurança fornecidas pelo Security Center
- Avaliar recomendações de segurança e decidir se devem aplicar ou rejeitar as recomendações.
- Aplicar recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Remediar ameaças usando recomendações
Como parte das suas atividades diárias de monitorização, Jeff assina no Azure e abre o Centro de Segurança. 

1. Jeff seleciona as assinaturas da carga de trabalho.

2. Jeff verifica o **Secure Score** para obter uma imagem geral de quão seguras as subscrições são e vê que a pontuação é 548.

3. Jeff tem que decidir quais recomendações para tratar primeiro. Assim, jeff clica em Secure Score e começa a lidar com recomendações com base no quanto melhora a sua [pontuação segura](secure-score-security-controls.md).

4. Como o Jeff tem muitos VMs ligados, o Jeff decide focar-se nas suas máquinas no [inventário de ativos.](asset-inventory.md)

5. Quando jeff abre o inventário de ativos, aparece uma lista de recomendações. O Jeff trata deles de acordo com o impacto da pontuação segura.

6. Jeff tem inúmeros VMs virados para a Internet, e como as suas portas estão expostas, estão preocupados que um intruso possa ganhar controlo sobre os servidores. Por isso, o Jeff opta por utilizar [**o acesso vm just-in-time**](security-center-just-in-time.md).

Jeff continua a mover-se através das recomendações de alta prioridade e média prioridade, e toma decisões sobre a implementação. Para cada recomendação, Jeff olha para as informações detalhadas fornecidas pelo Security Center para entender quais os recursos afetados, qual é o impacto do Secure Score, o que cada recomendação significa e passos de reparação para como mitigar cada problema.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Impor recomendações para evitar configurações erradas de segurança

Para garantir que os utilizadores não criam recursos que impactem negativamente a pontuação de Jeff, configuram as opções De Enforce e Deny nas recomendações mais importantes para eles. Saiba mais em [Prevenir configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).


## <a name="conclusion"></a>Conclusão
As recomendações de monitorização no Centro de Segurança ajudam-no a eliminar vulnerabilidades de segurança antes de ocorrer um ataque. Quando remediar recomendações, o seu Resultado Seguro e a postura de segurança das suas cargas de trabalho melhoram. O Security Center descobre automaticamente novos recursos que implementa, avalia-os contra a sua política de segurança e fornece novas recomendações para os assegurar.


## <a name="next-steps"></a>Passos seguintes
Certifique-se de que tem um processo de monitorização em vigor, no qual verifica regularmente as recomendações no Centro de Segurança para que possa manter os seus recursos seguros ao longo do tempo.

Este cenário mostrou-lhe como usar políticas de segurança e recomendações no Centro de Segurança para ajudar a mitigar um ataque de segurança.

Saiba como responder a ameaças com [a Gestão e responder aos alertas de segurança.](security-center-managing-and-responding-alerts.md)
