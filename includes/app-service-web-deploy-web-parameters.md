---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050223"
---
Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção chamada Parâmetros que contém todos os valores dos parâmetros.
Deve definir um parâmetro para os valores que variarão com base no projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que permaneçam sempre os mesmos. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados. 

Ao definir parâmetros, utilize o campo **Devues autorizado** para especificar quais os valores que um utilizador pode fornecer durante a implementação. Utilize o campo **Devão padrão** para atribuir um valor ao parâmetro, se não for fornecido qualquer valor durante a implementação.

Descreveremos cada parâmetro no modelo.

### <a name="sitename"></a>siteName
O nome da aplicação web que deseja criar.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hospedar O Nome DoPlan
O nome do Plano de Serviço de Aplicações para alojar a aplicação web.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku
O nível de preços para o plano de hospedagem.

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

O modelo define os valores que são permitidos para este parâmetro, e atribui um valor padrão (S1) se não for especificado qualquer valor.

### <a name="workersize"></a>trabalhadorEsize
O tamanho do plano de hospedagem (pequeno, médio ou grande).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

O modelo define os valores que são permitidos para este parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se não for especificado qualquer valor. Os valores correspondem aos pequenos, médios e grandes.

