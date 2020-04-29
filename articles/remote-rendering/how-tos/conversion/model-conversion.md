---
title: Conversão de modelo
description: Descreve o processo de conversão de um modelo para renderização
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681496"
---
# <a name="convert-models"></a>Converter modelos

A Renderização Remota Azure permite-lhe renderizar modelos muito complexos. Para alcançar o máximo desempenho, os dados devem ser pré-processados para estarem num formato ideal. Dependendo da quantidade de dados, este passo pode demorar um pouco. Seria impraticável, se este tempo fosse gasto durante o carregamento de modelos. Além disso, seria um desperdício repetir este processo para várias sessões. Por estas razões, o serviço ARR fornece um serviço de *conversão*dedicado, que pode correr com antecedência.
Uma vez convertido, um modelo pode ser carregado a partir de uma Conta de Armazenamento Azure.

## <a name="supported-source-formats"></a>Formatos de origem suportados

O serviço de conversão suporta estes formatos:

- **FBX** (versão 2011 e acima)
- **GLTF** (versão 2.x)
- **GLB** (versão 2.x)

Existem pequenas diferenças entre os formatos no que diz respeito à conversão de propriedade material, tal como listado no mapeamento de materiais de capítulo [para formatos de modelos.](../../reference/material-mapping.md)

## <a name="the-conversion-process"></a>O processo de conversão

1. [Preparar dois recipientes de armazenamento Azure Blob:](blob-storage.md)um para entrada, um para a saída
1. Faça upload do seu modelo para o recipiente de entrada (opcionalmente sob uma subpatia)
1. Desencadear o processo de conversão através [da conversão do modelo REST API](conversion-rest-api.md)
1. Sondagem o serviço para o progresso da conversão
1. Uma vez terminado, carregue um modelo
    - a partir de uma conta de armazenamento ligada (ver os passos "Conta de armazenamento de link" em [Criar uma Conta](../create-an-account.md#link-storage-accounts) para ligar a sua conta de armazenamento)
    - ou fornecendo uma Assinatura de *Acesso Partilhado (SAS)*.

Todos os dados do modelo (entrada e saída) são armazenados no utilizador desde o armazenamento de blob Azure. A Renderização Remota Azure dá-lhe total controlo sobre a gestão de ativos.

## <a name="conversion-parameters"></a>Parâmetros de conversão

Para as várias opções de conversão, consulte [este capítulo.](configure-model-conversion.md)

## <a name="examples"></a>Exemplos

- [Quickstart: Converter um modelo para renderização](../../quickstarts/convert-model.md) é uma introdução passo a passo como converter um modelo.
- [Exemplo, scripts PowerShell](../../samples/powershell-example-scripts.md), que demonstram a utilização do serviço de conversão, podem ser encontrados no [repositório](https://github.com/Azure/azure-remote-rendering) de amostras DE ARR na pasta *Scripts.*

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Armazenamento de Blobs do Azure para conversão de modelos](blob-storage.md)
- [A conversão do modelo REST API](conversion-rest-api.md)
- [Configurar a conversão do modelo](configure-model-conversion.md)
- [Mapeamento de materiais para formatos de modelos](../../reference/material-mapping.md)
