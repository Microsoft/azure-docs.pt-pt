---
title: Incidentes no Centro de Segurança Azure - correlações inteligentes de alertas
description: Este tópico explica como a fusão usa a correlação de alerta inteligente em nuvem para gerar incidentes de segurança no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73686483"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alerta inteligente em Azure Security Center (incidentes)

O Azure Security Center analisa continuamente as cargas de trabalho híbridos em nuvem utilizando análises avançadas e inteligência de ameaça para alertá-lo sobre atividades maliciosas.

A amplitude da cobertura de ameaças está a aumentar. A necessidade de detetar até o menor compromisso é importante, e pode ser um desafio para os analistas de segurança triagem dos diferentes alertas e identificar um ataque real. O Centro de Segurança ajuda os analistas a lidar com esta fadiga de alerta. Ajuda a diagnosticar ataques à medida que ocorrem, correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

A análise de fusão é a tecnologia e a traseira analítica que alimenta os incidentes do Centro de Segurança, permitindo-lhe correlacionar diferentes alertas e sinais contextuais em conjunto. A fusão olha para os diferentes sinais relatados numa subscrição através dos recursos. A fusão encontra padrões que revelam a progressão do ataque ou sinais com informações contextuais partilhadas, indicando que deve usar um procedimento de resposta unificado para eles.

A análise de fusão combina conhecimentos de domínio de segurança com IA para analisar alertas, descobrindo novos padrões de ataque à medida que ocorrem. 

O Centro de Segurança aproveita a MITRE Attack Matrix para associar alertas com a sua perceção de intenção, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando a informação recolhida para cada passo de um ataque, o Centro de Segurança pode excluir atividade que parece ser passos de um ataque, mas na verdade não é.

Como os ataques ocorrem frequentemente em diferentes inquilinos, o Security Center pode combinar algoritmos de IA para analisar sequências de ataque que são relatadas em cada subscrição. Esta técnica identifica as sequências de ataque como padrões de alerta predominantes, em vez de serem apenas associadas uns aos outros.

Durante uma investigação de um incidente, os analistas muitas vezes precisam de um contexto extra para chegar a um veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detetada, sem compreender o que mais está a acontecer na rede ou no que diz respeito ao recurso direcionado, é difícil entender que ações tomar a seguir. Para ajudar, um incidente de segurança pode incluir artefactos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam, dependendo do tipo de ameaça detetada e da configuração do seu ambiente. 

![Screenshot de incidente de segurança detetado relatório](./media/security-center-alerts-cloud-smart/security-incident.png)

Para melhor compreender os incidentes de segurança, veja como lidar com incidentes de segurança no Centro de [Segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-incident)

