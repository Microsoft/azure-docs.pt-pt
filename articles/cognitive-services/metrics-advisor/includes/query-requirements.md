---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377532"
---
Dentro da consulta utilize o `@StartTime` parâmetro para obter dados métricos para uma determinada hora de tempo. Isto será substituído por uma `yyyy-MM-ddTHH:mm:ss` corda de formato. 

> [!IMPORTANT]
> Certifique-se de que apenas os dados métricos de **um único timetamp** serão devolvidos pela consulta. O Metrics Advisor executará a consulta contra cada timetamp para obter os dados métricos correspondentes. Por exemplo, uma consulta para uma métrica com granularidade *diária* deve conter apenas uma única hora de tempo, como `2020-06-21T00:00:00Z` quando executa a consulta uma vez. 
