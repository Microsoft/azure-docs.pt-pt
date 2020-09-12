---
title: Conversão de modelo
description: Descreve o processo de conversão de um modelo de renderização
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 49d3fd953d069f4368d28e26265114e574e8100a
ms.sourcegitcommit: 655e4b75fa6d7881a0a410679ec25c77de196ea3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89506666"
---
# <a name="convert-models"></a>Converter modelos

A renderização remota Azure permite-lhe tornar modelos muito complexos. Para obter o máximo desempenho, os dados devem ser pré-processados para estarem num formato ideal. Dependendo da quantidade de dados, este passo pode demorar um pouco. Seria impraticável se este tempo fosse gasto durante o carregamento de modelos. Além disso, seria um desperdício repetir este processo para várias sessões. Por estas razões, o serviço ARR oferece um serviço de *conversão*dedicado, que pode funcionar com antecedência.
Uma vez convertido, um modelo pode ser carregado a partir de uma Conta de Armazenamento Azure.

## <a name="supported-source-formats"></a>Formatos de origem suportados

O serviço de conversão suporta estes formatos:

- **FBX**  (versão 2011 e acima)
- **GLTF** (versão 2.x)
- **GLB**  (versão 2.x)

Existem pequenas diferenças entre os formatos no que diz respeito à conversão de propriedade material, conforme listado no mapeamento de material do capítulo [para formatos de modelos.](../../reference/material-mapping.md)

## <a name="the-conversion-process"></a>O processo de conversão

1. [Prepare dois recipientes de armazenamento Azure Blob:](blob-storage.md)um para entrada, um para saída
1. Faça o upload do seu modelo para o recipiente de entrada (opcionalmente sob um subpatão)
1. Desencadear o processo de conversão através [da API de conversão de modelo](conversion-rest-api.md)
1. Sondagem do serviço para o progresso da conversão
1. Uma vez terminado, carregue um modelo
    - a partir de uma conta de armazenamento ligada (ver os passos "Link storage accounts" na [Criar uma Conta](../create-an-account.md#link-storage-accounts) para ligar a sua conta de armazenamento)
    - ou fornecendo uma *Assinatura de Acesso Partilhado (SAS)*.

Todos os dados do modelo (entrada e saída) são armazenados no armazenamento de bolhas Azure fornecido pelo utilizador. A renderização remota Azure dá-lhe total controlo sobre a sua gestão de ativos.

## <a name="pricing"></a>Preços

Para obter informações sobre preços para conversão, consulte a página [de preços de renderização remota.](https://azure.microsoft.com/pricing/details/remote-rendering)


## <a name="conversion-parameters"></a>Parâmetros de conversão

Para as várias opções de conversão, consulte [este capítulo.](configure-model-conversion.md)

## <a name="examples"></a>Exemplos

- [Quickstart: Converter um modelo de renderização](../../quickstarts/convert-model.md) é uma introdução passo a passo como converter um modelo.
- [Os scripts Exemplo PowerShell](../../samples/powershell-example-scripts.md), que demonstram a utilização do serviço de conversão, podem ser encontrados no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts.*

## <a name="next-steps"></a>Próximos passos

- [Utilizar o Armazenamento de Blobs do Azure para conversão de modelos](blob-storage.md)
- [A conversão do modelo REST API](conversion-rest-api.md)
- [Configurar a conversão do modelo](configure-model-conversion.md)
- [Preparação de ficheiros para conversão](layout-files-for-conversion.md)
- [Mapeamento de materiais para formatos de modelos](../../reference/material-mapping.md)
