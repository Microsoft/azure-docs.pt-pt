---
title: Incidentes no Centro de Segurança Azure - correlações inteligentes de alertas
description: Este tópico explica como a fusão usa a correlação de alerta inteligente em nuvem para gerar incidentes de segurança no Azure Security Center.
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
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613832"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alerta inteligente em nuvem no Centro de Segurança Azure (incidentes)

O Azure Security Center analisa continuamente as cargas de trabalho em nuvem híbrida, utilizando análises avançadas e inteligência de ameaças para alertá-lo sobre atividades maliciosas.

A amplitude da cobertura de ameaças está a aumentar. A necessidade de detetar até mesmo o menor compromisso é importante, e pode ser um desafio para os analistas de segurança triagem dos diferentes alertas e identificar um ataque real. O Centro de Segurança ajuda os analistas a lidar com esta fadiga de alerta. Ajuda a diagnosticar ataques à medida que ocorrem, correlacionando diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

A análise de fusão é a tecnologia e o back end analítico que alimenta os incidentes do Security Center, permitindo-lhe correlacionar diferentes alertas e sinais contextuais em conjunto. A fusão analisa os diferentes sinais relatados numa subscrição através dos recursos. A fusão encontra padrões que revelam progressão de ataque ou sinais com informações contextuais partilhadas, indicando que deve utilizar um procedimento de resposta unificado para eles.

A análise de fusão combina o conhecimento do domínio de segurança com a IA para analisar alertas, descobrindo novos padrões de ataque à medida que ocorrem. 

O Centro de Segurança aproveita a Matriz de Ataque MITRE para associar alertas com a sua perceção de intenção, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando a informação recolhida para cada passo de um ataque, o Centro de Segurança pode excluir atividade que parece ser passos de um ataque, mas na verdade não é.

Como os ataques ocorrem frequentemente em diferentes inquilinos, o Security Center pode combinar algoritmos de IA para analisar sequências de ataque que são reportadas em cada subscrição. Esta técnica identifica as sequências de ataque como padrões de alerta predominantes, em vez de serem acidentalmente associadas umas às outras.

Durante uma investigação de um incidente, os analistas muitas vezes precisam de um contexto extra para chegar a um veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detetada, sem entender o que mais está acontecendo na rede ou no que diz respeito ao recurso direcionado, é difícil entender que ações tomar a seguir. Para ajudar, um incidente de segurança pode incluir artefactos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam, dependendo do tipo de ameaça detetada e da configuração do seu ambiente. 

> [!TIP]
> Para obter uma lista de alertas de incidentes de segurança que podem ser produzidos pela análise de fusão, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Screenshot do incidente de segurança detetado relatório":::


Para melhor compreender os incidentes de segurança, consulte [como gerir incidentes de segurança no Centro de Segurança Azure.](security-center-incident.md)

