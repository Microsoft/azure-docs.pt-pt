---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050235"
---
### <a name="app-service-plan"></a>Plano do App Service
Cria o plano de serviço para hospedar a aplicação web. Você fornece o nome do plano através do parâmetro **hostingPlanName.** A localização do plano é o mesmo local utilizado para o grupo de recursos. O nível de preços e o tamanho dos trabalhadores são especificados no **sku** e nos parâmetros **de tamanho dos trabalhadores**

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
