---
title: Mapeamento de transformação de Conditional Split de fluxo de dados de fábrica de dados do Azure
description: Transformação de divisão condicional de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727813"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Mapeamento de transformação de Conditional Split de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação Conditional Split pode encaminhar linhas de dados para fluxos diferentes dependendo do conteúdo dos dados. A implementação da transformação Conditional Split é semelhante a uma estrutura de decisão maiúsculas numa linguagem de programação. A transformação avalia as expressões e com base nos resultados, direciona a linha de dados para a transmissão em fluxo especificada. Essa transformação também fornece uma saída predefinida, para que se uma linha não corresponde a nenhuma expressão é direcionado para a saída padrão.

![divisão condicional](media/data-flow/cd1.png "divisão condicional")

Para adicionar condições adicionais, selecione "Adicionar Stream" no painel de configuração na parte inferior e clique na caixa de texto do Expression Builder para criar a sua expressão.
