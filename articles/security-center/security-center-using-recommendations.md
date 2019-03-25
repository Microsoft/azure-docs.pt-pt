---
title: Recomendações do Centro de segurança do Azure de utilização para melhorar a segurança | Documentos da Microsoft
description: " Saiba como utilizar políticas de segurança e recomendações no Centro de segurança do Azure para ajudar a mitigar um ataque de segurança. "
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
ms.date: 3/24/2019
ms.author: monhaber
ms.openlocfilehash: 8c8108697e39153aef8727942c166a741cb8398c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402767"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Recomendações do Centro de segurança do Azure de utilização para melhorar a segurança
Pode reduzir as chances de um evento de segurança significativos ao configurar uma política de segurança e, em seguida, implementar as recomendações fornecidas pelo centro de segurança do Azure. Este artigo mostra-lhe como utilizar políticas de segurança e recomendações no Centro de segurança para ajudar a mitigar um ataque de segurança. 

Centro de segurança é automaticamente executado análises contínuas para analisar o estado de segurança dos seus recursos do Azure. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que descreve o processo de configurar os controlos de segurança necessário. Centro de segurança atualiza as recomendações, uma vez a cada 24 horas.

## <a name="scenario"></a>Cenário
Este cenário mostra como utilizar o Centro de segurança para ajudar a reduzir as chances de um incidente de segurança, recomendações do Centro de segurança de monitorização e tomar medidas. O cenário utiliza a empresa fictícia, Contoso e funções apresentadas no Centro de segurança [guia de operações e planeamento](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Neste cenário, nos concentraremos nas funções das seguintes pessoas:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

Contoso migrou recentemente alguns dos seus recursos no local para o Azure. Contoso quer proteger os seus recursos e reduzir a vulnerabilidade de seus recursos na cloud.

## <a name="use-azure-security-center"></a>Utilizar o Centro de Segurança do Azure
David, da Contoso da segurança de TI, já tenha optado por carregar o Centro de segurança em subscrições da Contoso para o Centro de segurança do Azure para evitar e detectar vulnerabilidades de segurança. 

Centro de segurança analisa o estado de segurança dos recursos do Azure da Contoso e aplica políticas de segurança padrão automaticamente. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, ele cria **recomendações** com base nos controlos definidos na política de segurança. 

David é executado o escalão standard de segurança do Azure, em todas as suas subscrições para obter o conjunto completo de recomendações e recursos de segurança disponíveis. Jeff também suporte a carrega todos os seus locais existentes servidores que ainda não foram migrados para a cloud, para que ele pode tirar partido de híbrida com o Centro de segurança em seu [Windows](quick-onboard-windows-computer.md) e [Linux](quick-onboard-linux-computer.md) servidores.

Jeff é um proprietário de carga de trabalho na cloud. Jeff é responsável pela aplicação de controlos de segurança de acordo com as políticas de segurança da Contoso. 

Jeff realiza as seguintes tarefas:

- Recomendações de segurança de monitor fornecidas pelo centro de segurança
- Recomendações de segurança de avaliar e decidir se ele deve aplicar ou dispensar
- Aplicar recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Corrigir as ameaças com as recomendações
Como parte de suas atividades de monitorização diárias, Jeff inicia sessão no Azure e abre o Centro de segurança. 

1. Jeff seleciona as subscrições da sua carga de trabalho.

2. Jeff verifica seu **secure pontuação** para obter um geral imagens de quão segura são as subscrições e vê que a sua pontuação é 548.

3. Jeff tem de decidir quais as recomendações para lidar com pela primeira vez. Portanto, Jeff clica pontuação segura e começa a processar recomendações com base no quanto ele melhora seu [proteger o impacto de pontuação](security-center-secure-score.md).

4. Como Jeff tem muitas VMs ligadas e de servidores, Jeff decide para se concentrar **computação e aplicações**.

5. Quando Jeff clica **computação e aplicações**, ele vê uma lista de recomendações e lida com eles, de acordo com a segura Pontuar o impacto.

6. Jeff tem vários Internet voltada para as VMs e porque suas portas são expostas, ele está preocupado que um atacante pode obter controle sobre os servidores. Portanto, Jeff opta por utilizar (**o acesso à VM just-in-time**) [segurança-center-just-em-time.md].

Jeff continua a percorrer as recomendações de prioridade média e alta prioridade e toma decisões de implementação. Para cada recomendação, Jeff analisa as informações detalhadas fornecido pelo centro de segurança para compreender quais recursos são afetados, o que afeta a classificação de segura é, o que cada recomendação meios e passos de remediação para saber como atenuar cada problema.

## <a name="conclusion"></a>Conclusão
Recomendações no Centro de segurança de monitorização ajuda a eliminar a vulnerabilidades de segurança antes de ocorre um ataque. Quando corrige recomendações, melhoram sua pontuação segura e a postura de segurança de suas cargas de trabalho. Centro de segurança Deteta automaticamente os novos recursos que implementar, avalia-los com a diretiva de segurança e fornece novas recomendações para protegê-los.


## <a name="next-steps"></a>Passos Seguintes
Certifique-se de que tem um processo de monitoramento, no qual regularmente verificar as recomendações no Centro de segurança para que pode verificar se a proteger os seus recursos ao longo do tempo.

Neste cenário de mostrar como utilizar políticas de segurança e recomendações no Centro de segurança para ajudar a mitigar um ataque de segurança. Consulte a [cenário de resposta a incidentes](security-center-incident-response.md) para aprender a ter uma plano em vigor antes que ocorra um ataque de resposta a incidente.

Saiba como responder a ameaças com [resposta a incidentes](security-center-incident-response.md).
