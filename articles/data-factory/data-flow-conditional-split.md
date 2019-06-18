---
title: Mapeamento de transformação de Conditional Split de fluxo de dados de fábrica de dados do Azure
description: Transformação de divisão condicional de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795651"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapeamento de fluxo de dados condicional dividir transformação

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![dividir a caixa de ferramentas de condicional](media/data-flow/conditionalsplit2.png "condicional dividir a caixa de ferramentas")

A transformação Conditional Split pode encaminhar linhas de dados para fluxos diferentes dependendo do conteúdo dos dados. A implementação da transformação Conditional Split é semelhante a uma estrutura de decisão maiúsculas numa linguagem de programação. A transformação avalia as expressões e com base nos resultados, direciona a linha de dados para a transmissão em fluxo especificada. Essa transformação também fornece uma saída predefinida, para que se uma linha não corresponde a nenhuma expressão é direcionado para a saída padrão.

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="multiple-paths"></a>Vários caminhos

Para adicionar condições adicionais, selecione "Adicionar Stream" no painel de configuração na parte inferior e clique na caixa de texto do Expression Builder para criar a sua expressão.

![condicional dividir transmissões](media/data-flow/conditionalsplit3.png "condicional dividir transmissões")

## <a name="next-steps"></a>Passos Seguintes

Transformações de fluxo do comuns dados utilizadas com a divisão condicional: [Junte-se a transformação](data-flow-join.md), [Loopup transformação](data-flow-lookup.md), [selecione transformação](data-flow-select.md)
