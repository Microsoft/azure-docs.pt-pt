---
title: Relatórios de informações sobre Ameaças do Centro de Segurança do Azure| Microsoft Docs
description: Esta página ajuda-o a usar relatórios de inteligência do Azure Security Center durante uma investigação para encontrar mais informações sobre alertas de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: f9b3fd0000a1b5dbba00995c37f96a89319de0e1
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440488"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de inteligência de ameaça do Centro de Segurança Azure

Esta página explica como os relatórios de inteligência de ameaça do Azure Security Center podem ajudá-lo a aprender mais sobre uma ameaça que desencadeou um alerta de segurança.


## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de informações sobre ameaças?

A proteção contra ameaças do Security Center funciona através da monitorização de informações de segurança dos seus recursos Azure, da rede e das soluções de parceiros conectados. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Para mais informações, consulte [como o Azure Security Center deteta e responde a ameaças.](security-center-alerts-overview.md#detect-threats)

Quando o Centro de Segurança identifica uma ameaça, desencadeia um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações detalhadas sobre o evento, incluindo sugestões de reparação. Para ajudar as equipas de resposta a incidentes a investigar e a remediar ameaças, o Security Center fornece relatórios de inteligência de ameaças que contêm informações sobre ameaças detetadas. O relatório inclui informações como:

* A identidade do atacante ou associações (se esta informação estiver disponível)
* Os objetivos dos atacantes
* As campanhas de ataque atuais e históricas (se esta informação estiver disponível)
* Táticas, ferramentas e procedimentos dos agressores
* Os indicadores de comprometimento (IoC) associados, como URLs e hashes de ficheiros
* A vitimologia, a prevalência do setor e geográfica para ajudá-lo a determinar se os recursos do Azure estão em risco
* Informações de mitigação e remediação

> [!NOTE]
> A quantidade de informações em qualquer relatório específico irá variar; o nível de detalhe baseia-se na atividade e na prevalência de software maligno.

O Centro de Segurança tem três tipos de relatórios de ameaças, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório do Grupo de Atividades**: proporciona mergulhos profundos em atacantes, seus objetivos e táticas.
* **Relatório da Campanha**: centra-se nos detalhes sobre campanhas de ataque específicas.
* **Relatório de Resumo da Ameaça**: abrange todos os itens nos dois relatórios anteriores.

Este tipo de informação é útil durante o processo de resposta a incidentes, onde há uma investigação em curso para entender a origem do ataque, as motivações do agressor, e o que fazer para mitigar esta questão no futuro.



## <a name="how-to-access-the-threat-intelligence-report"></a>Como aceder ao relatório de informações sobre ameaças?

1. Da barra lateral do Centro de Segurança, abra a página **de alertas de segurança.**
1. Selecione um alerta. 
    A página de detalhes dos alertas abre com mais detalhes sobre o alerta. Abaixo está a página de detalhes de alerta **detetados pelos indicadores ransomware.**

    [![Indicadores de ransomware detetados página de detalhes de alerta](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Selecione o link para o relatório e um PDF abrirá no seu navegador predefinido.

    [![Página de detalhes de alerta de ação potencialmente inseguro](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Pode, opcionalmente, fazer o download do relatório PDF. 

    >[!TIP]
    > A quantidade de informação disponível para cada alerta de segurança irá variar de acordo com o tipo de alerta.



## <a name="next-steps"></a>Próximos passos

Esta página explicou como abrir relatórios de inteligência de ameaça quando investigam alertas de segurança. Para obter informações relacionadas, consulte as seguintes páginas:

* [Gerir e responder aos alertas de segurança no Centro de Segurança Azure.](security-center-managing-and-responding-alerts.md) Saiba como gerir e responder a alertas de segurança.
* [Lidar com incidentes de segurança no Centro de Segurança Azure](security-center-incident.md)