---
title: Diferenças entre APIs de gestão e APIs de serviço - Lote Azure [ Lote De Serviço ] Microsoft Docs
description: As APIs funcionam nas diferentes camadas do serviço Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672769"
---
# <a name="service-level-and-management-level-apis"></a>APIs nível de serviço e de nível de gestão

O Azure Batch tem dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gestão. O nome é muitas vezes semelhante, mas devolvem resultados diferentes. Se deseja registos de atividade, então precisa de usar as APIs de gestão. As APIs de nível de serviço contornam a camada de Gestão de Recursos Azure e não estão registadas.


A gestão de lotes e o serviço de lote têm APIs para Pool, por exemplo. 
- Esta API para eliminar o pool é direcionada diretamente na conta do lote:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Esta API para https://docs.microsoft.com/rest/api/batchmanagement/pool/delete eliminar a piscina é direcionada para a camada management.azure.com.

