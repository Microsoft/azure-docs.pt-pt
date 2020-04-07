---
title: Materiais de sobreposição durante a conversão do modelo
description: Explica o fluxo de trabalho sobrepor-se ao material no tempo de conversão
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681483"
---
# <a name="override-materials-during-model-conversion"></a>Materiais de sobreposição durante a conversão do modelo

Durante a conversão, as definições de material no modelo de origem são utilizadas para definir os [materiais PBR utilizados](../../overview/features/pbr-materials.md) pelo renderizador.
Às vezes, a [conversão por defeito](../../reference/material-mapping.md) não dá os resultados desejados e é preciso fazer alterações.
Quando um modelo é convertido para utilização em Renderização Remota Azure, pode fornecer um ficheiro de sobreposição de material para personalizar como a conversão de material é feita por material.
A secção de [reconfiguração](configure-model-conversion.md) da conversão do modelo tem instruções para declarar o nome de ficheiro de substituição do material.

## <a name="the-override-file-used-during-conversion"></a>O ficheiro de substituição utilizado durante a conversão

Como exemplo simples, digamos que um modelo de caixa tem um único material, chamado "Padrão". A cor albedo precisa de ser ajustada para utilização em ARR.
Neste caso, `box_materials_override.json` um ficheiro pode ser criado da seguinte forma:

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

O `box_materials_override.json` ficheiro é colocado no recipiente `ConversionSettings.json` de entrada, e um é adicionado ao lado, `box.fbx`que indica a conversão onde encontrar o ficheiro de substituição (ver [Configurar a conversão do modelo):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

Quando o modelo for convertido, as novas definições serão aplicadas.

### <a name="color-materials"></a>Materiais de cor

O modelo de material de [cor](../../overview/features/color-materials.md) descreve uma superfície constantemente sombreada que é independente da iluminação.
Isto é útil para ativos feitos por algoritmos de fotogrammetria, por exemplo.
Em ficheiros de sobreposição de material, um material `unlit` `true`pode ser declarado como um material de cor, definindo para .

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

Às vezes, pode querer que o processo de conversão ignore mapas de textura específicos. Este pode ser o caso quando o seu modelo foi gerado por uma ferramenta que gera mapas especiais não compreendidos corretamente pelo renderizador. Por exemplo, um "OpacityMap" que é usado para definir algo diferente da opacidade, ou um modelo onde o "NormalMap" é armazenado como "BumpMap". (Neste último caso, pretende ignorar o "NormalMap", o que fará com que o conversor utilize o "BumpMap" como "NormalMap".)

O princípio é simples. Basta adicionar uma `ignoreTextureMaps` propriedade chamada e adicionar qualquer mapa de textura que queira ignorar:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Para obter a lista completa de mapas de textura que pode ignorar, consulte o esquema JSON abaixo.

## <a name="json-schema"></a>Esquema jSON

O esquema completo da JSON para ficheiros de materiais é dado aqui. Com exceção `unlit` `ignoreTextureMaps`e, as propriedades disponíveis são um subconjunto das propriedades descritas nas secções do material de [cor](../../overview/features/color-materials.md) e dos modelos de [materiais PBR.](../../overview/features/pbr-materials.md)

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