---
title: Selecione um domínio para um projeto de visão personalizada - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como selecionar um domínio para o seu projeto no Serviço de Visão Personalizada.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409387"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecione um domínio para um projeto de Visão Personalizada

A partir da lâmina de definições para o seu projeto Visão Personalizada, pode selecionar um domínio para o seu projeto. Escolha o domínio mais próximo do seu cenário.

## <a name="image-classification"></a>Classificação de Imagens

|Domínio|Objetivo|
|---|---|
|__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Comida__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Se quiser classificar fotografias de frutas ou vegetais individuais, utilize o domínio Food.|
|__Pontos de referência__|Otimizado para marcos reconhecíveis, naturais e artificiais. Este domínio funciona melhor quando o marco é claramente visível na fotografia. Este domínio funciona mesmo que o marco seja ligeiramente obstruído por pessoas à sua frente.|
|__Retail__|Otimizado para imagens que são encontradas em um catálogo de compras ou site de compras. Se quiser classificar de alta precisão entre vestidos, calças e camisas, use este domínio.|
|__Domínios compactos__| Otimizado para os constrangimentos da classificação em tempo real em dispositivos de borda.|

## <a name="object-detection"></a>Deteção de Objetos

|Domínio|Objetivo|
|---|---|
|__Geral__| Otimizado para uma ampla gama de tarefas de deteção de objetos. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Logótipo__|Otimizado para encontrar logotipos da marca em imagens.|
|__Produtos nas prateleiras__|Otimizado para detetar e classificar produtos nas prateleiras.|
|__Domínios compactos__| Otimizado para os constrangimentos da deteção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. Na API de pré-visualização pública Custom Vision 3.4, pode obter uma lista das plataformas exportáveis para domínios compactos, ligando para a API GetDomains.

O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela abaixo, reportamos o tamanho do modelo e o tempo de inferência no Intel Desktop CPU e no NVidia GPU \[ 1 \] . Estes números não incluem tempo de pré-processamento e pós-processamento.

|Tarefa|Domínio|Tamanho do Modelo|Tempo de inferência do CPU|Tempo de inferência da GPU|
|---|---|---|---|---|
|Classificação|General (compact) (Geral [compacto])|5 MB|13 ms|5 ms|
|Deteção de Objetos|General (compact) (Geral [compacto])|45 MB|35 ms|5 ms|
|Deteção de Objetos|Geral (compacto) [S1]|14 MB|27 ms|7 ms|

>[!NOTE]
>O domínio __geral (compacto)__ para deteção de objetos requer uma lógica especial de pós-processamento. Para obter os detalhes, consulte um roteiro de exemplo no pacote zip exportado. Se precisar de um modelo sem a lógica de pós-processamento, utilize __o General (compacto) [S1]__.

>[!IMPORTANT]
>Não há garantias de que os modelos exportados dêem exatamente o mesmo resultado que a previsão da API na nuvem. Uma ligeira diferença na plataforma de execução ou na implementação do pré-processamento pode causar uma maior diferença nas saídas do modelo. Para obter os detalhes da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
