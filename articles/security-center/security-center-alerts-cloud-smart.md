---
title: Cloud inteligente correlação de alertas no Centro de segurança do Azure (incidentes) | Documentos da Microsoft
description: Este tópico explica como fusion utiliza na cloud inteligente correlação de alertas para gerar incidentes de segurança estão no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571780"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Cloud inteligente correlação de alertas no Centro de segurança do Azure (incidentes)

Centro de segurança analisa continuamente as cargas de trabalho de cloud híbrida com análise avançada e inteligência de ameaças para o alertar sobre atividades maliciosas.

A ampla cobertura de ameaças à medida que e a necessidade de detetar até mesmo a menor sinal indicação de comprometido aumenta, pode ser um desafio para os analistas de segurança para os alertas de diferentes de fazer uma triagem e identificar um ataque real. Centro de segurança ajuda os analistas que lidar com alerta fadiga e diagnosticar os ataques à medida que ocorrem, ao correlacionar alertas diferentes e sinais de baixa fidelidade em incidentes de segurança.

Fusion é a tecnologia e análise back end que alimente os incidentes do Centro de segurança, permitindo que ele correlacione alertas diferentes e sinais contextuais em conjunto. Fusion funciona ao observar os diferentes sinais reportado numa subscrição entre os recursos e deve ser a descobrir padrões predominantes que mostra a progressão de ataque ou sinaliza com informações contextuais compartilhadas que indica um procedimento de resposta unificada direcionado para os mesmos.

Análise de Fusion combinar dados de conhecimento de domínio de segurança com a ia para analisar alertas, descobrir novos padrões de ataque à medida que ocorrem. 

Centro de segurança tira partido da matriz de ataque do MITRE para associar alertas a sua intenção percebida, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, ao utilizar as informações recolhidas de cada passo de um ataque, o Centro de segurança pode descartar atividade que parece ser passos de um ataque, mas não é.  

Uma vez que os ataques ocorrem com freqüência em diferentes inquilinos, o Centro de segurança pode combinar os algoritmos de ia para analisar as sequências de ataque que são enviadas em cada subscrição para identificá-los como padrões de alerta predominantes em vez de apenas que está a ser por acaso associado a cada outros.

Durante uma investigação de um incidente, os analistas precisam freqüentemente contexto extra para chegar a um veredito sobre a natureza da ameaça e como resolvê-la. Por exemplo, mesmo quando é detetada uma anomalia na rede, sem compreender o que mais está a acontecer na rede ou ao recurso afetado é difícil entender as ações para realizar a seguir. Para ajudar a, um incidente de segurança pode incluir artefactos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam consoante o tipo de ameaça detetada e a configuração do seu ambiente. 

![Detalhes do incidente de segurança](./media/security-center-alerts-cloud-smart/security-incident.png)

Para compreender melhor os incidentes de segurança, consulte [como lidar com incidentes de segurança no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-incident).

