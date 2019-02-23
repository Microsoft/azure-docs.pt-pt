---
title: Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação
description: Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734494"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Mapeamento de fluxo de dados de fábrica de dados do Azure existe transformação

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de Exists é uma transformação que interrompe ou permite linhas nos seus dados a fluir através de filtragem de linha. Existe transformar é semelhante à ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Depois de uma transformação de filtro, as linhas resultantes do seu fluxo de dados irão optar por incluir todas as linhas onde os valores de coluna da origem 1 existem na origem 2 ou não existem na origem 2.

![Definições de existe](media/data-flow/exsits.png "existe 1")

Escolha a segunda origem para sua Exists, de modo a que o fluxo de dados pode comparar valores de 1 de Stream contra Stream 2.

Selecione a coluna a partir de 1 de origem e de origem 2 cujos valores que pretende procurar contra Exists ou não existe.
