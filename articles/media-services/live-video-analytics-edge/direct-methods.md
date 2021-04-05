---
title: Utilize métodos diretos em Live Video Analytics em IoT Edge - Azure
description: Live Video Analytics on IoT Edge expõe vários métodos diretos. Os métodos diretos baseiam-se nas convenções descritas neste tópico.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656245"
---
# <a name="direct-methods"></a>Métodos diretos

Live Video Analytics on IoT Edge expõe vários métodos diretos que podem ser invocados a partir do IoT Hub. Os métodos diretos representam uma interação de resposta de pedido com um dispositivo semelhante a uma chamada HTTP na medida em que eles sucedem ou falham imediatamente (após um tempo limite especificado pelo utilizador). Esta abordagem é útil para cenários onde as medidas imediatas variam consoante a capacidade de resposta do dispositivo. Para mais informações, consulte [Compreender e invocar métodos diretos do IoT Hub.](../../iot-hub/iot-hub-devguide-direct-methods.md)

Este tópico descreve estes métodos e convenções.

## <a name="conventions"></a>Convenções

Os métodos diretos baseiam-se nas seguintes convenções:

1. Os métodos diretos têm uma versão especificada no MAJOR. Formato MINOR (como mostrado no exemplo seguinte):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Uma determinada versão do live video analytics no módulo IoT Edge irá suportar todos os métodos diretos até à sua versão atual. Por exemplo, a versão 1.3 do módulo irá suportar métodos diretos com versões 1.3, 1.2, 1.1 e 1.0.
3. Todos os métodos diretos são sincronizados.
4. Os resultados de erro seguem o esquema de erro do OData.
5. Os nomes devem respeitar os seguintes constrangimentos:
    
    * Apenas caracteres alfanuméricos e traços desde que não comece e termine com um traço
    * Sem espaços
    * Max de 32 caracteres

### <a name="example"></a>Exemplo

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Códigos de erro de nível superior     

|Estado |Código   |Mensagem|
|---|---|---|
|400|   BadRequest| Pedido não é válido|
|400|   InvalidResource|    Recurso não é válido|
|400|   InvalidVersion| A versão API não é válida|
|402|   ConectividadeRequirada    |O módulo edge requer que a conectividade da nuvem funcione corretamente.|
|404|   NotFound    |Recurso não foi encontrado|
|409|   Conflito|   Conflito de operações|
|500|   InternalServerError|    Erro de servidor interno|
|503|   ServerBusy| Servidor ocupado|

### <a name="detailed-error-codes"></a>Códigos de erro detalhados

Erro de validações detalhadas, como validações de módulos de gráfico, são adicionados como detalhes de erro:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Estado|    Código detalhado   |Description|
|---|---|---|
|400|   GraphValidationError|   Erros gráficos gerais, tais como ciclos ou divisórias, etc.|
|400|   MóduloValidationError|  Erros de validação específicos do módulo.|
|409|   GraphTopologyInUse| A topologia do gráfico ainda é referenciada por uma ou mais instâncias de gráfico.|
|409|   OperaçãoNotAllowedInState| A operação solicitada não pode ser realizada no estado atual.|
|409|   RecursoValidationError|    O recurso referenciado (exemplo: ativo) não se encontra num estado válido.|

## <a name="details"></a>Detalhes  

### <a name="graphtopologyget"></a>GraphTopologyGet

Este método direto recupera uma topologia de gráfico único.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Entidade encontrada | 200 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Entidade não encontrada | 404 |  |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Cria uma única topologia se não houver uma existente com o nome ou atualizações e existente com o mesmo nome.

Aspectos-chave:

* Topologia pode ser livremente atualizada é que não há nenhum gráfico referenciando-o.
* A topologia pode ser atualizada livremente se todos os gráficos de referência forem desativados desde que:

    * Os parâmetros recém-adicionados têm valores padrão
    * Os parâmetros removidos não são referenciados por nenhum gráfico
* Atualizações de topologia não são permitidas se houver gráficos ativos

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de estado

|Condição  |Código de estado    |Código de erro detalhado|
|---|---|---|
Entidade existente atualizada |200|   N/D|
Nova entidade criada  |201|   N/D|
Erros gerais do utilizador |Gama 400  ||
Erros de validação de gráficos |400    |GraphValidationError|
Erros de validação do módulo|   400 |MóduloValidationError|
Erros gerais do servidor   |Gama 500  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Elimina uma topologia de um único gráfico.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Entidade eliminada | 200 | N/D |
| Entidade não encontrada | 204 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Topologia de gráficos está sendo referenciada por uma ou mais instâncias de gráfico | 409 | GraphTopologyInUse |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphtopologylist"></a>Lista de Graftopologia

