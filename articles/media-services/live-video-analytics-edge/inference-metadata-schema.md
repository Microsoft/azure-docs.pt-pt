---
title: Esquema de metadados de inferência - Azure
description: Neste artigo, você vai aprender sobre esquema de metadados de inferência.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019499"
---
# <a name="inference-metadata-schema"></a>Esquema de metadados de inferência 

Cada objeto de inferência, independentemente da utilização de contrato baseado em HTTP ou contrato baseado em gRPC, segue o modelo de objeto descrito neste tópico.

## <a name="object-model"></a>Modelo de objeto

![Modelo de objeto](./media/inference-metadata-schema/object-model.png)
 
|Definição de Tipo|Description|
|---|---|
|Etiqueta|Etiqueta ou etiqueta associada ao resultado. Alng com marcação, até obtém o valor de confiança associado com a etiqueta.|
|Atributo|Atributos adicionais associados ao resultado. Pode adicionar novos atributos que recebe do motor de inferencing juntamente com o valor de confiança.|
|Lista de Atributos|Lista de atributos opcionais.|
|Retângulo|Região retangular relativa à imagem no canto superior esquerdo. As propriedades requeridas serão "comprimento", "largura", altura" e "distância superior da borda da origem".|
|Classificação|Rótulo de classificador frequentemente aplicável a toda a amostra. Com a ajuda de "tag" pode classificar o resultado.|
|Entidade|Entidade detetada ou identificada na amostra. Quando uma entidade é detetada pelo motor de inferencing, obtém uma "etiqueta", atributos adicionais que foram deduzidos e as coordenadas de uma caixa retangular em torno da entidade encontrada são devolvidas.|
|Evento|Evento detetado na amostra. Quando um evento é detetado na amostra, o nome do evento e as propriedades específicas do evento são devolvidos.|
|Movimento|Movimento detetado na amostra. Quando o movimento é detetado na amostra, as coordenadas de uma caixa de delimitação retangular onde o movimento é detetado, são devolvidas.|
|Texto|O texto associado à amostra juntamente com o tempo de início e fim do texto é devolvido.|
|Outro|Devolve outras informações genéricas de carga útil.|

O exemplo abaixo contém um único evento com alguns tipos de inferência suportados:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Passos seguintes

- [Contrato de dados gRPC](./grpc-extension-protocol.md)
- [Contrato de dados HTTP](./http-extension-protocol.md)