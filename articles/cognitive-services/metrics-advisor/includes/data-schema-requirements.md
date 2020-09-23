---
title: Requisitos de esquema de dados
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: a64bb5b28a06d9a013d59e022047f5e2841126ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940136"
---
Metrics Monitor é um serviço de deteção, diagnóstico e análise de anomalias em séries de tempo. Como serviço alimentado por IA, utiliza os seus dados para treinar o modelo utilizado. O serviço aceita tabelas de dados agregados com as seguintes colunas:

* **Medida** (necessária): uma ou mais colunas contendo valores numéricos.
* **Tempotamp** (opcional): zero ou uma coluna com tipo de `DateTime` ou `String` . Quando esta coluna não estiver definida, a tabela de tempos é definida como a hora de início de cada período de ingestão. Formate a hora de se atenção: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **A sua hora de jogo deve coincidir com a granularidade da métrica. Por exemplo, uma métrica diária deve garantir a hora, minuto `00:00:00` e segundo no selo de tempo rotulado como **.
* **Dimensão** (opcional): As colunas podem ser de qualquer tipo de dado. Tenha cuidado ao trabalhar com grandes volumes de colunas e valores, para evitar que um número excessivo de dimensões seja processado.

> [!Note]
> Para cada métrica, deve haver apenas um timetamp por medida, correspondente a uma combinação de dimensão. Agregar os seus dados antes do embarque ou utilizar a consulta para especificar os dados a ingeridos.