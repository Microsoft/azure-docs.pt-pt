---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043216"
---
Dentro da consulta utilize o `@StartTime` parâmetro para obter dados métricos para um único timetamp. O Metrics Advisor substituirá o parâmetro por uma `yyyy-MM-ddTHH:mm:ss` cadeia de formato quando executa a consulta.

> [!IMPORTANT]
> A consulta deve voltar no máximo um registo para cada combinação de dimensão, em cada hora de temperatura. E todos os registos devolvidos pela consulta devem ter os mesmos tempos. O Metrics Advisor executará esta consulta para cada timetamp para ingerir os seus dados. Consulte a [secção faq em consultas](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para obter mais informações e exemplos. 