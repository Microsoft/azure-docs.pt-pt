---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66167426"
---
Com o Azure Resource Manager, pode definir parâmetros para os valores a utilizar ao implementar o modelo. O modelo inclui um `parameters` seção que contém todos os valores de parâmetro. Cada valor de parâmetro é utilizado pelo modelo para definir os recursos que pretende implementar.

> [!NOTE]
> Não defina parâmetros para valores que permanecem sempre iguais. Defina parâmetros apenas para os valores que variam com base no projeto que está a implementar ou com base no ambiente onde está a implementar.

Quando define parâmetros:

* Para especificar os valores permitidos que um utilizador pode fornecer durante a implementação, utilize o **allowedValues** campo.

* Para atribuir valores predefinidos para o parâmetro quando não forem fornecidos valores durante a implementação, utilize o **defaultValue** campo. 
