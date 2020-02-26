---
title: Mapa de alerta de inteligência e segurança de ameaças - Centro de Segurança Azure
description: Aprenda a usar o mapa de alerta de segurança e a capacidade de inteligência de ameaça no Centro de Segurança Azure para identificar potenciais ameaças nos seus VMs e computadores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603433"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Informações sobre ameaças e mapa de alertas de segurança
Este artigo ajuda-o a usar o mapa de alertas de segurança do Azure Security Center e o mapa de inteligência de ameaças baseado em eventos de segurança para resolver questões relacionadas com a segurança.

> [!NOTE]
> O botão de mapa de *eventos* de segurança foi retirado a 31 de julho de 2019. Para mais informações e serviços alternativos, consulte [funcionalidades do Centro de Aposentações (julho 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Como funciona o mapa de alertas de segurança
O Centro de Segurança fornece-lhe um mapa que o ajuda a identificar ameaças de segurança contra o ambiente. Por exemplo, pode identificar se um determinado computador faz parte de uma botnet e de onde vem a ameaça. Os computadores podem tornar-se nódosos numa botnet quando os atacantes instalam ilicitamente malware que interage secretamente com o comando e o controlo que gerem a botnet. 

Para construir este mapa, o Security Center utiliza dados que provêm de várias fontes dentro da Microsoft. O Security Center utiliza estes dados para mapear potenciais ameaças contra o seu ambiente. 

Um dos passos de um [processo de resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, deve realizar as tarefas seguintes:

- Determinar a natureza do ataque.
- Determinar o ponto de origem do ataque.
- Determinar a intenção do ataque. O ataque foi direcionado à sua organização, para adquirir informações específicas, ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os ficheiros que foram acedidos e determinar a sensibilidade desses ficheiros.

Pode utilizar o mapa de alertas de Segurança no Centro de Segurança para ajudar nestas tarefas.

## <a name="access-the-security-alerts-map"></a>Aceda ao mapa de alertas de Segurança
Para visualizar as ameaças atuais no seu ambiente, abra o mapa de alertas de Segurança:

1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, sob **proteção contra ameaças** selecione mapa de **alertas**de segurança . O mapa abre.
3. Para obter mais informações sobre o alerta e receber etapas de reparação, clique no ponto alerta no mapa e siga as instruções. 
 
O mapa de alertas de segurança baseia-se em alertas. Estes alertas baseiam-se em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com sucesso, se o endereço IP é ou não um endereço IP de risco conhecido (por exemplo, um criptomineiro conhecido) ou um endereço IP que não é reconhecido anteriormente como arriscado. O mapa fornece alertas em todas as subscrições que selecionou anteriormente em Azure. 

Os alertas no mapa são apresentados de acordo com a localização geográfica de onde são detetados como originários, e são codificados por gravidade. 
    ](./media/security-center-threat-intel/security-center-alert-map.png) de informações sobre informações sobre ameaças de ameaças de ![



## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as informações sobre ameaças no Centro de Segurança para ajudar a identificar atividade suspeita. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.