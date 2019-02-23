---
title: Separador otimizar o fluxo de dados de mapeamento de fábrica de dados do Azure
description: Otimizar o mapeamento de fluxos de dados com configurações de partição do guia de otimização de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 3802a8475d8a39a2f275dbc7fcf21ce69892a117
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728782"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Separador de otimizar a transformação de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Cada transformação Data Flow tem uma guia "Otimizar". A guia de otimização contém definições opcionais para configurar os esquemas de particionamento para fluxos de dados.

<img src="media/data-flow/opt001.png" width="800">

A predefinição é "usar o particionamento atual". Criação de partições atual dá instruções ao Azure Data Factory para utilizar o esquema de particionamento nativo para os dados fluem em execução no Spark no Azure Databricks. Geralmente, esta é a abordagem recomendada.

No entanto, existem instâncias em que pode pretender ajustar a criação de partições. Por exemplo, se quiser as transformações para um único ficheiro no lake de saída, em seguida, optou por "partição única" na guia otimizar para criação de partições na transformação de Sink.

É outro caso em que pode ser útil ter controlo sobre esquemas de particionamento a ser utilizado para as transformações de dados em termos de desempenho. Ajustar a criação de partições de dados fornece um nível de controle sobre a distribuição dos seus dados em nós de computação e otimizações de localidade de dados que podem ter efeitos tanto positivos, bem como negativos sobre o desempenho do fluxo de dados global.

Se desejar alterar a criação de partições em qualquer transformação, simplesmente clique no separador de otimizar e selecione o botão de opção "Definir a criação de partições". , Em seguida, será apresentada uma série de opções para criação de partições. O melhor método de criação de partições para implementar serão diferentes com base nos seus volumes de dados, chaves de Release candidate, valores nulos e cardinalidade. Melhor prática é começar com a criação de partições de predefinido e, em seguida, tente as diferentes opções de criação de partições. Pode testar com a depuração executar no Pipeline e, em seguida, ver o tempo gasto em cada transformação de agrupamento, bem como a utilização de partição da vista de monitorização.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Round Robin

Round Robin é simple partição que distribui automaticamente dados igualmente entre partições. Utilize Round Robin quando não tem bons candidatos chave para implementar uma estratégia de particionamento sólida, inteligente. Pode definir o número de partições físicas.

### <a name="hash"></a>Hash

O Azure Data Factory irá produzir um hash de colunas para produzir partições uniformes, de modo que as linhas com valores semelhantes se encaixam na mesma partição. Quando utilizar a opção de Hash, teste para a partição possível distorção. Pode definir o número de partições físicas.

### <a name="dynamic-range"></a>Intervalo dinâmico

Intervalo dinâmico irá utilizar intervalos de dinâmicos de Spark com base nas colunas ou expressões que fornecer. Pode definir o número de partições físicas. 

### <a name="fixed-range"></a>Intervalo fixo

Tem de criar uma expressão que fornece um intervalo fixo de valores dentro de suas colunas de dados particionados. Deve ter uma boa compreensão dos seus dados antes de utilizar esta opção para evitar a distorção de partição. O valor que introduzir para a expressão será utilizado como parte de uma função de partição. Pode definir o número de partições físicas.

### <a name="key"></a>Chave

Se tiver uma boa compreensão da cardinalidade dos seus dados, a chave de criação de partições pode ser uma estratégia de partição seja segura. Chave de criação de partições, irá criar partições para cada valor exclusivo em sua coluna. Não é possível definir o número de partições porque o número será baseado em valores exclusivos nos dados.
