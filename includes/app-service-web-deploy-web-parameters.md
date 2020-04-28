---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184067"
---
Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção chamada Parâmetros que contém todos os valores dos parâmetros.
Deve definir um parâmetro para os valores que variarão em função do projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que se mantenham sempre os mesmos. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados. 

Ao definir parâmetros, utilize o campo **Values permitido** para especificar quais os valores que um utilizador pode fornecer durante a implementação. Utilize o campo **predefiniçãoValue** para atribuir um valor ao parâmetro, se não for fornecido qualquer valor durante a implantação.

Descreveremos cada parâmetro no modelo.

### <a name="sitename"></a>siteName
O nome da aplicação web que pretende criar.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hospedagemPlanName
O nome do plano do App Service para hospedar a aplicação web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
O nível de preços para o plano de hospedagem.

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

O modelo define os valores que são permitidos para este parâmetro, e atribui um valor predefinido (S1) se não for especificado qualquer valor.

### <a name="workersize"></a>trabalhadorTamanho
O tamanho da instância do plano de acolhimento (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

O modelo define os valores que são permitidos para este parâmetro (0, 1 ou 2), e atribui um valor padrão (0) se não for especificado qualquer valor. Os valores correspondem a pequenos, médios e grandes.

