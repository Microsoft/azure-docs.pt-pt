---
title: Requisitos de esquema de dados
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231434"
---
Metrics Advisor é um serviço de deteção, diagnóstico e análise de anomalias em séries de tempo. Como serviço alimentado por IA, utiliza os seus dados para treinar o modelo utilizado. O serviço aceita tabelas de dados agregados com as seguintes colunas:

* **Medida** (necessária): uma ou mais colunas contendo valores numéricos.
* **Tempotamp** (opcional): zero ou uma coluna com tipo de `DateTime` ou `String` . Quando esta coluna não estiver definida, a tabela de tempos é definida como a hora de início de cada período de ingestão. Formate a hora de se atenção: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **A sua hora de jogo deve coincidir com a granularidade da métrica. Por exemplo, uma métrica diária deve garantir a hora, minuto `00:00:00` e segundo no selo de tempo rotulado como**.
* **Dimensão** (opcional): As colunas podem ser de qualquer tipo de dado. Tenha cuidado ao trabalhar com grandes volumes de colunas e valores, para evitar que um número excessivo de dimensões seja processado.

> [!Note]
> Para cada métrica, deve haver apenas um timetamp por medida, correspondente a uma combinação de dimensão. Agregar os seus dados antes do embarque ou utilizar a consulta para especificar os dados a ingeridos.