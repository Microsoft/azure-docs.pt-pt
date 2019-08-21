---
title: Use as recomendações da central de segurança do Azure para aprimorar a segurança | Microsoft Docs
description: " Saiba como usar as políticas de segurança e recomendações na central de segurança do Azure para ajudar a mitigar um ataque de segurança. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 79a5f9a1269bdfc63d9d0b6fffd8458d011b777b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640864"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usar as recomendações da central de segurança do Azure para aumentar a segurança
Você pode reduzir as chances de um evento de segurança significativo Configurando uma política de segurança e, em seguida, implementando as recomendações fornecidas pela central de segurança do Azure. Este artigo mostra como usar as políticas de segurança e recomendações na central de segurança para ajudar a mitigar um ataque de segurança. 

A central de segurança executa verificações contínuas automaticamente para analisar o estado de segurança de seus recursos do Azure. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que o orientam durante o processo de configuração dos controles de segurança necessários. A central de segurança atualiza suas recomendações dentro de 24 horas, com as seguintes exceções:

- As recomendações de configuração de segurança do sistema operacional são atualizadas dentro de 48 horas
- As recomendações de Endpoint Protection problemas são atualizadas dentro de 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra como usar a central de segurança para ajudar a reduzir as chances de um incidente de segurança monitorando as recomendações da central de segurança e executando ações. O cenário usa a empresa fictícia, a Contoso e as funções apresentadas no guia de [planejamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)da central de segurança. Nesse cenário, estamos nos concentrando nas funções das seguintes pessoas:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

A contoso migrou recentemente alguns de seus recursos locais para o Azure. A contoso deseja proteger seus recursos e reduzir a vulnerabilidade de seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Utilizar o Centro de Segurança do Azure
David, da segurança de ti da Contoso, já escolheu a integração da central de segurança nas assinaturas da Contoso à central de segurança do Azure para evitar e detectar vulnerabilidades de segurança. 

A central de segurança analisa automaticamente o estado de segurança dos recursos do Azure da Contoso e aplica as políticas de segurança padrão. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria **recomendações** com base nos controles definidos na política de segurança. 

David executa a camada Standard de segurança do Azure, em todas as suas assinaturas, para obter o pacote completo de recomendações e recursos de segurança disponíveis. Jeff também integra todos os servidores locais existentes que ainda não foram migrados para a nuvem para que eles possam aproveitar o suporte híbrido da central de segurança em seus servidores [Windows](quick-onboard-windows-computer.md) e [Linux](quick-onboard-linux-computer.md) .

Jeff é um proprietário de carga de trabalho de nuvem. Jeff é responsável por aplicar controles de segurança de acordo com as políticas de segurança da contoso. 

Jeff executa as seguintes tarefas:

- Monitorar as recomendações de segurança fornecidas pela central de segurança
- Avalie as recomendações de segurança e decida se elas devem aplicar ou ignorar as recomendações.
- Aplicar recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Corrigir ameaças usando recomendações
Como parte de suas atividades diárias de monitoramento, Jeff entra no Azure e abre a central de segurança. 

1. Jeff seleciona as assinaturas da carga de trabalho.

2. Jeff verifica a **Pontuação segura** para obter uma visão geral de como as assinaturas são seguras e vê que a pontuação é 548.

3. Jeff precisa decidir quais recomendações tratar primeiro. Então Jeff clica em Pontuação segura e começa a lidar com recomendações com base em quanto isso melhora seu [impacto de Pontuação segura](security-center-secure-score.md).

4. Como Jeff tem muitas VMs e servidores conectados, Jeff decide se concentrar na **computação e nos aplicativos**.

5. Quando Jeff clica em **computação e aplicativos**, eles veem uma lista de recomendações e manipula-os de acordo com o impacto de Pontuação segura.

6. Jeff tem várias VMs voltadas para a Internet e, como suas portas são expostas, eles estão preocupados com o fato de um invasor poder ter controle sobre os servidores. Portanto, Jeff escolhe usar (**acesso de VM just-in-time**) [Security-Center-just-in-time.MD].

Jeff continua a percorrer as recomendações de alta prioridade e prioridade média e toma decisões sobre a implementação. Para cada recomendação, Jeff examina as informações detalhadas fornecidas pela central de segurança para entender quais recursos são afetados, qual é o impacto da Pontuação segura, o que cada recomendação significa e as etapas de correção para saber como atenuar cada problema.

## <a name="conclusion"></a>Conclusão
As recomendações de monitoramento na central de segurança ajudam a eliminar vulnerabilidades de segurança antes que ocorra um ataque. Quando você corrige recomendações, sua pontuação segura e a postura de segurança de suas cargas de trabalho melhoram. A central de segurança descobre automaticamente novos recursos que você implanta, avalia-os em relação à sua política de segurança e fornece novas recomendações para protegê-los.


## <a name="next-steps"></a>Passos Seguintes
Certifique-se de que você tenha um processo de monitoramento em vigor, no qual você verifica regularmente as recomendações na central de segurança para que você possa garantir que seus recursos sejam protegidos ao longo do tempo.

Este cenário mostrou como usar políticas de segurança e recomendações na central de segurança para ajudar a mitigar um ataque de segurança. Consulte o [cenário de resposta a incidentes](security-center-incident-response.md) para saber como ter um plano de resposta a incidentes em vigor antes que ocorra um ataque.

Saiba como responder a ameaças com [resposta a incidentes](security-center-incident-response.md).
