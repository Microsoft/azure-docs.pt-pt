---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050235"
---
### <a name="app-service-plan"></a>Plano do Serviço de Aplicações
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
