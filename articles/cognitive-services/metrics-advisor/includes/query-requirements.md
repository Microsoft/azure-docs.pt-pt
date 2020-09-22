---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940141"
---
Dentro da consulta utilize o `@StartTime` parâmetro para obter dados métricos para uma determinada hora de tempo. Isto será substituído por uma `yyyy-MM-ddTHH:mm:ss` corda de formato. 

> [!IMPORTANT]
> Certifique-se de que apenas os dados métricos de **um único timetamp** serão devolvidos pela consulta. O Metrics Advisor executará a consulta contra cada timetamp para obter os dados métricos correspondentes. Por exemplo, uma consulta para uma métrica com granularidade *diária* deve conter apenas uma única hora de tempo, como `2020-06-21T00:00:00Z` quando executa a consulta uma vez. 
