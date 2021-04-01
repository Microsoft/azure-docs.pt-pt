---
title: Substituir materiais durante a conversão de modelos
description: Explica o material que sobrepundo o fluxo de trabalho no tempo de conversão
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576647"
---
# <a name="override-materials-during-model-conversion"></a>Substituir materiais durante a conversão de modelos

As definições de material no modelo de origem são utilizadas para definir os [materiais PBR utilizados](../../overview/features/pbr-materials.md) pelo renderizador.
Por vezes, a [conversão predefinida](../../reference/material-mapping.md) não dá os resultados desejados e é necessário fazer alterações.
Quando um modelo é convertido para ser utilizado na Renderização Remota Azure, pode fornecer um ficheiro de substituição de material para personalizar como a conversão de material é feita numa base material.
Se um ficheiro chamado `<modelName>.MaterialOverrides.json` for encontrado no recipiente de entrada ao lado do modelo de `<modelName>.<ext>` entrada, então será utilizado como ficheiro de substituição de material.

## <a name="the-override-file-used-during-conversion"></a>O ficheiro de substituição utilizado durante a conversão

Como um exemplo simples, digamos que um modelo de caixa tem um único material, chamado "Padrão".
Além disso, digamos que a sua cor albedo precisa de ser ajustada para utilização em ARR.
Neste caso, um `box.MaterialOverrides.json` ficheiro pode ser criado da seguinte forma:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

O `box.MaterialOverrides.json` ficheiro é colocado no recipiente de entrada ao `box.fbx` lado, o que indica ao serviço de conversão para aplicar as novas definições.

### <a name="color-materials"></a>Materiais de cor

O modelo [de material de cor](../../overview/features/color-materials.md) descreve uma superfície constantemente sombreada que é independente da iluminação.
Os materiais de cor são úteis para os ativos feitos por algoritmos de fotogrammetria, por exemplo.
Nos ficheiros de sobreposição de materiais, um material pode ser declarado como um material de cor, definindo `unlit` para `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignore mapas de textura específicos

Às vezes pode querer que o processo de conversão ignore mapas de textura específicos. Este pode ser o caso quando o seu modelo foi gerado por uma ferramenta que gera mapas especiais não compreendidos corretamente pelo renderizador. Por exemplo, um "OpacityMap" que é usado para definir algo que não seja a opacidade, ou um modelo onde o "NormalMap" é armazenado como "BumpMap". (Neste último caso, pretende ignorar o "NormalMap", o que fará com que o conversor utilize o "BumpMap" como "NormalMap".)

O princípio é simples. Basta adicionar uma propriedade chamada `ignoreTextureMaps` e adicionar qualquer mapa de textura que queira ignorar:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Para obter a lista completa de mapas de textura que pode ignorar, consulte o esquema JSON abaixo.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Aplicação das mesmas sobreposições em vários materiais

Por predefinição, aplica-se uma entrada no ficheiro sobrepõe-se quando o seu nome corresponde exatamente ao nome do material.
Uma vez que é bastante comum que a mesma sobreposição se aplique a vários materiais, pode opcionalmente fornecer uma expressão regular como o nome de entrada.
O campo `nameMatching` tem um valor predefinido, `exact` mas pode ser definido para indicar que a entrada deve `regex` aplicar-se a todos os materiais correspondentes.
A sintaxe utilizada é a mesma utilizada para o JavaScript. O exemplo a seguir mostra uma sobreposição que se aplica a materiais com nomes como "Material2", "Material01" e "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

No máximo, uma entrada num ficheiro de sobreposição de material aplica-se a um único material.
Se houver uma correspondência exata (isto `nameMatching` é, ausente ou igual `exact` ) para o nome do material, então essa entrada é escolhida.
Caso contrário, é escolhida a primeira entrada regex no ficheiro que corresponda ao nome do material.

### <a name="getting-information-about-which-entries-applied"></a>Obtenção de informações sobre quais as entradas aplicadas

O [ficheiro de informações](get-information.md#information-about-a-converted-model-the-info-file) escrito ao recipiente de saída contém informações sobre o número de sobreposições fornecidas e o número de materiais que foram ultrapassados.

## <a name="json-schema"></a>Esquema JSON

O esquema completo do JSON para ficheiros de materiais é dado aqui. Com exceção de `unlit` `ignoreTextureMaps` e, as propriedades disponíveis são um subconjunto das propriedades descritas nas secções sobre os [modelos de material de cor](../../overview/features/color-materials.md) e material [PBR.](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Materiais de cor](../../overview/features/color-materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)
