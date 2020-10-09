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
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127779"
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

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela abaixo, reportamos o tamanho do modelo e o tempo de inferência no Intel Desktop CPU e no NVidia GPU \[ 1 \] . 

> [!NOTE]
> Estes números não incluem tempo de pré-processamento e pós-processamento.

|Tarefa|Domínio|Tamanho do modelo|Tempo de inferência do CPU|Tempo de inferência da GPU|
|---|---|---|---|---|
|Classificação|General (compact) (Geral [compacto])|5 MB|13 ms|5 ms|
|Deteção de Objetos|General (compact) (Geral [compacto])|45 MB|35 ms|5 ms|
|Deteção de Objetos|Geral (compacto) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Kit Vision Ai Dev)

Quando um domínio compacto é selecionado, é fornecida uma opção extra "Capacidades de exportação" que permite distinguir entre "Plataformas Básicas" e "Vision AI Dev Kit".

No âmbito _das capacidades de exportação,_ as duas opções são:

- Plataformas básicas (Tensorflow, CoreML, ONNX, etc.)
- Kit Vision AI Dev.

Quando o _Kit Vision AI Dev_ é selecionado, os _domínios_ _Genérico,_ Marcos e _Retalho,_ mas não os domínios compactos _de alimentos_ estão disponíveis para classificação de imagem, enquanto tanto _o Geral (compacto)_ como o _General (compacto) [compacto) [S1]_ estão disponíveis para deteção de objetos.

>[!NOTE]
>O domínio __geral (compacto)__ para deteção de objetos requer uma lógica especial de pós-processamento. Para obter os detalhes, consulte um roteiro de exemplo no pacote zip exportado. Se precisar de um modelo sem a lógica de pós-processamento, utilize __o General (compacto) [S1]__.

>[!IMPORTANT]
>Não há garantias de que os modelos exportados dêem exatamente o mesmo resultado que a previsão da API na nuvem. Uma ligeira diferença na plataforma de execução ou na implementação do pré-processamento pode causar uma maior diferença nas saídas do modelo. Para obter os detalhes da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