Recupera uma lista de todas as topologias de gráficos que correspondem aos critérios do filtro.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Suporte de filtro

|Operação |Campo(s)    |Operadores|
|---|---|---|
|$orderby|name  |asc|


#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Com êxito | 200 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Recupera uma única instância de gráfico:

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Entidade encontrada | 200 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Entidade não encontrada | 404 |  |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Cria uma única Instância de Gráfico se não existir uma existente com o nome ou atualizações e existente com o mesmo nome.

Aspectos-chave:

* A instância do gráfico pode ser atualizada livremente enquanto está no estado "Desativado".

    * O gráfico é revalidado em cada atualizado.
* As atualizações de instâncias de gráfico são parcialmente restritas enquanto o gráfico não está no estado "Inativo".
* As atualizações de instâncias de gráfico não são permitidas em gráficos ativos.

#### <a name="request"></a>Pedir

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Resposta

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Entidade existente atualizada | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Erros de validação de gráficos | 400 | GraphValidationError |
| Erros de validação do módulo | 400 | MóduloValidationError |
| Erros de validação de recursos | 409 | RecursoValidationError |
| Erros gerais do servidor | Gama 500 |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Elimina uma única instância de gráfico.

Aspectos-chave:

* Apenas gráficos desativados podem ser eliminados.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Gráfico eliminado com sucesso | 200 | N/D |
| Gráfico não encontrado | 204 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| O gráfico não está no estado "Parado" | 409 | OperaçãoNotAllowedInState |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Isto é semelhante ao GraphTopologyList. Permite a utilização para enumerar as instâncias do gráfico.
Recupera uma lista de todos os casos de gráficos que correspondem aos critérios do filtro.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Suporte de filtro

|Operação  |   Campo(s)|   Operadores|
|---|---|---|
|$orderby|  name|   asc|

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Com êxito | 200 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Ativa uma única instância de gráfico. 

Aspectos-chave

* O método só retorna quando o gráfico é ativado 
* O gráfico pressupõe o estado de "Ativação" durante a sua ativação.

    * Uma operação list/get no gráfico devolveria o gráfico no estado adequado.
* Idempoteência:

    * Iniciar um gráfico no estado de "Ativação" comporta-se da mesma forma que se o gráfico foi desativado (isto é: blocos de chamadas até que o gráfico seja ativado)
    * Ativar um gráfico no estado "Ative" regressa imediatamente com sucesso.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de estado

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Gráfico ativado com sucesso | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| Erros de validação do módulo | 400 | MóduloValidationError |
| Erros de validação de recursos | 409 | RecursoValidationError |
| O gráfico está em estado de desativar | 409 | OperaçãoNotAllowedInState |
| Erros gerais do servidor | Gama 500 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivo

Desativa uma única instância de gráfico. Desativar um gráfico desativa graciosamente o processamento dos meios de comunicação e garante que todos os eventos e meios de comunicação armazenados transitoriamente no limite estão comprometidos com a nuvem, sempre que aplicável.

Aspectos-chave:

* O método só retorna quando o gráfico é desativado
* O gráfico pressupõe o estado de "desativar" enquanto é desativado.

    * Uma operação list/get no gráfico devolveria o gráfico no estado adequado.
    * Pare apenas completa quando todos os meios de comunicação edurem para a nuvem.
* Idempoteência:

    * Desativar um gráfico sobre o estado de "Desativar" comporta-se da mesma forma que se o gráfico fosse desativado (isto é: blocos de chamadas até que o gráfico seja desativado)
    * Desativar um gráfico no estado "Inativo" regressa imediatamente com sucesso.

#### <a name="request"></a>Pedir

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

| Condição | Código de estado | Código de erro detalhado |
|--|--|--|
| Gráfico ativado com sucesso | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do utilizador | Gama 400 |  |
| O gráfico está em estado de ativação | 409 | OperaçãoNotAllowedInState |
| Erros gerais do servidor | Gama 500 |  |

## <a name="next-steps"></a>Passos seguintes

[Esquema de configuração de Módulo Duplo](module-twin-configuration-schema.md)
