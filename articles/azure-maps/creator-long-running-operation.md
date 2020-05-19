---
title: Azure Maps Operação De longa duração API
description: Saiba sobre o processamento de fundo assíncrono de longa duração no Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 360fc4af688e393bb8639ee773f0bf0de603a425
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598389"
---
# <a name="creator-long-running-operation-api"></a>Criador de Longa Operação API

Alguns API em Mapas Azure utilizam um [padrão assíncrono de resposta a pedidos.](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) Este padrão permite que o Azure Maps forneça serviços altamente disponíveis e responsivos. Este artigo explica a implementação específica do Azure Map de processamento de fundo assíncrono de longa duração.

## <a name="submitting-a-request"></a>Envio de um pedido

Uma aplicação de cliente inicia uma operação de longa duração através de uma chamada sincronizada para um Http API. Normalmente, esta chamada é sob a forma de um pedido HTTP POST. Quando uma carga de trabalho assíncrona for criada com sucesso, a API devolverá um código de `202` estado HTTP, indicando que o pedido foi aceite. Esta resposta contém um `Location` cabeçalho que aponta para um ponto final que o cliente pode fazer uma sondagem para verificar o estado da operação de longa duração.

### <a name="example-of-a-success-response"></a>Exemplo de uma resposta de sucesso

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Se a chamada não passar na validação, a API irá, em vez disso, devolver uma resposta HTTP `400` para um Pedido Mau. O órgão de resposta fornecerá ao cliente mais informações sobre o motivo pelo qual o pedido foi inválido.

### <a name="monitoring-the-operation-status"></a>Monitorização do estado de funcionamento

O ponto final de localização fornecido nos cabeçalhos de resposta aceites pode ser pesquisado para verificar o estado da operação de longa duração. O organismo de resposta do pedido de estado de operação conterá sempre `status` as propriedades e as `createdDateTime` propriedades. A `status` propriedade mostra o estado atual da operação de longa duração. Os estados possíveis `"NotStarted"` `"Running"` incluem, e `"Succeeded"` `"Failed"` . A propriedade mostra o momento em que `createdDateTime` o pedido inicial foi feito para iniciar a operação de longa duração. Quando o estado for `"NotStarted"` `"Running"` ou, um `Retry-After` cabeçalho também será fornecido com a resposta. O `Retry-After` cabeçalho, medido em segundos, pode ser utilizado para determinar quando deve ser feita a próxima chamada de votação para o estado de funcionamento API.

### <a name="example-of-running-a-status-response"></a>Exemplo de execução de uma resposta de estado

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Conclusão da operação de manuseamento

Após a conclusão da operação de longa duração, o estado da resposta será `"Succeeded"` ou `"Failed"` . Quando um novo recurso for criado a partir de uma operação de longa duração, a resposta de sucesso devolverá um código de `201 Created` estado HTTP. A resposta também conterá um `Location` cabeçalho que aponta para metadados sobre o recurso. Quando nenhum novo recurso tiver sido criado, a resposta de sucesso devolverá um código de `200 OK` estado HTTP. Após uma falha, o código de estado de resposta também será o `200 OK` código. No entanto, a resposta terá uma `error` propriedade no corpo. Os dados de erro aderem à especificação de erro OData.

### <a name="example-of-success-response"></a>Exemplo de resposta ao sucesso

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Exemplo de resposta a falhas

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
