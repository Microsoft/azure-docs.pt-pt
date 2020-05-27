---
title: Diferenças entre APIs de gestão e APIs de serviço
description: As APIs funcionam nas diferentes camadas do serviço Azure Batch.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867608"
---
# <a name="service-level-and-management-level-apis"></a>APIs nível de serviço e de nível de gestão

O Azure Batch tem dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gestão. O nome é muitas vezes semelhante, mas devolvem resultados diferentes. Se deseja registos de atividade, então precisa de usar as APIs de gestão. As APIs de nível de serviço contornam a camada de Gestão de Recursos Azure e não estão registadas.


A gestão de lotes e o serviço de lote têm APIs para Pool, por exemplo. 
- Esta API para eliminar o pool é direcionada diretamente na conta do lote:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Esta API para eliminar a piscina https://docs.microsoft.com/rest/api/batchmanagement/pool/delete é direcionada para a camada management.azure.com.

